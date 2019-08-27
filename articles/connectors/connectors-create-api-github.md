---
title: Připojení k GitHubu – Azure Logic Apps
description: Monitorování událostí GitHubu pomocí rozhraní REST API GitHubu a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050904"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Připojení k GitHubu z Azure Logic Apps

GitHub je webová služba hostující úložiště Git, která nabízí všechny funkce distribuovaných revizí a správy zdrojového kódu (SCM) v Gitu a další funkce.

Pokud chcete začít s konektorem GitHubu, [vytvořte nejdřív aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Vytvoření připojení k GitHubu

Pokud chcete použít konektor GitHubu v aplikaci logiky, musíte nejdřív vytvořit *připojení* a pak zadat podrobnosti těchto vlastností: 

| Vlastnost | Požadováno | Popis | 
| -------- | -------- | ----------- | 
| Podpisový | Ano | Zadejte svoje přihlašovací údaje GitHubu. |

Po vytvoření připojení můžete provést akce a naslouchat aktivačním událostem popsaným v tomto článku.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/github/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)