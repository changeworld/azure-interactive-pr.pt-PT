---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079697"
---
<span data-ttu-id="14370-103">Criou com êxito uma aplicação completa, sem servidor com serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="14370-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="14370-104">Limpar recursos</span><span class="sxs-lookup"><span data-stu-id="14370-104">Clean up resources</span></span>

<span data-ttu-id="14370-105">Quando terminar o seu trabalho nesta aplicação, pode utilizar o seguinte comando para eliminar todos os recursos criados durante o tutorial:</span><span class="sxs-lookup"><span data-stu-id="14370-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="14370-106">Escreva `y` quando lhe for pedido.</span><span class="sxs-lookup"><span data-stu-id="14370-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="14370-107">Passos seguintes</span><span class="sxs-lookup"><span data-stu-id="14370-107">Next steps</span></span>

<span data-ttu-id="14370-108">Neste tutorial, ficou a saber como:</span><span class="sxs-lookup"><span data-stu-id="14370-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="14370-109">Configure o armazenamento de Blobs do Azure para alojar um site estático e imagens carregadas.</span><span class="sxs-lookup"><span data-stu-id="14370-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="14370-110">Carregue imagens no armazenamento de Blobs do Azure, utilizando as Funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="14370-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="14370-111">Redimensione imagens com as Funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="14370-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="14370-112">Armazene os metadados da imagem no Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="14370-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="14370-113">Utilize a API de Imagem Digitalizada dos Serviços Cognitivos para gerar automaticamente legendas de imagem.</span><span class="sxs-lookup"><span data-stu-id="14370-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="14370-114">Utilize o Azure Active Directory para proteger a aplicação Web por utilizadores autenticados.</span><span class="sxs-lookup"><span data-stu-id="14370-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="14370-115">Para saber como ligar ainda mais serviços às Funções, avance para o tutorial do Logic Apps.</span><span class="sxs-lookup"><span data-stu-id="14370-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="14370-116">Funções com Logic Apps</span><span class="sxs-lookup"><span data-stu-id="14370-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)