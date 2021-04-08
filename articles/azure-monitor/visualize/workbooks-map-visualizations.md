---
title: Azure Monitor vizualizace map sešitu
description: Přečtěte si o Azure Monitor vizualizacích map sešitu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100610167"
---
# <a name="map-visualization"></a>Vizualizace mapy

Vizualizace mapy usnadňuje problémy ukazující na PIN kódu v určitých oblastech a zobrazuje agregovaná zobrazení dat monitorování na nejvyšší úrovni tím, že poskytuje možnost agregace všech dat mapovaných na každé umístění/zemi nebo oblast.

Níže uvedený snímek obrazovky ukazuje celkové transakce a koncovou latenci pro různé účty úložiště. Tato velikost je určena celkovým počtem transakcí a metrikami barev pod mapou zobrazuje koncovou latenci. Po prvním sledování je počet transakcí v oblasti **západní USA** malý v porovnání s **východní USA** oblastí, ale koncová latence pro **Západní USA** oblast je vyšší než **východní USA** oblast. To poskytuje počáteční přehled o tom, že je něco amiss **západní USA**.

![Snímek obrazovky s mapou umístění Azure](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Přidání mapy

Mapu je možné vizuálně rozlišit, pokud má podkladová data a metriky informace o zeměpisné šířce, informace o prostředcích Azure, informace o umístění Azure nebo zemi/oblast, název nebo kód země/oblasti.

### <a name="using-azure-location"></a>Použití umístění Azure

1. Přepněte sešit do režimu úprav výběrem položky na panelu nástrojů pro **Úpravy** .
2. Vyberte **Přidat** a pak *Přidat dotaz*.
3. Změňte *zdroj dat* tak `Azure Resource Graph` , aby vybral jakékoli předplatné, které má účet úložiště.
4. Zadejte následující dotaz pro analýzu a **dotaz SELECT Run**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Nastavte *Velikost* na `Large` .
6. Nastavte *vizualizaci* na `Map` .
7. Všechna nastavení budou automaticky vyplněna. Pro vlastní nastavení vyberte tlačítko **nastavení mapy** a otevřete tak podokno nastavení.
8. Níže je obrázek vizualizace mapy, která zobrazuje účty úložiště pro jednotlivé oblasti Azure pro vybrané předplatné.

![Snímek obrazovky s mapou umístění Azure s výše uvedeným dotazem](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Pomocí prostředku Azure

1. Přepněte sešit do režimu úprav výběrem položky na panelu nástrojů pro **Úpravy** .
2. Vyberte **Přidat** a pak *Přidat metriku*.
3. Použijte předplatné, které má účty úložiště.
4. Změňte *typ prostředku* na `storage account` a v *prostředku* vyberte víc účtů úložiště.
5. Vyberte **Přidat metriku** a přidejte metriku transakce.
    1. Obor názvů: `Account`
    2. Metriky `Transactions`
    3. Agregovat `Sum`
    
    ![Snímek obrazovky metriky transakcí](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Vyberte **Přidat metriku** a přidejte metriku latence e2e.
    1. Obor názvů: `Account`
    1. Metriky `Success E2E Latency`
    1. Agregovat `Average`
    
    ![Snímek obrazovky s kompletní latencí – metrika na konci](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Nastavte *Velikost* na `Large` .
1. Nastavte velikost *vizualizace* na `Map` .
1. V **Nastavení mapování** nastavte následující nastavení:
    1. Informace o umístění pomocí: `Azure Resource`
    1. Pole prostředku Azure: `Name`
    1. Velikost podle: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Agregace pro umístění: `Sum of values`
    1. Typ vybarvení: `Heatmap`
    1. Barva podle: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregace pro barvu: `Sum of values`
    1. Barevná paleta: `Green to Red`
    1. Minimální hodnota: `0`
    1. Hodnota metriky: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregovat jiné metriky podle: `Sum of values`
    1. Vyberte pole **vlastní formátování** .
    1. Jednotce `Milliseconds`
    1. Řez `Decimal`
    1. Maximální počet desetinných míst: `2`

### <a name="using-countryregion"></a>Použití země/oblasti

1. Přepněte sešit do režimu úprav výběrem položky na panelu nástrojů pro **Úpravy** .
2. Vyberte **Přidat* a pak *Přidat dotaz*.
3. Změňte *zdroj dat* na `Log` .
4. Vyberte *typ prostředku* jako `Application Insights` a pak vyberte libovolný prostředek Application Insights, který má pageViews data.
5. Pomocí Editoru dotazů zadejte KQL pro analýzu a vyberte **Spustit dotaz**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Nastavte hodnoty velikosti na `Large` .
7. Nastavte vizualizaci na `Map` .
8. Všechna nastavení budou automaticky vyplněna. Pro vlastní nastavení vyberte **mapování nastavení**.

### <a name="using-latitudelocation"></a>Používání zeměpisné šířky/místa

1. Přepněte sešit do režimu úprav výběrem položky na panelu nástrojů pro **Úpravy** .
2. Vyberte **Přidat* a pak *Přidat dotaz*.
3. Změňte *zdroj dat* na `JSON` .
1. V editoru dotazů zadejte níže data JSON a vyberte **Spustit dotaz**.
1. Nastavte hodnoty *velikosti* na `Large` .
1. Nastavte *vizualizaci* na `Map` .
1. V **Nastavení mapování** v části Nastavení metriky nastavte *popisek metriky* na `displayName` možnost **Uložit a zavřít**.

Následující vizualizace mapy ukazuje uživatele pro každou zeměpisnou šířku a zeměpisnou polohu s vybraným popiskem pro metriky.

![Snímek obrazovky vizualizace mapy, která zobrazuje uživatele pro každou zeměpisnou šířku a zeměpisnou polohu s vybraným popiskem pro metriky](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Nastavení mapy

### <a name="layout-settings"></a>Nastavení rozložení

| Nastavení | Vysvětlení |
|:-------------|:-------------|
| `Location info using` | Vyberte způsob, jak získat umístění položek zobrazených na mapě. <br> **Zeměpisná šířka/délka**: tuto možnost vyberte, pokud jsou sloupce s informacemi o zeměpisné šířce a délce. Každý řádek s daty o zeměpisné šířce a délce se zobrazí jako odlišná položka na mapě. <br> **Umístění Azure**: tuto možnost vyberte, pokud se nachází sloupec, který obsahuje informace o umístění Azure (eastus, westeurope, centralindia atd.). Zadejte tento sloupec a načtěte odpovídající zeměpisnou šířku a délku pro každé umístění v Azure, seskupte stejné řádky umístění (na základě agregace) a zobrazte umístění na mapě. <br> **Prostředek Azure**: tuto možnost vyberte, pokud se nachází sloupec, který obsahuje informace o prostředcích Azure (účet úložiště, účet cosmosdb atd.). Zadejte tento sloupec, který načte odpovídající zeměpisnou šířku a délku pro každý prostředek Azure, seskupovat stejné umístění (umístění Azure) (na základě agregace) společně s cílem zobrazit umístění na mapě. <br> **Země/oblast**: tuto možnost vyberte, pokud se nachází sloupec, který obsahuje informace o názvu nebo kódu země/oblasti (US, USA, in, Indie, CN, Čína). Zadejte tento sloupec a načtěte odpovídající zeměpisnou šířku a délku pro každou zemi/oblast/kód a seskupte řádky se stejným Country-Regionm názvem země/oblasti pro zobrazení umístění na mapě. Název země a kód země se na mapě seskupují dohromady jako jedna entita.
| `Latitude/Longitude` | Tyto dvě možnosti budou viditelné, pokud je hodnota pole informace o poloze: Zeměpisná šířka/délka. Vyberte sloupec, který má zeměpisnou šířku v poli Zeměpisná šířka a zeměpisná délka v poli Zeměpisná délka. |
| `Azure location field` | Tato možnost bude viditelná, pokud je hodnota pole informace o poloze: umístění Azure. Vyberte sloupec s informacemi o umístění Azure. |
| `Azure resource field` | Tato možnost bude viditelná, pokud je hodnota pole informace o poloze: prostředek Azure. Vyberte sloupec, který informace o prostředcích Azure. |
| `Country/Region field` | Tato možnost bude viditelná, pokud je hodnota pole informace o poloze: země nebo oblast. Vyberte sloupec, který má informace o zemi nebo oblasti. |
| `Size by` | Tato možnost určuje velikost položek zobrazených na mapě. Velikost závisí na hodnotě ve sloupci zadaném uživatelem. Poloměr kružnice v současné době je přímo úměrný druhému odmocnině hodnoty sloupce. Pokud ' None... ' je vybráno, všechny kroužky budou zobrazovat výchozí velikost oblasti.|
| `Aggregation for location` | Toto pole určuje, jak se má agregovat **velikost podle** sloupců, které mají stejné umístění Azure/prostředek Azure/oblast země. |
| `Minimum region size` | Toto pole určuje, jaký je minimální poloměr položky zobrazené na mapě. Tato hodnota se používá v případě, že existuje značný rozdíl mezi hodnotami velikosti podle sloupce, takže menší položky jsou špatně viditelné na mapě. |
| `Maximum region size` | Toto pole určuje, jaký je maximální poloměr položky zobrazené na mapě. Tato hodnota se používá, pokud jsou hodnoty velikosti podle sloupce extrémně velké a jsou pokrývá značnou oblast mapy.|
| `Default region size` | Toto pole určuje, jaký je výchozí poloměr položky zobrazené na mapě. Výchozí poloměr se používá v případě, že velikost podle sloupce je None... nebo je hodnota 0.|
| `Minimum value` | Minimální hodnota, která se používá k výpočtu velikosti oblasti Není-li tento parametr zadán, bude minimální hodnota po agregaci nejmenší. |
| `Maximum value` | Maximální hodnota, která se používá k výpočtu velikosti oblasti Není-li tento parametr zadán, bude maximální hodnota po agregaci největší.|
| `Opacity of items on Map` | Toto pole určuje, jak transparentní jsou položky zobrazené na mapě. Neprůhlednost: 1 znamená žádná průhlednost, kde neprůhlednost je 0 znamená, že položky nebudou na mapě viditelné. Pokud je na mapě příliš mnoho položek, neprůhlednost může být nastavena na nízkou hodnotu, aby se zobrazily všechny překrývající se položky.|

### <a name="color-settings"></a>Nastavení barev

| Typ barevného zbarvení | Vysvětlení |
|:------------- |:-------------|
| `None` | Všechny uzly mají stejnou barvu. |
| `Thresholds` | V tomto typu jsou barvy buňky nastavené podle mezních pravidel (například _CPU > 90% => Red, 60% > CPU > 90% => žlutě, CPU < 60% => zelenou_). <ul><li> **Color by**: hodnota tohoto sloupce bude použita logikou/heatmapu logikou.</li> <li>**Agregace pro barvy**: Toto pole určuje, jak agregovat **barvy podle** sloupců, které mají stejné umístění Azure/prostředek Azure/oblast země. </li> <ul> |
| `Heatmap` | V tomto typu jsou buňky barevné na základě palety barev a podle pole Color by. To má také stejnou **barvu podle** a **agregace pro možnosti barev** jako prahové hodnoty. |

### <a name="metric-settings"></a>Nastavení metriky
| Nastavení | Vysvětlení |
|:------------- |:-------------|
| `Metric Label` | Tato možnost bude viditelná, pokud je hodnota pole informace o poloze: Zeměpisná šířka/délka. Pomocí této funkce může uživatel vybrat popisek, který se zobrazí pro metriky zobrazené pod mapou. |
| `Metric Value` | Toto pole určuje hodnotu metriky, která se má zobrazit pod mapou. |
| `Create 'Others' group after` | Toto pole určuje limit před vytvořením skupiny "ostatní". |
| `Aggregate 'Others' metrics by` | Toto pole určuje agregaci, která se používá pro skupinu "ostatní", pokud je zobrazena. |
| `Custom formatting` | Pomocí tohoto pole můžete nastavit možnosti jednotek, stylů a formátování pro číselné hodnoty. To je stejné jako [vlastní formátování mřížky](../visualize/workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Další kroky

- Naučte se vytvářet [vizualizace v hřebenu v sešitech](../visualize/workbooks-honey-comb.md).