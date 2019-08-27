---
title: Připojení k boxu – Azure Logic Apps
description: Vytváření a Správa souborů s rozhraními REST API a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 5fbc64194182c41a70eb27a4049234973c39fffe
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050926"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Vytváření a Správa souborů v krabici pomocí Azure Logic Apps

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

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)