---
title: "Přidejte konektor OneDrive ve vašich Logic Apps | Microsoft Docs"
description: "Přehled konektoru OneDrive s parametry rozhraní REST API"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 47a8582a-1b1a-4fc3-beb5-97c60c4306fe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 15b88baf21ead5a6b9506c47ca9930d5088b8787
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-onedrive-connector"></a>Začínáme s konektorem OneDrive
Připojte se ke Onedrivu spravovat vaše soubory, včetně nahrávání, získat, odstraňte soubory a další. 

S OneDrive můžete: 

* Vytvoření pracovního postupu uložením souborů na Onedrivu, nebo aktualizovat existující soubory na OneDrive. 
* Spustit pracovní postup v případě, že soubor se vytvoří nebo aktualizuje v rámci OneDrive pomocí aktivační události.
* Použijte k vytvoření souboru, odstraňte soubor a další akce. Například pokud byl přijat nový Office 365 e-mail s přílohou (aktivační události) vytvořte nový soubor ve Onedrivu (akce).

Tento článek ukazuje, jak k používání konektoru OneDrive v aplikaci logiky a taky seznam triggery a akce.

Další informace o Logic Apps najdete v tématu [co jsou logic apps](../logic-apps/logic-apps-overview.md) a [vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Připojte se ke Onedrivu
Než se aplikace logiky k jakékoli služby, nejprve vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například pokud chcete připojit k Onedrivu, musíte nejdřív OneDrive *připojení*. Chcete-li vytvořit připojení, zadejte přihlašovací údaje, které standardně používáte k přístupu ke službě, který chcete připojit k. Ano s OneDrive, zadejte přihlašovací údaje ke svému účtu Onedrivu k vytvoření připojení.

### <a name="create-the-connection"></a>Vytvoření připojení
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Použít aktivační událost
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu definované v aplikaci logiky. Aktivační události "dotazování" službu v intervalem a frekvenci, který chcete. [Další informace o aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. V aplikaci logiky zadejte "onedrive" získat seznam aktivačních událostí:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Vyberte **změny souboru**. Pokud připojení již existuje, pak vyberte tlačítko Zobrazit výběr vyberte složku.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Pokud budete vyzváni k přihlášení, pak zadejte přihlašovací údaje pro vytvoření připojení. [Vytvoření připojení](connectors-create-api-onedrive.md#create-the-connection) v tomto článku jsou uvedené kroky. 
   
   > [!NOTE]
   > V tomto příkladu spustí aplikaci logiky při otevření souboru ve složce, kterou zvolíte, se aktualizuje. Pokud chcete zobrazit výsledky této aktivační události, přidejte další akci, která vám pošle e-mailu. Například přidejte Office 365 Outlook *e-mailovou zprávu* akci, která odešle e-mail, když se aktualizuje soubor. 

3. Vyberte **upravit** tlačítko a nastavte **frekvence** a **Interval** hodnoty. Například pokud chcete, aby aktivační událost pro cyklické dotazování každých 15 minut, nastavte **frekvence** k **minutu**a nastavte **Interval** k **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky je uloženo a automaticky povolí.

## <a name="use-an-action"></a>Použít akci.
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Vyberte znaménko plus. Zobrazí několik možností: **přidat akci**, **přidat podmínku**, nebo jeden z **Další** možnosti.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Zvolte **přidat akci**.
3. Do textového pole zadejte "onedrive" získat seznam všechny dostupné akce.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. V našem příkladu zvolte **OneDrive – vytvoření souboru**. Pokud připojení již existuje, pak vyberte **cesta ke složce** uvést soubor, zadejte **název souboru**a vyberte **obsah souboru** chcete:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Pokud se zobrazí výzva pro informace o připojení, zadejte podrobnosti k vytvoření připojení. [Vytvoření připojení](connectors-create-api-onedrive.md#create-the-connection) v tomto článku popisuje tyto vlastnosti. 
   
   > [!NOTE]
   > V tomto příkladu vytvoříme nový soubor ve složce OneDrive. Výstup z jiného aktivační událost můžete použít k vytvoření souboru OneDrive. Například přidejte Office 365 Outlook *při doručení nových e-mailů* aktivační události. Pak přidejte Onedrivu *vytvořit soubor* akci, která používá příloh a Content-Type pole v rámci příkazu ForEach k vytvoření nového souboru na Onedrivu. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Uložit** změny (levém horním rohu panelu nástrojů). Aplikace logiky je uloženo a automaticky povolí.


## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).