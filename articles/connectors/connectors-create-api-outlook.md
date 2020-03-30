---
title: Připojení k Outlook.com
description: Automatizace úloh a pracovních postupů, které spravují e-maily, kalendáře a kontakty v Outlook.com pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707182"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Správa e-mailů, kalendářů a kontaktů v Outlook.com pomocí Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Outlook.com](/connectors/outlook/)můžete vytvářet automatizované úlohy a pracovní postupy, které spravují vaše @outlook.com nebo @hotmail.com účet, a to vytvářením aplikací logiky. Můžete například automatizovat tyto úkoly:

* Získejte, odesílejte a odpovězte na e-mail.
* Naplánujte schůzky v kalendáři.
* Přidejte a upravte kontakty.

Ke spuštění pracovního postupu můžete použít libovolnou aktivační událost, například při příchodu nového e-mailu, při aktualizaci položky kalendáře nebo při události ve službě rozdíl. Můžete použít akce, které reagují na aktivační událost, například odeslat e-mail nebo vytvořit novou událost kalendáře.

> [!NOTE]
> Chcete-li automatizovat úkoly pro @fabrikam.onmicrosoft.compracovní účet Microsoft, například , použijte [konektor Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Požadavky

* [Účet Outlook.com](https://outlook.live.com/owa/)

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Aplikace logiky, kde chcete získat přístup k účtu Outlook.com. Chcete-li spustit pracovní postup pomocí Outlook.com aktivační události, musíte mít [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li přidat akci Outlook.com do pracovního postupu, aplikace logiky už musí mít aktivační událost.

## <a name="add-a-trigger"></a>Přidání triggeru

[Aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) je událost, která spustí pracovní postup v aplikaci logiky. Tato ukázková aplikace logiky používá aktivační událost "dotazování", která kontroluje všechny nové e-maily ve vašem e-mailovém účtu na základě zadaného intervalu a četnosti.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Do vyhledávacího pole zadejte jako filtr "outlook.com". V tomto příkladu vyberte **Při příchodu nového e-mailu**.

1. Pokud se zobrazí výzva k přihlášení, zadejte Outlook.com přihlašovací údaje, aby se aplikace logiky mohla připojit k vašemu účtu. V opačném případě, pokud připojení již existuje, zadejte informace o vlastnostech aktivační události:

1. V aktivační události nastavte hodnoty **Frekvence** a **Interval.**

   Chcete-li například, aby se aktivační událost každých 15 minut dotazovala, nastavte **frekvenci** na **minutu**a nastavte **interval** na **15**.

1. Na panelu nástrojů návrháře vyberte **Uložit**, který uloží aplikaci logiky.

Chcete-li reagovat na aktivační událost, přidejte další akci. Můžete například přidat akci Twilio **Odeslat zprávu,** která odešle textovou zprávu při doručení e-mailu.

## <a name="add-an-action"></a>Přidání akce

[Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je operace, která je spuštěna pracovním postupem v aplikaci logiky. Tento příklad aplikace logiky odešle e-mail z vašeho účtu Outlook.com. K naplnění akce můžete použít výstup z jiné aktivační události. Předpokládejme například, že vaše aplikace logiky používá SalesForce **Při vytvoření objektu** aktivační událost. Můžete přidat Outlook.com **Odeslat akci e-mailu** a použít výstupy z aktivační události SalesForce v e-mailu.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Chcete-li přidat akci jako poslední krok pracovního postupu, vyberte **Nový krok**. 

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole zadejte jako filtr "outlook.com". V tomto příkladu vyberte **Odeslat e-mail**. 

1. Pokud se zobrazí výzva k přihlášení, zadejte Outlook.com přihlašovací údaje, aby se aplikace logiky mohla připojit k vašemu účtu. V opačném případě, pokud připojení již existuje, zadejte informace o vlastnostech akce.

1. Na panelu nástrojů návrháře vyberte **Uložit**, který uloží aplikaci logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](/connectors/outlook/). 

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
