---
title: Připojit k boxu
description: Automatizace úloh a pracovních postupů, které vytvářejí a spravují soubory v boxu, pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666767"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Vytváření a Správa souborů v boxu pomocí Azure Logic Apps

Tento článek ukazuje, jak můžete vytvářet a spravovat soubory v krabicích zevnitř aplikace logiky pomocí konektoru box. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy pro správu souborů a dalších akcí, například:

* Sestavte svůj obchodní tok na základě dat, která získáte v poli.

* Spustí automatizované úlohy a pracovní postup při vytvoření nebo aktualizaci souboru.

* Spustí akci, která zkopíruje soubor nebo odstraní soubor.

  Když tyto akce obdrží odpověď, zpřístupní výstup pro jiné akce. 
  Například když se změní soubor v poli, můžete tento soubor poslat e-mailem pomocí Office 365.

## <a name="prerequisites"></a>Požadavky

* [Účet box](https://www.box.com/home)

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu box. Pokud chcete aplikaci logiky spustit pomocí triggeru box, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)
Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/box/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)