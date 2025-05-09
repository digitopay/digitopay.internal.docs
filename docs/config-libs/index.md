# Configuração para Leitura de Packages da Organização no GitHub Packages (.NET CLI)

Este guia passo a passo explica como configurar sua máquina para ler e instalar pacotes NuGet privados da sua organização no GitHub Packages usando a .NET CLI (`dotnet`). Isso é necessário para instalar templates `dotnet new` ou consumir outras bibliotecas que sua organização publica.

## Pré-requisitos

* **Conta do GitHub:** Você precisa ter uma conta no GitHub com acesso à organização que hospeda os packages.
* **.NET SDK:** Certifique-se de ter o .NET SDK instalado na sua máquina. Você pode verificar a instalação executando `dotnet --version` no terminal.
* **Acesso à Organização:** Você deve ser membro da organização no GitHub ou ter permissões para acessar seus packages.

## Passo 1: Gerar um Token de Acesso Pessoal (PAT) com Permissões de Leitura

Para interagir com o GitHub Packages, você precisará de um Personal Access Token (PAT) com as permissões adequadas.

1.  **Acesse as configurações de desenvolvedor do GitHub:**
    * No canto superior direito de qualquer página do GitHub, clique na sua **foto de perfil**.
    * No menu suspenso, clique em **Settings**.
    * Na barra lateral esquerda, role para baixo e clique em **Developer settings**.

2.  **Vá para Personal Access Tokens:**
    * Na barra lateral esquerda, clique em **Personal access tokens**.
    * Clique em **Tokens (classic)**.

3.  **Gere um novo token:**
    * Clique no botão **Generate new token**.

4.  **Configure o novo token:**
    * **Note:** Dê um nome descritivo ao seu token (por exemplo, "GitHub Packages Read").
    * **Expiration:** Defina uma data de expiração para o token, se desejar.
    * **Select scopes:** Selecione o escopo **`read:packages`**. Este escopo é essencial para baixar e instalar pacotes do GitHub Package Registry. Se você precisar acessar outros recursos da organização (como repositórios), selecione os escopos apropriados.
    * Clique no botão **Generate token** no final da página.

5.  **Copie e guarde o token:**
    * **Na tela seguinte, o seu novo token será exibido uma única vez.** Copie este token e guarde-o em um lugar seguro. Você não poderá vê-lo novamente.

## Passo 2: Localizar o Arquivo `NuGet.config`

O arquivo de configuração global do NuGet geralmente está localizado no seguinte caminho:

C:\Users{SeuNomeDeUsuário}\AppData\Roaming\NuGet\NuGet.config

Substitua `{SeuNomeDeUsuário}` pelo seu nome de usuário do Windows.

1.  Abra o Explorador de Arquivos e navegue até o caminho especificado no Passo 1.

2.  Abra o arquivo `NuGet.config` com um editor de texto (como o Bloco de Notas, Notepad++, VS Code, etc.).

3.  Localize a seção `<packageSources>`. Se ela não existir, você pode criá-la dentro da tag raiz `<configuration>`.

4.  Adicione uma nova entrada `<add>` dentro da seção `<packageSources>` para o feed da sua organização no GitHub Packages. A estrutura deve ser a seguinte:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
        <packageSources>
            <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
            <add key="digitopay.org" value="https://nuget.pkg.github.com/digitopay/index.json" />
        </packageSources>  
        <packageSourceCredentials>
        <digitopay.org>
            <add key="Username" value="{seu_usuario_do_github}" />
            <add key="ClearTextPassword" value="{seu_token}" />
        </digitopay.org>
        </packageSourceCredentials>
    </configuration>
    ```

    * Substitua `{seu_usuario_do_github}` pelo seu nome de usuário do GitHub (a conta pessoal que gerou o token).
    * Substitua `{seu_token}` pelo token de acesso pessoal com a permissão `read:packages` que você gerou.
    
    * **Importante:** Estamos usando `ClearTextPassword` para armazenar o token em texto, por favor, mantenha o token em segurança e salve em um local seguro.

5.  **Salve as alterações** no arquivo `NuGet.config`.

## Passo 3: Instalar o Template (`dotnet new`)

Com a fonte configurada, você agora pode instalar o template da sua organização.

1.  Abra o terminal.

2.  Execute o seguinte comando para instalar o template:

    ```bash
    dotnet new --install {id_do_template}
    ```

    * `{id_do_template}`: O ID do template que foi publicado (por exemplo, `DigitoPay.Template.Api`). 

    Se você quiser instalar uma versão específica do template, você pode adicionar a versão:

    ```bash
    dotnet new --install {id_do_template}::{versao}
    ```

    Substitua `{versao}` pela versão desejada (por exemplo, `1.0.0`).

3.  **Verifique se o template foi instalado com sucesso:**

    ```bash
    dotnet new --list
    ```

    Procure o nome do seu template na lista de templates disponíveis. Ele deve aparecer na seção referente a templates instalados de pacotes NuGet.

## Passo 4: Usar o Template para Criar um Novo Projeto

Após a instalação bem-sucedida, você pode usar o template para criar um novo projeto:

1.  Navegue até o diretório onde você deseja criar o novo projeto no terminal.

2.  Execute o seguinte comando:

    ```bash
    dotnet new {nome_do_template} -n {nome_do_novo_projeto}
    ```

    * `{nome_do_template}`: O nome do seu template (o mesmo que aparece na lista do `dotnet new --list`, por exemplo, `DigitoPay.Template.Api`).
    * `{nome_do_novo_projeto}`: O nome que você deseja dar à pasta do seu novo projeto.

Parabéns! Você configurou com sucesso sua máquina para ler packages da sua organização no GitHub Packages e instalou o seu template `dotnet new`. Agora você pode usar o template para criar novos projetos.