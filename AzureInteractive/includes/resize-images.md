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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079597"
---
<span data-ttu-id="30b54-103">No módulo anterior, viu como uma função sem servidor pode facilitar o carregamento seguro de imagens para o armazenamento de Blobs de um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="30b54-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="30b54-104">Neste módulo, vai criar outra função sem servidor para imagens carregadas e criar miniaturas a partir das mesmas.</span><span class="sxs-lookup"><span data-stu-id="30b54-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="30b54-105">Criar um contentor de armazenamento de blobs para as miniaturas</span><span class="sxs-lookup"><span data-stu-id="30b54-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="30b54-106">As imagens de tamanho completo são armazenadas num contentor com o nome **imagens**.</span><span class="sxs-lookup"><span data-stu-id="30b54-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="30b54-107">Vai precisar de outro contentor para armazenar as miniaturas dessas imagens.</span><span class="sxs-lookup"><span data-stu-id="30b54-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="30b54-108">Certifique-se de que ainda está com sessão iniciada no Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="30b54-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="30b54-109">Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="30b54-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="30b54-110">Crie um novo contentor com o nome **miniaturas** na sua conta de armazenamento com acesso público a todos os blobs.</span><span class="sxs-lookup"><span data-stu-id="30b54-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="30b54-111">Criar uma função acionada por blobs sem servidor</span><span class="sxs-lookup"><span data-stu-id="30b54-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="30b54-112">Um acionador define como uma função é invocada.</span><span class="sxs-lookup"><span data-stu-id="30b54-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="30b54-113">A função que criares a seguir utiliza um acionador de blob: a função é invocada automaticamente quando um blob (ficheiro de imagem) é carregado para o contentor de **imagens**.</span><span class="sxs-lookup"><span data-stu-id="30b54-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="30b54-114">Uma função deve ter um acionador.</span><span class="sxs-lookup"><span data-stu-id="30b54-114">A function must have one trigger.</span></span> <span data-ttu-id="30b54-115">Os acionadores têm dados associados, que é normalmente o payload que acionou a função.</span><span class="sxs-lookup"><span data-stu-id="30b54-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="30b54-116">Os enlaces definem como uma função lê ou escreve dados no Azure ou serviços de terceiros.</span><span class="sxs-lookup"><span data-stu-id="30b54-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="30b54-117">Essa função cria uma versão em miniatura da imagem que a aciona e guarda a miniatura num contentor de *miniaturas*.</span><span class="sxs-lookup"><span data-stu-id="30b54-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="30b54-118">Abra a aplicação de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="30b54-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="30b54-119">No painel de navegação esquerdo da janela da aplicação de funções, coloque o cursor sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.</span><span class="sxs-lookup"><span data-stu-id="30b54-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="30b54-120">Se for apresentada uma página de início rápido, clique em **função Personalizada** para ver uma lista de modelos de função.</span><span class="sxs-lookup"><span data-stu-id="30b54-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="30b54-121">Aceda ao modelo **BlobTrigger** e selecione-o.</span><span class="sxs-lookup"><span data-stu-id="30b54-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="30b54-122">Utilize estes valores para criar uma função que cria miniaturas à medida que as imagens são carregadas.</span><span class="sxs-lookup"><span data-stu-id="30b54-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="30b54-123">Definição</span><span class="sxs-lookup"><span data-stu-id="30b54-123">Setting</span></span>      |  <span data-ttu-id="30b54-124">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="30b54-124">Suggested value</span></span>   | <span data-ttu-id="30b54-125">Descrição</span><span class="sxs-lookup"><span data-stu-id="30b54-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="30b54-126">**Language** (Idioma)</span><span class="sxs-lookup"><span data-stu-id="30b54-126">**Language**</span></span> | <span data-ttu-id="30b54-127">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="30b54-127">C# or JavaScript</span></span> | <span data-ttu-id="30b54-128">Escolha o seu idioma preferencial.</span><span class="sxs-lookup"><span data-stu-id="30b54-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="30b54-129">**Dar um nome à função**</span><span class="sxs-lookup"><span data-stu-id="30b54-129">**Name your function**</span></span> | <span data-ttu-id="30b54-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="30b54-130">ResizeImage</span></span> | <span data-ttu-id="30b54-131">Escreva este nome exatamente como mostrado, para que a aplicação possa detetar a função.</span><span class="sxs-lookup"><span data-stu-id="30b54-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="30b54-132">**Caminho**</span><span class="sxs-lookup"><span data-stu-id="30b54-132">**Path**</span></span> | <span data-ttu-id="30b54-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="30b54-133">images/{name}</span></span> | <span data-ttu-id="30b54-134">Execute a função quando um ficheiro aparece no contentor de **imagens**.</span><span class="sxs-lookup"><span data-stu-id="30b54-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="30b54-135">**Informações da conta de armazenamento**</span><span class="sxs-lookup"><span data-stu-id="30b54-135">**Storage account information**</span></span> | <span data-ttu-id="30b54-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="30b54-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="30b54-137">Utilize a variável de ambiente criada anteriormente com a cadeia de ligação.</span><span class="sxs-lookup"><span data-stu-id="30b54-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Introduzir as definições para a nova função](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="30b54-139">Clique em **Criar** para criar a função.</span><span class="sxs-lookup"><span data-stu-id="30b54-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="30b54-140">Quando a função estiver criada, clique em **Integrar** para ver os respetivos enlaces de entrada e saída e acionador.</span><span class="sxs-lookup"><span data-stu-id="30b54-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="30b54-141">Clique em **Nova saída** para criar um novo enlace de saída.</span><span class="sxs-lookup"><span data-stu-id="30b54-141">Click **New output** to create a new output binding.</span></span>

    ![Selecionar Nova Saída no separador Integrar](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="30b54-143">Selecione **Armazenamento de Blobs do Azure** e clique em **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="30b54-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="30b54-144">Poderá ter de deslocar para baixo para ver o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="30b54-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Selecionar armazenamento de Blobs do Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="30b54-146">Introduza os seguintes valores.</span><span class="sxs-lookup"><span data-stu-id="30b54-146">Enter the following values.</span></span>

    | <span data-ttu-id="30b54-147">Definição</span><span class="sxs-lookup"><span data-stu-id="30b54-147">Setting</span></span>      |  <span data-ttu-id="30b54-148">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="30b54-148">Suggested value</span></span>   | <span data-ttu-id="30b54-149">Descrição</span><span class="sxs-lookup"><span data-stu-id="30b54-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="30b54-150">**Nome do parâmetro de blob**</span><span class="sxs-lookup"><span data-stu-id="30b54-150">**Blob parameter name**</span></span> | <span data-ttu-id="30b54-151">miniatura</span><span class="sxs-lookup"><span data-stu-id="30b54-151">thumbnail</span></span> | <span data-ttu-id="30b54-152">A função usa o parâmetro com este nome para escrever a miniatura.</span><span class="sxs-lookup"><span data-stu-id="30b54-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="30b54-153">**Utilizar o valor de retorno da função**</span><span class="sxs-lookup"><span data-stu-id="30b54-153">**Use function return value**</span></span> | <span data-ttu-id="30b54-154">Não</span><span class="sxs-lookup"><span data-stu-id="30b54-154">No</span></span> |  |
    | <span data-ttu-id="30b54-155">**Caminho**</span><span class="sxs-lookup"><span data-stu-id="30b54-155">**Path**</span></span> | <span data-ttu-id="30b54-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="30b54-156">thumbnails/{name}</span></span> | <span data-ttu-id="30b54-157">As miniaturas são escritas num contentor com o nome **miniaturas**.</span><span class="sxs-lookup"><span data-stu-id="30b54-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="30b54-158">**Ligação da conta de armazenamento**</span><span class="sxs-lookup"><span data-stu-id="30b54-158">**Storage account connection**</span></span> | <span data-ttu-id="30b54-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="30b54-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="30b54-160">Utilize a variável de ambiente criada anteriormente com a cadeia de ligação.</span><span class="sxs-lookup"><span data-stu-id="30b54-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Introduzir as definições para o enlace de saída do Blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="30b54-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="30b54-162">**JavaScript**</span></span>

    1. <span data-ttu-id="30b54-163">(JavaScript) Clique em **Editor avançado** no canto superior direito da janela para revelar o JSON que representa os enlaces.</span><span class="sxs-lookup"><span data-stu-id="30b54-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="30b54-164">(JavaScript) No enlace `blobTrigger`, adicione uma propriedade com o nome `dataType` com um valor de `binary`.</span><span class="sxs-lookup"><span data-stu-id="30b54-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="30b54-165">Esta ação configura o enlace para passar os conteúdos do blob para a função como dados binários.</span><span class="sxs-lookup"><span data-stu-id="30b54-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

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

1. <span data-ttu-id="30b54-166">Clica em **Salvar** para criar o novo enlace.</span><span class="sxs-lookup"><span data-stu-id="30b54-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="30b54-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="30b54-167">**C#**</span></span>

    1. <span data-ttu-id="30b54-168">(C#) Selecione o nome de função **ResizeImage** no painel de navegação esquerdo para abrir o código fonte da função.</span><span class="sxs-lookup"><span data-stu-id="30b54-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="30b54-169">(C#) A função requer um pacote NuGet com o nome **ImageResizer** para gerar as miniaturas.</span><span class="sxs-lookup"><span data-stu-id="30b54-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="30b54-170">Os pacotes NuGet são adicionados às funções C# usando um ficheiro **project.json**.</span><span class="sxs-lookup"><span data-stu-id="30b54-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="30b54-171">Para criar o ficheiro, clique em **Ver Ficheiros** à direita para revelar os ficheiros que compõem a função.</span><span class="sxs-lookup"><span data-stu-id="30b54-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="30b54-172">(C#) Clique em **Adicionar** para adicionar um novo ficheiro com o nome **project.json**.</span><span class="sxs-lookup"><span data-stu-id="30b54-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="30b54-173">(C#) Copie o conteúdo de [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) para o ficheiro recentemente criado.</span><span class="sxs-lookup"><span data-stu-id="30b54-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="30b54-174">Guarde o ficheiro.</span><span class="sxs-lookup"><span data-stu-id="30b54-174">Save the file.</span></span> <span data-ttu-id="30b54-175">Os pacotes são restaurados automaticamente quando o ficheiro é atualizado.</span><span class="sxs-lookup"><span data-stu-id="30b54-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Ficheiro project.json com ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="30b54-177">(C#) Selecione **run.csx** sob **Ver Ficheiros** e substitui o conteúdo pelo conteúdo em [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="30b54-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="30b54-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="30b54-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="30b54-179">(JavaScript) Esta função requer que o `jimp` pacote a partir do npm redimensione a fotografia.</span><span class="sxs-lookup"><span data-stu-id="30b54-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="30b54-180">Para instalar o pacote npm, clique no nome da Function App no painel de navegação esquerdo e clique em **Funcionalidades da Plataforma**.</span><span class="sxs-lookup"><span data-stu-id="30b54-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="30b54-181">(JavaScript) Clique em **Consola** para revelar uma janela de consola.</span><span class="sxs-lookup"><span data-stu-id="30b54-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="30b54-182">(JavaScript) Execute o comando `npm install jimp` na consola.</span><span class="sxs-lookup"><span data-stu-id="30b54-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="30b54-183">Esta operação pode demorar alguns minutos até ficar concluída.</span><span class="sxs-lookup"><span data-stu-id="30b54-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="30b54-184">(JavaScript) Clique no nome da função **ResizeImage** no painel de navegação esquerdo para revelar a função e substituir todos os **index.js** pelo conteúdo de [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="30b54-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="30b54-185">Clique em **Registos** abaixo a janela de código para expandir o painel de registos.</span><span class="sxs-lookup"><span data-stu-id="30b54-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="30b54-186">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="30b54-186">Click **Save**.</span></span> <span data-ttu-id="30b54-187">Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.</span><span class="sxs-lookup"><span data-stu-id="30b54-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="30b54-188">Testar a função sem servidor</span><span class="sxs-lookup"><span data-stu-id="30b54-188">Test the serverless function</span></span>

1. <span data-ttu-id="30b54-189">Abra a aplicação num browser.</span><span class="sxs-lookup"><span data-stu-id="30b54-189">Open the application in a browser.</span></span> <span data-ttu-id="30b54-190">Selecione um ficheiro de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="30b54-190">Select an image file and upload it.</span></span> <span data-ttu-id="30b54-191">O carregamento é concluído, mas porque ainda não adicionamos a capacidade de exibir imagens, a aplicação não mostra a fotografia carregada.</span><span class="sxs-lookup"><span data-stu-id="30b54-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="30b54-192">No Cloud Shell, confirme que a imagem foi carregada para o contentor de **imagens**.</span><span class="sxs-lookup"><span data-stu-id="30b54-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="30b54-193">Certifique-se de que a miniatura foi criada num contentor com o nome **miniaturas**.</span><span class="sxs-lookup"><span data-stu-id="30b54-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="30b54-194">Obtenha o URL da miniatura.</span><span class="sxs-lookup"><span data-stu-id="30b54-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="30b54-195">Abra o URL num browser e confirme que a miniatura foi criada corretamente.</span><span class="sxs-lookup"><span data-stu-id="30b54-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="30b54-196">Antes de passar para o tutorial seguinte, elimine todos os ficheiros nos contentores de **imagens** e **miniaturas**.</span><span class="sxs-lookup"><span data-stu-id="30b54-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="30b54-197">Resumo</span><span class="sxs-lookup"><span data-stu-id="30b54-197">Summary</span></span>

<span data-ttu-id="30b54-198">Neste módulo, criou uma função sem servidor para criar uma miniatura, sempre que uma imagem é carregada para um contentor de armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="30b54-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="30b54-199">Em seguida, vai aprender a utilizar o Azure Cosmos DB para armazenar e listar os metadados de imagem.</span><span class="sxs-lookup"><span data-stu-id="30b54-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
