---
title: "Připojení ke Githubu službou Azure Logic Apps | Microsoft Docs"
description: "Automatizovat pracovní postupy pro GitHub službou Azure Logic Apps"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: estfan; ladocs
ms.openlocfilehash: 830c3e6b5462c5d2d917388c0d0924e444342f93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
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