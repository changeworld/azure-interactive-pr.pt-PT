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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079686"
---
Neste tutorial, vai implementar uma aplicação Web simples que apresenta uma interface de utilizador baseada em HTML. Um back-end sem servidor permite que a aplicação carregue imagens e obtenha legendas que as descrevam automaticamente.

![Executar a aplicação Web](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

O diagrama seguinte mostra os serviços do Azure utilizados pela aplicação:

1. O Armazenamento de Blobs processa conteúdo Web estático (HTML, CSS, JS) e armazena imagens.
2. As Funções do Azure gerem o redimensionamento e carregamento de imagens, e o armazenamento de metadados.
3. O Cosmos DB armazena os metadados de imagem.
4. O Logic Apps obtém as legendas de imagem da API de Imagem Digitalizada.
5. O Azure Active Directory gere a autenticação de utilizador.

![Diagrama da arquitetura de soluções](media/functions-first-serverless-web-app/0-architecture.jpg)

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure o armazenamento de Blobs do Azure para alojar um site estático e imagens carregadas.
> * Carregue imagens no armazenamento de Blobs do Azure, utilizando as Funções do Azure.
> * Redimensione imagens com as Funções do Azure.
> * Armazene os metadados da imagem no Azure Cosmos DB.
> * Utilize a API de Imagem Digitalizada dos Serviços Cognitivos para gerar automaticamente legendas de imagem.
> * Utilize o Azure Active Directory para proteger a aplicação Web por utilizadores autenticados.