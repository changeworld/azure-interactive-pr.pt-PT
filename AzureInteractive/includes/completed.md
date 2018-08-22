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
Criou com êxito uma aplicação completa, sem servidor com serviços do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar o seu trabalho nesta aplicação, pode utilizar o seguinte comando para eliminar todos os recursos criados durante o tutorial:

```azurecli
az group delete --name first-serverless-app
```

Escreva `y` quando lhe for pedido.  

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> * Configure o armazenamento de Blobs do Azure para alojar um site estático e imagens carregadas.
> * Carregue imagens no armazenamento de Blobs do Azure, utilizando as Funções do Azure.
> * Redimensione imagens com as Funções do Azure.
> * Armazene os metadados da imagem no Azure Cosmos DB.
> * Utilize a API de Imagem Digitalizada dos Serviços Cognitivos para gerar automaticamente legendas de imagem.
> * Utilize o Azure Active Directory para proteger a aplicação Web por utilizadores autenticados.

Para saber como ligar ainda mais serviços às Funções, avance para o tutorial do Logic Apps. 

> [!div class="nextstepaction"]
> [Funções com Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)