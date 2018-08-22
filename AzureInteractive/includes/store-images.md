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
<span data-ttu-id="7c652-103">O Azure Cosmos DB é uma base de dados com vários modelos, sem servidor e de distribuição global da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7c652-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="7c652-104">Neste módulo, vai aprender a utilizar as Funções do Azure para armazenar e obter os metadados de imagem como documentos JSON no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="7c652-105">Criar uma conta, base de dados e coleção do Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="7c652-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="7c652-106">Uma conta do Cosmos DB é um recurso do Azure que contém bases de dados do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="7c652-107">Certifique-se de que está ainda com sessão iniciada no Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="7c652-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="7c652-108">Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="7c652-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="7c652-109">Crie uma conta do Cosmos DB com um nome exclusivo, no mesmo grupo de recursos que os outros recursos neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c652-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="7c652-110">Depois de criar a conta do Cosmos DB, crie uma nova base de dados com o nome **imagesdb** na conta.</span><span class="sxs-lookup"><span data-stu-id="7c652-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="7c652-111">Depois de criar a base de dados, crie uma nova coleção com o nome **imagens** na base de dados com um débito de 400 unidades de pedido (RUs).</span><span class="sxs-lookup"><span data-stu-id="7c652-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="7c652-112">Guardar um documento do Cosmos DB quando é criada uma miniatura</span><span class="sxs-lookup"><span data-stu-id="7c652-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="7c652-113">O enlace de saída do Cosmos DB permite-lhe criar documentos numa coleção do Cosmos DB nas Funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="7c652-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="7c652-114">Nos passos seguintes, vai configurar um enlace de saída do Cosmos DB na função **ResizeImage** e modificar a função para devolver um documento (objeto) e guardá-lo.</span><span class="sxs-lookup"><span data-stu-id="7c652-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="7c652-115">Abra a aplicação de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="7c652-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="7c652-116">No painel de navegação esquerdo, expanda a função **ResizeImage** e, em seguida, selecione **Integrar**.</span><span class="sxs-lookup"><span data-stu-id="7c652-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="7c652-117">Em **Saídas**, clique em **Nova Saída**.</span><span class="sxs-lookup"><span data-stu-id="7c652-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="7c652-118">Aceda ao item **do Azure Cosmos DB** e selecione-o.</span><span class="sxs-lookup"><span data-stu-id="7c652-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="7c652-119">Em seguida, clique em **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="7c652-119">Then click **Select**.</span></span>

    ![Selecionar Nova Saída](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="7c652-121">Preencha os campos em **saída do Azure Cosmos DB** com os seguintes valores.</span><span class="sxs-lookup"><span data-stu-id="7c652-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="7c652-122">Definição</span><span class="sxs-lookup"><span data-stu-id="7c652-122">Setting</span></span>      |  <span data-ttu-id="7c652-123">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="7c652-123">Suggested value</span></span>   | <span data-ttu-id="7c652-124">Descrição</span><span class="sxs-lookup"><span data-stu-id="7c652-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="7c652-125">**Nome do parâmetro do documento**</span><span class="sxs-lookup"><span data-stu-id="7c652-125">**Document parameter name**</span></span> | <span data-ttu-id="7c652-126">Selecionar **Utilizar valor de retorno de função**</span><span class="sxs-lookup"><span data-stu-id="7c652-126">Select **Use function return value**</span></span> | <span data-ttu-id="7c652-127">O valor da caixa de texto é automaticamente definido como **$return**.</span><span class="sxs-lookup"><span data-stu-id="7c652-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="7c652-128">**Nome da base de dados**</span><span class="sxs-lookup"><span data-stu-id="7c652-128">**Database name**</span></span> | <span data-ttu-id="7c652-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="7c652-129">imagesdb</span></span> | <span data-ttu-id="7c652-130">Utilize o nome da base de dados que criou.</span><span class="sxs-lookup"><span data-stu-id="7c652-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="7c652-131">**Nome da coleção**</span><span class="sxs-lookup"><span data-stu-id="7c652-131">**Collection name**</span></span> | <span data-ttu-id="7c652-132">imagens</span><span class="sxs-lookup"><span data-stu-id="7c652-132">images</span></span> | <span data-ttu-id="7c652-133">Utilize o nome da coleção que criou.</span><span class="sxs-lookup"><span data-stu-id="7c652-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="7c652-134">Ao lado de **Ligação de conta do Azure Cosmos DB**, clique em **novo**.</span><span class="sxs-lookup"><span data-stu-id="7c652-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="7c652-135">Selecione a conta do Cosmos DB que criou anteriormente.</span><span class="sxs-lookup"><span data-stu-id="7c652-135">Select the Cosmos DB account you previously created.</span></span>

    ![Introduzir as definições para o enlace de saída do Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="7c652-137">Clique em **Guardar** para criar o enlace de saída do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="7c652-138">Clique no nome da função **ResizeImage** no lado esquerdo para abrir a função.</span><span class="sxs-lookup"><span data-stu-id="7c652-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="7c652-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="7c652-139">**C#**</span></span>

    1. <span data-ttu-id="7c652-140">(C#) Alterar o tipo de retorno da função de **nulo** para **objeto**.</span><span class="sxs-lookup"><span data-stu-id="7c652-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="7c652-141">(C#) No final da função, adicione o seguinte bloco de código para devolver o documento e guardá-lo:</span><span class="sxs-lookup"><span data-stu-id="7c652-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx para a função ResizeImages depois das modificações](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="7c652-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7c652-143">**JavaScript**</span></span>

    1. <span data-ttu-id="7c652-144">(JavaScript) Altere a declaração de `context.done()` na cláusula `else` para retornar o documento e guardá-lo no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

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

1. <span data-ttu-id="7c652-145">Clique em **Registos** abaixo a janela de código para expandir o painel de registos.</span><span class="sxs-lookup"><span data-stu-id="7c652-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="7c652-146">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="7c652-146">Click **Save**.</span></span> <span data-ttu-id="7c652-147">Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.</span><span class="sxs-lookup"><span data-stu-id="7c652-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="7c652-148">Criar uma função para listar imagens do Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="7c652-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="7c652-149">A aplicação Web requer uma API para obter os metadados de imagem do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="7c652-150">Nos passos seguintes.</span><span class="sxs-lookup"><span data-stu-id="7c652-150">In the following steps.</span></span> <span data-ttu-id="7c652-151">Vai criar uma função acionada por HTTP que utiliza um enlace de entrada do Cosmos DB para consultar a coleção de base de dados.</span><span class="sxs-lookup"><span data-stu-id="7c652-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="7c652-152">Na sua aplicação de funções, coloque o cursor sobre **Funções** no lado esquerdo e clique em **+** para criar uma nova função.</span><span class="sxs-lookup"><span data-stu-id="7c652-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="7c652-153">Aceda ao modelo **HttpTrigger** e selecione-o.</span><span class="sxs-lookup"><span data-stu-id="7c652-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="7c652-154">Utilize estes valores para criar uma função que gera um URL de GetImages.</span><span class="sxs-lookup"><span data-stu-id="7c652-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="7c652-155">Definição</span><span class="sxs-lookup"><span data-stu-id="7c652-155">Setting</span></span>      |  <span data-ttu-id="7c652-156">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="7c652-156">Suggested value</span></span>   | <span data-ttu-id="7c652-157">Descrição</span><span class="sxs-lookup"><span data-stu-id="7c652-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="7c652-158">**Dar um nome à função**</span><span class="sxs-lookup"><span data-stu-id="7c652-158">**Name your function**</span></span> | <span data-ttu-id="7c652-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="7c652-159">GetImages</span></span> | <span data-ttu-id="7c652-160">Escreva este nome exatamente como mostrado, para que a aplicação possa detetar a função.</span><span class="sxs-lookup"><span data-stu-id="7c652-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="7c652-161">**Nível de autorização**</span><span class="sxs-lookup"><span data-stu-id="7c652-161">**Authorization level**</span></span> | <span data-ttu-id="7c652-162">Anónimo</span><span class="sxs-lookup"><span data-stu-id="7c652-162">Anonymous</span></span> | <span data-ttu-id="7c652-163">Permita que a função fique acessível publicamente.</span><span class="sxs-lookup"><span data-stu-id="7c652-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="7c652-164">Clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c652-164">Click **Create**.</span></span>

1. <span data-ttu-id="7c652-165">Quando a nova função estiver criada, clique em **Integrar** sob o nome da função no painel de navegação esquerdo.</span><span class="sxs-lookup"><span data-stu-id="7c652-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="7c652-166">Clique em **Nova Entrada** e selecione **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="7c652-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Selecionar Nova Entrada](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="7c652-168">Clique em **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="7c652-168">Click **Select**.</span></span>

1. <span data-ttu-id="7c652-169">Preencha os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="7c652-169">Fill out the following values:</span></span>

    | <span data-ttu-id="7c652-170">Definição</span><span class="sxs-lookup"><span data-stu-id="7c652-170">Setting</span></span>      |  <span data-ttu-id="7c652-171">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="7c652-171">Suggested value</span></span>   | <span data-ttu-id="7c652-172">Descrição</span><span class="sxs-lookup"><span data-stu-id="7c652-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="7c652-173">**Nome do parâmetro do documento**</span><span class="sxs-lookup"><span data-stu-id="7c652-173">**Document parameter name**</span></span> | <span data-ttu-id="7c652-174">documentos</span><span class="sxs-lookup"><span data-stu-id="7c652-174">documents</span></span> | <span data-ttu-id="7c652-175">Corresponde ao nome do parâmetro na função.</span><span class="sxs-lookup"><span data-stu-id="7c652-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="7c652-176">**Nome da base de dados**</span><span class="sxs-lookup"><span data-stu-id="7c652-176">**Database name**</span></span> | <span data-ttu-id="7c652-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="7c652-177">imagesdb</span></span> |  |
    | <span data-ttu-id="7c652-178">**Nome da coleção**</span><span class="sxs-lookup"><span data-stu-id="7c652-178">**Collection name**</span></span> | <span data-ttu-id="7c652-179">imagens</span><span class="sxs-lookup"><span data-stu-id="7c652-179">images</span></span> |  |
    | <span data-ttu-id="7c652-180">**Consulta SQL**</span><span class="sxs-lookup"><span data-stu-id="7c652-180">**SQL query**</span></span> | <span data-ttu-id="7c652-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="7c652-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="7c652-182">Obtenha documentos, documentos mais recentes primeiro.</span><span class="sxs-lookup"><span data-stu-id="7c652-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="7c652-183">**Ligação de conta do Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="7c652-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="7c652-184">Selecionar uma cadeia de ligação existente</span><span class="sxs-lookup"><span data-stu-id="7c652-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="7c652-185">Clique em **Guardar** para criar um enlace de entrada.</span><span class="sxs-lookup"><span data-stu-id="7c652-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="7c652-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="7c652-186">**C#**</span></span>

    1. <span data-ttu-id="7c652-187">Clique no nome da função para abrir a janela de código e, em seguida, substitua todo o **run.csx** com o conteúdo em [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="7c652-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="7c652-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="7c652-188">**JavaScript**</span></span>

    1. <span data-ttu-id="7c652-189">Clique no nome da função para abrir a janela de código e, em seguida, substitua todo o **index.js** com o conteúdo em [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="7c652-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="7c652-190">Clique em **Registos** abaixo a janela de código para expandir o painel de registos.</span><span class="sxs-lookup"><span data-stu-id="7c652-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="7c652-191">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="7c652-191">Click **Save**.</span></span> <span data-ttu-id="7c652-192">Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.</span><span class="sxs-lookup"><span data-stu-id="7c652-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="7c652-193">Testar a aplicação</span><span class="sxs-lookup"><span data-stu-id="7c652-193">Test the application</span></span>

1. <span data-ttu-id="7c652-194">Abra a aplicação num browser.</span><span class="sxs-lookup"><span data-stu-id="7c652-194">Open the application in a browser.</span></span> <span data-ttu-id="7c652-195">Selecione um ficheiro de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="7c652-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="7c652-196">Após alguns segundos, a miniatura da nova imagem aparece na página.</span><span class="sxs-lookup"><span data-stu-id="7c652-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="7c652-197">No portal do Azure, utilize a caixa de Pesquisa para procurar a sua conta do Cosmos DB por nome.</span><span class="sxs-lookup"><span data-stu-id="7c652-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="7c652-198">Clique nela para abri-la.</span><span class="sxs-lookup"><span data-stu-id="7c652-198">Click it to open it.</span></span>

1. <span data-ttu-id="7c652-199">Clique em **Data Explorer** à esquerda para procurar documentos e coleções.</span><span class="sxs-lookup"><span data-stu-id="7c652-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="7c652-200">Sob a base de dados **imagesdb**, selecione a coleção **imagens**.</span><span class="sxs-lookup"><span data-stu-id="7c652-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="7c652-201">Certifique-se de que um documento foi criado para a imagem carregada.</span><span class="sxs-lookup"><span data-stu-id="7c652-201">Confirm that a document was created for the uploaded image.</span></span>

    ![O Data Explorer mostra um documento para uma imagem carregada](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="7c652-203">Resumo</span><span class="sxs-lookup"><span data-stu-id="7c652-203">Summary</span></span>

<span data-ttu-id="7c652-204">Neste módulo, aprendeu a criar uma conta, base de dados e coleção do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="7c652-205">Também aprendeu a utilizar os enlaces do Cosmos DB para guardar e obter os metadados de imagem na coleção do Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7c652-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="7c652-206">Em seguida, vai aprender a gerar uma legenda para cada imagem carregada, automaticamente, com os Serviços Cognitivos da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7c652-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>