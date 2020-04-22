---
title: Parametry textu sešitů Azure Monitor
description: Zjednodušte složité vytváření sestav pomocí předem sestavených a vlastních parametrizovaných sešitů. Přečtěte si další informace o parametrech textu sešitu.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687326"
---
# <a name="workbook-text-parameters"></a>Parametry textu sešitu

Parametry textového pole poskytují jednoduchý způsob shromažďování textového vstupu od uživatelů sešitu. Používají se v případě, že není praktické použít rozevírací nabídku ke shromažďování vstupu (například libovolný práh nebo obecné filtry). Sešity umožňují autorům získat výchozí hodnotu textového pole z dotazu. To umožňuje zajímavé scénáře, jako je nastavení výchozí prahové hodnoty na základě p95 metriky.

Běžné použití textových polí je jako interní proměnné používané jinými ovládacími prvky sešitu. To se provádí využitím dotazu pro výchozí hodnoty a zneviditelně v režimu čtení. Uživatel může například chtít prahovou hodnotu, která má pocházet ze vzorce (nikoli z uživatele) a poté tuto prahovou hodnotu použít v následujících dotazech.

## <a name="creating-a-text-parameter"></a>Vytvoření textového parametru
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`SlowRequestThreshold`
    2. Typ parametru:`Text`
    3. Požadované:`checked`
    4. Získat výchozí hodnotu z dotazu:`unchecked`
5. Chcete-li vytvořit parametr, zvolte "Uložit".

    ![Obrázek znázorňující vytvoření textového parametru](./media/workbooks-text/text-create.png)

Takto bude sešit vypadat v režimu čtení.

![Obrázek znázorňující textový parametr v režimu čtení](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Odkazování na textový parametr
1. Přidejte do sešitu ovládací prvek `Add query` dotazu tak, že vyberete modrý odkaz a vyberete prostředek Application Insights.
2. Do pole KQL přidejte tento úryvek:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Pomocí parametru text s hodnotou 500 spolu s ovládacím prvkem dotazu efektivně spustíte následující dotaz:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Spuštění dotazu pro zobrazení výsledků

    ![Obrázek znázorňující textový parametr odkazovaný v KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> Ve výše uvedeném příkladu `{SlowRequestThreshold}` představuje hodnotu celé číslo. Pokud jste byli dotazování `{ComputerName}` na řetězec, jako byste museli upravit `"{ComputerName}"` kusto dotaz přidat uvozovky v pořadí pro pole parametru přijmout vstup bez uvozovek.

## <a name="setting-default-values"></a>Nastavení výchozích hodnot
1. Začněte s prázdným sešitem v režimu úprav.
2. Z odkazů v sešitu zvolte _Přidat parametry._
3. Klikněte na modré tlačítko _Přidat parametr._
4. V novém podokně parametrů, které se objeví, zadejte:
    1. Název parametru:`SlowRequestThreshold`
    2. Typ parametru:`Text`
    3. Požadované:`checked`
    4. Získat výchozí hodnotu z dotazu:`checked`
5. Do pole KQL přidejte tento úryvek:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Tento dotaz nastaví výchozí hodnotu textového pole na 95.
6. Spuštění dotazu zobrazíte výsledek
7. Chcete-li vytvořit parametr, zvolte "Uložit".

    ![Obrázek znázorňující textový parametr s výchozí hodnotou z KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Zatímco tento příklad dotazuje data Application Insights, přístup lze použít pro libovolný zdroj dat založený na protokolu – Log Analytics, Azure Resource Graph atd.

## <a name="next-steps"></a>Další kroky

* [Začínáte](workbooks-visualizations.md) se učit další informace o sešitech, mnoho bohatých možností vizualizací.
* [Řízení](workbooks-access-control.md) a sdílení přístupu k prostředkům sešitu
