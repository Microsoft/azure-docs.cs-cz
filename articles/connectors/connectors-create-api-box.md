---
title: Připojit se k boxu – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření a správa souborů pomocí pole rozhraní REST API a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: b5c8c18c6d02710646560f29d4bc7b5784f730a2
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746873"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Vytvářet a spravovat soubory v boxu pomocí Azure Logic Apps

Tento článek popisuje, jak můžete vytvořit a spravovat vaše soubory v boxu z uvnitř aplikace logiky s konektorem pole. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro správu vašich souborů a další akce, například:

* Vytvoření obchodní toku na základě dat, který jste získali z pole. 

* Aktivace automatizovaných úloh a pracovní postup, když se vytvoří nebo aktualizuje soubor.

* Spouštění akcí, které se zkopíruje soubor, odstraní soubor a další. 

  Když se tyto akce získat odpověď, využívají výstupu k dispozici pro další akce. 
  Například při změně souboru v boxu, můžete odeslat tento soubor v e-mailu v Office 365.

## <a name="prerequisites"></a>Požadavky

* A [pole účtu](https://www.box.com/home)

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Aplikace logiky, ve které chcete přístup k účtu Box. Spuštění aplikace logiky s triggerem pole, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/box/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)