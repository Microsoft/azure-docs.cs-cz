---
title: Připojení k Office 365 Outlooku
description: Automatizace úloh a pracovních postupů, které spravují e-maily, kontakty a kalendáře v Office 365 Outlooku pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732665"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Správa e-mailů, kontaktů a kalendářů v Office 365 Outlooku s využitím Azure Logic Apps

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru Office 365 Outlook](/connectors/office365connector/)můžete vytvářet automatizované úkoly a pracovní postupy, které spravují váš účet Office 365, a to vytvářením logických aplikací. Můžete například automatizovat tyto úkoly:

* Získejte, odesílejte a odpovězte na e-mail. 
* Naplánujte schůzky v kalendáři.
* Přidejte a upravte kontakty. 

Ke spuštění pracovního postupu můžete použít libovolnou aktivační událost, například při příchodu nového e-mailu, při aktualizaci položky kalendáře nebo při události v rozdílové službě, například salesforce. Můžete použít akce, které reagují na aktivační událost, například odeslat e-mail nebo vytvořit novou událost kalendáře. 

> [!NOTE]
> Chcete-li automatizovat @outlook.com @hotmail.com úlohy pro účet nebo účet, použijte [konektor Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Požadavky

* [Účet Office 365](https://www.office.com/)

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Aplikace logiky, ve které chcete získat přístup ke svému účtu Office 365 Outlook. Pokud chcete pracovní postup spustit pomocí aktivační události Outlooku Office 365, musíte mít [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete do pracovního postupu přidat akci Outlooku Office 365, musí mít aplikace logiky už aktivační událost.

## <a name="add-a-trigger"></a>Přidání triggeru

[Aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) je událost, která spustí pracovní postup v aplikaci logiky. Tato ukázková aplikace logiky používá aktivační událost "dotazování", která kontroluje všechny aktualizované události kalendáře ve vašem e-mailovém účtu na základě zadaného intervalu a četnosti.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Do vyhledávacího pole `office 365 outlook` zadejte jako filtr. Tento příklad vybere **Když nadcházející událost začíná brzy**.
   
   ![Výběraktivační spuštění aplikace logiky](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje office 365, aby se aplikace logiky mohla připojit k vašemu účtu. V opačném případě, pokud připojení již existuje, zadejte informace o vlastnostech aktivační události.

   Tento příklad vybere kalendář, který aktivační událost kontroluje, například:

   ![Konfigurace vlastností aktivační události](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. V aktivační události nastavte hodnoty **Frekvence** a **Interval.** Chcete-li přidat další dostupné vlastnosti aktivační události, například **Časové pásmo**, vyberte tyto vlastnosti ze seznamu **Přidat nový parametr.**

   Chcete-li například, aby aktivační událost kontrolovala kalendář každých 15 minut, nastavte **možnost Frekvence** na **minutu**a nastavte **možnost Interval** na `15`. 

   ![Nastavení frekvence a intervalu pro aktivační událost](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Na panelu nástrojů návrháře vyberte **Uložit**.

Nyní přidejte akci, která se spustí po aktivaci aktivační události. Můžete například přidat akci Twilio **Send message,** která odešle text při zahájení události kalendáře za 15 minut.

## <a name="add-an-action"></a>Přidání akce

[Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je operace, která je spuštěna pracovním postupem v aplikaci logiky. Tato ukázková aplikace logiky vytvoří nový kontakt v Office 365 Outlook. K vytvoření kontaktu můžete použít výstup z jiné aktivační události nebo akce. Předpokládejme například, že vaše aplikace logiky používá aktivační událost Dynamics 365 **Při vytvoření záznamu**. Můžete přidat akci **Vytvoření kontaktu** aplikace Office 365 Outlook a k vytvoření nového kontaktu použít výstupy ze spouště SalesForce.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Chcete-li přidat akci jako poslední krok pracovního postupu, vyberte **Nový krok**. 

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole `office 365 outlook` zadejte jako filtr. V tomto příkladu se vybere **možnost Vytvořit kontakt**.

   ![Vyberte akci, která se má spustit v aplikaci logiky.](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje office 365, aby se aplikace logiky mohla připojit k vašemu účtu. V opačném případě, pokud připojení již existuje, zadejte informace o vlastnostech akce.

   Tento příklad vybere složku kontaktů, kde akce vytvoří nový kontakt, například:

   ![Konfigurace vlastností akce](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Chcete-li přidat další dostupné vlastnosti akce, vyberte tyto vlastnosti ze seznamu **Přidat nový parametr.**

1. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Technické podrobnosti o aktivačních událostech, akcích a omezeních popsaných v souboru Swagger konektoru naleznete na [referenční stránce konektoru](/connectors/office365connector/). 

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
