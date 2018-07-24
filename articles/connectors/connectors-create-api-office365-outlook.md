---
title: Připojte se k Office 365 Outlook – Azure Logic Apps | Dokumentace Microsoftu
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
ms.openlocfilehash: 0355f121a09e1ba89f98a8af5037eb1371db2242
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215634"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Začínáme s konektorem Office 365 Outlook
Konektor Office 365 Outlooku umožňuje interakci s Outlookem v Office 365. Pomocí tohoto konektoru můžete vytvořit, upravit a aktualizovat kontakty a položky kalendáře a taky získat, odesílání a odpovědět na e-mail.

S Office 365 Outlook můžete:

* Vytvoření pracovního postupu pomocí funkce e-mailu a kalendáře v rámci Office 365. 
* Použití aktivačních procedur pro spuštění pracovního postupu, pokud nový e-mail, když dojde k aktualizaci položky kalendáře a další.
* Pomocí akcí můžete odesílat e-mailu, vytvářet nové události kalendáře a další. Například když nový objekt v Salesforce (aktivační procedura), odešlete e-mailu do Office 365 Outlooku (akce). 

V tomto článku se dozvíte, jak používat konektor Office 365 Outlook v aplikaci logiky a také obsahuje triggery a akce.

> [!NOTE]
> Tato verze článku se vztahuje na Logic Apps – obecné dostupnosti (GA).
> 
> 

Další informace o Logic Apps najdete v tématu [co jsou logic apps](../logic-apps/logic-apps-overview.md) a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Připojení k Office 365
Aplikace logiky mohli získat přístup ke službám, nejprve vytvoříte *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například pokud chcete připojit k Office 365 Outlook, musíte nejprve Office 365 *připojení*. Chcete-li vytvořit připojení, zadejte přihlašovací údaje, které běžně používáte pro přístup ke službě, kterou chcete připojit k. Proto s Office 365 Outlooku, zadejte přihlašovací údaje ke svému účtu Office 365 k vytvoření připojení.

## <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Pomocí aktivační události
Trigger je událost, která umožňuje spustit pracovní postup definovaný v aplikaci logiky. Aktivační události "dotazovat" služba interval a frekvenci, kterou chcete. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V aplikaci logiky zadejte "office 365" zobrazíte seznam aktivačních událostí:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Vyberte **Office 365 Outlook – při zahájením nadcházející události**. Pokud už připojení existuje, vyberte z rozevíracího seznamu kalendáře.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací podrobnosti k vytvoření připojení. [Vytvoření připojení](connectors-create-api-office365-outlook.md#create-the-connection) v tomto tématu jsou uvedené kroky. 
   
   > [!NOTE]
   > V tomto příkladu spuštění aplikace logiky při aktualizaci události kalendáře. Pokud chcete zobrazit výsledky této aktivační události, přidejte akci, která se odešle textovou zprávu. Například přidejte Twilio *odeslat zprávu* akce tohoto texty vás, když se události kalendáře, se spouští za 15 minut. 
   > 
   > 
3. Vyberte **upravit** tlačítko a nastavte **frekvence** a **Interval** hodnoty. Například pokud chcete, aby aktivační událost pro dotazování každých 15 minut, nastavte **frekvence** k **minutu**a nastavte **Interval** k **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky se uloží a automaticky povolí.

## <a name="use-an-action"></a>Použít akci
Akce je operace prováděné pracovním postupu definovaném v aplikaci logiky. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Uvidíte několik možností: **přidat akci**, **přidat podmínku**, nebo jeden z **Další** možnosti.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Zvolte **přidat akci**.
3. Do textového pole zadejte "office 365" zobrazíte seznam dostupných akcí.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. V našem příkladu zvolte **Office 365 Outlook – vytvořit kontakt**. Pokud už připojení existuje, klikněte na tlačítko **ID složky**, **křestní jméno**a další vlastnosti:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Pokud se výzva k zadání informací o připojení, potom zadejte podrobnosti, které chcete vytvořit připojení. [Vytvoření připojení](connectors-create-api-office365-outlook.md#create-the-connection) v tomto tématu popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu vytvoříme nový kontakt v Office 365 Outlook. Výstup z další trigger slouží k vytvoření kontaktu. Přidat například SalesForce *při vytvoření objektu* aktivační události. Pak přidejte Office 365 Outlook *vytvořit kontakt* akci, která používá pole SalesForce k vytvoření nového kontaktu v Office 365. 
   > 
   > 
5. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky se uloží a automaticky povolí.

## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/office365connector/). 

## <a name="next-steps"></a>Další kroky
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Prozkoumejte další dostupné konektory v Logic Apps na naše [rozhraní API seznamu](apis-list.md).

