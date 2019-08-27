---
title: Připojení k Outlook.com-Azure Logic Apps | Microsoft Docs
description: Správa e-mailů, kalendářů a kontaktů pomocí rozhraní REST API Outlook.com a Azure Logic Apps
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
ms.openlocfilehash: 4586255e96647267bc913f2bc054610163e16bd3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050875"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Správa e-mailů, kalendářů a kontaktů v Outlook.com pomocí Azure Logic Apps

Tento článek ukazuje, jak můžete vytvořit a spravovat účet Outlook.com uvnitř aplikace logiky pomocí konektoru box. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy pro svůj účet Outlook.com, například:

* Odeslat e-mail. 
* Plánování schůzek.
* Přidat kontakty. 

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* [Účet Outlook.com](https://outlook.live.com/owa/)

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Aplikace logiky, ke které chcete získat přístup k účtu Outlook.com. Pokud chcete aplikaci logiky spustit pomocí triggeru Outlooku, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="connect-to-outlookcom"></a>Připojení k Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/outlook/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)