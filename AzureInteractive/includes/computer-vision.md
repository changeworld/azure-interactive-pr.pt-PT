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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079717"
---
<span data-ttu-id="f1e6e-103">Neste momento, a aplicação é uma galeria funcional que lhe permite carregar e ver imagens.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="f1e6e-104">Neste módulo, vai aprender a utilizar a API de Imagem Digitalizada dos Serviços Cognitivos da Microsoft para gerar legendas para as imagens carregadas e guardar as legendas com os metadados de imagem no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="f1e6e-105">Criar uma conta de Imagem Digitalizada</span><span class="sxs-lookup"><span data-stu-id="f1e6e-105">Create a Computer Vision account</span></span>

<span data-ttu-id="f1e6e-106">Os Serviços Cognitivos da Microsoft são uma coleção de serviços disponíveis para programadores, permitindo-lhes tornarem as respetivas aplicações mais inteligentes.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="f1e6e-107">A API de Imagem Digitalizada é um serviço sem servidor que processa imagens, utilizando algoritmos avançados e devolve informações sobre cada imagem.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="f1e6e-108">Conta com um escalão gratuito que fornece até 5000 chamadas à API por mês.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="f1e6e-109">Certifique-se de que está ainda com sessão iniciada no Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="f1e6e-110">Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="f1e6e-111">Crie uma nova conta de Serviços Cognitivos de tipo **ComputerVision** com um nome exclusivo no seu grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="f1e6e-112">Para o escalão gratuito, utilize **F0** para SKU.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="f1e6e-113">Se já tiver uma conta de Imagem Digitalizada, poderá ter de criar uma conta Standard (S1), o que poderá acarretar custos.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="f1e6e-114">Criar definições do Function App para a chave e o URL de Imagem Digitalizada</span><span class="sxs-lookup"><span data-stu-id="f1e6e-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="f1e6e-115">Para chamar a API de Imagem Digitalizada, é necessário ter um URL e uma chave.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="f1e6e-116">Obtenha o URL e a chave de API de Imagem Digitalizada, e guarde-os em variáveis de bash.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="f1e6e-117">Crie definições de aplicação com o nome **COMP_VISION_KEY** e **COMP_VISION_URL**, respectivamente, na aplicação de funções.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="f1e6e-118">Chamar a API de Imagem Digitalizada da função de ResizeImage</span><span class="sxs-lookup"><span data-stu-id="f1e6e-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="f1e6e-119">Nos passos seguintes, modifique a função **ResizeImage** para chamar a API de Imagem Digitalizada para descrever cada imagem carregada e guarde a descrição no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="f1e6e-120">Abra a aplicação de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="f1e6e-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="f1e6e-121">**C#**</span></span>

    1. <span data-ttu-id="f1e6e-122">(C#) Utilize o painel de navegação esquerdo para localizar a função **ResizeImage** e abra a janela de código.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="f1e6e-123">(C#) Substitua o código com o conteúdo de [ **/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="f1e6e-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="f1e6e-124">Este código utiliza `HttpClient` para chamar a API de Imagem Digitalizada e guarde o resultado no Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="f1e6e-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="f1e6e-125">**JavaScript**</span></span>

    1. <span data-ttu-id="f1e6e-126">(JavaScript) Esta função requer que o pacote `axios` do npm faça uma chamada HTTP para a API de Imagem Digitalizada.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="f1e6e-127">Para instalar o pacote npm, clique no nome da Function App no painel de navegação esquerdo e clique em **Funcionalidades da Plataforma**.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="f1e6e-128">(JavaScript) Clique em **Consola** para revelar uma janela de consola.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="f1e6e-129">(JavaScript) Execute o comando `npm install axios` na consola.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="f1e6e-130">Esta operação pode demorar alguns minutos até ficar concluída.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="f1e6e-131">(JavaScript) Clique no nome da função (**ResizeImage**) no painel de navegação esquerdo para revelar a função e, em seguida, substitua todos os **index.js** pelo conteúdo de [ **/javascript/ ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="f1e6e-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="f1e6e-132">Clique em **Registos** abaixo a janela de código para expandir o painel de registos.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="f1e6e-133">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-133">Click **Save**.</span></span> <span data-ttu-id="f1e6e-134">Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="f1e6e-135">Testar a aplicação</span><span class="sxs-lookup"><span data-stu-id="f1e6e-135">Test the application</span></span>

1. <span data-ttu-id="f1e6e-136">Abra a aplicação num browser.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-136">Open the application in a browser.</span></span> <span data-ttu-id="f1e6e-137">Selecione um ficheiro de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="f1e6e-138">Após alguns segundos, a miniatura da nova imagem deve aparecer na página.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="f1e6e-139">Coloque o cursor sobre a imagem para obter uma descrição gerada pela Imagem Digitalizada.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="f1e6e-140">Resumo</span><span class="sxs-lookup"><span data-stu-id="f1e6e-140">Summary</span></span>

<span data-ttu-id="f1e6e-141">Neste módulo, aprendeu a gerar legendas para cada imagem carregada, automaticamente, com a API de Imagem Digitalizada dos Serviços Cognitivos da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="f1e6e-142">A seguir, vai aprender a adicionar autenticação à aplicação, utilizando a Autenticação do Serviço de Aplicações do Azure.</span><span class="sxs-lookup"><span data-stu-id="f1e6e-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>