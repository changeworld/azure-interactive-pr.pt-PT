---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460091"
---
No módulo anterior, viu como uma função sem servidor pode facilitar o carregamento seguro de imagens para o armazenamento de Blobs de um aplicativo Web. Neste módulo, vai criar outra função sem servidor para imagens carregadas e criar miniaturas a partir das mesmas.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Criar um contentor de armazenamento de blobs para as miniaturas

As imagens de tamanho completo são armazenadas num contentor com o nome **imagens**. Vai precisar de outro contentor para armazenar as miniaturas dessas imagens.

1. Certifique-se de que ainda está com sessão iniciada no Cloud Shell (bash).  Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell. 

1. Crie um novo contentor com o nome **miniaturas** na sua conta de armazenamento com acesso público a todos os blobs.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Criar uma função acionada por blobs sem servidor

Um acionador define como uma função é invocada. A função que criares a seguir utiliza um acionador de blob: a função é invocada automaticamente quando um blob (ficheiro de imagem) é carregado para o contentor de **imagens**. Uma função deve ter um acionador. Os acionadores têm dados associados, que é normalmente o payload que acionou a função.

Os enlaces definem como uma função lê ou escreve dados no Azure ou serviços de terceiros. Essa função cria uma versão em miniatura da imagem que a aciona e guarda a miniatura num contentor de *miniaturas*.

1. Abra a aplicação de funções no Portal do Azure.

1. No painel de navegação esquerdo da janela da aplicação de funções, coloque o cursor sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor. Se for apresentada uma página de início rápido, clique em **função Personalizada** para ver uma lista de modelos de função.

1. Aceda ao modelo **BlobTrigger** e selecione-o.

1. Utilize estes valores para criar uma função que cria miniaturas à medida que as imagens são carregadas.

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Language** (Idioma) | C# ou JavaScript | Escolha o seu idioma preferencial. |
    | **Dar um nome à função** | ResizeImage | Escreva este nome exatamente como mostrado, para que a aplicação possa detetar a função. |
    | **Caminho** | images/{name} | Execute a função quando um ficheiro aparece no contentor de **imagens**. |
    | **Informações da conta de armazenamento** | AZURE_STORAGE_CONNECTION_STRING | Utilize a variável de ambiente criada anteriormente com a cadeia de ligação. |

    ![Introduzir as definições para a nova função](media/functions-first-serverless-web-app/3-new-function.png)

1. Clique em **Criar** para criar a função.

1. Quando a função estiver criada, clique em **Integrar** para ver os respetivos enlaces de entrada e saída e acionador.

1. Clique em **Nova saída** para criar um novo enlace de saída.

    ![Selecionar Nova Saída no separador Integrar](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Selecione **Armazenamento de Blobs do Azure** e clique em **Selecionar**. Poderá ter de deslocar para baixo para ver o botão **Selecionar**.

    ![Selecionar armazenamento de Blobs do Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Introduza os seguintes valores.

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Nome do parâmetro de blob** | miniatura | A função usa o parâmetro com este nome para escrever a miniatura. |
    | **Utilizar o valor de retorno da função** | Não |  |
    | **Caminho** | thumbnails/{name} | As miniaturas são escritas num contentor com o nome **miniaturas**. |
    | **Ligação da conta de armazenamento** | AZURE_STORAGE_CONNECTION_STRING | Utilize a variável de ambiente criada anteriormente com a cadeia de ligação. |

    ![Introduzir as definições para o enlace de saída do Blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Clique em **Editor avançado** no canto superior direito da janela para revelar o JSON que representa os enlaces.

    1. (JavaScript) No enlace `blobTrigger`, adicione uma propriedade com o nome `dataType` com um valor de `binary`. Esta ação configura o enlace para passar os conteúdos do blob para a função como dados binários.

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. Clica em **Salvar** para criar o novo enlace.

1. **C#**

    1. (C#) Selecione o nome de função **ResizeImage** no painel de navegação esquerdo para abrir o código fonte da função.

    1. (C#) A função requer um pacote NuGet com o nome **ImageResizer** para gerar as miniaturas. Os pacotes NuGet são adicionados às funções C# usando um ficheiro **project.json**. Para criar o ficheiro, clique em **Ver Ficheiros** à direita para revelar os ficheiros que compõem a função.
    
    1. (C#) Clique em **Adicionar** para adicionar um novo ficheiro com o nome **project.json**.
    
    1. (C#) Copie o conteúdo de [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) para o ficheiro recentemente criado. Guarde o ficheiro. Os pacotes são restaurados automaticamente quando o ficheiro é atualizado.
    
        ![Ficheiro project.json com ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Selecione **run.csx** sob **Ver Ficheiros** e substitui o conteúdo pelo conteúdo em [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Esta função requer que o `jimp` pacote a partir do npm redimensione a fotografia. Para instalar o pacote npm, clique no nome da Function App no painel de navegação esquerdo e clique em **Funcionalidades da Plataforma**.

    1. (JavaScript) Clique em **Consola** para revelar uma janela de consola.

    1. (JavaScript) Execute o comando `npm install jimp` na consola. Esta operação pode demorar alguns minutos até ficar concluída.

    1. (JavaScript) Clique no nome da função **ResizeImage** no painel de navegação esquerdo para revelar a função e substituir todos os **index.js** pelo conteúdo de [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Clique em **Registos** abaixo a janela de código para expandir o painel de registos.

1. Clique em **Guardar**. Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.


## <a name="test-the-serverless-function"></a>Testar a função sem servidor

1. Abra a aplicação num browser. Selecione um ficheiro de imagem e carregue-o. O carregamento é concluído, mas porque ainda não adicionamos a capacidade de exibir imagens, a aplicação não mostra a fotografia carregada.

1. No Cloud Shell, confirme que a imagem foi carregada para o contentor de **imagens**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Certifique-se de que a miniatura foi criada num contentor com o nome **miniaturas**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Obtenha o URL da miniatura.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Abra o URL num browser e confirme que a miniatura foi criada corretamente.

1. Antes de passar para o tutorial seguinte, elimine todos os ficheiros nos contentores de **imagens** e **miniaturas**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Resumo

Neste módulo, criou uma função sem servidor para criar uma miniatura, sempre que uma imagem é carregada para um contentor de armazenamento de Blobs. Em seguida, vai aprender a utilizar o Azure Cosmos DB para armazenar e listar os metadados de imagem.
