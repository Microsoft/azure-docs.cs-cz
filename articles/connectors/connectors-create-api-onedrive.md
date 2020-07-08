---
title: Přístup a Správa souborů na Microsoft OneDrive
description: Nahrávání a Správa souborů na OneDrivu vytvořením automatizovaných pracovních postupů v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75378428"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Přístup k souborům v konektoru OneDrive a jejich správa pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru OneDrive](/connectors/onedriveconnector/)můžete vytvářet automatizované úlohy a pracovní postupy pro správu souborů, včetně nahrávání, získávání, odstraňování souborů a dalších. S OneDrivem můžete provádět tyto úlohy:

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

1. V návrháři aplikace logiky zadejte `onedrive` seznam aktivačních událostí:  

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

3. Do vyhledávacího pole zadejte `onedrive` seznam všech dostupných akcí.

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