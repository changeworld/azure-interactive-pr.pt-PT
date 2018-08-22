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
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079707"
---
<span data-ttu-id="84e5f-103">A Autenticação do Serviço de Aplicações permite um suporte de autenticação pronto a usar numa aplicação da Função do Azure.</span><span class="sxs-lookup"><span data-stu-id="84e5f-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="84e5f-104">Integra-se perfeitamente com o Facebook, Twitter, Conta Microsoft, Google e Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="84e5f-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="84e5f-105">Adicionará a Autenticação do Serviço de Aplicações para proteger as APIs de back-end da sua aplicação Web.</span><span class="sxs-lookup"><span data-stu-id="84e5f-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="84e5f-106">Ativar a Autenticação do Serviço de Aplicações</span><span class="sxs-lookup"><span data-stu-id="84e5f-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="84e5f-107">Abra a aplicação de funções no Portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="84e5f-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="84e5f-108">Em **funcionalidades da Plataforma**, selecione **Autenticação/Autorização**.</span><span class="sxs-lookup"><span data-stu-id="84e5f-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Selecionar Autenticação/Autorização](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="84e5f-110">Selecione os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="84e5f-110">Select the following values:</span></span>
    
    | <span data-ttu-id="84e5f-111">Definição</span><span class="sxs-lookup"><span data-stu-id="84e5f-111">Setting</span></span>      |  <span data-ttu-id="84e5f-112">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="84e5f-112">Suggested value</span></span>   | <span data-ttu-id="84e5f-113">Descrição</span><span class="sxs-lookup"><span data-stu-id="84e5f-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="84e5f-114">**Autenticação do Serviço de Aplicações**</span><span class="sxs-lookup"><span data-stu-id="84e5f-114">**App Service Authentication**</span></span> | <span data-ttu-id="84e5f-115">Ativado</span><span class="sxs-lookup"><span data-stu-id="84e5f-115">On</span></span> | <span data-ttu-id="84e5f-116">Ative a autenticação.</span><span class="sxs-lookup"><span data-stu-id="84e5f-116">Enable authentication.</span></span> |
    | <span data-ttu-id="84e5f-117">**Ação quando o pedido não é autenticado**</span><span class="sxs-lookup"><span data-stu-id="84e5f-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="84e5f-118">Iniciar sessão com o Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="84e5f-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="84e5f-119">Selecione um método de autenticação configurado (abaixo).</span><span class="sxs-lookup"><span data-stu-id="84e5f-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="84e5f-120">**Fornecedores de Autenticação**</span><span class="sxs-lookup"><span data-stu-id="84e5f-120">**Authentication Providers**</span></span> | <span data-ttu-id="84e5f-121">Veja abaixo</span><span class="sxs-lookup"><span data-stu-id="84e5f-121">See below</span></span> | <span data-ttu-id="84e5f-122">Veja abaixo</span><span class="sxs-lookup"><span data-stu-id="84e5f-122">See below</span></span> |
    | <span data-ttu-id="84e5f-123">**Arquivo de tokens**</span><span class="sxs-lookup"><span data-stu-id="84e5f-123">**Token store**</span></span> | <span data-ttu-id="84e5f-124">Ativado</span><span class="sxs-lookup"><span data-stu-id="84e5f-124">On</span></span> | <span data-ttu-id="84e5f-125">Permita que o Serviço de Aplicações armazene e faça a gestão dos tokens.</span><span class="sxs-lookup"><span data-stu-id="84e5f-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="84e5f-126">**URLs de redirecionamento externo permitidos**</span><span class="sxs-lookup"><span data-stu-id="84e5f-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="84e5f-127">O URL da sua aplicação, por exemplo: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="84e5f-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="84e5f-128">URLs para os quais o Serviço de Aplicações pode redirecionar depois de um utilizador ser autenticado.</span><span class="sxs-lookup"><span data-stu-id="84e5f-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="84e5f-129">Selecione o **Azure Active Directory** para revelar as **Definições do Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="84e5f-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="84e5f-130">Selecione **Expresso** como **Modo de Gestão** e preencha as informações seguintes.</span><span class="sxs-lookup"><span data-stu-id="84e5f-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="84e5f-131">Definição</span><span class="sxs-lookup"><span data-stu-id="84e5f-131">Setting</span></span>      |  <span data-ttu-id="84e5f-132">Valor sugerido</span><span class="sxs-lookup"><span data-stu-id="84e5f-132">Suggested value</span></span>   | <span data-ttu-id="84e5f-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="84e5f-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="84e5f-134">**Modo de gestão**</span><span class="sxs-lookup"><span data-stu-id="84e5f-134">**Management mode**</span></span> | <span data-ttu-id="84e5f-135">Expresso, Criar nova aplicação AD</span><span class="sxs-lookup"><span data-stu-id="84e5f-135">Express, Create new AD app</span></span> | <span data-ttu-id="84e5f-136">Configure automaticamente um principal de serviço e uma autenticação do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="84e5f-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="84e5f-137">**Criar aplicação**</span><span class="sxs-lookup"><span data-stu-id="84e5f-137">**Create app**</span></span> | <span data-ttu-id="84e5f-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="84e5f-138">my-serverless-webapp</span></span> | <span data-ttu-id="84e5f-139">Introduza um nome de aplicação único.</span><span class="sxs-lookup"><span data-stu-id="84e5f-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="84e5f-140">Clique em **OK** para guardar as definições do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="84e5f-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Autenticação/Autorização e definições do Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="84e5f-142">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="84e5f-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="84e5f-143">Modificar a aplicação Web para ativar a autenticação</span><span class="sxs-lookup"><span data-stu-id="84e5f-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="84e5f-144">No Cloud Shell, certifique-se de que o diretório atual é a pasta **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="84e5f-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="84e5f-145">Para ativar a autenticação na sua aplicação de funções, anexe a seguinte linha de código a **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="84e5f-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="84e5f-146">Faça a alteração e veja o resultado, utilizando um dos seguintes comandos ou um editor de linha de comandos como o VIM.</span><span class="sxs-lookup"><span data-stu-id="84e5f-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="84e5f-147">Certifique-se de que a alteração foi feita no ficheiro.</span><span class="sxs-lookup"><span data-stu-id="84e5f-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="84e5f-148">Carregue o ficheiro para o armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="84e5f-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="84e5f-149">Testar a aplicação</span><span class="sxs-lookup"><span data-stu-id="84e5f-149">Test the application</span></span>

1. <span data-ttu-id="84e5f-150">Abra a aplicação num browser.</span><span class="sxs-lookup"><span data-stu-id="84e5f-150">Open the application in a browser.</span></span> <span data-ttu-id="84e5f-151">Clique em **Iniciar sessão** e inicie sessão.</span><span class="sxs-lookup"><span data-stu-id="84e5f-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="84e5f-152">Selecione um ficheiro de imagem e carregue-o.</span><span class="sxs-lookup"><span data-stu-id="84e5f-152">Select an image file and upload it.</span></span>

    ![Página de início de sessão](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="84e5f-154">Resumo</span><span class="sxs-lookup"><span data-stu-id="84e5f-154">Summary</span></span>

<span data-ttu-id="84e5f-155">Neste módulo, aprendeu como adicionar autenticação à aplicação, utilizando a Autenticação do Serviço de Aplicações.</span><span class="sxs-lookup"><span data-stu-id="84e5f-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
