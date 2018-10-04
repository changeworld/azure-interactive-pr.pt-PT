---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460007"
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
