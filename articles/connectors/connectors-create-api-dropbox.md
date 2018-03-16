---
title: Konektor dropbox v Azure Logic Apps | Microsoft Docs
description: "Vytvoření aplikace logiky službou Azure App service. Připojení k Dropboxu ke správě souborů. Můžete provádět různé akce, jako je například nahrávání, aktualizovat, získání a odstranit soubory v Dropbox."
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7ac72cf5b18fa19bc0294abc67bf0a7089774a89
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-dropbox-connector"></a>Začínáme s konektorem Dropbox
Připojení k Dropboxu ke správě souborů. Můžete provádět různé akce, jako je například nahrávání, aktualizovat, získání a odstranit soubory v Dropbox.

Chcete-li použít [všechny konektory](apis-list.md), musíte nejprve vytvořit aplikaci logiky. Abyste mohli začít podle [vytvoření aplikace logiky teď](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Připojení k Dropboxu
Než se aplikace logiky k jakékoli služby, musíte nejprve vytvořit *připojení* ke službě. Připojení poskytuje připojení mezi aplikace logiky a jiné služby. Například, aby bylo možné připojit k Dropbox, musíte nejdřív Dropbox *připojení*. Vytvoření připojení, musíte zadat přihlašovací údaje, které standardně používáte k přístupu ke službě, který chcete připojit k. Ano v příkladu Dropbox, potřebovali byste přihlašovací údaje ke svému účtu Dropboxu k vytvoření připojení k Dropboxu. [Další informace o připojení]()

### <a name="create-a-connection-to-dropbox"></a>Umožňuje vytvořit připojení k Dropboxu
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Použít aktivační událost Dropbox
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu definované v aplikaci logiky. [Další informace o aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts).

V tomto příkladu použijeme **vytvoření souboru** aktivační události. Když dojde k této aktivační události, můžeme vám zavoláme **získat obsah souboru pomocí cesty** Dropbox akce. 

1. Zadejte *dropbox* do vyhledávacího pole v designeru Logic Apps, pak vyberte **Dropboxu - Pokud dojde k vytvoření souboru** aktivační události.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Vyberte složku, ve kterém chcete sledovat vytváření souborů. Vyberte... (označená červeným rámečkem) a přejděte do složky, kterou chcete vybrat pro vstup aktivační událost.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Pomocí akce Dropbox
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Teď, když byla přidána aktivační událost, použijte následující postup přidání akce, která bude získat nový soubor obsahu.

1. Vyberte **+ nový krok** přidat akci chcete provést, když je vytvořen nový soubor.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Vyberte **přidat akci**. Otevře se tato, které chcete do vyhledávacího pole, kde můžete vyhledat všechny akce můžete provést.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Zadejte *dropbox* k vyhledání akcí souvisejících s Dropbox.  
4. Vyberte **Dropbox - Get obsah souboru pomocí cesty** jako pro případ, kdy je vytvořen nový soubor ve vybrané složce Dropbox. Otevře se řídicí blok akce. Zobrazí se výzva k autorizaci aplikace logiky pro přístup k účtu Dropbox, pokud jste tak dosud neučinili dříve.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Vyberte... (nachází na pravé straně **cesta k souboru** ovládací prvek) a přejděte do cesty k souboru se má použít. Nebo použijte **cesta k souboru** tokenu urychlit vytváření vašeho logiku aplikace.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Uložte si práci a vytvořte nový soubor v Dropbox aktivovat pracovní postup.  

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/dropbox/).

## <a name="more-connectors"></a>Více konektorů
Přejděte zpět [rozhraní API seznamu](apis-list.md).