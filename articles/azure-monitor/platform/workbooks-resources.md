---
title: Azure Monitor parametry prostředků sešitů
description: Zjednodušení složitých sestav pomocí předem sestavených a vlastních parametrizovaných sešitů
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658098"
---
# <a name="workbook-resource-parameters"></a>Parametry prostředků sešitu

Parametry zdroje umožňují výběr zdrojů v sešitech. To je užitečné při nastavování oboru, ze kterého chcete získat data. Příkladem je povolení uživatelům vybrat sadu virtuálních počítačů, které grafy později použije při prezentaci dat.

Hodnoty z výběru prostředků mohou pocházet z kontextu sešitu, statického seznamu nebo z dotazů Azure Resource Graph.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Vytvoření parametru zdroje (zdroje sešitu)
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`Applications`
    2. Typ parametru:`Resource picker`
    3. Požadované:`checked`
    4. Povolit více výběrů:`checked`
5. Získat data z:`Workbook Resources`
6. Zahrnout pouze typy prostředků:`Application Insights`
7. Chcete-li vytvořit parametr, zvolte "Uložit".

![Obrázek znázorňující vytvoření parametru zdroje pomocí zdrojů sešitu](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Vytvoření parametru prostředku (Azure Resource Graph)
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`Applications`
    2. Typ parametru:`Resource picker`
    3. Požadované:`checked`
    4. Povolit více výběrů:`checked`
5. Získat data z:`Query`
    1. Typ dotazu:`Azure Resource Graph`
    2. Odběry:`Use default subscriptions`
    3. V ovládacím prvku dotazu přidejte tento úryvek.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Chcete-li vytvořit parametr, zvolte "Uložit".

![Obrázek znázorňující vytvoření parametru prostředku pomocí Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph ještě není k dispozici ve všech cloudech. Pokud zvolíte tento přístup, ujistěte se, že je podporována v cílovém cloudu.

[Dokumentace ke službě Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Vytvoření parametru prostředku (seznam JSON)
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`Applications`
    2. Typ parametru:`Resource picker`
    3. Požadované:`checked`
    4. Povolit více výběrů:`checked`
5. Získat data z:`JSON`
    1. V ovládacím prvku content přidejte tento fragment json.
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Stiskněte modré tlačítko _Aktualizovat._
6. Volitelně nastavte `Include only resource types` na _Přehledy aplikací_
7. Chcete-li vytvořit parametr, zvolte "Uložit".

## <a name="referencing-a-resource-parameter"></a>Odkazování na parametr prostředku
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Pomocí rozevíracího přehledu _aplikace_ svázat parametr s ovládacím prvkem. Tím nastaví rozsah dotazu na prostředky vrácené parametrem v době běhu.
4. V ovládacím prvku KQL přidejte tento úryvek
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Spusťte dotaz, abyste viděli výsledky. 

![Obrázek znázorňující parametr prostředku odkazovaný v ovládacím prvku dotazu](./media/workbooks-resources/resource-reference.png)

> Tento přístup lze vytvořit vazbu prostředky na jiné ovládací prvky, jako jsou metriky.

## <a name="resource-parameter-options"></a>Možnosti parametru zdroje
| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `{Applications}` | ID vybraného zdroje | _/subscriptions/<sub-id>/resourceGroups/<>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | Popisek vybraného zdroje | `acmefrontend` |
| `{Applications:value}` | Hodnota vybraného zdroje | _"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication"_ |
| `{Applications:name}` | Název vybraného zdroje | `acmefrontend` |
| `{Applications:resourceGroup}` | Skupina prostředků vybraného zdroje | `acmegroup` |
| `{Applications:resourceType}` | Typ vybraného zdroje | _microsoft.insights/components_ |
| `{Applications:subscription}` | Předplatné vybraného prostředku |  |
| `{Applications:grid}` | Mřížka zobrazující vlastnosti prostředku. Užitečné vykreslení v textovém bloku při ladění  |  |

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
