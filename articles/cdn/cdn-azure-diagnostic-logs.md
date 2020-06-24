---
title: Diagnostické protokoly Azure | Microsoft Docs
description: Zákazník může povolit analýzu protokolů pro Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 2c432b28250dca382f69a992de73d633b5ea45b8
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84883992"
---
# <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Pomocí diagnostických protokolů Azure můžete zobrazit základní analýzy a uložit je do jednoho nebo více cílů, včetně:

 - Účet služby Azure Storage
 - Azure Event Hubs
 - [Pracovní prostor služby Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Tato funkce je k dispozici na koncových bodech CDN pro všechny cenové úrovně. 

Protokoly diagnostiky Azure umožňují exportovat základní metriky využití z vašeho koncového bodu CDN do nejrůznějších zdrojů, abyste je mohli spotřebovávat vlastním způsobem. Například můžete provést následující typy exportu dat:

- Exportujte data do služby Blob Storage, exportujte je do sdíleného svazku clusteru a generujte grafy v Excelu.
- Exportujte data Event Hubs a korelujte s daty z jiných služeb Azure.
- Export dat do Azure Monitor protokolů a zobrazení dat ve vlastním pracovním prostoru Log Analytics

Následující diagram znázorňuje typické zobrazení dat ve službě CDN Core Analytics.

![portál – diagnostické protokoly](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Obrázek 1 – zobrazení Core Analytics pro CDN*

Další informace o diagnostických protokolech najdete v tématu [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Povolení protokolování prostřednictvím webu Azure Portal

Postupujte podle těchto kroků a povolte protokolování pomocí sítě CDN Core Analytics:

Přihlaste se k webu [Azure Portal](https://portal.azure.com). Pokud jste ještě nepovolili CDN pro svůj pracovní postup, vytvořte před pokračováním [Azure CDN profil a koncový bod](cdn-create-new-endpoint.md) .

1. V Azure Portal přejděte do **profilu CDN**.

2. V Azure Portal vyhledejte profil CDN nebo ho vyberte na řídicím panelu. Pak vyberte koncový bod CDN, pro který chcete povolit diagnostické protokoly.

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. V části monitorování vyberte **protokoly diagnostiky** .

   Zobrazí se stránka **diagnostické protokoly** .

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Povolit protokolování pomocí Azure Storage

Pokud chcete použít účet úložiště pro ukládání protokolů, postupujte podle těchto kroků:
    
1. Jako **název**zadejte název nastavení diagnostického protokolu.
 
2. Vyberte možnost **archivovat do účtu úložiště**a pak vyberte **CoreAnalytics**. 

2. V poli **Doba uchování (dny)** vyberte počet dnů uchování. Uchovávání nulových dnů ukládá protokoly po neomezenou dobu. 

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Vyberte **účet úložiště**.

    Zobrazí se stránka **Vyberte účet úložiště** .

4. V rozevíracím seznamu vyberte účet úložiště a pak vyberte **OK**.

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po dokončení vytváření nastavení diagnostického protokolu vyberte **Uložit**.

### <a name="logging-with-azure-monitor"></a>Protokolování s využitím Azure Monitoru

Chcete-li použít Azure Monitor k uložení protokolů, postupujte podle následujících kroků:

1. Na stránce **diagnostické protokoly** vyberte **Odeslat do Log Analytics**. 

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Vyberte **Konfigurovat** a nakonfigurujte Azure monitor protokolování. 

   Zobrazí se stránka **log Analyticsé pracovní prostory** .

    >[!NOTE] 
    >Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Vyberte **vytvořit nový pracovní prostor**.

    Zobrazí se stránka **Log Analytics pracovní prostor** .

    >[!NOTE] 
    >Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/07_Create-new.png)

4. Pro **Log Analytics pracovní prostor**zadejte název pracovního prostoru Log Analytics. Název Log Analytics pracovního prostoru musí být jedinečný a musí obsahovat jenom písmena, číslice a spojovníky. mezery a podtržítka nejsou povolena. 

5. V poli **předplatné**vyberte existující předplatné z rozevíracího seznamu. 

6. V případě **skupiny prostředků**vytvořte novou skupinu prostředků nebo vyberte některou z existujících.

7. V poli **umístění**vyberte umístění ze seznamu.

8. Pokud chcete uložit konfiguraci protokolu na řídicí panel, vyberte **Připnout na řídicí panel** . 

9. Kliknutím na **tlačítko OK** dokončete konfiguraci.

10. Po vytvoření pracovního prostoru budete vráceni na stránku **diagnostické protokoly** . Potvrďte název nového pracovního prostoru Log Analytics.

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Vyberte **CoreAnalytics**a pak vyberte **Save (Uložit**).

12. Pokud chcete zobrazit nový pracovní prostor Log Analytics, vyberte **základní analýzy** ze stránky koncového bodu CDN.

    ![portál – diagnostické protokoly](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Váš pracovní prostor Log Analytics je teď připravený k protokolování dat. Aby bylo možné tato data využívat, je nutné použít [řešení protokolu Azure monitor](#consuming-diagnostics-logs-from-a-log-analytics-workspace), které je popsáno dále v tomto článku.

Další informace o zpoždění dat protokolu najdete v tématu [zpoždění dat protokolu](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Povolení protokolování prostřednictvím PowerShellu

Následující příklad ukazuje, jak povolit diagnostické protokoly pomocí rutin Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Povolení diagnostických protokolů v účtu úložiště

1. Přihlaste se a vyberte předplatné:

    Connect – AzAccount 

    SELECT-AzureSubscription-SubscriptionId 

2. Pokud chcete povolit diagnostické protokoly v účtu úložiště, zadejte tento příkaz:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Pokud chcete povolit diagnostické protokoly v pracovním prostoru Log Analytics, zadejte tento příkaz:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Využívání diagnostických protokolů ze služby Azure Storage
Tato část popisuje schéma služby CDN Core Analytics, jak je uspořádána do účtu služby Azure Storage, a obsahuje ukázkový kód ke stažení protokolů v souboru CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Použití Průzkumník služby Microsoft Azure Storage
Než budete moct získat přístup k základním analytickým datům z účtu služby Azure Storage, budete nejdřív potřebovat nástroj pro přístup k obsahu v účtu úložiště. I když je na trhu k dispozici několik nástrojů, je ten, který doporučujeme, Průzkumník služby Microsoft Azure Storage. Pokud si chcete nástroj stáhnout, přečtěte si téma [Průzkumník služby Azure Storage](https://storageexplorer.com/). Po stažení a instalaci softwaru ho nakonfigurujte tak, aby používal stejný účet služby Azure Storage, který byl nakonfigurovaný jako cíl, do protokolů pro diagnostiku CDN.

1.  Otevřít **Průzkumník služby Microsoft Azure Storage**
2.  Vyhledání účtu úložiště
3.  Rozbalte uzel **kontejnery objektů BLOB** pod tímto účtem úložiště.
4.  Vyberte kontejner s názvem *Insights-logs-coreanalytics*.
5.  Výsledky se zobrazí v pravém podokně, počínaje první úrovní, jako *ResourceID =*. Pokračujte v výběru všech úrovní, dokud soubor nenajdete *PT1H.js*. Vysvětlení cesty najdete v tématu [Formát cesty objektu BLOB](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Každý objekt BLOB *PT1H.jsv* souboru představuje protokoly analýz na jednu hodinu pro konkrétní koncový bod CDN nebo jeho vlastní doménu.
7.  Schéma obsahu tohoto souboru JSON je popsané v části schéma základních protokolů analýzy.


#### <a name="blob-path-format"></a>Formát cesty objektu BLOB

Základní protokoly analýzy se generují každou hodinu a data se shromažďují a ukládají v jednom objektu blob Azure jako datová část JSON. Vzhledem k tomu, že Nástroj Průzkumník úložišť interpretuje "/" jako oddělovač adresářů a zobrazuje hierarchii, cesta k objektu blob Azure se zobrazí jako v případě, že existuje hierarchická struktura a představuje název objektu BLOB. Název objektu blob následuje po následující konvenci pojmenování:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Popis polí:**

|Hodnota|Description|
|-------|---------|
|ID předplatného    |ID předplatného Azure ve formátu identifikátoru GUID|
|Název skupiny prostředků |Název skupiny prostředků, do které patří prostředky CDN.|
|Profile Name (Název profilu) |Název profilu CDN|
|Název koncového bodu |Název koncového bodu CDN|
|Year|  Znázornění čtyřmístného roku, například 2017|
|Month| Vyjádření čísla měsíce se dvěma číslicemi. 01 = leden... 12 = prosinec|
|Den|   Vyjádření dne v měsíci se dvěma číslicemi|
|PT1H.jsna| Skutečný soubor JSON, ve kterém jsou uložená data analýzy|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Export základních analytických dat do souboru CSV

Pro usnadnění přístupu ke Core Analytics je k dispozici vzorový kód pro nástroj. Tento nástroj umožňuje stažení souborů JSON do samostatného formátu souboru odděleného čárkami, který se dá použít k vytváření grafů nebo jiných agregací.

Tady je postup, jak můžete použít nástroj:

1.  Navštivte odkaz na GitHub:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Stáhněte si kód.
3.  Postupujte podle pokynů pro zkompilování a konfiguraci.
4.  Spusťte nástroj.
5.  Výsledný soubor CSV zobrazuje analytická data v jednoduché ploché hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Využívání diagnostických protokolů z pracovního prostoru služby Log Analytics
Azure Monitor je služba Azure, která monitoruje cloudové a místní prostředí a udržuje jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji. 

Pokud chcete použít Azure Monitor, musíte [Povolit protokolování](#enable-logging-with-azure-storage) do pracovního prostoru Azure Log Analytics, který je popsaný výše v tomto článku.

### <a name="using-the-log-analytics-workspace"></a>Používání pracovního prostoru Log Analytics

 Následující diagram znázorňuje architekturu vstupů a výstupů úložiště:

![Pracovní prostor služby Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Obrázek 3 – Log Analytics úložiště*

Pomocí řešení pro správu můžete zobrazit data různými způsoby. Řešení pro správu můžete získat z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Řešení monitorování můžete nainstalovat z webu Azure Marketplace tak, že v dolní části každého řešení kliknete na odkaz **získat nyní** .

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Přidání řešení pro monitorování CDN Azure Monitor

Pomocí následujících kroků přidejte řešení Azure Monitor Monitoring:

1.   Přihlaste se k Azure Portal pomocí svého předplatného Azure a přejít na řídicí panel.
    ![Řídicí panel Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na **nové** stránce v části **Marketplace**vyberte **monitorování a Správa**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na stránce **monitorování a Správa** vyberte **Zobrazit vše**.

    ![Zobrazit vše](./media/cdn-diagnostics-log/15_See-all.png)

4. Do vyhledávacího pole vyhledejte CDN.

    ![Zobrazit vše](./media/cdn-diagnostics-log/16_Search-for.png)

5. Vyberte **Azure CDN Core Analytics**. 

    ![Zobrazit vše](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po výběru **vytvořit**budete požádáni o vytvoření nového pracovního prostoru Log Analytics nebo použít existující. 

    ![Zobrazit vše](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Vyberte pracovní prostor, který jste vytvořili dříve. Pak budete muset přidat účet Automation.

    ![Zobrazit vše](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Následující obrazovka zobrazuje formulář účtu Automation, který musíte vyplnit. 

    ![Zobrazit vše](./media/cdn-diagnostics-log/20_Automation.png)

9. Po vytvoření účtu Automation jste připraveni přidat své řešení. Vyberte tlačítko **Vytvořit**.

    ![Zobrazit vše](./media/cdn-diagnostics-log/21_Ready.png)

10. Vaše řešení bylo teď přidané do vašeho pracovního prostoru. Vraťte se do řídicího panelu Azure Portal.

    ![Zobrazit vše](./media/cdn-diagnostics-log/22_Dashboard.png)

    Vyberte pracovní prostor Log Analytics, který jste vytvořili pro přechod do svého pracovního prostoru. 

11. Pokud chcete zobrazit nové řešení, vyberte dlaždici na **portálu OMS** .

    ![Zobrazit vše](./media/cdn-diagnostics-log/23_workspace.png)

12. Váš portál by teď měl vypadat jako na následující obrazovce:

    ![Zobrazit vše](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Vyberte jednu z dlaždic pro zobrazení několika zobrazení do vašich dat.

    ![Zobrazit vše](./media/cdn-diagnostics-log/25_Interior-view.png)

    Posunutím doleva nebo doprava můžete zobrazit další dlaždice reprezentující jednotlivá zobrazení do dat. 

    Výběrem jedné z dlaždic zobrazíte další podrobnosti o vašich datech.

     ![Zobrazit vše](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Nabídky a cenové úrovně

[Tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)můžete zobrazit nabídky a cenové úrovně řešení pro správu.

### <a name="customizing-views"></a>Přizpůsobení zobrazení

Zobrazení můžete přizpůsobit na data pomocí **návrháře zobrazení**. Chcete-li začít navrhovat, otevřete pracovní prostor Log Analytics a vyberte dlaždici **Zobrazit návrháře** .

![Návrhář zobrazení](./media/cdn-diagnostics-log/27_Designer.png)

Přetáhněte typy grafů a vyplňte podrobnosti dat, které chcete analyzovat.

![Návrhář zobrazení](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Zpoždění dat protokolu

Následující tabulka ukazuje zpoždění dat protokolu pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai**a **Azure CDN úrovně Standard/Premium z Verizon**.

Zpoždění dat protokolu Microsoft | Zpoždění dat protokolu Verizon | Zpoždění dat protokolu Akamai
--- | --- | ---
Zpožděné o 1 hodinu. | Zpožděné o 1 hodinu a může trvat až 2 hodiny, než se začne zobrazovat po dokončení šíření koncového bodu. | Zpoždění za 24 hodin; Pokud byl vytvořen před více než 24 hodinami, bude možné začít se objevovat až 2 hodiny. Pokud byl nedávno vytvořen, může trvat až 25 hodin, než se protokoly začnou zobrazovat.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy diagnostických protokolů pro CDN Core Analytics

Microsoft v současné době nabízí jenom základní protokoly analýzy, které obsahují metriky ukazující statistiku odpovědí HTTP a statistiky odchozího přenosu dat, které se zobrazují z položek POP a hran CDN.

### <a name="core-analytics-metrics-details"></a>Podrobnosti metriky Core Analytics
Následující tabulka obsahuje seznam metrik dostupných v základních protokolech analýzy pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Akamai**a **Azure CDN Standard/Premium z Verizon**. Ne všechny metriky jsou dostupné ze všech zprostředkovatelů, i když jsou tyto rozdíly minimální. Tabulka také zobrazuje, zda je daná metrika k dispozici od poskytovatele. Metriky jsou k dispozici pouze pro koncové body CDN, které na ně mají provoz.


|Metric                     | Popis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Celkový počet přístupů k žádosti v průběhu tohoto období. | Ano | Ano |Ano |
| RequestCountHttpStatus2xx | Počet všech požadavků, které vedly k 2xx kódu HTTP (například 200, 202). | Ano | Ano |Ano |
| RequestCountHttpStatus3xx | Počet všech požadavků, které vedly k 3xx kódu HTTP (například 300, 302). | Ano | Ano |Ano |
| RequestCountHttpStatus4xx | Počet všech požadavků, které vedly k 4xx kódu HTTP (například 400, 404). | Ano | Ano |Ano |
| RequestCountHttpStatus5xx | Počet všech požadavků, které vedly k 5xx kódu HTTP (například 500, 504). | Ano | Ano |Ano |
| RequestCountHttpStatusOthers | Počet všech ostatních kódů HTTP (mimo 2xx-5xx). | Ano | Ano |Ano |
| RequestCountHttpStatus200 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 200. | Ano | Ne  |Ano |
| RequestCountHttpStatus206 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 206. | Ano | Ne  |Ano |
| RequestCountHttpStatus302 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 302. | Ano | Ne  |Ano |
| RequestCountHttpStatus304 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 304. | Ano | Ne  |Ano |
| RequestCountHttpStatus404 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 404. | Ano | Ne  |Ano |
| RequestCountCacheHit | Počet všech požadavků, jejichž výsledkem byl úspěšný přístup do mezipaměti. Asset byl obsluhován přímo z místního klienta. | Ano | Ano | Ne  |
| RequestCountCacheMiss | Počet všech požadavků, které způsobily Neúspěšné přístupy do mezipaměti. Při neúspěšném ukládání do mezipaměti znamená, že se Asset nenajde na bodu POP, který je nejblíže klientovi, a proto byl načten ze zdroje. | Ano | Ano | Ne |
| RequestCountCacheNoCache | Počet všech požadavků na prostředek, jejichž ukládání do mezipaměti brání v důsledku konfigurace uživatele na hraničních zařízeních. | Ano | Ano | Ne |
| RequestCountCacheUncacheable | Počet všech požadavků na prostředky, které nemohou být ukládány do mezipaměti pomocí hlaviček pro řízení mezipaměti a vypršení platnosti assetu, což znamená, že by neměl být uložen do mezipaměti v případě POP nebo pomocí klienta HTTP. | Ano | Ano | Ne |
| RequestCountCacheOthers | Počet všech požadavků s mezipamětí, na které se nevztahuje stav mezipaměti. | Ne | Ano | Ne  |
| EgressTotal | Přenos odchozích dat v GB | Ano |Ano |Ano |
| EgressHttpStatus2xx | Přenos odchozích dat * pro odpovědi se stavovým kódem HTTP 2xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus3xx | Přenos odchozích dat pro odpovědi se stavovým kódem HTTP 3xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus4xx | Přenos odchozích dat pro odpovědi se stavovým kódem HTTP 4xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus5xx | Přenos odchozích dat pro odpovědi se stavovým kódem HTTP 5xx v GB. | Ano | Ano | Ne |
| EgressHttpStatusOthers | Přenos odchozích dat pro odpovědi s dalšími kódy stavu HTTP v GB. | Ano | Ano | Ne  |
| EgressCacheHit | Přenos odchozích dat pro odpovědi, které byly doručeny přímo z mezipaměti CDN na pokusůch pop a hran CDN. | Ano | Ano | Ne |
| EgressCacheMiss. | Odchozí přenos dat pro odpovědi, které nebyly nalezeny na nejbližším serveru POP a načteny ze zdrojového serveru. | Ano | Ano | Ne |
| EgressCacheNoCache | Přenos odchozích dat pro prostředky, jejichž ukládání do mezipaměti brání z důvodu konfigurace uživatele na hraničních zařízeních. | Ano | Ano | Ne |
| EgressCacheUncacheable | Přenos odchozích dat pro prostředky, které brání v ukládání do mezipaměti pomocí hlaviček pro řízení mezipaměti a/nebo vypršení platnosti assetu. Určuje, že by neměl být uložen do mezipaměti pro POP nebo klienta HTTP. | Ano | Ano | Ne |
| EgressCacheOthers | Přenosy odchozích dat pro jiné scénáře mezipaměti. | Ne | Ano | Ne |

* Přenos odchozích dat odkazuje na provoz doručený ze serverů POP CDN klientovi.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma základních protokolů analýzy 

Všechny protokoly jsou uložené ve formátu JSON a každá položka má pole řetězců podle následujícího schématu:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Kde *Time* představuje počáteční čas hranice hodiny, pro kterou jsou statistiky hlášeny. Pokud metrika není podporována poskytovatelem CDN namísto hodnoty Double nebo integer, je hodnota null. Tato hodnota null označuje absenci metriky a je odlišná od hodnoty 0. Pro každou doménu nakonfigurovanou v koncovém bodě je jedna sada těchto metrik.

Příklad vlastností:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Další zdroje

* [Diagnostické protokoly Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Základní analýzy prostřednictvím Azure CDN doplňkového portálu](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Protokoly služby Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [REST API Log Analytics Azure](https://docs.microsoft.com/rest/api/loganalytics)







