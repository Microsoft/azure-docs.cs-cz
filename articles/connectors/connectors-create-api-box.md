---
title: Připojit k krabici
description: Automatizace úloh a pracovních postupů, které vytvářejí a spravují soubory v boxu pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666767"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Vytváření a správa souborů v boxu pomocí Aplikací Azure Logic Apps

Tento článek ukazuje, jak můžete vytvářet a spravovat soubory v boxu z aplikace logiky s konektorem Box. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úkoly a pracovní postupy pro správu souborů a dalších akcí, například:

* Sestavte si svůj obchodní tok na základě dat, která získáte z boxu.

* Při vytváření nebo aktualizaci souboru můžete aktivovat automatické úlohy a pracovní postup.

* Spusťte akci, která zkopíruje soubor nebo odstraní soubor.

  Když tyto akce získat odpověď, zpřístupní výstup pro jiné akce. 
  Když se například změní soubor v poli, můžete ho odeslat e-mailem pomocí Office 365.

## <a name="prerequisites"></a>Požadavky

* [Účet Box](https://www.box.com/home)

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Aplikace logiky, kde chcete získat přístup k účtu Box. Chcete-li spustit aplikaci logiky s aktivační událostí Box, potřebujete [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pokud s aplikacemi logiky tečte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru OpenAPI (dříve Swagger) konektoru, naleznete [na referenční stránce konektoru](/connectors/box/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)