---
title: "Naučte se používat konektor Twitter v aplikace logiky | Microsoft Docs"
description: "Přehled konektor Twitter s parametry rozhraní REST API"
services: 
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: eb953ee7701d407b9b75a0699f53b9b64828a0e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twitter-connector"></a>Začínáme s konektorem služby Twitter.
Konektor Twitter můžete:

* Odesílání tweetů a tweetů get
* Časové osy přístup, přátele a délky
* Proveďte jednu z dalších akcí a aktivačních událostí, které jsou popsané v tomto článku

Chcete-li použít [všechny konektory](apis-list.md), musíte nejprve vytvořit aplikaci logiky. Abyste mohli začít podle [vytvoření aplikace logiky teď](../logic-apps/quickstart-create-first-logic-app-workflow.md).  

## <a name="connect-to-twitter"></a>Připojení k Twitteru
Než se aplikace logiky k jakékoli služby, musíte nejprve vytvořit *připojení* ke službě. A [připojení](connectors-overview.md) poskytuje připojení mezi aplikace logiky a jiné služby.  

### <a name="create-a-connection-to-twitter"></a>Umožňuje vytvořit připojení k Twitteru
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Použít aktivační událost služby Twitter.
Aktivační událost je událost, která můžete použít ke spuštění pracovního postupu definované v aplikaci logiky. [Další informace o aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts).

V tomto příkladu použijete **při odeslání nové tweet** aktivační událost pro vyhledávání #Seattle. A pokud najde #Seattle, aktualizujte soubor v Dropbox text z tweet. V příklad enterprise může vyhledat název vaší společnosti a aktualizace databáze SQL z tweet textu.

1. Zadejte *twitter* do vyhledávacího pole v designeru aplikace logiky zvolte **Twitter - při odeslání nové tweet** aktivační události   
   ![Obrázek aktivační události služby Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Zadejte *#Seattle* v **hledaný Text** ovládací prvek  
   ![Obrázek aktivační události služby Twitter 2](./media/connectors-create-api-twitter/trigger-2.png) 

Aplikace logiky v tomto okamžiku je nakonfigurovaná s aktivační událost, která zahájí spuštění ostatních triggery a akce v pracovním postupu. 

> [!NOTE]
> Pro aplikace logiky být funkční musí obsahovat nejméně jedna aktivační událost a jedna akce. Postupujte podle kroků v další části a přidejte akci.

## <a name="add-a-condition"></a>Přidat podmínku
Snažíme se pouze zájem o tweetů od uživatelů s více než 50 uživatelů. Ano podmínku, která potvrdí počet délky se přidá do aplikace logiky nejdřív.  

1. Vyberte **+ nový krok** přidat akci chcete provést, když #Seattle se nachází v nové tweet  
   ![Obrázek akce Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Vyberte **přidat podmínku** odkaz.  
   ![Obrázek stavu Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Tím se otevře **podmínku** řízení, kde můžete zkontrolovat podmínky, jako *rovná*, *je menší než*, *je větší než*, *obsahuje*atd.  
   ![Obrázek stavu Twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Vyberte **zvolte hodnotu** ovládacího prvku. U tohoto ovládacího prvku můžete vybrat jednu nebo více vlastností z libovolné předchozí akce nebo aktivační události. Podmínka hodnoty této vlastnosti je vyhodnocena jako true nebo false.
   ![Obrázek stavu Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Vyberte **...**  rozbalte seznam vlastností, aby se zobrazily všechny vlastnosti, které jsou k dispozici.        
   ![Obrázek stavu Twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Vyberte **délky počet** vlastnost.    
   ![Obrázek stavu Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Všimněte si vlastnost počet délky je nyní v ovládacím prvku hodnotu.    
   ![Obrázek stavu Twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Vyberte **je větší než** ze seznamu operátory.    
   ![Obrázek stavu Twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Zadejte 50 jako operand pro *je větší než* operátor.  
   Podmínka je nyní přidána. Uložit vaší práci pomocí **Uložit** odkaz v nabídce.    
   ![Twitter podmínku obrázek 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Pomocí akce služby Twitter.
Akce je operace prováděné definované v aplikaci logiky pracovního postupu. [Další informace o akcích](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Teď, když je aktivační událost, přidejte akci, která odešle nové tweet s obsahem tweetů nalezena. aktivační událost. Pro tento návod jsou odeslány pouze tweetů od uživatelů s více než 50 délky.  

V dalším kroku přidejte Twitter akce, který odesílá data tweet pomocí některé z vlastností jednotlivých tweet, která je odeslána uživatelem s více než 50 délky.  

1. Vyberte **přidat akci**. Tento krok otevře prvku vyhledávání, kde můžete vyhledat další akce a aktivační události.  
   ![Obrázek stavu Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Zadejte *twitter* do pole pro vyhledávání vyberte **Twitter – Post tweet** akce. Otevře se tento krok **Post tweet** řídit, kde zadáte všechny podrobnosti pro tweet odeslání.      
   ![Twitter akce obrázek 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Vyberte **Tweet text** ovládacího prvku. Všechny výstupy z předchozí akce a aktivačních událostí v aplikaci logiky jsou nyní zobrazeny. Můžete vyberte některé z těchto výstupů a použít je jako součást tweet text nové tweet.     
   ![Obrázek akce Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Vyberte **uživatelské jméno**   
5. Ihned po uživatelské jméno, zadejte *říká:* v ovládacím prvku tweet text.
6. Vyberte *Tweet text*.       
   ![Obrázek akce Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Pokud chcete aktivovat pracovní postup, uložte si práci a odeslala tweet s #Seattle hashtag.


## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Zobrazit všechny aktivační události a akce definované v swagger a také zobrazit žádné limity v [connector – podrobnosti](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Další postup
[Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)