---
title: Připojení k Sharepointu z Azure Logic Apps | Dokumentace Microsoftu
description: Automatizace úloh a pracovní postupy monitorování a správě prostředků v Sharepointu Online nebo SharePoint serveru v místním prostředí pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 5a3968e1aec98092767712220d8aaf676aba89cd
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053521"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorování a Správa prostředků služby SharePoint pomocí Azure Logic Apps

S Azure Logic Apps a konektor služby SharePoint můžete vytvořit automatizovaných úloh a pracovních postupů, které monitorovat a spravovat prostředky, jako jsou soubory, složky, seznamy, položky, osoby, a tak dále do Sharepointu Online nebo SharePoint serveru v místním prostředí, například:

* Monitorování souborů nebo položky vytvořené, změněné ani odstraněné.
* Vytvořit, získat, aktualizovat nebo odstranit položky.
* Přidání, získání nebo odstranění přílohy. Získáte obsah z příloh.
* Vytváření, kopírování, aktualizovat a odstraňovat soubory. 
* Aktualizujte vlastnosti souboru. Získáte obsah, metadata nebo vlastnosti souboru.
* Seznam nebo výpis složky.
* Získáte seznam nebo seznamy.
* Nastaví stav schválení obsahu.
* Vyřešte osoby.
* Odešlete požadavky HTTP na server SharePoint.
* Načíst hodnoty entit.

Můžete použít aktivační události, které odpovědi ze Sharepointu a zpřístupnit výstup dalších akcí. Akce ve službě logic apps můžete použít k provádění úloh v Sharepointu. Také můžete mít další akce pomocí výstupu z akce Sharepointu. Například pokud pravidelně Načtení souborů ze Sharepointu, může odesílání zpráv do vašeho týmu pomocí konektoru systému Slack.
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Pověření SharePoint serveru adresa a uživatel

  Vaše přihlašovací údaje autorizaci aplikace logiky k vytvoření připojení a přístup k vašemu účtu služby SharePoint. 

* Předtím, než aplikace logiky můžete připojit k místním systémům, jako je SharePoint Server, budete muset [instalace a nastavení místní brány dat](../logic-apps/logic-apps-gateway-install.md). Díky tomu můžete při vytváření připojení k serveru SharePoint pro vaši aplikaci logiky použít instalaci brány.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete přístup k vašemu účtu služby SharePoint. Spustit s triggerem SharePoint [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akce Sharepointu, spusťte aplikaci logiky s triggerem, například aktivační událost Salesforce, pokud již máte účet služby Salesforce.

  Například můžete spustit aplikaci logiky s **při vytvoření záznamu** aktivační události Salesforce. 
  Tento trigger se spustí pokaždé, když se v Salesforce vytvoří nový záznam, jako je například nový zájemce. 
  Potom postupujte podle této aktivační události pomocí služby SharePoint **vytvořit soubor** akce. Tímto způsobem, když se vytvoří nový záznam, aplikace logiky vytvoří soubor v Sharepointu s informacemi o tohoto nového záznamu.

## <a name="connect-to-sharepoint"></a>Připojení k SharePointu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. V případě prázdné logic apps do vyhledávacího pole zadejte jako filtr "sharepoint". V seznamu triggerů vyberte trigger, který chcete. 

   -nebo-

   Pro existující aplikace logiky v posledním kroku, ve které chcete přidat akce Sharepointu, zvolte **nový krok**. 
   Do vyhledávacího pole zadejte jako filtr "sharepoint". 
   V seznamu akcí vyberte požadovanou akci.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
   Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

1. Po zobrazení výzvy k přihlášení, zadejte připojovací údaje. Pokud používáte SharePoint Server, ujistěte se, že vyberete **připojit přes bránu na místní data**. Jakmile budete hotoví, vyberte **Vytvořit**.

1. Zadejte potřebné podrobnosti o vybrané aktivační události nebo akce a pokračujte v rozvíjení pracovní postup aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/sharepoint/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
