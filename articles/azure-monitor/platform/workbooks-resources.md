---
title: Parametry prostředků Azure Monitorch sešitů
description: Naučte se používat parametry prostředků k povolení vybírání prostředků v sešitech. Pomocí parametrů prostředků nastavte obor, ze kterého se mají data získat.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 27e98ae51cbea654ca0f06979b289a497a5aedf3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143510"
---
# <a name="workbook-resource-parameters"></a>Parametry prostředku sešitu

Parametry prostředků umožňují vyzvednutí prostředků v sešitech. To je užitečné při nastavení rozsahu, ze kterého se mají data získat. Příklad umožňuje uživatelům vybrat sadu virtuálních počítačů, které grafy později použijí při prezentování dat.

Hodnoty z výběrů prostředků můžou pocházet z kontextu sešitu, statického seznamu nebo dotazů na Azure Resource Graph.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Vytvoření parametru prostředku (zdroje sešitu)
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `Applications`
    2. Typ parametru: `Resource picker`
    3. Požadovanou `checked`
    4. Povolí vícenásobné výběry: `checked`
5. Získat data z: `Workbook Resources`
6. Zahrnout pouze typy prostředků: `Application Insights`
7. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

![Obrázek znázorňující vytvoření parametru prostředku pomocí prostředků sešitu](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Vytvoření parametru prostředku (Azure Resource Graph)
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `Applications`
    2. Typ parametru: `Resource picker`
    3. Požadovanou `checked`
    4. Povolí vícenásobné výběry: `checked`
5. Získat data z: `Query`
    1. Typ dotazu: `Azure Resource Graph`
    2. Odběru `Use default subscriptions`
    3. V ovládacím prvku dotaz přidejte tento fragment kódu.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

![Obrázek znázorňující vytvoření parametru prostředku pomocí Azure Resource graphu](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Graf prostředků Azure ještě není k dispozici ve všech cloudech. Pokud zvolíte tento přístup, ujistěte se, že je podporovaný v cílovém cloudu.

[Dokumentace ke službě Azure Resource Graph](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Vytvoření parametru prostředku (seznam JSON)
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `Applications`
    2. Typ parametru: `Resource picker`
    3. Požadovanou `checked`
    4. Povolí vícenásobné výběry: `checked`
5. Získat data z: `JSON`
    1. V ovládacím prvku Content přidejte tento fragment kódu JSON.
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Stiskněte tlačítko modrá _aktualizace_ .
6. Volitelně můžete nastavit `Include only resource types` na _Application Insights_
7. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

## <a name="referencing-a-resource-parameter"></a>Odkazování na parametr prostředku
1. Přidejte do sešitu ovládací prvek dotazu a vyberte prostředek Application Insights.
2. Pomocí rozevíracího seznamu _Application Insights_ navažte parametr k ovládacímu prvku. Tím se nastaví rozsah dotazu na prostředky vrácené parametrem v době běhu.
4. Do ovládacího prvku KQL přidejte tento fragment kódu.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Spusťte dotaz, aby se zobrazily výsledky. 

![Obrázek znázorňující parametr prostředku, na který se odkazuje v ovládacím prvku dotazu](./media/workbooks-resources/resource-reference.png)

> Tento přístup se dá použít k vázání prostředků na jiné ovládací prvky, jako jsou metriky.

## <a name="resource-parameter-options"></a>Možnosti parametrů prostředku
| Parametr | Vysvětlení | Příklad |
| ------------- |:-------------|:-------------|
| `{Applications}` | Vybrané ID prostředku | _/Subscriptions/<dílčí identifikátor>/resourceGroups/<Resource-Group>/Providers/<Resource-Type>/acmeauthentication_ |
| `{Applications:label}` | Popisek vybraného prostředku | `acmefrontend` |
| `{Applications:value}` | Hodnota vybraného prostředku | _'/Subscriptions/<sub-ID>/resourceGroups/<Resource-Group>/Providers/<Resource-Type>/acmeauthentication '_ |
| `{Applications:name}` | Název vybraného prostředku | `acmefrontend` |
| `{Applications:resourceGroup}` | Skupina prostředků vybraného prostředku | `acmegroup` |
| `{Applications:resourceType}` | Typ vybraného prostředku | _Microsoft. Insights/Components_ |
| `{Applications:subscription}` | Předplatné vybraného prostředku |  |
| `{Applications:grid}` | Mřížka znázorňující vlastnosti prostředku. Užitečné pro vykreslování v textovém bloku při ladění  |  |

## <a name="next-steps"></a>Další kroky

* [Začínáme](./workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.