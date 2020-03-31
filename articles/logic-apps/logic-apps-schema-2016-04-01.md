---
title: Aktualizace schématu červen-1-2016
description: Aktualizované schéma verze 2016-06-01 pro definice aplikací logiky v Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792879"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Aktualizace schématu pro aplikace Azure Logic Apps – 1.

[Aktualizované schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) a verze rozhraní API pro Azure Logic Apps obsahuje klíčová vylepšení, která usnadňují a snadněji se používají aplikace logiky:

* [Obory](#scopes) umožňují seskupit nebo vnořit akce jako kolekci akcí.
* [Podmínky a smyčky](#conditions-loops) jsou nyní prvotřídní akce.
* Přesnější řazení pro spuštění `runAfter` akcí s vlastností, nahrazení`dependsOn`

Chcete-li upgradovat aplikace logiky ze schématu náhledu [1.](#upgrade-your-schema)

<a name="scopes"></a>

## <a name="scopes"></a>Obory

Toto schéma zahrnuje obory, které umožňují seskupit akce společně nebo vnořovat akce uvnitř sebe. Podmínka může například obsahovat jinou podmínku. Přečtěte si další informace o [syntaxi oboru](../logic-apps/logic-apps-loops-and-scopes.md)nebo si přečtěte tento základní příklad oboru:

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

V předchozích verzích schématu byly podmínky a smyčky parametry přidružené k jedné akci. Toto schéma zruší toto omezení, takže podmínky a smyčky jsou nyní k dispozici jako typy akcí. Další informace o [smyčkách a oborech](../logic-apps/logic-apps-loops-and-scopes.md), [podmínkách](../logic-apps/logic-apps-control-flow-conditional-statement.md)nebo v tomto základním příkladu, který ukazuje akci podmínky:

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

## <a name="runafter-property"></a>vlastnost runAfter

Vlastnost `runAfter` nahradí `dependsOn`, poskytuje větší přesnost při zadání pořadí spuštění pro akce na základě stavu předchozích akcí. Vlastnost `dependsOn` uvedla, zda "akce proběhla a byla úspěšná", na základě toho, zda předchozí akce proběhla úspěšně, se nezdařila nebo byla přeskočena - nikoli kolikrát jste chtěli akci spustit. Vlastnost `runAfter` poskytuje flexibilitu jako objekt, který určuje všechny názvy akcí, po kterém je objekt spuštěn. Tato vlastnost také definuje pole stavů, které jsou přijatelné jako aktivační události. Pokud například chcete, aby byla akce spuštěna po akci A úspěšné a také `runAfter` po úspěšné akci B nebo neúspěšné akci, nastavte tuto vlastnost:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Inovujte schéma

Chcete-li upgradovat na [nejnovější schéma](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), stačí provést pouze několik kroků. Proces upgradu zahrnuje spuštění skriptu upgradu, uložení jako novou aplikaci logiky a pokud chcete, případně přepsat předchozí aplikaci logiky.

1. Na webu Azure Portal otevřete aplikaci logiky.

2. Přejděte na **Přehled**. Na panelu nástrojů aplikace logiky zvolte **Aktualizovat schéma**.
   
   ![Zvolte Schéma aktualizace.][1]
   
   Je vrácena upgradovaná definice, kterou můžete v případě potřeby zkopírovat a vložit do definice prostředku. 

   > [!IMPORTANT]
   > *Ujistěte se, že* zvolíte **Uložit jako,** aby všechny odkazy na připojení zůstaly v upgradované aplikaci logiky platné.

3. Na panelu nástrojů okna upgradu zvolte **Uložit jako**.

4. Zadejte název a stav logiky. Pokud chcete nasadit upgradovologickou aplikaci, zvolte **Vytvořit**.

5. Zkontrolujte, zda upgradovaná aplikace logiky funguje podle očekávání.
   
   > [!NOTE]
   > Pokud používáte ruční nebo aktivační událost požadavku, změní se adresa URL zpětného volání v nové aplikaci logiky. Otestujte novou adresu URL a ujistěte se, že prostředí od konce do konce funguje. Chcete-li zachovat předchozí adresy URL, můžete klonovat přes existující aplikaci logiky.

6. *Nepovinné* Chcete-li přepsat předchozí aplikaci logiky novou verzí schématu, zvolte na panelu nástrojů **možnost Klonování**vedle **položky Aktualizovat schéma**. Tento krok je nutný pouze v případě, že chcete zachovat stejné ID prostředku nebo adresu URL aktivační adresy pro aktivaci aplikace logiky.

## <a name="upgrade-tool-notes"></a>Poznámky k nástroji pro upgrade

### <a name="mapping-conditions"></a>Podmínky mapování

V upgradované definici nástroj vynakládá maximální úsilí na seskupení skutečné a falešné akce větve společně jako obor. Konkrétně návrhářvzor `@equals(actions('a').status, 'Skipped')` se zobrazí `else` jako akce. Pokud však nástroj detekuje nerozpoznatelné vzory, může nástroj vytvořit samostatné podmínky pro větev true i false. V případě potřeby můžete akce po upgradu přemapovat.

#### <a name="foreach-loop-with-condition"></a>"foreach" smyčka s podmínkou

V novém schématu můžete použít akci filtru k replikaci vzoru, který používá **For each** loop s jednou podmínkou pro jednu položku. Změna se však automaticky stane při upgradu. Podmínka se stane akce filtru, který se zobrazí před **Pro každou** smyčku, vrácení pouze pole položek, které odpovídají podmínce a předávání tohoto pole **pro každou** akci. Příklad viz [Smyčky a obory](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Značky prostředků

Po upgradu jsou odebrány značky prostředků, takže je nutné je obnovit pro upgradovaný pracovní postup.

## <a name="other-changes"></a>Další změny

### <a name="renamed-manual-trigger-to-request-trigger"></a>Přejmenovaný aktivační událost "ruční" na aktivační událost požadavku

Typ `manual` aktivační události byl zastarala `request` a `http`přejmenována na typ . Tato změna vytvoří větší konzistenci pro druh vzoru, který aktivační událost slouží k sestavení.

### <a name="new-filter-action"></a>Nová akce filtru

Chcete-li filtrovat velké pole dolů na `filter` menší sadu položek, nový typ přijme pole a podmínku, vyhodnotí podmínku pro každou položku a vrátí pole s položkami splňujícími podmínku.

### <a name="restrictions-for-foreach-and-until-actions"></a>Omezení pro akce "foreach" a "do"

Smyčka `foreach` `until` a je omezena na jednu akci.

### <a name="new-trackedproperties-for-actions"></a>Nové "trackedProperties" pro akce

Akce nyní může mít `trackedProperties`další vlastnost s `runAfter` názvem `type` , která je na stejné úrovni vlastnosti a. Tento objekt určuje určité vstupy akce nebo výstupy, které chcete zahrnout do telemetrie Diagnostika Azure, vyzařované jako součást pracovního postupu. Například:

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

* [Vytváření definic pracovních postupů pro aplikace logiky](../logic-apps/logic-apps-author-definitions.md)
* [Automatizace nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
