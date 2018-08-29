---
title: Schéma aktualizací z června-1-2016 – Azure Logic Apps | Dokumentace Microsoftu
description: Aktualizované schéma verze 2016-06-01 pro definic aplikací logiky v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 43fd52dd04e679b9756c07e8c6e260323469026a
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126198"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizace schématu pro Azure Logic Apps – 1. června 2016

[Aktualizovat schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) verze rozhraní API pro Azure Logic Apps, včetně klíčových vylepšení, které spolehlivější a usnadňuje používání logic apps:

* [Obory](#scopes) umožňují seskupit nebo vnořené akce jako kolekci akcí.
* [Podmínek a cyklů](#conditions-loops) jsou nyní prvotřídní akce.
* Více preciznímu objednávání pro spouštění akcí, které se `runAfter` vlastnost nahrazení `dependsOn`

Upgrade aplikací logiky ze schématu ve verzi preview 1. srpna 2015 schématu 1. června 2016 [podívejte se na část upgrade](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Obory

Toto schéma obsahuje obory, které umožňují skupiny najednou, nebo vnoření akce uvnitř sebe navzájem. Například podmínky může obsahovat další podmínku. Další informace o [rozsah syntaxe](../logic-apps/logic-apps-loops-and-scopes.md), případně si můžete přečíst v tomto příkladu základní obor:

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

## <a name="conditions-and-loops-changes"></a>Změny podmínek a cyklů

Ve schématu předchozí verze, podmínek a cyklů byly parametry přidružené k jedné akce. Toto schéma výtahů toto omezení, takže podmínek a cyklů teď budou zobrazovat jako typy akcí. Další informace o [smyčky a obory](../logic-apps/logic-apps-loops-and-scopes.md), případně si můžete přečíst tento základní příklad pro podmínku akce:

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

## <a name="runafter-property"></a>Vlastnost "runAfter.

`runAfter` Nahradí vlastnost `dependsOn`, poskytuje větší přesnost při zadávání pořadí spuštění pro akce na základě stavu z předchozích akcí.

`dependsOn` Vlastnost se synonymem "akci, která spustila a bylo úspěšné", bez ohledu na tom, kolikrát byste chtěli provést akce, v závislosti na tom, jestli byl úspěšný, předchozí akci se nezdařilo nebo přeskočíte. `runAfter` Vlastnost poskytuje flexibilita jako objekt, který určuje všechny názvy akcí, po jejichž uplynutí se spouští na objekt. Tato vlastnost také definuje pole objektů stavy, které jsou přijatelné jako aktivační události. Například pokud chcete spustit po úspěšné krok a také po kroku B úspěšné nebo neúspěšné, vytvoříte tím `runAfter` vlastnost:

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

Upgradovat [nejnovější schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), třeba trvat jenom pár kroků. Proces upgradu zahrnuje spuštění skriptu pro upgrade, uložit jako novou aplikaci logiky a pokud chcete, potenciálně přepsaly předchozí aplikaci logiky.

1. Na webu Azure Portal otevřete aplikaci logiky.

2. Přejděte na **přehled**. Na panelu nástrojů aplikace logiky zvolte **aktualizovat schéma**.
   
    ![Zvolte Aktualizovat schéma][1]
   
    Upgradované definice se vrátí, který můžete zkopírovat a vložit do definicí prostředků, v případě potřeby. 
    Ale jsme **důrazně doporučujeme** zvolíte **uložit jako** abyste měli jistotu, že všechny odkazy na připojení jsou platné v upgradovanou aplikaci logiky.

3. Na panelu nástrojů okno upgradu položku **uložit jako**.

4. Zadejte název logiky a stav. Chcete-li nasadit svou aplikaci logiky upgradované, zvolte **vytvořit**.

5. Potvrďte, že vaše upgradovanou aplikaci logiky funguje podle očekávání.
   
   > [!NOTE]
   > Pokud používáte trigger ruční nebo abychom si vyžádali, změní adresu URL zpětného volání v svou novou aplikaci logiky. Otestujte novou adresu URL k Ujistěte se, že funguje prostředí začátku do konce. Pokud chcete zachovat předchozí adresy URL, můžete klonovat přes svou stávající aplikaci logiky.

6. *Volitelné* přepsat předchozí aplikace logiky v nové verzi schématu, na panelu nástrojů zvolte **klonování**vedle možnosti **aktualizovat schéma**. Tento krok je nutný jenom v případě, že chcete zachovat stejné ID prostředku, nebo požádat o adresu URL triggeru aplikace logiky.

### <a name="upgrade-tool-notes"></a>Poznámky k upgradu nástroje

#### <a name="mapping-conditions"></a>Mapování podmínky

V definici upgradovaný nástroj provede pokusí na seskupování větve true a false akce jako obor. Konkrétně návrháře vzor `@equals(actions('a').status, 'Skipped')` by se měla zobrazit jako `else` akce. Nicméně pokud nástroj zjistí nelze rozpoznat vzorce, nástroj může vytvořit samostatné podmínky pro hodnotu true a false větev. Po provedení upgradu můžete přemapovat akce v případě potřeby.

#### <a name="foreach-loop-with-condition"></a>smyčka "foreach" s podmínkou

V novém schématu, můžete použít filtr akce k replikaci vzorec, podle kterého `foreach` smyčky s podmínkou jednu položku, ale tato změna by měl automaticky stát při upgradu. Podmínka nestane filtr akce před smyčky foreach pro vrácení pouze pole položek, které splňují podmínku a toto pole je předána do akce foreach. Příklad najdete v tématu [smyčky a obory](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Značky prostředků

Po upgradu, značky prostředku se odeberou, takže je nutné obnovit upgradovaný pracovního postupu.

## <a name="other-changes"></a>Další změny

### <a name="renamed-manual-trigger-to-request-trigger"></a>Přejmenované "Ruční" aktivační události trigger "požadavku.

`manual` Typ aktivační události se zastaralé a přejmenovat na `request` s typem `http`. Tato změna vytváří více konzistence pro druh vzor, který se aktivační událost se používá k sestavení.

### <a name="new-filter-action"></a>Nová akce "filtrování"

Chcete-li filtrovat velká pole na menší sadu položek, nové `filter` typu přijímá pole a podmínky, vyhodnocuje podmínku pro každou položku a vrátí pole s položkami, které podmínku.

### <a name="restrictions-for-foreach-and-until-actions"></a>Omezení pro "foreach" a "do" akce

`foreach` a `until` smyčky mohly probíhat jenom v rámci jedné akce.

### <a name="new-trackedproperties-for-actions"></a>Nový "trackedProperties" pro akce

Akce teď můžou mít další vlastnost s názvem `trackedProperties`, což je na stejné úrovni k `runAfter` a `type` vlastnosti. Tento objekt určuje určité akce vstupy nebo výstupy, které chcete zahrnout do Azure diagnostická telemetrie generované jako součást pracovního postupu. Příklad:

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
* [Vytvoření šablony nasazení pro logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
