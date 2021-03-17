---
title: Připojení k Salesforce z Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které sledují, vytvářejí a spravují záznamy a úlohy Salesforce pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 9950951ab5189c8c7b72de78bca9465ec5f22748
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87290590"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorování, vytváření a správa prostředků Salesforce s využitím Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Salesforce můžete vytvářet automatizované úlohy a pracovní postupy pro vaše prostředky Salesforce, například záznamy, úlohy a objekty, například:

* Monitorování při vytváření nebo změně záznamů. 
* Vytváření, získávání a správa úloh a záznamů, včetně akcí vložení, aktualizace a odstranění.

Můžete použít triggery Salesforce, které získávají odpovědi ze služby Salesforce, a zpřístupnit výstup ostatním akcím. Akce v aplikacích logiky můžete použít k provádění úloh s prostředky Salesforce. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Účet Salesforce](https://salesforce.com/)

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu Salesforce. Pokud chcete začít s triggerem Salesforce, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Salesforce, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-salesforce"></a>Připojte se k Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * Pro prázdné aplikace logiky zadejte do vyhledávacího pole "Salesforce" jako filtr. 
   V seznamu triggery vyberte aktivační událost, kterou chcete. 

     -nebo-

   * Pro existující aplikace logiky v kroku, kde chcete přidat akci, vyberte možnost **Nový krok**. Do vyhledávacího pole zadejte "Salesforce" jako filtr. V seznamu akce vyberte akci, kterou chcete.

1. Pokud se zobrazí výzva k přihlášení do Salesforce, přihlaste se hned a povolte přístup.

   Vaše přihlašovací údaje opravňují aplikaci logiky vytvořit připojení k Salesforce a přístup k datům.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/salesforce/)konektoru.

## <a name="get-support"></a>Získat podporu

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
