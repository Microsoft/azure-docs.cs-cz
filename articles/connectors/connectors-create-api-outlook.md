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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75707182"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Správa e-mailů, kalendářů a kontaktů v Outlook.com pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Outlook.com](/connectors/outlook/)můžete vytvářet automatizované úlohy a pracovní postupy, které spravují svůj @outlook.com @hotmail.com účet nebo pomocí vytváření Logic Apps. Tyto úlohy můžete automatizovat například takto:

* Získání, odeslání a odpověď na e-mail.
* Naplánujte schůzky v kalendáři.
* Přidávání a úpravy kontaktů.

Můžete použít libovolný Trigger ke spuštění pracovního postupu, například při přijetí nového e-mailu, při aktualizaci položky kalendáře nebo v případě, že dojde k události v rozdílové službě. Můžete použít akce, které reagují na událost triggeru, například odeslat e-mail nebo vytvořit novou událost v kalendáři.

> [!NOTE]
> K automatizaci úloh pro pracovní účet Microsoft, jako @fabrikam.onmicrosoft.com je například, použijte [konektor Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Požadavky

* [Účet Outlook.com](https://outlook.live.com/owa/)

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Aplikace logiky, ke které chcete získat přístup k účtu Outlook.com. Abyste mohli pracovní postup spustit pomocí triggeru Outlook.com, musíte mít [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li do pracovního postupu přidat akci Outlook.com, musí mít aplikace logiky již Trigger.

## <a name="add-a-trigger"></a>Přidání triggeru

[Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) je událost, která spouští pracovní postup ve vaší aplikaci logiky. Tato ukázková aplikace logiky používá aktivační událost "cyklického dotazování", která v závislosti na zadaném intervalu a četnosti kontroluje všechny nové e-maily v e-mailovém účtu.

1. V [Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. Do vyhledávacího pole zadejte "outlook.com" jako filtr. V tomto příkladu vyberte, **kdy přijde nový e-mail**.

1. Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje pro Outlook.com, aby se vaše aplikace logiky mohla připojit k vašemu účtu. Jinak, pokud připojení již existuje, zadejte informace o vlastnostech triggeru:

1. V aktivační události nastavte hodnoty **frekvence** a **interval** .

   Například pokud chcete, aby se Trigger dotazoval každých 15 minut, nastavte **četnost** na **minuty**a nastavte **interval** na **15**.

1. Na panelu nástrojů návrháře vyberte **Save (Uložit**), která uloží vaši aplikaci logiky.

Pokud chcete na Trigger reagovat, přidejte další akci. Můžete například přidat akci **Odeslat zprávu** Twilio, která pošle text při přijetí e-mailu.

## <a name="add-an-action"></a>Přidání akce

[Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je operace, kterou spouští pracovní postup ve vaší aplikaci logiky. Tato ukázková aplikace logiky pošle e-mail z vašeho účtu Outlook.com. K naplnění akce můžete použít výstup z jiné triggeru. Předpokládejme například, že vaše aplikace logiky používá SalesForce **při vytvoření objektu** . Můžete přidat akci Outlook.com **Odeslat e-mail** a použít výstupy z triggeru Salesforce v e-mailu.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud chcete přidat akci jako poslední krok pracovního postupu, vyberte **Nový krok**. 

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte "outlook.com" jako filtr. V tomto příkladu vyberte **Odeslat e-mail**. 

1. Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje pro Outlook.com, aby se vaše aplikace logiky mohla připojit k vašemu účtu. V opačném případě, pokud vaše připojení již existuje, zadejte informace o vlastnostech akce.

1. Na panelu nástrojů návrháře vyberte **Save (Uložit**), která uloží vaši aplikaci logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/outlook/). 

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
