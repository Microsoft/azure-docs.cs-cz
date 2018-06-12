---
title: Vlastní kód pro Azure Logic Apps s Azure Functions | Microsoft Docs
description: Vytvoření a spuštění vlastní kód pro Azure Logic Apps s Azure Functions
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: befdd7af0a91623af4c5ea0fd957141cad84eb53
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297865"
---
# <a name="add-and-run-custom-code-for-logic-apps-through-azure-functions"></a>Přidejte a spuštění vlastní kód pro logic apps prostřednictvím Azure Functions

Spustit vlastní fragmenty kódu C# nebo node.js v aplikace logiky, můžete vytvořit vlastní funkce prostřednictvím Azure Functions. 
[Azure Functions](../azure-functions/functions-overview.md) nabízí práci bez serveru v Microsoft Azure a jsou užitečné pro provádění těchto úkolů:

* Pokročilé formátování nebo výpočetní polí v aplikace logiky
* Provádění výpočtů v pracovním postupu.
* Rozšířit funkce aplikace logiky s funkcemi, které jsou podporovány v C# nebo node.js

## <a name="create-custom-functions-for-your-logic-apps"></a>Vytvoření vlastní funkce pro logic apps

Doporučujeme vytvořit funkce na portálu Azure Functions z **obecný Webhook - uzlu** nebo **obecný Webhook - C#** šablony. Výsledek vytvoří automaticky vyplněna šablonu, která přijímá `application/json` z aplikace logiky. Funkce, které můžete vytvořit z těchto šablon se automaticky zjišťují a zobrazí v návrháři aplikace logiky v rámci **Azure Functions v mojí oblasti.**

Na portálu Azure na **integrací** v podokně funkce, vaše šablona by měla ukazují, že **režimu** nastavena na **Webhooku** a **Webhooku typ** je nastaven na **obecné JSON**. 

Funkce Webhooku žádost o přijmout a předejte ji do metody prostřednictvím `data` proměnné. Máte přístup k vlastnosti vaše datové části pomocí zápisu s tečkou jako `data.function-name`. Například jednoduché funkce JavaScript, která převede hodnotu data a času na datum řetězec vypadá jako v následujícím příkladu:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Azure Functions volání z aplikace logiky

Seznam kontejnery v rámci vašeho předplatného a vyberte funkci, kterou chcete volat v návrháři aplikace logiky, klikněte na tlačítko **akce** nabídce a vyberte z **Azure Functions v mojí oblasti**.

Po výběru funkce, budete vyzváni k zadání datová část vstupního objektu. Tento objekt je zpráva, že aplikace logiky odešle do funkce a musí být objekt JSON. Například, pokud chcete předat **poslední změny** data ze služby Salesforce aktivační událost, datové části funkce může vypadat jako tento příklad:

![Datum poslední změny][1]

## <a name="trigger-logic-apps-from-a-function"></a>Aplikace logiky aktivační události z funkce

Můžete aktivovat aplikaci logiky z uvnitř funkce. V tématu [Logic apps jako koncové body s](logic-apps-http-endpoint.md). Vytvoření aplikace logiky, který má aktivační události ruční a potom z uvnitř vaší funkce generování HTTP POST na ruční aktivaci, adresa URL s odebranou datovou částí, který chcete odeslat do aplikace logiky.

### <a name="create-a-function-from-logic-app-designer"></a>Vytvoření funkce z návrháře aplikace logiky

Můžete také vytvořit funkci webhooku node.js z návrháře. Nejdřív vyberte **Azure Functions v mojí oblasti** a potom vyberte kontejner pro funkce. Pokud ještě nemáte kontejner, budete muset vytvořit z [portálu Azure Functions](https://functions.azure.com/signin). Potom vyberte **vytvořit nový**.  

Chcete-li vygenerovat šablony založené na datech, které chcete vypočítat, zadejte objektu context, který chcete předat do funkce. Tento objekt musí být objekt JSON. Například pokud předáte obsah souboru z akce FTP, datové části kontextu vypadá v tomto příkladu:

![Datová část kontextu][2]

> [!NOTE]
> Protože tento objekt nebyl přetypovat jako řetězec, obsah se přidá přímo do datové části JSON. Však dojde k chybě, pokud objekt není token JSON (to znamená, že řetězec nebo JSON objekt nebo pole). Převést objekt jako řetězec, přidáte uvozovky, jak je znázorněno v první obrázek v tomto článku.
> 

Návrhář potom vytvoří šablonu funkce, můžete vytvořit vložený. Proměnné jsou předem vytvořené podle kontextu, kterou chcete použít k předání do funkce.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png
