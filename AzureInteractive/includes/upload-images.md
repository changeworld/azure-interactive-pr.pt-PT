---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 10/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3779c2e130afa7ee8d5879f30a924e258b7a41e9
ms.sourcegitcommit: fdb43556b8dcf67cb39c18e532b5fab7ac53eaee
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49315981"
---
A aplicação que está a criar é uma galeria de fotos. Esta utiliza JavaScript do lado do cliente para chamar as APIs para carregar e exibir imagens. Neste módulo, vai criar uma API com uma função sem servidor que gera um URL de tempo limitado para carregar uma imagem. A aplicação Web utiliza o URL gerado para carregar uma imagem para o armazenamento de Blobs com a [API REST do armazenamento de Blobs](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Criar um contentor de armazenamento de blobs para as imagens

A aplicação requer um contentor de armazenamento separado para carregar e hospedar imagens.

1. Certifique-se de que ainda está com sessão iniciada no Cloud Shell (bash). Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell.

1.  Crie um novo contentor com o nome **imagens** na sua conta de armazenamento com acesso público a todos os blobs.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

As Funções do Azure são um serviço para executar funções sem servidor. Uma função sem servidor pode ser acionada (chamada) por eventos, tais como um pedido de HTTP, ou quando um blob é criado um contentor de armazenamento.

Uma aplicação de funções do Azure é um contentor para uma ou mais funções sem servidor.

Crie uma nova aplicação de funções do Azure com um nome exclusivo no grupo de recursos que criou anteriormente com o nome **first-serverless-app**. Aplicações de funções requerem uma conta de Armazenamento. Neste tutorial, vai utilizar a conta de armazenamento existente.

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a>Configurar a aplicação de funções

A aplicação de funções neste tutorial requer a versão 1.x do runtime das Funções. Definir a configuração de aplicação `FUNCTIONS_EXTENSION_VERSION` para `~1` afixa a aplicação de funções à versão 1.x mais recente. Configure as definições da aplicação com o comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

No seguinte comando da CLI do Azure, <app_name> é o nome da sua aplicação de funções.

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a>Criar uma função sem servidor acionada por HTTP

A aplicação Web da galeria de fotos faz um pedido de HTTP para uma função sem servidor para gerar um URL de tempo limitado para carregar com segurança uma imagem para o armazenamento de Blobs. A função é acionada pelo pedido de HTTP e utiliza o SDK do Armazenamento do Microsoft Azure para gerar e devolver um URL seguro.

1. Depois de criada a aplicação de funções, aceda-lhe no Portal do Azure utilizando a caixa de pesquisa e clique para abrir.

    ![Abrir a aplicação de funções](media/functions-first-serverless-web-app/2-search-function-app.png)

1. No painel de navegação esquerdo da janela da aplicação de funções, coloque o cursor sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.

    ![Criar uma nova função](media/functions-first-serverless-web-app/2-new-function.png)

1. Clique em **função Personalizada** para ver uma lista de modelos de função.

1. Aceda ao modelo **HttpTrigger** e clique no idioma a utilizar (C# ou JavaScript).

1. Utilize estes valores para criar uma função que gera um URL de carregamento de blobs.

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Language** (Idioma) | C# ou JavaScript | Selecione o idioma que pretende utilizar. |
    | **Dar um nome à função** | GetUploadUrl | Escreva este nome exatamente como mostrado, para que a aplicação possa detetar a função. |
    | **Nível de autorização** | Anónimo | Permita que a função fique acessível publicamente. |

    ![Introduzir as definições para uma nova função acionada por HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Clique em **Criar** para criar a função.

1. **C#** 

    1. Quando for apresentado o código fonte da função, substitua todo o **run.csx** pelo conteúdo de [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Esta função requer que o pacote `azure-storage` do npm para gerar o token de assinatura de acesso partilhado (SAS) necessário para criar o URL seguro. Para instalar o pacote npm, clique no nome da Function App no painel de navegação esquerdo e clique em **Funcionalidades da Plataforma**.

    1. (JavaScript) Clique em **Consola** para revelar uma janela de consola.

        ![Abrir uma janela de consola](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Certifique-se de que o diretório atual é **d:\home\site\wwwroot** ao executar o comando `cd d:\home\site\wwwroot`.

    1. (JavaScript) Execute o comando `npm init -y` para criar ficheiro **package.json** vazio.

    1. (JavaScript) Execute o comando `npm install --save azure-storage` na consola para instalar o pacote e guardá-lo em **package.json**. Esta operação pode demorar alguns minutos até ficar concluída.

    1. (JavaScript) Clique no nome da função (**GetUploadUrl**) no painel de navegação esquerdo para revelar a função e substituir todos os **index.js** pelo conteúdo de [**/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![index.js após atualização](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Clique em **Registos** abaixo a janela de código para expandir o painel de registos.

1. Clique em **Guardar**. Verifique o painel de registos para garantir que a função está compilada corretamente.

A função gera o que é chamado de um URL de assinatura de acesso partilhado (SAS), que é utilizado para carregar um ficheiro para o armazenamento de Blobs. O URL de SAS é válido durante um curto período de tempo e só permite que um único ficheiro seja carregado. Consulte a documentação de armazenamento de Blobs para obter mais informações sobre a [utilização de assinaturas de acesso partilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Adicionar uma variável de ambiente para a cadeia de ligação de armazenamento

A função que acabou de criar requer uma cadeia de ligação para a conta de Armazenamento, para que possa gerar o URL de SAS. Em vez de codificar a cadeia de ligação no corpo da função, pode ser armazenada como uma definição da aplicação. As definições da aplicação são acessíveis como variáveis de ambiente por todas as funções na aplicação de Funções.

1. No Cloud Shell, consulte a cadeia de ligação da conta de Armazenamento e guarde-a numa variável com o nome **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Certifique-se de que a variável é definida com êxito.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Crie uma nova definição de aplicação com o nome **AZURE_STORAGE_CONNECTION_STRING**, utilizando o valor guardado do passo anterior.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Certifique-se de que a saída do comando contém a nova definição de aplicação com o valor correto.


## <a name="test-the-serverless-function"></a>Testar a função sem servidor

Além de criar e editar funções, o portal do Azure também fornece uma ferramenta incorporada para testar as funções.

1. Para testar a função HTTP sem servidor, clique no separador **Testar** no lado direito da janela de código, para expandir o painel de teste.

1. Altere o **método Http** para **GET**.

1. Em **Consulta**, clique em *Adicionar parâmetro** e adicione o seguinte parâmetro:

    | nome      |  valor   | 
    | --- | --- |
    | filename | image1.jpg |

1. Clique em **Executar** no painel de teste para enviar um pedido de HTTP para a função.

1. A função devolve um URL de carregamento na saída. A execução de função é apresentada no painel de registos.

    ![Os registos com uma função foram executados com êxito](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>Configurar CORS na aplicação de funções

Como front-end da aplicação está hospedado no armazenamento de Blobs, tem um nome de domínio diferente daquele da aplicação de Função do Azure. Para que o JavaScript do lado do cliente chame a função que acabou de criar com êxito, a aplicação de funções tem de ser configurada para a partilha de recursos transversais às origens (CORS).

1. Na barra de navegação esquerda da janela da aplicação de funções, clique no nome da sua aplicação de funções.

1. Clique em **Funcionalidades de plataforma** para ver uma lista das funcionalidades avançadas.

1. Em **API**, clique em **CORS**.

    ![Selecionar CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Adicione uma origem de permissões para o URL a partir da aplicação do módulo anterior, omitindo **/** à direita (por exemplo: `https://firstserverlessweb.z4.web.core.windows.net`).

    ![Definições de CORS mostrando um URL da aplicação Web adicionado sem servidor](media/functions-first-serverless-web-app/2-add-cors.png)

1. Clique em **Guardar**.

1. C#

   1. (C#) Navegue de volta para a função `GetUploadUrl` e, em seguida, selecione o separador **Integrar**.

   1. (C#) Em Métodos HTTP Selecionados, selecione **OPÇÕES**.

      **OBTER**, **PUBLICAR**, e **OPÇÕES** devem todos ser selecionados. CORS utiliza o método OPÇÕES, que não está selecionado por predefinição para funções C#.  

   1. (C#) Clique em **Guardar**.

1. Ainda no portal, navegue para a aplicação de funções, selecione o separador **Descrição geral** e, em seguida, clique em **Reiniciar** para garantir que as alterações a CORS entrem em vigor.

## <a name="configure-cors-in-the-storage-account"></a>Configurar CORS na conta de Armazenamento

Uma vez que a aplicação também faz chamadas de JavaScript do lado do cliente para o Armazenamento de Blobs para carregar ficheiros, tem também de configurar a conta de Armazenamento para CORS.

1. Execute o seguinte comando para permitir que todas as origens carreguem ficheiros para a conta de Armazenamento.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>Modificar a aplicação Web para carregar imagens

A aplicação Web obtém as definições a partir do ficheiro denominado **settings.js**. Nos passos seguintes, vai criar o ficheiro com o Cloud Shell e, em seguida, definir `window.apiBaseUrl` para o URL da aplicação de Funções e `window.blobBaseUrl` para o URL do ponto final do Armazenamento de Blobs do Azure.

1. No Cloud Shell, certifique-se de que o diretório atual é a pasta **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Consulte o URL da aplicação de funções e armazene-o numa variável de bash chamada **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Certifique-se de que a variável está corretamente definida.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Para definir o URI base de chamadas API para a aplicação de funções, crie **settings.js** e adicione o URL da aplicação de funções semelhante ao seguinte.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    Pode fazer a alteração executando o seguinte comando ou utilizando um editor de linha de comandos como o VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Certifique-se de que o ficheiro foi escrito com êxito.

    ```azurecli
    cat settings.js
    ```

1. Consulte o URL base do Armazenamento de Blobs e armazene-o numa variável de bash denominada **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Certifique-se de que a variável está corretamente definida.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Para definir o URI base de chamadas API para a aplicação de funções, anexe o URL de armazenamento, como a linha de código seguinte, para **settings.js**.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    Pode fazer a alteração executando o seguinte comando ou utilizando um editor de linha de comandos como o VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Certifique-se de que o ficheiro foi escrito com êxito e que contém agora duas linhas.

    ```azurecli
    cat settings.js
    ```

1. Carregue o ficheiro para o armazenamento de Blobs.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Testar a aplicação Web

Neste momento, a aplicação de galeria é capaz de carregar uma imagem para o armazenamento de Blobs, mas não pode exibir imagens. Vai tentar chamar uma função `GetImages` que ainda não existe uma vez que a criou num módulo posterior. Essa chamada falhará e a página Web parecerá estar presa em "A analisar...", mas a imagem que selecionou será carregada com êxito.

Pode verificar se uma imagem é carregada com êxito ao verificar o conteúdo do contentor de **imagens** no portal do Azure.

1. Numa janela do browser, navegue até à aplicação. Selecione um ficheiro de imagem e carregue-o. O carregamento é concluído, mas porque ainda não adicionamos a capacidade de exibir imagens, a aplicação não mostra a fotografia carregada. (A página Web parece estar bloqueada em "A analisar a imagem..."; este problema será resolvido mais tarde.)

1. No Cloud Shell, confirme que a imagem foi carregada para o contentor de **imagens**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Antes de passar para o tutorial seguinte, elimine todos os ficheiros no contentor de **imagens**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Resumo

Neste módulo, criou uma aplicação de Função do Azure e aprendeu a usar uma função sem servidor para permitir que uma aplicação Web para carregar imagens para o armazenamento de Blobs. Em seguida, irá aprender a criar miniaturas para as imagens carregadas, usando uma função sem servidor acionada por Blobs.
