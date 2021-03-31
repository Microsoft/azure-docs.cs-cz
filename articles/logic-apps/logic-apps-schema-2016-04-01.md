---
title: Aktualizace schématu – Červen 1-2016
description: Aktualizované schéma verze 2016-06-01 pro definice aplikací logiky v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000313"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizace schématu pro Azure Logic Apps – 1. června 2016

[Aktualizované schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) a verze rozhraní API pro Azure Logic Apps obsahují klíčová vylepšení, která umožňují spolehlivější a snazší používání Logic Apps:

* [Obory](#scopes) umožňují seskupit nebo vnořovat akce jako kolekci akcí.
* [Podmínky a smyčky](#conditions-loops) jsou nyní akce první třídy.
* Přesnější řazení pro spouštění akcí s `runAfter` vlastností, která nahrazuje `dependsOn`

Pokud chcete upgradovat aplikace logiky ze schématu verze 1, 2015 Preview na schéma 1. června 2016, [Podívejte se do části Upgrade](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Obory

Toto schéma zahrnuje rozsahy, které umožňují seskupit akce společně, nebo vnořené akce mezi sebou. Podmínka může například obsahovat jinou podmínku. Přečtěte si další informace o [syntaxi oboru](./logic-apps-control-flow-loops.md)nebo Projděte si příklad tohoto základního oboru:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
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

## <a name="conditions-and-loops-changes"></a>Změny podmínek a smyček

V předchozích verzích schématu byly podmínky a smyčky parametry přidružené k jedné akci. Toto omezení výtahem toto omezení, takže podmínky a smyčky jsou nyní k dispozici jako typy akcí. Přečtěte si další informace o [cyklech a oborech](./logic-apps-control-flow-loops.md), [podmínkách](../logic-apps/logic-apps-control-flow-conditional-statement.md)nebo Projděte si tento základní příklad, který ukazuje akci podmínky:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>vlastnost ' Runafter šablonové '

`runAfter`Vlastnost nahrazuje `dependsOn` a poskytuje větší přesnost při zadání pořadí spouštění pro akce na základě stavu předchozích akcí. `dependsOn`Vlastnost ukázala, zda "akce běžela a byla úspěšná" na základě toho, zda předchozí akce proběhla úspěšně, selhala nebo jako vynechána, nikoli počet pokusů, kolikrát jste chtěli akci spustit. `runAfter`Vlastnost poskytuje flexibilitu jako objekt, který určuje všechny názvy akcí, po kterých se objekt spustí. Tato vlastnost také definuje pole stavů, které jsou přijatelné jako triggery. Například pokud chcete, aby akce běžela po úspěšném provedení akce a také po úspěšném nebo neúspěšném provedení akce B, nastavte tuto `runAfter` vlastnost:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Upgrade schématu

Pokud chcete upgradovat na nejnovější [schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), stačí provést jenom několik kroků. Proces upgradu zahrnuje spuštění skriptu upgradu, uložení jako nové aplikace logiky, a pokud chcete, možná jste přepsali předchozí aplikaci logiky.

1. V Azure Portal otevřete aplikaci logiky.

2. Přejít na **Přehled** Na panelu nástrojů aplikace logiky vyberte **Aktualizovat schéma**.
   
   ![Zvolit aktualizaci schématu][1]
   
   Je vrácena upgradovaná definice, kterou můžete v případě potřeby zkopírovat a vložit do definice prostředků. 

   > [!IMPORTANT]
   > Ujistěte se, *že* jste zvolili **Uložit jako** , takže všechny odkazy na připojení zůstávají platné v aktualizované aplikaci logiky.

3. Na panelu nástrojů okna pro upgrade vyberte **Uložit jako**.

4. Zadejte název a stav logiky. Pokud chcete nasadit upgradovanou aplikaci logiky, klikněte na **vytvořit**.

5. Ověřte, že vaše upgradovaná aplikace logiky funguje podle očekávání.
   
   > [!NOTE]
   > Pokud používáte Trigger manuální nebo Request, adresa URL zpětného volání se změní v nové aplikaci logiky. Otestujte novou adresu URL, aby se zajistilo, že bude fungovat na konci. Chcete-li zachovat předchozí adresy URL, můžete klonovat stávající aplikaci logiky.

6. *Volitelné* Pokud chcete předchozí aplikaci logiky přepsat novou verzí schématu, klikněte na panelu nástrojů na tlačítko **klonovat** a vedle možnosti **Aktualizovat schéma**. Tento krok je nutný jenom v případě, že chcete zachovat stejné ID prostředku nebo adresu URL triggeru požadavku vaší aplikace logiky.

## <a name="upgrade-tool-notes"></a>Poznámky k nástroji pro upgrade

### <a name="mapping-conditions"></a>Podmínky mapování

V upgradovaných definicích nástroj provádí nejlepší úsilí při seskupení akcí na true a false větvích dohromady jako obor. Konkrétně se vzor návrháře `@equals(actions('a').status, 'Skipped')` zobrazí jako `else` akce. Pokud ale nástroj zjistí nerozpoznatelný vzor, nástroj může vytvořit samostatné podmínky pro větev true i false. V případě potřeby můžete přemapovat akce po upgradu.

#### <a name="foreach-loop-with-condition"></a>smyčka foreach s podmínkou

V novém schématu můžete použít akci filtru k replikaci vzoru, který používá **pro každou** smyčku s jednou podmínkou na položku. Tato změna se ale při upgradu provede automaticky. Podmínka se stala akcí filtru, která se zobrazí před smyčkou **for each** a vrátí pouze pole položek, které se shodují s podmínkou, a předání tohoto pole **pro každou** akci. Příklad naleznete v tématu [smyčky a rozsahy](./logic-apps-control-flow-loops.md).

### <a name="resource-tags"></a>Značky prostředků

Po upgradu se značky prostředků odeberou, takže je musíte obnovit pro upgradovaný pracovní postup.

## <a name="other-changes"></a>Další změny

### <a name="renamed-manual-trigger-to-request-trigger"></a>Aktivační událost Manual se přejmenovala na Trigger Request.

`manual`Typ triggeru byl zastaralý a přejmenován na `request` typ `http` . Tato změna vytvoří větší konzistenci pro druh vzoru, který je použit k sestavení triggeru.

### <a name="new-filter-action"></a>Nová akce Filter

Chcete-li filtrovat velké pole dolů na menší sadu položek, je nový `filter` typ akceptovat pole a podmínku, vyhodnotí podmínku pro každou položku a vrátí pole s položkami, které splňují podmínku.

### <a name="restrictions-for-foreach-and-until-actions"></a>Omezení pro akce foreach a ne

`foreach` `until` Smyčka and je omezená jenom na jednu akci.

### <a name="new-trackedproperties-for-actions"></a>New ' trackedProperties ' pro akce

Akce teď můžou mít navolanou další vlastnost `trackedProperties` , která je stejná jako `runAfter` u `type` vlastností a. Tento objekt určuje konkrétní vstupy a výstupy akcí, které chcete zahrnout do diagnostické telemetrie Azure, které jsou vygenerovány jako součást pracovního postupu. Například:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
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

* [Vytváření definic pracovních postupů pro Logic Apps](../logic-apps/logic-apps-author-definitions.md)
* [Automatizace nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
