---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079687"
---
O Azure Cosmos DB é uma base de dados com vários modelos, sem servidor e de distribuição global da Microsoft. Neste módulo, vai aprender a utilizar as Funções do Azure para armazenar e obter os metadados de imagem como documentos JSON no Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Criar uma conta, base de dados e coleção do Cosmos DB

Uma conta do Cosmos DB é um recurso do Azure que contém bases de dados do Cosmos DB.

1. Certifique-se de que está ainda com sessão iniciada no Cloud Shell.  Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell. 

1. Crie uma conta do Cosmos DB com um nome exclusivo, no mesmo grupo de recursos que os outros recursos neste tutorial.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Depois de criar a conta do Cosmos DB, crie uma nova base de dados com o nome **imagesdb** na conta.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Depois de criar a base de dados, crie uma nova coleção com o nome **imagens** na base de dados com um débito de 400 unidades de pedido (RUs).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Guardar um documento do Cosmos DB quando é criada uma miniatura

O enlace de saída do Cosmos DB permite-lhe criar documentos numa coleção do Cosmos DB nas Funções do Azure. Nos passos seguintes, vai configurar um enlace de saída do Cosmos DB na função **ResizeImage** e modificar a função para devolver um documento (objeto) e guardá-lo.

1. Abra a aplicação de funções no Portal do Azure.

1. No painel de navegação esquerdo, expanda a função **ResizeImage** e, em seguida, selecione **Integrar**.

1. Em **Saídas**, clique em **Nova Saída**.

1. Aceda ao item **do Azure Cosmos DB** e selecione-o. Em seguida, clique em **Selecionar**.

    ![Selecionar Nova Saída](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Preencha os campos em **saída do Azure Cosmos DB** com os seguintes valores.

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Nome do parâmetro do documento** | Selecionar **Utilizar valor de retorno de função** | O valor da caixa de texto é automaticamente definido como **$return**. |
    | **Nome da base de dados** | imagesdb | Utilize o nome da base de dados que criou. |
    | **Nome da coleção** | imagens | Utilize o nome da coleção que criou. |

1. Ao lado de **Ligação de conta do Azure Cosmos DB**, clique em **novo**. Selecione a conta do Cosmos DB que criou anteriormente.

    ![Introduzir as definições para o enlace de saída do Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Clique em **Guardar** para criar o enlace de saída do Cosmos DB.

1. Clique no nome da função **ResizeImage** no lado esquerdo para abrir a função.

1. **C#**

    1. (C#) Alterar o tipo de retorno da função de **nulo** para **objeto**.

    1. (C#) No final da função, adicione o seguinte bloco de código para devolver o documento e guardá-lo:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx para a função ResizeImages depois das modificações](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Altere a declaração de `context.done()` na cláusula `else` para retornar o documento e guardá-lo no Cosmos DB.

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. Clique em **Registos** abaixo a janela de código para expandir o painel de registos.

1. Clique em **Guardar**. Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Criar uma função para listar imagens do Cosmos DB

A aplicação Web requer uma API para obter os metadados de imagem do Cosmos DB. Nos passos seguintes. Vai criar uma função acionada por HTTP que utiliza um enlace de entrada do Cosmos DB para consultar a coleção de base de dados.

1. Na sua aplicação de funções, coloque o cursor sobre **Funções** no lado esquerdo e clique em **+** para criar uma nova função.

1. Aceda ao modelo **HttpTrigger** e selecione-o.

1. Utilize estes valores para criar uma função que gera um URL de GetImages.

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Dar um nome à função** | GetImages | Escreva este nome exatamente como mostrado, para que a aplicação possa detetar a função. |
    | **Nível de autorização** | Anónimo | Permita que a função fique acessível publicamente. |

1. Clique em **Criar**.

1. Quando a nova função estiver criada, clique em **Integrar** sob o nome da função no painel de navegação esquerdo.

1. Clique em **Nova Entrada** e selecione **Azure Cosmos DB**. 

    ![Selecionar Nova Entrada](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Clique em **Selecionar**.

1. Preencha os seguintes valores:

    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Nome do parâmetro do documento** | documentos | Corresponde ao nome do parâmetro na função. |
    | **Nome da base de dados** | imagesdb |  |
    | **Nome da coleção** | imagens |  |
    | **Consulta SQL** | select * from c order by c._ts desc | Obtenha documentos, documentos mais recentes primeiro. |
    | **Ligação de conta do Azure Cosmos DB** | Selecionar uma cadeia de ligação existente |  |

1. Clique em **Guardar** para criar um enlace de entrada.

1. **C#**

    1. Clique no nome da função para abrir a janela de código e, em seguida, substitua todo o **run.csx** com o conteúdo em [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Clique no nome da função para abrir a janela de código e, em seguida, substitua todo o **index.js** com o conteúdo em [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Clique em **Registos** abaixo a janela de código para expandir o painel de registos.

1. Clique em **Guardar**. Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.


## <a name="test-the-application"></a>Testar a aplicação

1. Abra a aplicação num browser. Selecione um ficheiro de imagem e carregue-o.

1. Após alguns segundos, a miniatura da nova imagem aparece na página.

1. No portal do Azure, utilize a caixa de Pesquisa para procurar a sua conta do Cosmos DB por nome. Clique nela para abri-la.

1. Clique em **Data Explorer** à esquerda para procurar documentos e coleções.

1. Sob a base de dados **imagesdb**, selecione a coleção **imagens**.

1. Certifique-se de que um documento foi criado para a imagem carregada.

    ![O Data Explorer mostra um documento para uma imagem carregada](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Resumo

Neste módulo, aprendeu a criar uma conta, base de dados e coleção do Cosmos DB. Também aprendeu a utilizar os enlaces do Cosmos DB para guardar e obter os metadados de imagem na coleção do Cosmos DB. Em seguida, vai aprender a gerar uma legenda para cada imagem carregada, automaticamente, com os Serviços Cognitivos da Microsoft.