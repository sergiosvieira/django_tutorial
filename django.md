# Tutorial de Criação de Tela de Login com Django, HTML e CSS para um Sistema de Almoxarifado

Vamos construir uma aplicação web simples usando Django, HTML e CSS que inclui uma tela de login para um sistema de almoxarife. Este tutorial fornecerá um passo-a-passo desde a criação do projeto Django até a implementação de uma interface completa de login.

---

## Etapa 1: Criar o Projeto Django

1. Crie e ative um ambiente Conda:

   ```sh
   conda create -n django_env python=3.12
   conda activate django_env
   ```

2. Instale o Django (certifique-se de que o Python está instalado no ambiente):

   ```sh
   pip install django
   ```

3. Crie o projeto:

   ```sh
   django-admin startproject almoxarife_project
   ```

4. Entre no diretório do projeto:

   ```sh
   cd almoxarifado_project
   ```

5. Crie um novo aplicativo chamado `almoxarife`:

   ```sh
   python manage.py startapp almoxarife
   ```

6. Adicione o aplicativo `almoxarife` ao `settings.py` do projeto:

   ```python
   INSTALLED_APPS = [
       ...
       'almoxarife',
   ]
   ```

---

# Explicações

- O comando `django-admin startproject` é usado para criar a estrutura inicial de um projeto Django, incluindo diretórios e arquivos de configuração necessários para iniciar o desenvolvimento. Já o comando `startapp` é usado para criar um novo aplicativo dentro desse projeto, responsável por uma funcionalidade específica. Por exemplo, você pode ter um aplicativo para gerenciar usuários e outro para gerenciar o inventário. O comando `startapp` é adequado para criar a tela de login, pois permite encapsular toda a lógica de autenticação e as interfaces relacionadas em um único módulo, facilitando a organização e a reutilização do código.

---

## Etapa 2: Definir URLs

1. Crie um arquivo `urls.py` dentro do app `almoxarife`:

   ```python
   # almoxarife/urls.py
   from django.urls import path  # Importa o método path, que é usado para definir URLs.
   from . import views  # Importa o módulo views do diretório atual para associar as URLs às views.

   urlpatterns = [
       path('login/', views.login_view, name='login'),  # Associa a URL 'login/' à view 'login_view', com o nome 'login'.
   ]
   ```

2. Configure as URLs do projeto principal para incluir as URLs do app `almoxarife`:

   ```python
   # almoxarifado_project/urls.py
   from django.urls import include, path

   urlpatterns = [
       path('almoxarife/', include('almoxarife.urls')),
       path('admin/', admin.site.urls),
   ]
   ```

---

## Navegação de Links no Django

No Django, a navegação entre páginas é feita através de URLs configuradas, que associam endereços específicos a funções de visualização (`views`). A seguir, explicamos como essa navegação funciona com vários exemplos.

1. URLs e `path()`: No Django, as URLs são definidas nos arquivos `urls.py` usando o método `path()`. Cada caminho é associado a uma função de view que processa a solicitação e retorna uma resposta. Por exemplo:

   ```python
   # almoxarife/urls.py
   from django.urls import path
   from . import views

   urlpatterns = [
       path('login/', views.login_view, name='login'),  # Associa a URL 'login/' à view 'login_view'.
       path('dashboard/', views.dashboard_view, name='dashboard'),  # Exemplo adicional de URL associada a outra view.
   ]
   ```

   - Neste exemplo, quando o usuário acessa `/login/`, a função `login_view` é chamada.
   - URLs são geralmente nomeadas (por exemplo, `name='login'`) para facilitar referências em templates HTML.

2. URLs no Projeto Principal: O arquivo de URLs do projeto principal (`almoxarifado_project/urls.py`) é onde você conecta as URLs dos diferentes aplicativos ao projeto Django.

   ```python
   # almoxarifado_project/urls.py
   from django.urls import include, path

   urlpatterns = [
       path('almoxarife/', include('almoxarife.urls')),  # Inclui as URLs do app almoxarife.
       path('admin/', admin.site.urls),  # URL para a interface administrativa do Django.
   ]
   ```

   - Aqui, o caminho `'almoxarife/'` aponta para as URLs definidas no aplicativo `almoxarife`.

3. Uso de `reverse()` e `{% url %}`: Para gerar URLs dinamicamente em templates e no código Python, Django oferece o método `reverse()` e a tag `{% url %}`:

   - Tag `{% url %}`: Usada nos templates para gerar URLs. Por exemplo:
     ```html
     <a href="{% url 'login' %}">Login</a>
     ```
     - Aqui, a tag `{% url 'login' %}` gera o link para a URL nomeada `'login'`.
   - Método `reverse()`: Usado no código Python para obter URLs a partir de seus nomes. Por exemplo:
     ```python
     from django.urls import reverse
     login_url = reverse('login')
     ```
     - Isso é útil quando você precisa gerar URLs dinamicamente, como ao redirecionar o usuário após uma ação.

4. Incluindo URLs de Aplicativos: A inclusão de URLs de diferentes aplicativos ajuda a manter a modularidade do projeto. Cada aplicativo pode ter seu próprio arquivo `urls.py` que define suas rotas.

   ```python
   # Exemplo de inclusão no projeto principal
   path('almoxarife/', include('almoxarife.urls'))
   ```

   - Isso permite que todas as URLs do aplicativo `almoxarife` sejam acessadas sob o prefixo `/almoxarife/`.

5. Navegação entre Páginas: Após configurar as URLs e views, você pode criar links nos templates para permitir a navegação entre páginas. Aqui está um exemplo de como criar um menu de navegação:

   ```html
   <nav>
       <ul>
           <li><a href="{% url 'login' %}">Login</a></li>
           <li><a href="{% url 'dashboard' %}">Dashboard</a></li>
       </ul>
   </nav>
   ```

   - Os links são gerados dinamicamente usando a tag `{% url %}`, garantindo que, mesmo que as URLs mudem, os links ainda apontarão para os destinos corretos.

---

## Etapa 3: Criar a View de Login

1. No arquivo `views.py` do app `almoxarife`, crie a view de login:
   ```python
   # almoxarife/views.py
   from django.shortcuts import render, redirect  # Importa funções para renderizar templates e redirecionar URLs.
   from django.contrib.auth import authenticate, login  # Importa funções para autenticar e fazer login de usuários.
   from django.contrib import messages  # Importa para exibir mensagens ao usuário.

   def login_view(request):
       if request.method == 'POST':
           username = request.POST['username']  # Obtém o nome de usuário do formulário.
           password = request.POST['password']  # Obtém a senha do formulário.
           user = authenticate(request, username=username, password=password)  # Autentica o usuário.
           if user is not None:
               login(request, user)  # Faz o login do usuário se a autenticação for bem-sucedida.
               return redirect('home')  # Redireciona para a página inicial após login bem-sucedido.
           else:
               messages.error(request, 'Usuário ou senha inválidos.')  # Exibe uma mensagem de erro se a autenticação falhar.

       return render(request, 'almoxarife/login.html')  # Renderiza o template de login.
   ```

---

## Verbos do HTTP no Django

Antes de avançarmos para a criação da view de login, é importante entender os verbos HTTP, que são amplamente usados em interações web, inclusive em Django. Aqui estão os principais verbos e suas funções:

1. GET: Este verbo é usado para solicitar dados de um servidor. Ele é o mais comum e é utilizado ao navegar para páginas, carregar dados, ou quando você simplesmente quer obter informações sem fazer qualquer modificação. Exemplo em Django:

   ```python
   def home_view(request):
       # Esta view é acessada por uma requisição GET quando alguém acessa a URL associada.
       return render(request, 'almoxarife/home.html')
   ```

2. POST: Usado para enviar dados ao servidor para serem processados, geralmente ao submeter um formulário. No contexto de Django, o método POST é comum para operações que modificam dados, como login, registro ou envio de informações. Exemplo:

   ```python
   def login_view(request):
       if request.method == 'POST':  # O método POST é usado para autenticar o usuário.
           username = request.POST['username']
           password = request.POST['password']
           # lógica de autenticação...
   ```

3. PUT: Este verbo é usado para atualizar dados existentes no servidor. No Django, é mais comum em APIs REST, especialmente quando utilizamos o Django REST Framework. O PUT substitui completamente o recurso existente.

4. PATCH: Semelhante ao PUT, mas ao invés de substituir todo o recurso, o PATCH é usado para fazer atualizações parciais.

5. DELETE: Como o nome sugere, este verbo é usado para deletar um recurso do servidor. Também é mais comum em APIs REST. Exemplo:

   ```python
   from django.http import HttpResponse

   def delete_item(request, item_id):
       if request.method == 'DELETE':
           # lógica para deletar item...
           return HttpResponse('Item deletado', status=204)
   ```

Esses verbos são fundamentais para definir o tipo de ação que a aplicação irá realizar em resposta a uma solicitação HTTP. Em Django, eles são manipulados principalmente através das views e formulários.

---

## Etapa 4: Criar o Template HTML

1. Crie uma pasta `templates` dentro do app `almoxarife` e dentro dela uma subpasta `almoxarife`:

   ```
   almoxarife/
       templates/
           almoxarife/
               login.html
   ```

2. Crie o arquivo `login.html`:

   ```html
   <!-- almoxarife/templates/almoxarife/login.html -->
   <!DOCTYPE html>
   <html lang="pt-br">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Login - Almoxarifado</title>
       <link rel="stylesheet" href="{% static 'almoxarife/login.css' %}">
   </head>
   <body>
       <div class="login-container">
           <h2>Login - Almoxarifado</h2>
           {% if messages %}
               {% for message in messages %}
                   <p class="error">{{ message }}</p>
               {% endfor %}
           {% endif %}
           <form method="post">
               {% csrf_token %}
               <div class="input-group">
                   <label for="username">Usuário:</label>
                   <input type="text" id="username" name="username" required>
               </div>
               <div class="input-group">
                   <label for="password">Senha:</label>
                   <input type="password" id="password" name="password" required>
               </div>
               <button type="submit">Entrar</button>
           </form>
       </div>
   </body>
   </html>
   ```

---

## Como Usar Templates no Django

Os templates no Django são usados para separar a lógica de visualização do conteúdo dinâmico de uma página web. Isso permite criar páginas HTML que são mais fáceis de manter e modificar. Vou explicar detalhadamente como utilizar templates no Django, fornecendo exemplos práticos ao longo do caminho.

1. Configuração do Diretório de Templates: No arquivo `settings.py` do seu projeto, você precisa definir onde o Django deve procurar por templates. Por exemplo:

   ```python
   TEMPLATES = [
       {
           'BACKEND': 'django.template.backends.django.DjangoTemplates',
           'DIRS': [BASE_DIR / 'templates'],  # Diretório principal de templates.
           'APP_DIRS': True,
           'OPTIONS': {
               'context_processors': [
                   'django.template.context_processors.debug',
                   'django.template.context_processors.request',
                   'django.contrib.auth.context_processors.auth',
                   'django.contrib.messages.context_processors.messages',
               ],
           },
       },
   ]
   ```

   - A configuração `'DIRS': [BASE_DIR / 'templates']` indica onde os templates globais estão localizados. Além disso, `'APP_DIRS': True` permite que o Django também busque templates nas pastas de cada app.

2. Estrutura de Diretórios: Recomenda-se criar uma pasta `templates` dentro de cada aplicativo. Dessa forma, cada aplicativo terá seus próprios templates organizados:

   ```
   almoxarife/
       templates/
           almoxarife/
               login.html
   ```

   - Neste exemplo, o template `login.html` está na pasta `almoxarife/templates/almoxarife/` para garantir que ele seja exclusivo ao aplicativo `almoxarife`.

3. Criando Templates: Um template Django é um arquivo HTML que pode conter tags especiais do Django para incluir conteúdo dinâmico. Por exemplo:

   ```html
   <!-- almoxarife/templates/almoxarife/login.html -->
   <!DOCTYPE html>
   <html lang="pt-br">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Login - Almoxarifado</title>
       {% load static %}  <!-- Carrega as tags para usar arquivos estáticos, como CSS -->
       <link rel="stylesheet" href="{% static 'almoxarife/login.css' %}">
   </head>
   <body>
       <div class="login-container">
           <h2>Login - Almoxarifado</h2>
           {% if messages %}
               {% for message in messages %}
                   <p class="error">{{ message }}</p>
               {% endfor %}
           {% endif %}
           <form method="post">
               {% csrf_token %}  <!-- Token de segurança para prevenir ataques CSRF -->
               <div class="input-group">
                   <label for="username">Usuário:</label>
                   <input type="text" id="username" name="username" required>
               </div>
               <div class="input-group">
                   <label for="password">Senha:</label>
                   <input type="password" id="password" name="password" required>
               </div>
               <button type="submit">Entrar</button>
           </form>
       </div>
   </body>
   </html>
   ```

   - O exemplo acima inclui tags como `{% csrf_token %}` e `{% load static %}` para funcionalidades específicas de segurança e carregamento de arquivos estáticos, respectivamente.
   - 

### CSRF Token no Django

O CSRF (Cross-Site Request Forgery) Token é uma medida de segurança que o Django utiliza para evitar ataques CSRF. O ataque CSRF ocorre quando um usuário autenticado é induzido a executar ações indesejadas em um site onde está autenticado, geralmente por meio de um link malicioso ou de um formulário escondido em outro site.

O token CSRF é um valor gerado aleatoriamente que é incluído em cada formulário de submissão HTML e verificado no servidor ao processar a requisição POST. Sem esse token, o servidor rejeita a solicitação, evitando que um site externo envie requisições em nome do usuário.

#### Exemplo de Uso do CSRF Token em um Formulário

No Django, para adicionar um token CSRF ao formulário, basta incluir a tag `{% csrf_token %}` dentro do elemento `<form>`:

```html
<form method="post">
    {% csrf_token %}
    <label for="username">Usuário:</label>
    <input type="text" id="username" name="username">
    <button type="submit">Entrar</button>
</form>
```

#### Exemplo de Ataque sem CSRF Token

Imagine que você tenha uma página de transferência de dinheiro e não inclua a proteção do token CSRF. Um atacante poderia criar um formulário em um site externo que enviasse uma solicitação POST para o seu site, como abaixo:

```html
<form action="http://seusite.com/transfer" method="post">
    <input type="hidden" name="conta_destino" value="123456">
    <input type="hidden" name="valor" value="1000">
    <input type="submit" value="Clique aqui">
</form>
```

Se um usuário autenticado no seu site clicar neste link, ele poderá involuntariamente transferir dinheiro, pois não há uma verificação CSRF para garantir que a requisição veio de uma fonte confiável.

O token CSRF ajuda a mitigar esse tipo de ataque, garantindo que apenas requisições originadas diretamente do seu site sejam aceitas.

4. Tags do Template Django: As tags Django são usadas para adicionar lógica dentro dos templates. Algumas das principais tags incluem:

   - `{% for ... %} ... {% endfor %}`: Usada para iteração. Exemplo:
     ```html
     <ul>
         {% for item in itens %}
             <li>{{ item.nome }}</li>
         {% endfor %}
     </ul>
     ```
   - `{% if ... %} ... {% endif %}`: Usada para expressões condicionais. Exemplo:
     ```html
     {% if user.is_authenticated %}
         <p>Bem-vindo, {{ user.username }}!</p>
     {% else %}
         <p>Você não está logado.</p>
     {% endif %}
     ```
   - `{% include %}`: Usada para incluir um template em outro. Exemplo:
     ```html
     {% include 'almoxarife/navbar.html' %}
     ```
     - Isso é útil para reutilizar componentes como cabeçalhos ou barras de navegação em várias páginas.

5. Usando Contexto nos Templates: Ao renderizar um template em uma view, você pode passar dados para ele usando um dicionário chamado "contexto". Exemplo de uma view que passa dados para um template:

   ```python
   from django.shortcuts import render

   def dashboard_view(request):
       itens = [
           {'nome': 'Item 1', 'quantidade': 10},
           {'nome': 'Item 2', 'quantidade': 5},
       ]
       return render(request, 'almoxarife/dashboard.html', {'itens': itens})
   ```

   - No template, você pode acessar esses dados usando `{{ itens }}` e iterar sobre eles com `{% for item in itens %} ... {% endfor %}`.

6. Arquivos Estáticos e Templates: Para incluir CSS, JavaScript ou imagens nos templates, você usa a tag `{% static %}`. É importante garantir que o Django consiga encontrar esses arquivos configurando corretamente os diretórios estáticos no `settings.py`.

   ```html
   <link rel="stylesheet" href="{% static 'almoxarife/styles.css' %}">
   <img src="{% static 'almoxarife/logo.png' %}" alt="Logo">
   ```

---

## Banco de Dados no Django

Django inclui um ORM (Object-Relational Mapper) poderoso que permite trabalhar com bancos de dados de uma forma intuitiva, sem precisar escrever SQL manualmente. Vamos entender como configurar e utilizar o banco de dados no contexto de nosso projeto de login para o sistema de almoxarifado.

### 1. Configuração do Banco de Dados

Por padrão, o Django usa o banco de dados SQLite, que é ideal para desenvolvimento e prototipagem. Você pode configurar o banco de dados no arquivo `settings.py` do seu projeto:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

- ENGINE: Define o tipo de banco de dados. Pode ser alterado para `'django.db.backends.postgresql'` para usar PostgreSQL, `'django.db.backends.mysql'` para MySQL, entre outros.
- NAME: Especifica o nome e o local do banco de dados.

### 2. Criação de Modelos (Models)

Os models são classes Python que definem a estrutura do banco de dados. Vamos criar um modelo para armazenar informações dos usuários do sistema almoxarifado. Edite o arquivo `models.py` dentro do app `almoxarife`:

```python
# almoxarife/models.py
from django.db import models
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    # Adicionando novos campos para o usuário
    cargo = models.CharField(max_length=100, null=True, blank=True)
    setor = models.CharField(max_length=100, null=True, blank=True)

    def __str__(self):
        return self.username
```

- CustomUser: Estendemos o modelo padrão de usuário do Django (`AbstractUser`) para adicionar campos extras, como `cargo` e `setor`, que são relevantes para um sistema de almoxarifado.

### 3. Migrando o Banco de Dados

Após definir os modelos, precisamos informar ao Django que queremos criar as tabelas no banco de dados. Isso é feito com as migrações:

```sh
python manage.py makemigrations
python manage.py migrate
```

- makemigrations: Cria arquivos de migração com as alterações feitas nos modelos.
- migrate: Aplica as migrações e cria/atualiza as tabelas no banco de dados.

### 4. Registro e Login de Usuários

Como nosso sistema envolve login, vamos usar o modelo de usuário personalizado que criamos. Certifique-se de registrar o modelo no arquivo `admin.py` para que seja possível gerenciar usuários pela interface de administração do Django:

```python
# almoxarife/admin.py
from django.contrib import admin
from .models import CustomUser
from django.contrib.auth.admin import UserAdmin

@admin.register(CustomUser)
class CustomUserAdmin(UserAdmin):
    fieldsets = UserAdmin.fieldsets + (
        (None, {'fields': ('cargo', 'setor')}),
    )
```

- Isso permite adicionar e editar informações adicionais dos usuários, como cargo e setor, através do painel administrativo.

### 5. Criando Superusuário e Testando

Precisamos criar um superusuário para testar o sistema e gerenciar usuários pelo admin:

```sh
python manage.py createsuperuser
```

- Insira o nome de usuário, e-mail e senha quando solicitado.
- Com o superusuário criado, acesse o painel administrativo (`/admin/`) e gerencie os usuários do sistema.

---

## Etapa 5: Adicionar Estilo com CSS

1. Crie uma pasta `static/almoxarife` dentro do app `almoxarife` e nela o arquivo `login.css`:

   ```
   almoxarife/
       static/
           almoxarife/
               login.css
   ```

2. Adicione o CSS para estilizar a página de login:

   ```css
   / almoxarife/static/almoxarife/login.css /
   body {
       font-family: Arial, sans-serif;
       background-color: #f0f0f0;
       display: flex;
       justify-content: center;
       align-items: center;
       height: 100vh;
       margin: 0;
   }

   .login-container {
       background-color: white;
       padding: 20px;
       border-radius: 8px;
       box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
       width: 300px;
   }

   h2 {
       text-align: center;
       margin-bottom: 20px;
   }

   .input-group {
       margin-bottom: 15px;
   }

   label {
       display: block;
       margin-bottom: 5px;
   }

   input {
       width: 100%;
       padding: 8px;
       border: 1px solid #ccc;
       border-radius: 4px;
   }

   button {
       width: 100%;
       padding: 10px;
       background-color: #007bff;
       color: white;
       border: none;
       border-radius: 4px;
       cursor: pointer;
   }

   button:hover {
       background-color: #0056b3;
   }

   .error {
       color: red;
       font-size: 0.9em;
       text-align: center;
   }
   ```

---

## Etapa 6: Configurar Arquivos Estáticos

1. Adicione a configuração de arquivos estáticos no arquivo `settings.py`:

   ```python
   STATIC_URL = '/static/'
   STATICFILES_DIRS = [BASE_DIR / "static"]
   ```

2. Certifique-se de que o template `login.html` inclui `{% load static %}` para carregar arquivos CSS:

   ```html
   {% load static %}
   ```

---

## Etapa 7: Testar o Login

1. Crie um superusuário para testar o login:

   ```sh
   python manage.py createsuperuser
   ```

2. Execute o servidor:

   ```sh
   python manage.py runserver
   ```

3. Acesse a URL `/almoxarife/login/` para testar a tela de login.

---

## Conclusão

Neste tutorial, você aprendeu a:

- Configurar um projeto Django.
- Criar um aplicativo `almoxarife` e uma view de login.
- Definir URLs para a aplicação.
- Criar um template HTML estilizado com CSS para a página de login.

Você pode agora expandir este projeto para incluir registro de usuários e funcionalidades de autenticação avançadas!

