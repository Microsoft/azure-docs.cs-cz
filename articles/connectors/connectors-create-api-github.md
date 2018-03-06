---
title: "Připojení ke Githubu službou Azure Logic Apps | Microsoft Docs"
description: "Automatizovat pracovní postupy pro GitHub službou Azure Logic Apps"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
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
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
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