---
title: Připojení k Salesforce z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovních postupů, které monitorování, vytvářet a spravovat záznamy Salesforce a úlohy s využitím Azure Logic Apps
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
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233833"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorování, vytváření a správě prostředků služby Salesforce pomocí Azure Logic Apps

S Azure Logic Apps a konektor služby Salesforce můžete vytvořit automatizovaných úloh a pracovních postupů pro prostředky služby Salesforce, jako jsou záznamy, úlohy a objekty, například:

* Monitorování, když jsou záznamy vytvořené nebo změněné. 
* Vytvořit, získat a spravovat úlohy a záznamy, včetně vložení, aktualizaci a odstranit některé akce.

Pomocí aktivační události Salesforce, které odpovědi ze Salesforce a zpřístupnit výstup dalších akcí. Akce ve svých aplikacích logiky můžete provádět úlohy s prostředky služby Salesforce. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* A [účtu Salesforce](https://salesforce.com/)

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu Salesforce. Spustit s triggerem Salesforce [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Použít akci Salesforce, začněte svou aplikaci logiky s další trigger, například, **opakování** aktivační události.

## <a name="connect-to-salesforce"></a>Připojení k Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Zvolte cestu: 

   * V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "salesforce". 
   V seznamu triggerů vyberte trigger, který chcete. 

     -nebo-

   * Pro existující aplikace logiky, v části krok, ve které chcete přidat akci, zvolte **nový krok**. Do vyhledávacího pole zadejte jako filtr "salesforce". V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení k Salesforce, přihlaste se hned a povolte přístup.

   Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení k Salesforce a přístup k vašim datům.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/salesforce/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)