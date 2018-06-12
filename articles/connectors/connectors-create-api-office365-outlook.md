---
title: Připojit k Office 365 Outlook - Azure Logic Apps | Microsoft Docs
description: Správa e-mailu, kontaktů a kalendáři pomocí rozhraní REST API Office 365 a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: aa900a486b9b0bd0f570da866f3d0ab78c31bc6e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296148"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Začínáme s konektor Office 365 Outlook
Konektor Office 365 Outlook umožňuje interakci s aplikací Outlook v Office 365. Pomocí tohoto konektoru můžete vytvořit, upravit a aktualizovat a položky kalendáře, kontaktů a také získat, odeslání a odpovědi k e-mailu.

S aplikací Outlook Office 365 můžete:

* Vytvořte pracovní postup pomocí funkce e-mailu a kalendář v rámci služeb Office 365. 
* Pomocí aktivační události spustit pracovní postup v případě, že je nový e-mail, když je aktualizována položky kalendáře a další.
* Pomocí akcí můžete odesílat e-mailu, vytvořte novou událost kalendáře a další. Například po vytvoření nového objektu v Salesforce (aktivační události) poslat e-mailu aplikace Outlook Office 365 (akce). 

Tento článek ukazuje, jak používat konektor Office 365 Outlook v aplikaci logiky a taky seznam triggery a akce.

> [!NOTE]
> Tato verze článku se vztahuje na Logic Apps obecné dostupnosti (GA).
> 
> 

Další informace o Logic Apps najdete v tématu [co jsou logic apps](../logic-apps/logic-apps-overview.md) a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Připojení k Office 365
Než se aplikace logiky k jakékoli služby, nejprve vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například pokud chcete připojit k Office 365 Outlook, musíte nejprve Office 365 *připojení*. Chcete-li vytvořit připojení, zadejte přihlašovací údaje, které standardně používáte k přístupu ke službě, který chcete připojit k. Proto s Office 365 Outlook, zadejte přihlašovací údaje k účtu služeb Office 365 k vytvoření připojení.

## <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Použít aktivační událost
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu definované v aplikaci logiky. Aktivační události "dotazování" službu v intervalem a frekvenci, který chcete. [Další informace o aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V aplikaci logiky zadejte "office 365" získat seznam aktivačních událostí:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Vyberte **Office 365 Outlook - během spouštění brzy nadcházející události**. Pokud připojení již existuje, pak vyberte kalendář z rozevíracího seznamu.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Pokud budete vyzváni k přihlášení, pak zadejte přihlašovací údaje pro vytvoření připojení. [Vytvoření připojení](connectors-create-api-office365-outlook.md#create-the-connection) v tomto tématu jsou uvedeny kroky. 
   
   > [!NOTE]
   > V tomto příkladu spustí aplikaci logiky při aktualizaci události kalendáře. Pokud chcete zobrazit výsledky této aktivační události, přidejte další akci, která vám pošle textovou zprávu. Například přidat Twilio *odeslat zprávu* akce této texty můžete během spouštění událostí kalendáře za 15 minut. 
   > 
   > 
3. Vyberte **upravit** tlačítko a nastavte **frekvence** a **Interval** hodnoty. Například pokud chcete, aby aktivační událost pro cyklické dotazování každých 15 minut, nastavte **frekvence** k **minutu**a nastavte **Interval** k **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky je uloženo a automaticky povolí.

## <a name="use-an-action"></a>Použít akci.
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Zobrazí několik možností: **přidat akci**, **přidat podmínku**, nebo jeden z **Další** možnosti.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Zvolte **přidat akci**.
3. Do textového pole zadejte "office 365" získat seznam všechny dostupné akce.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. V našem příkladu zvolte **Office 365 Outlook - vytvoření kontaktu**. Pokud připojení již existuje, zvolte **ID složku**, **křestní jméno**a další vlastnosti:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Pokud se zobrazí výzva pro informace o připojení, zadejte podrobnosti k vytvoření připojení. [Vytvoření připojení](connectors-create-api-office365-outlook.md#create-the-connection) v tomto tématu popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu vytvoříme nový kontakt v aplikaci Outlook Office 365. Výstup z jiné trigger slouží k vytvoření kontakt. Například přidejte SalesForce *když je vytvořen objekt* aktivační události. Pak přidejte Office 365 Outlook *vytvoření kontaktu* akci, která používá pole SalesForce k vytvoření nové nového kontaktu v Office 365. 
   > 
   > 
5. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky je uloženo a automaticky povolí.

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/office365connector/). 

## <a name="next-steps"></a>Další kroky
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte dalších dostupných konektorů v Logic Apps v našem [rozhraní API seznamu](apis-list.md).

