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
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460008"
---
Neste momento, a aplicação é uma galeria funcional que lhe permite carregar e ver imagens. Neste módulo, vai aprender a utilizar a API de Imagem Digitalizada dos Serviços Cognitivos da Microsoft para gerar legendas para as imagens carregadas e guardar as legendas com os metadados de imagem no Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Criar uma conta de Imagem Digitalizada

Os Serviços Cognitivos da Microsoft são uma coleção de serviços disponíveis para programadores, permitindo-lhes tornarem as respetivas aplicações mais inteligentes. A API de Imagem Digitalizada é um serviço sem servidor que processa imagens, utilizando algoritmos avançados e devolve informações sobre cada imagem. Conta com um escalão gratuito que fornece até 5000 chamadas à API por mês.

1. Certifique-se de que está ainda com sessão iniciada no Cloud Shell. Caso tal não se verifique, selecione **Entrar no modo de detalhe** para abrir uma janela do Cloud Shell. 

1. Crie uma nova conta de Serviços Cognitivos de tipo **ComputerVision** com um nome exclusivo no seu grupo de recursos. Para o escalão gratuito, utilize **F0** para SKU. Se já tiver uma conta de Imagem Digitalizada, poderá ter de criar uma conta Standard (S1), o que poderá acarretar custos.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Criar definições do Function App para a chave e o URL de Imagem Digitalizada

Para chamar a API de Imagem Digitalizada, é necessário ter um URL e uma chave.

1. Obtenha o URL e a chave de API de Imagem Digitalizada, e guarde-os em variáveis de bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Crie definições de aplicação com o nome **COMP_VISION_KEY** e **COMP_VISION_URL**, respectivamente, na aplicação de funções.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Chamar a API de Imagem Digitalizada da função de ResizeImage

Nos passos seguintes, modifique a função **ResizeImage** para chamar a API de Imagem Digitalizada para descrever cada imagem carregada e guarde a descrição no Cosmos DB.

1. Abra a aplicação de funções no Portal do Azure.

1. **C#**

    1. (C#) Utilize o painel de navegação esquerdo para localizar a função **ResizeImage** e abra a janela de código.

    1. (C#) Substitua o código com o conteúdo de [ **/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Este código utiliza `HttpClient` para chamar a API de Imagem Digitalizada e guarde o resultado no Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Esta função requer que o pacote `axios` do npm faça uma chamada HTTP para a API de Imagem Digitalizada. Para instalar o pacote npm, clique no nome da Function App no painel de navegação esquerdo e clique em **Funcionalidades da Plataforma**.

    1. (JavaScript) Clique em **Consola** para revelar uma janela de consola.

    1. (JavaScript) Execute o comando `npm install axios` na consola. Esta operação pode demorar alguns minutos até ficar concluída.

    1. (JavaScript) Clique no nome da função (**ResizeImage**) no painel de navegação esquerdo para revelar a função e, em seguida, substitua todos os **index.js** pelo conteúdo de [ **/javascript/ ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Clique em **Registos** abaixo a janela de código para expandir o painel de registos.

1. Clique em **Guardar**. Verifique o painel de registos para garantir que a função é guardada corretamente e que não há erros.


## <a name="test-the-application"></a>Testar a aplicação

1. Abra a aplicação num browser. Selecione um ficheiro de imagem e carregue-o.

1. Após alguns segundos, a miniatura da nova imagem deve aparecer na página. Coloque o cursor sobre a imagem para obter uma descrição gerada pela Imagem Digitalizada.


## <a name="summary"></a>Resumo

Neste módulo, aprendeu a gerar legendas para cada imagem carregada, automaticamente, com a API de Imagem Digitalizada dos Serviços Cognitivos da Microsoft. A seguir, vai aprender a adicionar autenticação à aplicação, utilizando a Autenticação do Serviço de Aplicações do Azure.
