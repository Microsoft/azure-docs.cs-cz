---
title: Připojte se k Onedrivu – Azure Logic Apps | Dokumentace Microsoftu
description: Odesílat a spravovat soubory pomocí rozhraní REST API pro OneDrive a Azure Logic Apps
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
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106223"
---
# <a name="get-started-with-the-onedrive-connector"></a>Začínáme s konektor OneDrive
Připojte se k Onedrivu pro správu souborů, včetně odesílání, získávání, odstraňování souborů a dalších. 

S Onedrivem můžete: 

* Vytvoření pracovního postupu uložením souborů na Onedrivu nebo aktualizovat stávající soubory v Onedrivu. 
* Použití aktivačních procedur pro spuštění pracovního postupu při vytvoření nebo aktualizaci vašeho onedrivu soubor.
* Pomocí akcí můžete vytvořit soubor, odstraňte soubor a další. Například při přijetí nového e-mailu Office 365 s přílohou (aktivační procedura) vytvořte nový soubor ve Onedrivu (akce).

V tomto článku se dozvíte, jak používat konektor OneDrive v aplikaci logiky a také obsahuje triggery a akce.

Další informace o Logic Apps najdete v tématu [co jsou logic apps](../logic-apps/logic-apps-overview.md) a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Připojte se k Onedrivu
Aplikace logiky mohli získat přístup ke službám, nejprve vytvoříte *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například pokud chcete připojit k Onedrivu, musíte nejprve Onedrivu *připojení*. Chcete-li vytvořit připojení, zadejte přihlašovací údaje, které běžně používáte pro přístup ke službě, kterou chcete připojit k. Ano s Onedrivem, zadejte přihlašovací údaje ke svému účtu Onedrivu k vytvoření připojení.

### <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Pomocí aktivační události
Trigger je událost, která umožňuje spustit pracovní postup definovaný v aplikaci logiky. Aktivační události "dotazovat" služba interval a frekvenci, kterou chcete. [Další informace o aktivačních událostech](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V aplikaci logiky zadejte "onedrivu" zobrazíte seznam aktivačních událostí:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Vyberte **při změně souboru**. Pokud už připojení existuje, pak vyberte tlačítko Zobrazit výběr a vyberte složku.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Pokud se zobrazí výzva k přihlášení, zadejte přihlašovací podrobnosti k vytvoření připojení. [Vytvoření připojení](connectors-create-api-onedrive.md#create-the-connection) v tomto článku jsou uvedené kroky. 
   
   > [!NOTE]
   > V tomto příkladu spuštění aplikace logiky při otevření souboru ve složce, kterou zvolíte, se aktualizuje. Pokud chcete zobrazit výsledky této aktivační události, přidáte další akci, která odešle e-mail. Například přidejte Office 365 Outlook *odeslat e-mailu* akci, která odešle e-mail, pokud při aktualizaci souboru. 

3. Vyberte **upravit** tlačítko a nastavte **frekvence** a **Interval** hodnoty. Například pokud chcete, aby aktivační událost pro dotazování každých 15 minut, nastavte **frekvence** k **minutu**a nastavte **Interval** k **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky se uloží a automaticky povolí.

## <a name="use-an-action"></a>Použít akci
Akce je operace prováděné pracovním postupu definovaném v aplikaci logiky. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Uvidíte několik možností: **Přidání akce**, **přidat podmínku**, nebo jeden z **Další** možnosti.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Zvolte **přidat akci**.
3. Do textového pole zadejte "onedrivu" zobrazíte seznam dostupných akcí.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. V našem příkladu zvolte **OneDrive – vytvořit soubor**. Pokud už připojení existuje, můžete vybrat **cesta ke složce** umístit soubor, zadejte **název souboru**a zvolte **obsah souboru** chcete:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Pokud se výzva k zadání informací o připojení, potom zadejte podrobnosti, které chcete vytvořit připojení. [Vytvoření připojení](connectors-create-api-onedrive.md#create-the-connection) v tomto článku popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu vytvoříme nový soubor ve složce Onedrivu. Výstup z další trigger slouží k vytvoření souboru na Onedrivu. Například přidejte Office 365 Outlook *při přijetí nového e-mailu* aktivační události. Pak přidejte OneDrive *vytvořit soubor* akci, která používá přílohy a Content-Type pole v rámci příkazu ForEach k vytvoření nového souboru na Onedrivu. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky se uloží a automaticky povolí.


## <a name="connector-specific-details"></a>Podrobné informace specifické pro konektor

Zobrazit všechny aktivační události a akce definované ve swaggeru a také zjistit žádné omezení [podrobnosti o konektoru](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).