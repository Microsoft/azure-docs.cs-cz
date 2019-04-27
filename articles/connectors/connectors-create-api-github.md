---
title: Připojte se k GitHub – Azure Logic Apps | Dokumentace Microsoftu
description: Sledování událostí GitHub pomocí rozhraní REST API pro GitHub a Azure Logic Apps
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
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462560"
---
# <a name="connect-to-github"></a>Připojení ke Githubu

GitHub je webová Git úložiště hostitelská služba, která nabízí všechny revize distribuované řízení a funkce správy (SCM) zdrojového kódu v úložišti Git a další funkce.

Abyste mohli začít s konektorem Githubu [nejprve vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Vytvořte připojení ke Githubu

Chcete-li použít konektor GitHub v aplikaci logiky, musíte nejdřív vytvořit *připojení* a pak zadejte podrobnosti pro tyto vlastnosti: 

| Vlastnost | Požaduje se | Popis | 
| -------- | -------- | ----------- | 
| Podpisový | Ano | Zadejte svoje přihlašovací údaje Githubu. |

Po vytvoření připojení můžete provést akce a naslouchat aktivační procedury popsané v tomto článku.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Triggery a akce definované ve Swaggeru a žádná omezení, najdete v tématu [podrobnosti o konektoru](/connectors/github/).

## <a name="find-more-connectors"></a>Najít další konektory

* Zkontrolujte [seznam konektorů](apis-list.md).