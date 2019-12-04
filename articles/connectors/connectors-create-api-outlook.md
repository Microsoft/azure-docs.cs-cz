---
title: Připojení k Outlook.com
description: Správa e-mailů, kalendářů a kontaktů pomocí rozhraní REST API Outlook.com a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789357"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Správa e-mailů, kalendářů a kontaktů v Outlook.com pomocí Azure Logic Apps

Tento článek ukazuje, jak můžete vytvořit a spravovat účet Outlook.com uvnitř aplikace logiky pomocí konektoru box. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy pro svůj účet Outlook.com, například:

* Odeslat e-mail. 
* Plánování schůzek.
* Přidat kontakty. 

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Předpoklady

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