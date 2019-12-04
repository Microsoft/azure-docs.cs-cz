---
title: Připojení k OneDrivu
description: Nahrávání a Správa souborů pomocí rozhraní REST API pro OneDrive a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 93528e257ab45644a79c58fbf600dca10317eb0b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789679"
---
# <a name="get-started-with-the-onedrive-connector"></a>Začínáme s konektorem OneDrive

Připojte se k OneDrivu, abyste mohli spravovat svoje soubory, včetně nahrávání, získávání, odstraňování souborů a dalších. S OneDrivem můžete provádět tyto úlohy:

* Sestavte pracovní postup uložením souborů na OneDrivu nebo aktualizujte stávající soubory na OneDrivu. 
* Pomocí aktivačních procedur spustíte pracovní postup při vytvoření nebo aktualizaci souboru na OneDrivu.
* Pomocí akcí můžete vytvořit soubor, odstranit soubor a další. Například když se v rámci přílohy (triggeru) přijme nový e-mailová sada Office 365, vytvoří se nový soubor na OneDrivu (akce).

V tomto článku se dozvíte, jak používat konektor OneDrive v aplikaci logiky, a taky seznam triggerů a akcí.

Další informace o Logic Apps najdete v tématu [co jsou Logic Apps](../logic-apps/logic-apps-overview.md) a [vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Připojení k OneDrivu

Předtím, než vaše aplikace logiky bude mít přístup k jakékoli službě, musíte nejprve vytvořit *připojení* ke službě. Připojení zajišťuje připojení mezi aplikací logiky a jinou službou. Pokud se například chcete připojit k OneDrivu, budete nejdřív potřebovat *připojení*k OneDrivu. Pokud chcete vytvořit připojení, zadejte přihlašovací údaje, které běžně používáte pro přístup ke službě, ke které se chcete připojit. Pokud tedy máte na OneDrivu, zadejte přihlašovací údaje k účtu OneDrive a vytvořte připojení.

### <a name="create-the-connection"></a>Vytvoření připojení

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Použití triggeru

Trigger je událost, která se dá použít ke spuštění pracovního postupu definovaného v aplikaci logiky. Aktivuje službu cyklického dotazování služby v intervalu a četnosti, které chcete. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V aplikaci logiky zadejte "OneDrive", abyste získali seznam aktivačních událostí:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Vyberte, **kdy se má soubor upravovat**. Pokud připojení již existuje, vyberte složku kliknutím na tlačítko Zobrazit výběr.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje pro vytvoření připojení. [Vytvoření připojení](connectors-create-api-onedrive.md#create-the-connection) v tomto článku obsahuje seznam kroků.

   V tomto příkladu se aplikace logiky spustí, když se aktualizuje soubor v zvolené složce. Pokud chcete zobrazit výsledky této aktivační události, přidejte další akci, která vám pošle e-mail. Přidejte například *e-mailovou* akci pro Office 365 Outlook, která vás pošle e-mailem při aktualizaci souboru.

3. Vyberte tlačítko **Upravit** a nastavte hodnoty **frekvence** a **interval** . Například pokud chcete, aby se Trigger dotazoval každých 15 minut, nastavte **četnost** na **minuty**a nastavte **interval** na **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Uložte** změny (v levém horním rohu panelu nástrojů). Vaše aplikace logiky se uloží a může se automaticky povolit.

## <a name="use-an-action"></a>Použít akci

Akce je operace prováděná pracovním postupem, který je definován v aplikaci logiky. [Přečtěte si další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Zobrazí se několik možností: **přidat akci**, **Přidat podmínku**nebo jednu z **dalších** možností.

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Vyberte **přidat akci**.

3. Do textového pole zadejte "OneDrive", abyste získali seznam všech dostupných akcí.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. V našem příkladu vyberte **OneDrive – vytvořit soubor**. Pokud již existuje připojení, vyberte **cestu ke složce** , kam chcete soubor vložit, zadejte **název souboru**a zvolte požadovaný **obsah souboru** :  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Pokud se zobrazí výzva k zadání informací o připojení, zadejte podrobnosti pro [vytvoření připojení, jak je popsáno](#create-the-connection) v tomto tématu.

   V tomto příkladu vytvoříte nový soubor ve složce OneDrive. K vytvoření souboru OneDrivu můžete použít výstup z jiné triggeru. Přidejte například sadu Office 365 Outlook, *když přijde nový e-mail* . Pak přidejte akci *Vytvoření souboru* na OneDrivu, která používá pole příloh a typ obsahu v rámci foreach k vytvoření nového souboru na OneDrivu.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Uložte** změny (v levém horním rohu panelu nástrojů). Vaše aplikace logiky se uloží a může se automaticky povolit.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Zobrazit všechny triggery a akce definované v Swagger a také zobrazit omezení v [podrobnostech konektoru](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Další kroky

* [Konektory pro Azure Logic Apps](apis-list.md)