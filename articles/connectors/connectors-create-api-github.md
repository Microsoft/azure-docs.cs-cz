---
title: Připojení ke Githubu – Azure Logic Apps | Microsoft Docs
description: Sledování událostí Githubu s Githubu REST API a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295808"
---
# <a name="connect-to-github"></a>Připojení ke Githubu

Githubu je založené na webu Git úložiště hostování služba, která nabízí celou ovládacího prvku distribuované revize a funkce správy (SCM) zdrojového kódu v úložišti Git a další funkce.

Začněte s konektorem Githubu [nejprve vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Umožňuje vytvořit připojení ke Githubu

K používání konektoru Githubu v aplikaci logiky, musíte nejdřív vytvořit *připojení* a pak zadejte podrobnosti pro tyto vlastnosti: 

| Vlastnost | Požaduje se | Popis | 
| -------- | -------- | ----------- | 
| Podpisový | Ano | Zadejte svoje přihlašovací údaje Githubu. |

Po vytvoření připojení, můžete provést akce a naslouchat aktivační události popsané v tomto článku.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Triggery a akce definované v Swagger a všechny limity, zkontrolujte [connector – podrobnosti](/connectors/github/).

## <a name="find-more-connectors"></a>Vyhledání konektorů pro další

* Zkontrolujte [seznam konektorů](apis-list.md).