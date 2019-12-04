---
title: Připojení k Office 365 Outlooku
description: Správa e-mailů, kontaktů a kalendářů pomocí rozhraní REST API Office 365 a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789615"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Začínáme s konektorem Office 365 Outlook
Konektor sady Office 365 Outlook umožňuje interakci s aplikací Outlook v sadě Office 365. Pomocí tohoto konektoru můžete vytvářet, upravovat a aktualizovat kontakty a položky kalendáře a také získávat, odesílat a reagovat na e-mail.

V případě Office 365 Outlooku máte tyto akce:

* Sestavujte pracovní postup pomocí funkcí e-mailu a kalendáře v sadě Office 365. 
* Použijte triggery ke spuštění pracovního postupu v případě, že je k dispozici nový e-mail, když je položka kalendáře aktualizována a další.
* Použijte akce k odeslání e-mailu, vytvoření nové události kalendáře a dalších akcí. Pokud je například nový objekt v Salesforce (aktivační událost), pošle se e-mail na Office 365 Outlook (akce). 

V tomto článku se dozvíte, jak používat konektor Office 365 Outlook v aplikaci logiky a také seznam triggerů a akcí.

> [!NOTE]
> Tato verze článku se týká Logic Apps všeobecné dostupnosti (GA).
> 
> 

Další informace o Logic Apps najdete v tématu [co jsou Logic Apps](../logic-apps/logic-apps-overview.md) a [vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Připojení k Office 365
Předtím, než vaše aplikace logiky bude mít přístup k jakékoli službě, musíte nejprve vytvořit *připojení* ke službě. Připojení zajišťuje připojení mezi aplikací logiky a jinou službou. Pokud se například chcete připojit k Office 365 Outlook, budete nejdřív potřebovat *připojení*Office 365. Pokud chcete vytvořit připojení, zadejte přihlašovací údaje, které běžně používáte pro přístup ke službě, ke které se chcete připojit. Takže v Office 365 Outlooku zadejte přihlašovací údaje k vašemu účtu Office 365 a vytvořte připojení.

## <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Použití triggeru
Trigger je událost, která se dá použít ke spuštění pracovního postupu definovaného v aplikaci logiky. Aktivuje službu cyklického dotazování služby v intervalu a četnosti, které chcete. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V aplikaci logiky zadáním příkazu "Office 365" získáte seznam aktivačních událostí:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Vyberte **Office 365 Outlook – až se nadcházející událost začne brzo spouštět**. Pokud připojení již existuje, pak v rozevíracím seznamu vyberte kalendář.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje pro vytvoření připojení. [Vytvoření připojení](connectors-create-api-office365-outlook.md#create-the-connection) v tomto tématu uvádí postup. 
   
   > [!NOTE]
   > V tomto příkladu se aplikace logiky spustí při aktualizaci události kalendáře. Chcete-li zobrazit výsledky této aktivační události, přidejte další akci, která vám pošle textovou zprávu. Můžete například přidat akci Twilio *Odeslat zprávu* , která text při zahájení události kalendáře začíná za 15 minut. 
   > 
   > 
3. Vyberte tlačítko **Upravit** a nastavte hodnoty **frekvence** a **interval** . Například pokud chcete, aby se Trigger dotazoval každých 15 minut, nastavte **četnost** na **minuty**a nastavte **interval** na **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Uložte** změny (v levém horním rohu panelu nástrojů). Vaše aplikace logiky se uloží a může se automaticky povolit.

## <a name="use-an-action"></a>Použít akci
Akce je operace prováděná pracovním postupem, který je definován v aplikaci logiky. [Přečtěte si další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Zobrazí se několik možností: **přidat akci**, **Přidat podmínku**nebo jednu z **dalších** možností.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Vyberte **přidat akci**.
3. Do textového pole zadejte "Office 365" a získejte seznam všech dostupných akcí.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. V našem příkladu vyberte **Office 365 Outlook – vytvořit kontakt**. Pokud připojení již existuje, zvolte **ID složky**, **název**a další vlastnosti:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Pokud se zobrazí výzva k zadání informací o připojení, zadejte podrobnosti pro vytvoření připojení. [Vytvoření připojení](connectors-create-api-office365-outlook.md#create-the-connection) v tomto tématu popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu vytvoříme nový kontakt v Office 365 Outlooku. K vytvoření kontaktu můžete použít výstup z jiné aktivační události. Například přidejte SalesForce *při vytvoření objektu* Trigger. Pak přidejte kontaktní akci pro *Vytvoření kontaktu* Office 365 Outlooku, která pomocí polí Salesforce vytvoří nový kontakt v Office 365. 
   > 
   > 
5. **Uložte** změny (v levém horním rohu panelu nástrojů). Vaše aplikace logiky se uloží a může se automaticky povolit.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Zobrazit všechny triggery a akce definované v Swagger a také zobrazit omezení v [podrobnostech konektoru](/connectors/office365connector/). 

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)