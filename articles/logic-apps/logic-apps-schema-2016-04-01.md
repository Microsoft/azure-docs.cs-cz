---
title: Schéma se aktualizuje června-1-2016 - Azure Logic Apps | Microsoft Docs
description: Vytvoření definic JSON pro Azure Logic Apps s verzí schématu 2016-06-01
author: jeffhollan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 1cbb20a96f39ffa6f4c7375a38b3bf8046d0bd3a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299687"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizace schématu pro Azure Logic Apps – 1. června 2016

[Aktualizovat schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) a verze rozhraní API pro Azure Logic Apps zahrnuje klíčových vylepšení, které aplikace logiky spolehlivější a jednodušší použít:

* [Obory](#scopes) umožňují seskupení nebo vnoření akce jako sadu akcí.
* [Podmínky a smyčky](#conditions-loops) jsou nyní prvotřídní akce.
* Přesnější řazení pro spouštění akcí, které se `runAfter` vlastnost nahrazení `dependsOn`

K upgradu aplikace logiky na schéma 1. června 2016 schéma preview 1 srpen 2015 [podívejte se na část upgrade](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Obory

Toto schéma zahrnuje obory, které umožňují akce skupiny společně nebo akce vnoření uvnitř sebe navzájem. Podmínku může například obsahovat jiné podmínky. Další informace o [obor syntaxe](../logic-apps/logic-apps-loops-and-scopes.md), nebo zkontrolujte tento příklad základní obor:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Podmínky a smyčky změny

Ve schématu předchozí verze, podmínky a smyčky byly parametry přidružené k jedné akce. Toto schéma zruší toto omezení, aby podmínky a smyčky se nyní zobrazí jako typy akcí. Další informace o [smyčky a oborů](../logic-apps/logic-apps-loops-and-scopes.md), nebo zkontrolujte tento základní příklad pro podmínku akce:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Vlastnost 'runAfter.

`runAfter` Vlastnost nahradí `dependsOn`, poskytuje další přesnost, když zadáte pořadí spouštění pro akce na základě stavu předchozí akcí.

`dependsOn` Vlastnost byla totožná s "akce byla spuštěna a bylo úspěšné", bez ohledu na počet opakování, které jste chtěli provést akci, v závislosti na tom, jestli byla úspěšná, předchozí akce se nezdařilo nebo přeskočen. `runAfter` Vlastnost poskytuje tuto flexibilitu jako objekt, který určuje všechny názvy akcí, po jejichž uplynutí se spouští objekt. Tato vlastnost také definuje pole stavy, které mohou být použity jako aktivační události. Například pokud chcete spustit po kroku A úspěšné a také po kroku B úspěšná nebo neúspěšná, vytvoříte tím `runAfter` vlastnost:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Upgradu vašeho schématu

K upgradu [nejnovější schématu](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), je nutné pouze provést pár kroků. Proces upgradu zahrnuje spuštěním skriptu upgradu, uložit jako novou aplikaci logiky a pokud chcete, které by mohly mít přepsal předchozí aplikaci logiky.

1. Na portálu Azure otevřete aplikaci logiky.

2. Přejděte na **přehled**. Na panelu nástrojů aplikace logiky, zvolte **aktualizovat schéma**.
   
    ![Zvolte Aktualizovat schéma][1]
   
    Upgradovaná definice se vrátí, které můžete zkopírovat a vložit do definice prostředků v případě potřeby. 
    Ale jsme **důrazně doporučujeme** zvolíte **uložit jako** a ujistěte se, že všechny odkazy na připojení jsou platné v aplikaci logiky upgradovaný.

3. Na panelu nástrojů okna upgradu zvolte **uložit jako**.

4. Zadejte název logiku a stav. Chcete-li nasadit aplikaci logiky upgradovaný, zvolte **vytvořit**.

5. Potvrďte, že aplikace logiky upgradovaný funguje podle očekávání.
   
   > [!NOTE]
   > Pokud používáte ruční nebo žádostí o aktivační událost, se změní adresa URL zpětného volání v nové aplikace logiky. Otestujte novou adresu URL a ujistěte, že funguje prostředí začátku do konce. Pokud chcete zachovat předchozí adresy URL, může klonovat přes existující aplikace logiky.

6. *Volitelné* přepsat předchozí aplikaci logiky v nové verzi schématu, na panelu nástrojů vyberte **klon**vedle možnosti **aktualizovat schéma**. Tento krok je nutné pouze v případě, že chcete zachovat stejné ID prostředku nebo aktivační událost adresa URL aplikace logiky požadavku.

### <a name="upgrade-tool-notes"></a>Poznámky k upgradu nástroje

#### <a name="mapping-conditions"></a>Mapování podmínky

V definici upgradovaný nástroj provede usilovně na seskupování true a false větve akce jako obor. Konkrétně návrháře vzor `@equals(actions('a').status, 'Skipped')` mají zobrazit jako `else` akce. Ale pokud nástroj zjistí nelze rozpoznat vzory, nástroj může vytvořit oddělené podmínky pro hodnotu true a false větev. Můžete změnit mapování akcí po upgradu, v případě potřeby.

#### <a name="foreach-loop-with-condition"></a>smyčka 'typu foreach' s podmínkou

V novém schématu, můžete použít k replikaci vzor akci filtru `foreach` smyčky s podmínkou na položku, ale tato změna automaticky dojde při upgradu. Stav se změní na filtr akce před smyčka typu foreach pro vrácení pouze pole položek, které splňují podmínku a tohoto pole je předána do foreach akce. Příklad, naleznete v části [smyčky a obory](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Značky prostředku

Po upgradu se značky prostředku se odeberou, takže je nutné obnovit upgradovaný pracovního postupu.

## <a name="other-changes"></a>Další změny

### <a name="renamed-manual-trigger-to-request-trigger"></a>Přejmenovat aktivační události "Ruční" k aktivační události "žádostí"

`manual` Typ aktivační událost byla zastaralé a přejmenován na `request` s typem `http`. Tato změna vytvoří další konzistenci pro druh vzor, která má aktivační procedura slouží k vytvoření.

### <a name="new-filter-action"></a>Nová akce 'filtru.

Chcete-li filtrovat velké pole dolů menší sadu položek, nové `filter` typu přijímá pole a podmínku, vyhodnocuje podmínku pro každou položku a vrátí pole s položkami, které splňují podmínku.

### <a name="restrictions-for-foreach-and-until-actions"></a>Omezení pro "foreach" a "do" akce

`foreach` a `until` smyčky jsou omezeny na jednu akci.

### <a name="new-trackedproperties-for-actions"></a>Nový 'trackedProperties' pro akce

Akce nyní můžete mít další vlastnost s názvem `trackedProperties`, který je na stejné úrovni k `runAfter` a `type` vlastnosti. Tento objekt určuje určité akce vstupů nebo výstupů, které chcete zahrnout do Azure diagnostiky telemetrii, vygenerované jako součást pracovního postupu. Příklad:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
* [Vytvoření definice pracovního postupu pro logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Vytvoření šablony pro nasazení pro logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
