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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460029"
---
<span data-ttu-id="390d6-103">Neste tutorial, vai implementar uma aplicação Web simples que apresenta uma interface de utilizador baseada em HTML.</span><span class="sxs-lookup"><span data-stu-id="390d6-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="390d6-104">Um back-end sem servidor permite que a aplicação carregue imagens e obtenha legendas que as descrevam automaticamente.</span><span class="sxs-lookup"><span data-stu-id="390d6-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Executar a aplicação Web](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="390d6-106">O diagrama seguinte mostra os serviços do Azure utilizados pela aplicação:</span><span class="sxs-lookup"><span data-stu-id="390d6-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="390d6-107">O Armazenamento de Blobs processa conteúdo Web estático (HTML, CSS, JS) e armazena imagens.</span><span class="sxs-lookup"><span data-stu-id="390d6-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="390d6-108">As Funções do Azure gerem o redimensionamento e carregamento de imagens, e o armazenamento de metadados.</span><span class="sxs-lookup"><span data-stu-id="390d6-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="390d6-109">O Cosmos DB armazena os metadados de imagem.</span><span class="sxs-lookup"><span data-stu-id="390d6-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="390d6-110">O Logic Apps obtém as legendas de imagem da API de Imagem Digitalizada.</span><span class="sxs-lookup"><span data-stu-id="390d6-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="390d6-111">O Azure Active Directory gere a autenticação de utilizador.</span><span class="sxs-lookup"><span data-stu-id="390d6-111">Azure Active Directory manages user authentication.</span></span>

![Diagrama da arquitetura de soluções](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="390d6-113">Neste tutorial, ficará a saber como:</span><span class="sxs-lookup"><span data-stu-id="390d6-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="390d6-114">Configure o armazenamento de Blobs do Azure para alojar um site estático e imagens carregadas.</span><span class="sxs-lookup"><span data-stu-id="390d6-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="390d6-115">Carregue imagens no armazenamento de Blobs do Azure, utilizando as Funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="390d6-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="390d6-116">Redimensione imagens com as Funções do Azure.</span><span class="sxs-lookup"><span data-stu-id="390d6-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="390d6-117">Armazene os metadados da imagem no Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="390d6-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="390d6-118">Utilize a API de Imagem Digitalizada dos Serviços Cognitivos para gerar automaticamente legendas de imagem.</span><span class="sxs-lookup"><span data-stu-id="390d6-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="390d6-119">Utilize o Azure Active Directory para proteger a aplicação Web por utilizadores autenticados.</span><span class="sxs-lookup"><span data-stu-id="390d6-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
