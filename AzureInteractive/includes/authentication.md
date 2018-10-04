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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460046"
---
A Autenticação do Serviço de Aplicações permite um suporte de autenticação pronto a usar numa aplicação da Função do Azure. Integra-se perfeitamente com o Facebook, Twitter, Conta Microsoft, Google e Azure Active Directory. Adicionará a Autenticação do Serviço de Aplicações para proteger as APIs de back-end da sua aplicação Web.

## <a name="enable-app-service-authentication"></a>Ativar a Autenticação do Serviço de Aplicações

1. Abra a aplicação de funções no Portal do Azure.

1. Em **funcionalidades da Plataforma**, selecione **Autenticação/Autorização**.

    ![Selecionar Autenticação/Autorização](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Selecione os seguintes valores:
    
    | Definição      |  Valor sugerido   | Descrição                                        |
    | --- | --- | ---|
    | **Autenticação do Serviço de Aplicações** | Ativado | Ative a autenticação. |
    | **Ação quando o pedido não é autenticado** | Iniciar sessão com o Azure Active Directory | Selecione um método de autenticação configurado (abaixo). |
    | **Fornecedores de Autenticação** | Veja abaixo | Veja abaixo |
    | **Arquivo de tokens** | Ativado | Permita que o Serviço de Aplicações armazene e faça a gestão dos tokens. |
    | **URLs de redirecionamento externo permitidos** | O URL da sua aplicação, por exemplo: https://firstserverlessweb.z4.web.core.windows.net/ | URLs para os quais o Serviço de Aplicações pode redirecionar depois de um utilizador ser autenticado. |

1. Selecione o **Azure Active Directory** para revelar as **Definições do Azure Active Directory**.

    1. Selecione **Expresso** como **Modo de Gestão** e preencha as informações seguintes.
    
        | Definição      |  Valor sugerido   | Descrição                                        |
        | --- | --- | ---|
        | **Modo de gestão** | Expresso, Criar nova aplicação AD | Configure automaticamente um principal de serviço e uma autenticação do Azure Active Directory. |
        | **Criar aplicação** | my-serverless-webapp | Introduza um nome de aplicação único. |
    
    1. Clique em **OK** para guardar as definições do Azure Active Directory.

    ![Autenticação/Autorização e definições do Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. Clique em **Guardar**.


## <a name="modify-the-web-app-to-enable-authentication"></a>Modificar a aplicação Web para ativar a autenticação

1. No Cloud Shell, certifique-se de que o diretório atual é a pasta **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Para ativar a autenticação na sua aplicação de funções, anexe a seguinte linha de código a **settings.js**.

    `window.authEnabled = true`

    Faça a alteração e veja o resultado, utilizando um dos seguintes comandos ou um editor de linha de comandos como o VIM.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Certifique-se de que a alteração foi feita no ficheiro.

    ```azurecli
    cat settings.js
    ```

1. Carregue o ficheiro para o armazenamento de Blobs.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Testar a aplicação

1. Abra a aplicação num browser. Clique em **Iniciar sessão** e inicie sessão.

1. Selecione um ficheiro de imagem e carregue-o.

    ![Página de início de sessão](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Resumo

Neste módulo, aprendeu como adicionar autenticação à aplicação, utilizando a Autenticação do Serviço de Aplicações.
