---
title: Přístup k souborům a správa na Microsoft OneDrivu
description: Nahrávání a správa souborů na OneDrivu vytvořením automatizovaných pracovních postupů v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378428"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Přístup k souborům v konektoru OneDrive a správa pomocí Azure Logic Apps

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [konektoru OneDrive](/connectors/onedriveconnector/)můžete vytvářet automatizované úlohy a pracovní postupy pro správu souborů, včetně nahrávání, získání, odstranění souborů a dalších. S OneDrivem můžete provádět tyto úkoly:

* Vytvořte si pracovní postup ukládáním souborů na OneDrive nebo aktualizujte existující soubory na OneDrivu. 
* Pomocí aktivačních událostí můžete spustit pracovní postup při vytvoření nebo aktualizaci souboru na OneDrivu.
* Pomocí akcí můžete vytvořit soubor, odstranit soubor a další. Když se například přijme nový e-mail Office 365 s přílohou (aktivační událostí), vytvořte na OneDrivu (akci) nový soubor.

Tento článek ukazuje, jak používat konektor OneDrive v aplikaci logiky a také seznam aktivačních událostí a akcí.

Další informace o logic aplikacích najdete v tématu [Co jsou aplikace logiky](../logic-apps/logic-apps-overview.md) a [vytvořte aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Připojení k OneDrivu

Před aplikace logiky přístup k libovolné službě, nejprve vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikací logiky a jinou službou. Chcete-li se například připojit k OneDrivu, musíte nejdřív připojit *OneDrive*. Chcete-li vytvořit připojení, zadejte pověření, která obvykle používáte pro přístup ke službě, ke které se chcete připojit. Takže s OneDrivem zadejte přihlašovací údaje ke svému účtu OneDrivu a vytvořte si připojení.

### <a name="create-the-connection"></a>Vytvoření připojení

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Použití aktivační události

Aktivační událost je událost, kterou lze použít ke spuštění pracovního postupu definovaného v aplikaci logiky. Aktivuje "dotazování" služby v intervalu a frekvenci, které chcete. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V Návrháři aplikace `onedrive` logiky zadejte seznam aktivačních událostí:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Vyberte **při změně souboru**. Pokud připojení již existuje, vyberte složku výběrem položky Zobrazit.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací údaje pro vytvoření připojení. [Vytvořte připojení](connectors-create-api-onedrive.md#create-the-connection) v tomto článku uvádí kroky.

   V tomto příkladu se aplikace logiky spustí, když se aktualizuje soubor ve zvolené složce. Chcete-li zobrazit výsledky této aktivační události, přidejte další akci, která vám pošle e-mail. Přidejte například aplikaci Office 365 *Outlook: Odešlete e-mailovou* akci, která vám pošle e-mail při aktualizaci souboru.

3. Vyberte tlačítko **Upravit** a nastavte hodnoty **Frekvence** a **Interval.** Chcete-li například, aby se aktivační událost každých 15 minut dotazovala, nastavte **frekvenci** na **minutu**a nastavte **interval** na **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Uložte** změny (levý horní roh panelu nástrojů). Aplikace logiky je uložena a může být automaticky povolena.

## <a name="use-an-action"></a>Použití akce

Akce je operace prováděná pracovním postupem definovaným v aplikaci logiky. [Přečtěte si další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Zobrazí se několik možností: **Přidejte akci**, **Přidat podmínku**nebo jednu z možností **Další.**

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Zvolte **Přidat akci**.

3. Do vyhledávacího pole `onedrive` zadejte seznam všech dostupných akcí.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. V našem příkladu zvolte **OneDrive – Vytvořit soubor**. Pokud připojení již existuje, vyberte **cestu ke složce, chcete-li** soubor umístit, zadejte název **souboru**a zvolte požadovaný **obsah souboru:**  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Pokud se zobrazí výzva k zadání informací o připojení, zadejte podrobnosti pro [vytvoření připojení, jak je popsáno](#create-the-connection) v tomto tématu.

   V tomto příkladu vytvoříte nový soubor ve složce OneDrive. K vytvoření souboru OneDrivu můžete použít výstup z jiné aktivační události. Můžete například přidat aktivační událost Office 365 Outlook *when a new email arrives.* Pak přidejte akci *Vytvoření souboru* onedru, která k vytvoření nového souboru na OneDrivu používá pole Přílohy a Typ obsahu v rámci foreachu.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Uložte** změny (levý horní roh panelu nástrojů). Aplikace logiky je uložena a může být automaticky povolena.

## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Zobrazit všechny aktivační události a akce definované v naparování a také zobrazit všechny limity v [podrobnostech konektoru](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Další kroky

* [Konektory pro Azure Logic Apps](apis-list.md)