---
title: Textové parametry sešitů v Azure Monitor
description: Zjednodušte složité vytváření sestav pomocí předem sestavených a vlastních parametrizovaných sešitů. Přečtěte si další informace o textových parametrech sešitu.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725522"
---
# <a name="workbook-text-parameters"></a>Textové parametry sešitu

Parametry TextBox poskytují jednoduchý způsob, jak shromažďovat textový vstup od uživatelů sešitu. Používají se, pokud není praktické použít rozevírací seznam ke shromáždění vstupu (například libovolné prahové hodnoty nebo obecné filtry). Sešity umožňují autorům získat výchozí hodnotu TextBox z dotazu. To umožňuje zajímavým scénářům, jako je nastavení výchozí prahové hodnoty na základě p95 metriky.

Běžné použití textových polí je jako vnitřní proměnné používané jinými ovládacími prvky sešitu. K tomu je potřeba využít dotaz na výchozí hodnoty a ovládací prvek vstupu není v režimu pro čtení viditelný. Uživatel může například požadovat, aby prahová hodnota pocházela ze vzorce (nikoli uživatele) a pak používala prahovou hodnotu v následujících dotazech.

## <a name="creating-a-text-parameter"></a>Vytvoření textového parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `SlowRequestThreshold`
    2. Typ parametru: `Text`
    3. Požadovanou `checked`
    4. Získat výchozí hodnotu z dotazu: `unchecked`
5. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

    ![Obrázek znázorňující vytvoření textového parametru](./media/workbooks-text/text-create.png)

V takovém případě bude sešit vypadat jako v režimu čtení.

![Obrázek ukazující textový parametr v režimu čtení](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Odkazování na textový parametr
1. Přidejte do sešitu ovládací prvek dotazu tak, že vyberete modrý `Add query` odkaz a vyberete prostředek Application Insights.
2. Do pole KQL přidejte tento fragment kódu:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Pomocí parametru text s hodnotou 500 společně s ovládacím prvkem dotazu můžete efektivně spustit následující dotaz:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Spusťte dotaz, aby se zobrazily výsledky.

    ![Obrázek znázorňující parametr textu odkazovaný v KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> V předchozím příkladu `{SlowRequestThreshold}` představuje celočíselnou hodnotu. Pokud jste se dotazem na řetězec, například byste `{ComputerName}` museli změnit dotaz Kusto, aby se přidaly uvozovky, aby `"{ComputerName}"` pole parametru přijímalo vstup bez uvozovek.

## <a name="setting-default-values"></a>Nastavení výchozích hodnot
1. Začněte s prázdným sešitem v režimu úprav.
2. Vyberte možnost _přidat parametry_ z odkazů v rámci sešitu.
3. Klikněte na modré tlačítko _Přidat parametr_ .
4. V podokně nového parametru, které se objeví, zadejte:
    1. Název parametru: `SlowRequestThreshold`
    2. Typ parametru: `Text`
    3. Požadovanou `checked`
    4. Získat výchozí hodnotu z dotazu: `checked`
5. Do pole KQL přidejte tento fragment kódu:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Tento dotaz nastaví výchozí hodnotu textového pole na dobu 95. percentilu pro všechny požadavky v aplikaci.
6. Spustit dotaz pro zobrazení výsledku
7. Pokud chcete vytvořit parametr, klikněte na tlačítko Uložit na panelu nástrojů.

    ![Obrázek zobrazující textový parametr s výchozí hodnotou z KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> I když se tento příklad dotazuje Application Insights dat, můžete použít přístup k libovolnému zdroji dat založenému na protokolu, Log Analytics, grafu prostředků Azure atd.

## <a name="next-steps"></a>Další kroky

* [Začínáme](./workbooks-overview.md#visualizations) se dozvědět více o seznámcích s mnoha různými možnostmi vizualizací.
* [Řízení](./workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu.