---
title: Připojte se k Outlook.com – Azure Logic Apps | Dokumentace Microsoftu
description: Správa e-mailu, kalendáře a kontaktů Outlook.com rozhraní REST API a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50228751"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Spravovat e-mailu, kalendáře a kontakty na Outlook.com s Azure Logic Apps

Tento článek popisuje, jak můžete vytvořit a spravovat váš účet Outlook.com v aplikaci logiky s konektorem pole. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro váš účet Outlook.com, například:

* Odeslání e-mailu. 
* Plánování schůzek.
* Přidáte kontakty. 

Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* [Účet Outlook.com](https://outlook.live.com/owa/)

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Aplikace logiky, kde má přístup ke svému účtu Outlook.com. Spuštění aplikace logiky se aktivační událost Outlooku, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Připojte se k Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/outlook/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)