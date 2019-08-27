---
title: Připojení k Salesforce z Azure Logic Apps | Microsoft Docs
description: Automatizace úloh a pracovních postupů, které sledují, vytvářejí a spravují záznamy a úlohy Salesforce pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: e3e9f7c841d25fa988ae7e0c97adf64a51d8ef87
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050821"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorování, vytváření a Správa prostředků Salesforce pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Salesforce můžete vytvářet automatizované úlohy a pracovní postupy pro vaše prostředky Salesforce, například záznamy, úlohy a objekty, například:

* Monitorování při vytváření nebo změně záznamů. 
* Vytváření, získávání a správa úloh a záznamů, včetně akcí vložení, aktualizace a odstranění.

Můžete použít triggery Salesforce, které získávají odpovědi ze služby Salesforce, a zpřístupnit výstup ostatním akcím. Akce v aplikacích logiky můžete použít k provádění úloh s prostředky Salesforce. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Účet Salesforce](https://salesforce.com/)

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu Salesforce. Pokud chcete začít s triggerem Salesforce, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Salesforce, spusťte aplikaci logiky s jinou triggerovou procedurou, například Trigger **opakování** .

## <a name="connect-to-salesforce"></a>Připojení k Salesforce

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

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)