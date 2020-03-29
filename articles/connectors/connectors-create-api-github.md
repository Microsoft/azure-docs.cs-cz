---
title: Přístup, monitorování a správa úložiště GitHub
description: Sledujte události GitHubu a spravujte úložiště GitHub vytvořením automatizovaných pracovních postupů pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378445"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Sledování a správa úložiště GitHub pomocí Azure Logic Apps

GitHub je webová hostingová služba úložiště Git, která nabízí všechny funkce distribuované kontroly revizí a správy zdrojového kódu (SCM) v Gitu a další funkce.

Chcete-li začít s konektorem GitHub, [nejprve vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Vytvoření připojení k GitHubu

Chcete-li použít konektor GitHub v aplikaci logiky, musíte nejprve vytvořit *připojení* a pak poskytnout podrobnosti o těchto vlastnostech: 

| Vlastnost | Požaduje se | Popis | 
| -------- | -------- | ----------- | 
| Podpisový | Ano | Zadejte svá přihlašovací údaje githubu. |

Po vytvoření připojení můžete provést akce a naslouchat aktivačním událostem popsaným v tomto článku.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/github/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)