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
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079696"
---
<span data-ttu-id="836ec-103">A aplicação que está a criar é uma galeria de fotos.</span><span class="sxs-lookup"><span data-stu-id="836ec-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="836ec-104">Esta utiliza JavaScript do lado do cliente para chamar as APIs para carregar e exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="836ec-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="836ec-105">Neste módulo, vai criar uma API com uma função sem servidor que gera um URL de tempo limitado para carregar uma imagem.</span><span class="sxs-lookup"><span data-stu-id="836ec-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="836ec-106">A aplicação Web utiliza o URL gerado para carregar uma imagem para o armazenamento de Blobs com a [API REST do armazenamento de Blobs](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="836ec-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="836ec-107">Criar um contentor de armazenamento de blobs para as imagens</span><span class="sxs-lookup"><span data-stu-id="836ec-107">Create a blob storage container for images</span></span>

<span data-ttu-id="836ec-108">A aplicação requer um contentor de armazenamento separado para carregar e hospedar imagens.</span><span class="sxs-lookup"><span data-stu-id="836ec-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="836ec-109">Certifique-se de que ainda está com sessão iniciada no Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="836ec-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="836ec-110">Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="836ec-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="836ec-111">Crie um novo contentor com o nome **imagens** na sua conta de armazenamento com acesso público a todos os blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="836ec-112">Criar uma aplicação de Funções do Azure</span><span class="sxs-lookup"><span data-stu-id="836ec-112">Create an Azure Function app</span></span>

<span data-ttu-id="836ec-113">As Funções do Azure são um serviço para executar funções sem servidor.</span><span class="sxs-lookup"><span data-stu-id="836ec-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="836ec-114">Uma função sem servidor pode ser acionada (chamada) por eventos, tais como um pedido de HTTP, ou quando um blob é criado um contentor de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="836ec-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="836ec-115">Uma aplicação de funções do Azure é um contentor para uma ou mais funções sem servidor.</span><span class="sxs-lookup"><span data-stu-id="836ec-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="836ec-116">Crie uma nova aplicação de funções do Azure com um nome exclusivo no grupo de recursos que criou anteriormente com o nome **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="836ec-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="836ec-117">Aplicações de funções requerem uma conta de Armazenamento. Neste tutorial, vai utilizar a conta de armazenamento existente.</span><span class="sxs-lookup"><span data-stu-id="836ec-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="836ec-118">Criar uma função sem servidor acionada por HTTP</span><span class="sxs-lookup"><span data-stu-id="836ec-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="836ec-119">A aplicação Web da galeria de fotos faz um pedido de HTTP para uma função sem servidor para gerar um URL de tempo limitado para carregar com segurança uma imagem para o armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="836ec-120">A função é acionada pelo pedido de HTTP e utiliza o SDK do Armazenamento do Microsoft Azure para gerar e devolver um URL seguro.</span><span class="sxs-lookup"><span data-stu-id="836ec-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="836ec-121">Depois de criada a aplicação de funções, aceda-lhe no Portal do Azure utilizando a caixa de pesquisa e clique para abrir.</span><span class="sxs-lookup"><span data-stu-id="836ec-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Abrir a aplicação de funções](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="836ec-123">No painel de navegação esquerdo da janela da aplicação de funções, coloque o cursor sobre **Funções** e clique em **+** para começar a criar uma nova função sem servidor.</span><span class="sxs-lookup"><span data-stu-id="836ec-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Criar uma nova função](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="836ec-125">Clique em **função Personalizada** para ver uma lista de modelos de função.</span><span class="sxs-lookup"><span data-stu-id="836ec-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="836ec-126">Aceda ao modelo **HttpTrigger** e clique no idioma a utilizar (C# ou JavaScript).</span><span class="sxs-lookup"><span data-stu-id="836ec-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="836ec-127">Utilize estes valores para criar uma função que gera um URL de carregamento de blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="836ec-128">Definição</span><span class="sxs-lookup"><span data-stu-id="836ec-128">Setting</span></span>      |  <span data-ttu-id="836ec-129">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="836ec-129">Suggested value</span></span>   | <span data-ttu-id="836ec-130">Descrição</span><span class="sxs-lookup"><span data-stu-id="836ec-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="836ec-131">**Language** (Idioma)</span><span class="sxs-lookup"><span data-stu-id="836ec-131">**Language**</span></span> | <span data-ttu-id="836ec-132">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="836ec-132">C# or JavaScript</span></span> | <span data-ttu-id="836ec-133">Selecione o idioma que pretende utilizar.</span><span class="sxs-lookup"><span data-stu-id="836ec-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="836ec-134">**Dar um nome à função**</span><span class="sxs-lookup"><span data-stu-id="836ec-134">**Name your function**</span></span> | <span data-ttu-id="836ec-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="836ec-135">GetUploadUrl</span></span> | <span data-ttu-id="836ec-136">Escreva este nome exatamente como mostrado, para que a aplicação possa detetar a função.</span><span class="sxs-lookup"><span data-stu-id="836ec-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="836ec-137">**Nível de autorização**</span><span class="sxs-lookup"><span data-stu-id="836ec-137">**Authorization level**</span></span> | <span data-ttu-id="836ec-138">Anónimo</span><span class="sxs-lookup"><span data-stu-id="836ec-138">Anonymous</span></span> | <span data-ttu-id="836ec-139">Permita que a função fique acessível publicamente.</span><span class="sxs-lookup"><span data-stu-id="836ec-139">Allow the function to be accessed publicly.</span></span> |

    ![Introduzir as definições para uma nova função acionada por HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="836ec-141">Clique em **Criar** para criar a função.</span><span class="sxs-lookup"><span data-stu-id="836ec-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="836ec-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="836ec-142">**C#**</span></span> 

    1. <span data-ttu-id="836ec-143">Quando for apresentado o código fonte da função, substitua todo o **run.csx** pelo conteúdo de [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="836ec-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="836ec-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="836ec-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="836ec-145">(JavaScript) Esta função requer que o pacote `azure-storage` do npm para gerar o token de assinatura de acesso partilhado (SAS) necessário para criar o URL seguro.</span><span class="sxs-lookup"><span data-stu-id="836ec-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="836ec-146">Para instalar o pacote npm, clique no nome da Function App no painel de navegação esquerdo e clique em **Funcionalidades da Plataforma**.</span><span class="sxs-lookup"><span data-stu-id="836ec-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="836ec-147">(JavaScript) Clique em **Consola** para revelar uma janela de consola.</span><span class="sxs-lookup"><span data-stu-id="836ec-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Abrir uma janela de consola](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="836ec-149">(JavaScript) Certifique-se de que o diretório atual é **d:\home\site\wwwroot** ao executar o comando `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="836ec-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="836ec-150">(JavaScript) Execute o comando `npm init -y` para criar ficheiro **package.json** vazio.</span><span class="sxs-lookup"><span data-stu-id="836ec-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="836ec-151">(JavaScript) Execute o comando `npm install --save azure-storage` na consola para instalar o pacote e guardá-lo em **package.json**.</span><span class="sxs-lookup"><span data-stu-id="836ec-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="836ec-152">Esta operação pode demorar alguns minutos até ficar concluída.</span><span class="sxs-lookup"><span data-stu-id="836ec-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="836ec-153">(JavaScript) Clique no nome da função (**GetUploadUrl**) no painel de navegação esquerdo para revelar a função e substituir todos os **index.js** pelo conteúdo de [**/javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="836ec-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js após atualização](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="836ec-155">Clique em **Registos** abaixo a janela de código para expandir o painel de registos.</span><span class="sxs-lookup"><span data-stu-id="836ec-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="836ec-156">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="836ec-156">Click **Save**.</span></span> <span data-ttu-id="836ec-157">Verifique o painel de registos para garantir que a função está compilada corretamente.</span><span class="sxs-lookup"><span data-stu-id="836ec-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="836ec-158">A função gera o que é chamado de um URL de assinatura de acesso partilhado (SAS), que é utilizado para carregar um ficheiro para o armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="836ec-159">O URL de SAS é válido durante um curto período de tempo e só permite que um único ficheiro seja carregado.</span><span class="sxs-lookup"><span data-stu-id="836ec-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="836ec-160">Consulte a documentação de armazenamento de Blobs para obter mais informações sobre a [utilização de assinaturas de acesso partilhado](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="836ec-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="836ec-161">Adicionar uma variável de ambiente para a cadeia de ligação de armazenamento</span><span class="sxs-lookup"><span data-stu-id="836ec-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="836ec-162">A função que acabou de criar requer uma cadeia de ligação para a conta de Armazenamento, para que possa gerar o URL de SAS.</span><span class="sxs-lookup"><span data-stu-id="836ec-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="836ec-163">Em vez de codificar a cadeia de ligação no corpo da função, pode ser armazenada como uma definição da aplicação.</span><span class="sxs-lookup"><span data-stu-id="836ec-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="836ec-164">As definições da aplicação são acessíveis como variáveis de ambiente por todas as funções na aplicação de Funções.</span><span class="sxs-lookup"><span data-stu-id="836ec-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="836ec-165">No Cloud Shell, consulte a cadeia de ligação da conta de Armazenamento e guarde-a numa variável com o nome **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="836ec-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="836ec-166">Certifique-se de que a variável é definida com êxito.</span><span class="sxs-lookup"><span data-stu-id="836ec-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="836ec-167">Crie uma nova definição de aplicação com o nome **AZURE_STORAGE_CONNECTION_STRING**, utilizando o valor guardado do passo anterior.</span><span class="sxs-lookup"><span data-stu-id="836ec-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="836ec-168">Certifique-se de que a saída do comando contém a nova definição de aplicação com o valor correto.</span><span class="sxs-lookup"><span data-stu-id="836ec-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="836ec-169">Testar a função sem servidor</span><span class="sxs-lookup"><span data-stu-id="836ec-169">Test the serverless function</span></span>

<span data-ttu-id="836ec-170">Além de criar e editar funções, o portal do Azure também fornece uma ferramenta incorporada para testar as funções.</span><span class="sxs-lookup"><span data-stu-id="836ec-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="836ec-171">Para testar a função HTTP sem servidor, clique no separador **Testar** no lado direito da janela de código, para expandir o painel de teste.</span><span class="sxs-lookup"><span data-stu-id="836ec-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="836ec-172">Altere o **método Http** para **GET**.</span><span class="sxs-lookup"><span data-stu-id="836ec-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="836ec-173">Em **Consulta**, clique em *Adicionar parâmetro*\* e adicione o seguinte parâmetro:</span><span class="sxs-lookup"><span data-stu-id="836ec-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="836ec-174">nome</span><span class="sxs-lookup"><span data-stu-id="836ec-174">name</span></span>      |  <span data-ttu-id="836ec-175">valor</span><span class="sxs-lookup"><span data-stu-id="836ec-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="836ec-176">filename</span><span class="sxs-lookup"><span data-stu-id="836ec-176">filename</span></span> | <span data-ttu-id="836ec-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="836ec-177">image1.jpg</span></span> |

1. <span data-ttu-id="836ec-178">Clique em **Executar** no painel de teste para enviar um pedido de HTTP para a função.</span><span class="sxs-lookup"><span data-stu-id="836ec-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="836ec-179">A função devolve um URL de carregamento na saída.</span><span class="sxs-lookup"><span data-stu-id="836ec-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="836ec-180">A execução de função é apresentada no painel de registos.</span><span class="sxs-lookup"><span data-stu-id="836ec-180">The function execution appears in the logs panel.</span></span>

    ![Os registos com uma função foram executados com êxito](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="836ec-182">Configurar CORS na aplicação de funções</span><span class="sxs-lookup"><span data-stu-id="836ec-182">Configure CORS in the function app</span></span>

<span data-ttu-id="836ec-183">Como front-end da aplicação está hospedado no armazenamento de Blobs, tem um nome de domínio diferente daquele da aplicação de Função do Azure.</span><span class="sxs-lookup"><span data-stu-id="836ec-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="836ec-184">Para que o JavaScript do lado do cliente chame a função que acabou de criar com êxito, a aplicação de funções tem de ser configurada para a partilha de recursos transversais às origens (CORS).</span><span class="sxs-lookup"><span data-stu-id="836ec-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="836ec-185">Na barra de navegação esquerda da janela da aplicação de funções, clique no nome da sua aplicação de funções.</span><span class="sxs-lookup"><span data-stu-id="836ec-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="836ec-186">Clique em **Funcionalidades de plataforma** para ver uma lista das funcionalidades avançadas.</span><span class="sxs-lookup"><span data-stu-id="836ec-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="836ec-187">Em **API**, clique em **CORS**.</span><span class="sxs-lookup"><span data-stu-id="836ec-187">Under **API**, click **CORS**.</span></span>

    ![Selecionar CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="836ec-189">Adicione uma origem de permissões para o URL a partir da aplicação do módulo anterior, omitindo **/** à direita (por exemplo: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="836ec-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Definições de CORS mostrando um URL da aplicação Web adicionado sem servidor](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="836ec-191">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="836ec-191">Click **Save**.</span></span>

1. <span data-ttu-id="836ec-192">C#</span><span class="sxs-lookup"><span data-stu-id="836ec-192">C#</span></span>

   1. <span data-ttu-id="836ec-193">(C#) Navegue de volta para a função `GetUploadUrl` e, em seguida, selecione o separador **Integrar**.</span><span class="sxs-lookup"><span data-stu-id="836ec-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="836ec-194">(C#) Em Métodos HTTP Selecionados, selecione **OPÇÕES**.</span><span class="sxs-lookup"><span data-stu-id="836ec-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="836ec-195">**OBTER**, **PUBLICAR**, e **OPÇÕES** devem todos ser selecionados.</span><span class="sxs-lookup"><span data-stu-id="836ec-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="836ec-196">CORS utiliza o método OPÇÕES, que não está selecionado por predefinição para funções C#.</span><span class="sxs-lookup"><span data-stu-id="836ec-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="836ec-197">(C#) Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="836ec-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="836ec-198">Ainda no portal, navegue para a aplicação de funções, selecione o separador **Descrição geral** e, em seguida, clique em **Reiniciar** para garantir que as alterações a CORS entrem em vigor.</span><span class="sxs-lookup"><span data-stu-id="836ec-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="836ec-199">Configurar CORS na conta de Armazenamento</span><span class="sxs-lookup"><span data-stu-id="836ec-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="836ec-200">Uma vez que a aplicação também faz chamadas de JavaScript do lado do cliente para o Armazenamento de Blobs para carregar ficheiros, tem também de configurar a conta de Armazenamento para CORS.</span><span class="sxs-lookup"><span data-stu-id="836ec-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="836ec-201">Execute o seguinte comando para permitir que todas as origens carreguem ficheiros para a conta de Armazenamento.</span><span class="sxs-lookup"><span data-stu-id="836ec-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="836ec-202">Modificar a aplicação Web para carregar imagens</span><span class="sxs-lookup"><span data-stu-id="836ec-202">Modify the web app to upload images</span></span>

<span data-ttu-id="836ec-203">A aplicação Web obtém as definições a partir do ficheiro denominado **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="836ec-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="836ec-204">Nos passos seguintes, vai criar o ficheiro com o Cloud Shell e, em seguida, definir `window.apiBaseUrl` para o URL da aplicação de Funções e `window.blobBaseUrl` para o URL do ponto final do Armazenamento de Blobs do Azure.</span><span class="sxs-lookup"><span data-stu-id="836ec-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="836ec-205">No Cloud Shell, certifique-se de que o diretório atual é a pasta **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="836ec-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="836ec-206">Consulte o URL da aplicação de funções e armazene-o numa variável de bash chamada **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="836ec-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="836ec-207">Certifique-se de que a variável está corretamente definida.</span><span class="sxs-lookup"><span data-stu-id="836ec-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="836ec-208">Para definir o URI base de chamadas API para a aplicação de funções, crie **settings.js** e adicione o URL da aplicação de funções semelhante ao seguinte.</span><span class="sxs-lookup"><span data-stu-id="836ec-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="836ec-209">Pode fazer a alteração executando o seguinte comando ou utilizando um editor de linha de comandos como o VIM.</span><span class="sxs-lookup"><span data-stu-id="836ec-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="836ec-210">Certifique-se de que o ficheiro foi escrito com êxito.</span><span class="sxs-lookup"><span data-stu-id="836ec-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="836ec-211">Consulte o URL base do Armazenamento de Blobs e armazene-o numa variável de bash denominada **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="836ec-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="836ec-212">Certifique-se de que a variável está corretamente definida.</span><span class="sxs-lookup"><span data-stu-id="836ec-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="836ec-213">Para definir o URI base de chamadas API para a aplicação de funções, anexe o URL de armazenamento, como a linha de código seguinte, para **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="836ec-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="836ec-214">Pode fazer a alteração executando o seguinte comando ou utilizando um editor de linha de comandos como o VIM.</span><span class="sxs-lookup"><span data-stu-id="836ec-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="836ec-215">Certifique-se de que o ficheiro foi escrito com êxito e que contém agora duas linhas.</span><span class="sxs-lookup"><span data-stu-id="836ec-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="836ec-216">Carregue o ficheiro para o armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="836ec-217">Testar a aplicação Web</span><span class="sxs-lookup"><span data-stu-id="836ec-217">Test the web application</span></span>

<span data-ttu-id="836ec-218">Neste momento, a aplicação de galeria é capaz de carregar uma imagem para o armazenamento de Blobs, mas não pode exibir imagens.</span><span class="sxs-lookup"><span data-stu-id="836ec-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="836ec-219">Vai tentar chamar uma função `GetImages` que ainda não existe uma vez que a criou num módulo posterior.</span><span class="sxs-lookup"><span data-stu-id="836ec-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="836ec-220">Essa chamada falhará e a página Web parecerá estar presa em "A analisar...", mas a imagem que selecionou será carregada com êxito.</span><span class="sxs-lookup"><span data-stu-id="836ec-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="836ec-221">Pode verificar se uma imagem é carregada com êxito ao verificar o conteúdo do contentor de **imagens** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="836ec-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="836ec-222">Numa janela do browser, navegue até à aplicação.</span><span class="sxs-lookup"><span data-stu-id="836ec-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="836ec-223">Selecione um ficheiro de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="836ec-223">Select an image file and upload it.</span></span> <span data-ttu-id="836ec-224">O carregamento é concluído, mas porque ainda não adicionamos a capacidade de exibir imagens, a aplicação não mostra a fotografia carregada.</span><span class="sxs-lookup"><span data-stu-id="836ec-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="836ec-225">(A página Web parece estar bloqueada em "A analisar a imagem..."; este problema será resolvido mais tarde.)</span><span class="sxs-lookup"><span data-stu-id="836ec-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="836ec-226">No Cloud Shell, confirme que a imagem foi carregada para o contentor de **imagens**.</span><span class="sxs-lookup"><span data-stu-id="836ec-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="836ec-227">Antes de passar para o tutorial seguinte, elimine todos os ficheiros no contentor de **imagens**.</span><span class="sxs-lookup"><span data-stu-id="836ec-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="836ec-228">Resumo</span><span class="sxs-lookup"><span data-stu-id="836ec-228">Summary</span></span>

<span data-ttu-id="836ec-229">Neste módulo, criou uma aplicação de Função do Azure e aprendeu a usar uma função sem servidor para permitir que uma aplicação Web para carregar imagens para o armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="836ec-230">Em seguida, irá aprender a criar miniaturas para as imagens carregadas, usando uma função sem servidor acionada por Blobs.</span><span class="sxs-lookup"><span data-stu-id="836ec-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
