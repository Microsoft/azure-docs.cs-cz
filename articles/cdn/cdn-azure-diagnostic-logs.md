---
title: Diagnostické protokoly Azure | Dokumenty společnosti Microsoft
description: Zákazník může povolit analýzu protokolu pro Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: 86696ed6715b4e43a9d02232c013eb64feb61f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594211"
---
# <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Pomocí diagnostických protokolů Azure můžete zobrazit základní analýzy a uložit je do jednoho nebo více cílů, včetně:

 - Účet služby Azure Storage
 - Azure Event Hubs
 - [Pracovní prostor Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Tato funkce je k dispozici na koncových bodech CDN pro všechny cenové úrovně. 

Protokoly diagnostiky Azure umožňují exportovat základní metriky využití z koncového bodu CDN do různých zdrojů, takže je můžete využívat přizpůsobeným způsobem. Můžete například provést následující typy exportu dat:

- Exportujte data do úložiště objektů blob, exportujte do CSV a generujte grafy v Excelu.
- Exportujte data do centra událostí a korelujte s daty z jiných služeb Azure.
- Export dat do protokolů Azure Monitoru a zobrazení dat ve vlastním pracovním prostoru Log Analytics

Následující diagram znázorňuje typické zobrazení dat jádra CDN.

![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Obrázek 1 – zobrazení základní analýzy CDN*

Další informace o diagnostických protokolech naleznete [v tématu Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Povolení protokolování prostřednictvím webu Azure Portal

Povolte protokolování pomocí základní analýzy CDN následujícím způsobem:

Přihlaste se k [portálu Azure](https://portal.azure.com). Pokud jste ještě nepovolili CDN pro váš pracovní postup, [vytvořte profil azure CDN a koncový bod,](cdn-create-new-endpoint.md) než budete pokračovat.

1. Na webu Azure Portal přejděte na **profil CDN**.

2. Na webu Azure Portal vyhledejte profil CDN nebo ho vyberte z řídicího panelu. Potom vyberte koncový bod CDN, pro který chcete povolit protokoly diagnostiky.

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. V části MONITOROVÁNÍ vyberte **protokoly diagnostiky.**

   Zobrazí se stránka **Protokoly diagnostiky.**

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Povolení protokolování pomocí Azure Storage

Chcete-li k ukládání protokolů použít účet úložiště, postupujte takto:
    
1. Do **pole Název**zadejte název nastavení diagnostického protokolu.
 
2. Vyberte **Archivovat do účtu úložiště**a pak vyberte **CoreAnalytics**. 

2. V **případě uchovávání informací (dny)** zvolte počet dnů uchovávání. Uchovávání nula dnů ukládá protokoly neomezeně dlouho. 

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Vyberte **účet úložiště**.

    Zobrazí se stránka **Vybrat účet úložiště.**

4. V rozevíracím seznamu vyberte účet úložiště a pak vyberte **OK**.

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po dokončení nastavení diagnostického protokolu vyberte **uložit**.

### <a name="logging-with-azure-monitor"></a>Protokolování pomocí Azure Monitoru

Pokud chcete protokoly ukládat pomocí Azure Monitoru, postupujte takto:

1. Na stránce **Protokoly diagnostiky** vyberte **Odeslat do analýzy protokolů**. 

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Vyberte **Konfigurovat** pro konfiguraci protokolování Azure Monitoru. 

   Zobrazí se stránka **pracovních prostorů Analýzy protokolů.**

    >[!NOTE] 
    >Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Vyberte **Vytvořit nový pracovní prostor**.

    Zobrazí se stránka **pracovního prostoru Analýzy protokolů.**

    >[!NOTE] 
    >Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/07_Create-new.png)

4. Do **pracovního prostoru Log Analytics**zadejte název pracovního prostoru Analýzy protokolů. Název pracovního prostoru Analýzy protokolů musí být jedinečný a musí obsahovat pouze písmena, čísla a pomlčky. mezery a podtržítka nejsou povoleny. 

5. V **části Předplatné**vyberte existující předplatné z rozevíracího seznamu. 

6. Pro **skupinu prostředků**vytvořte novou skupinu prostředků nebo vyberte existující skupinu.

7. V **části Umístění**vyberte umístění ze seznamu.

8. Pokud chcete uložit konfiguraci protokolu na řídicí panel, vyberte **Připnout na řídicí panel.** 

9. Chcete-li konfiguraci dokončit, vyberte **možnost OK.**

10. Po vytvoření pracovního prostoru se vrátíte na stránku **Diagnostické protokoly.** Potvrďte název nového pracovního prostoru Log Analytics.

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Vyberte **CoreAnalytics**a pak vyberte **Uložit**.

12. Chcete-li zobrazit nový pracovní prostor Log Analytics, vyberte **základní analýzy** na stránce koncového bodu CDN.

    ![portál - Diagnostické protokoly](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Pracovní prostor Log Analytics je nyní připraven k protokolování dat. Chcete-li tato data využívat, musíte použít [řešení protokolů Azure Monitor](#consuming-diagnostics-logs-from-a-log-analytics-workspace), které je popsáno dále v tomto článku.

Další informace o zpoždění dat protokolu naleznete v [tématu Zpoždění dat protokolu](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Povolení protokolování prostřednictvím PowerShellu

Následující příklad ukazuje, jak povolit diagnostické protokoly prostřednictvím rutin prostředí Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Povolení diagnostických protokolů v účtu úložiště

1. Přihlaste se a vyberte předplatné:

    Účet Connect-Az 

    Select-AzureSubscription -SubscriptionId 

2. Chcete-li povolit diagnostické protokoly v účtu úložiště, zadejte tento příkaz:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Chcete-li povolit protokoly diagnostiky v pracovním prostoru Analýzy protokolů, zadejte tento příkaz:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Využívání diagnostických protokolů ze služby Azure Storage
Tato část popisuje schéma analýzy jádra CDN, jak je uspořádána uvnitř účtu úložiště Azure a poskytuje ukázkový kód pro stažení protokolů v souboru CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Použití Průzkumníka úložiště Microsoft Azure
Než budete mít přístup k základním analytickým datům z účtu úložiště Azure, musíte nejprve získat nástroj pro přístup k obsahu v účtu úložiště. I když existuje několik nástrojů, které jsou k dispozici na trhu, ten, který doporučujeme, je Microsoft Azure Storage Explorer. Pokud si nástroj stáhnete, přečtěte si jeho obsah na [webu Azure Storage Explorer](https://storageexplorer.com/). Po stažení a instalaci softwaru jej nakonfigurujte tak, aby používal stejný účet úložiště Azure, který byl nakonfigurován jako cíl protokolů diagnostiky CDN.

1.  Otevření **Průzkumníka úložiště Microsoft Azure**
2.  Vyhledání účtu úložiště
3.  Rozbalte uzel **kontejnerů objektů blob** pod tímto účtem úložiště.
4.  Vyberte kontejner s názvem *insights-logs-coreanalytics*.
5.  Výsledky se zobrazí v pravém podokně, počínaje první úrovní, jako *resourceId=*. Pokračujte ve výběru každé úrovně, dokud nenajdete soubor *PT1H.json*. Vysvětlení cesty naleznete v tématu [Formát cesty objektu blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Každý soubor objektu blob *PT1H.json* představuje protokoly analýzy po dobu jedné hodiny pro konkrétní koncový bod CDN nebo jeho vlastní doménu.
7.  Schéma obsahu tohoto souboru JSON je popsáno ve schématu části protokolů základní analýzy.


#### <a name="blob-path-format"></a>Formát cesty objektu blob

Protokoly základní analýzy se generují každou hodinu a data se shromažďují a ukládají uvnitř jednoho objektu blob Azure jako datová část JSON. Vzhledem k tomu, že nástroj průzkumník úložiště interpretuje '/' jako oddělovač adresářů a zobrazuje hierarchii, cesta k objektu blob Azure se zobrazí, jako by existovala hierarchická struktura a představuje název objektu blob. Název objektu blob se řídí následující konvencí pojmenování:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Popis polí:**

|Hodnota|Popis|
|-------|---------|
|ID předplatného    |ID předplatného Azure ve formátu Guid.|
|Název skupiny prostředků |Název skupiny prostředků, do které prostředky CDN patří.|
|Profile Name (Název profilu) |Název profilu CDN|
|Název koncového bodu |Název koncového bodu CDN|
|Year|  Čtyřmístné znázornění roku, například 2017|
|Month| Dvoumístné znázornění čísla měsíce. 01=Leden ... 12=Prosinec|
|Den|   Dvouciferné znázornění dne v měsíci|
|PT1H.json| Skutečný soubor JSON, ve kterém jsou uložena analytická data|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Export základních analytických dat do souboru CSV

Chcete-li usnadnit přístup k základní analýzy, ukázkový kód pro nástroj je k dispozici. Tento nástroj umožňuje stahování souborů JSON do plochého formátu souboru odděleného čárkami, který lze použít k vytvoření grafů nebo jiných agregací.

Zde je návod, jak můžete použít nástroj:

1.  Navštivte odkaz na GitHub:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Stáhněte si kód.
3.  Podle pokynů zkompilujte a nakonfigurujte.
4.  Spusťte nástroj.
5.  Výsledný soubor CSV zobrazuje analytická data v jednoduché ploché hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Využívání diagnostických protokolů z pracovního prostoru služby Log Analytics
Azure Monitor je služba Azure, která monitoruje vaše cloudová a místní prostředí, aby byla zachována jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji. 

Chcete-li používat Azure Monitor, musíte [povolit protokolování](#enable-logging-with-azure-storage) do pracovního prostoru Azure Log Analytics, který je popsáno dříve v tomto článku.

### <a name="using-the-log-analytics-workspace"></a>Použití pracovního prostoru Analýzy protokolů

 Následující diagram znázorňuje architekturu vstupů a výstupů úložiště:

![Pracovní prostor služby Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Obrázek 3 – Úložiště analýzy protokolů*

Data můžete zobrazit různými způsoby pomocí řešení pro správu. Řešení pro správu můžete získat na [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Řešení monitorování můžete nainstalovat z Azure Marketplace výběrem odkazu **Získat je teď** v dolní části každého řešení.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Přidání řešení monitorování CDN azure monitoru

Podle následujících kroků přidejte řešení monitorování Azure Monitoru:

1.   Přihlaste se k portálu Azure pomocí předplatného Azure a přejděte na řídicí panel.
    ![Řídicí panel Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na stránce **Nový** vyberte v části **Marketplace** **položku Sledování + správa**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na stránce **Monitorování + správa** vyberte Zobrazit **vše**.

    ![Zobrazit vše](./media/cdn-diagnostics-log/15_See-all.png)

4. Vyhledejte cdn ve vyhledávacím poli.

    ![Zobrazit vše](./media/cdn-diagnostics-log/16_Search-for.png)

5. Vyberte **Azure CDN Core Analytics**. 

    ![Zobrazit vše](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po výběru **možnosti Vytvořit**budete vyzváni k vytvoření nového pracovního prostoru Analýzy protokolů nebo k použití existujícího pracovního prostoru. 

    ![Zobrazit vše](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Vyberte pracovní prostor, který jste vytvořili dříve. Potom je třeba přidat účet automatizace.

    ![Zobrazit vše](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Na následující obrazovce se zobrazí formulář účtu automatizace, který musíte vyplnit. 

    ![Zobrazit vše](./media/cdn-diagnostics-log/20_Automation.png)

9. Po vytvoření účtu automatizace jste připraveni přidat řešení. Vyberte tlačítko **Vytvořit**.

    ![Zobrazit vše](./media/cdn-diagnostics-log/21_Ready.png)

10. Vaše řešení bylo nyní přidáno do pracovního prostoru. Vraťte se na řídicí panel portálu Azure.

    ![Zobrazit vše](./media/cdn-diagnostics-log/22_Dashboard.png)

    Vyberte pracovní prostor Log Analytics, který jste vytvořili, a přejděte do pracovního prostoru. 

11. Vyberte dlaždici **Portál OMS** a podívejte se na své nové řešení.

    ![Zobrazit vše](./media/cdn-diagnostics-log/23_workspace.png)

12. Portál by teď měl vypadat jako na následující obrazovce:

    ![Zobrazit vše](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Vyberte jednu z dlaždic, chcete-li zobrazit několik zobrazení v datech.

    ![Zobrazit vše](./media/cdn-diagnostics-log/25_Interior-view.png)

    Posouváním doleva nebo doprava zobrazíte další dlaždice představující jednotlivá zobrazení do dat. 

    Výběrem jedné z dlaždic zobrazíte další podrobnosti o datech.

     ![Zobrazit vše](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Nabídky a cenové úrovně

Nabídky a cenové úrovně pro řešení pro správu naleznete [zde](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Přizpůsobení zobrazení

Zobrazení můžete přizpůsobit data pomocí **Návrháře zobrazení**. Chcete-li začít navrhovat, přejděte do pracovního prostoru Analýzy protokolů a vyberte dlaždici **Návrhář zobrazení.**

![Návrhář zobrazení](./media/cdn-diagnostics-log/27_Designer.png)

Přetáhněte typy grafů a vyplňte podrobnosti o datech, které chcete analyzovat.

![Návrhář zobrazení](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Zpoždění dat protokolu

V následující tabulce jsou uvedena zpoždění dat protokolu pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Akamai**a **Azure CDN Standard/Premium od společnosti Verizon**.

Zpoždění dat protokolu společnosti Microsoft | Zpoždění dat protokolu Verizon | Zpoždění dat protokolu Akamai
--- | --- | ---
Zpožděno o 1 hodinu. | Zpožděno o 1 hodinu a může trvat až 2 hodiny, než se začne zobrazovat po dokončení šíření koncového bodu. | Zpožděno o 24 hodin; Pokud byla vytvořena před více než 24 hodinami, trvá až 2 hodiny, než se začne zobrazovat. Pokud byl nedávno vytvořen, může trvat až 25 hodin, než se protokoly začnou zobrazovat.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostické typy protokolů pro analýzu jádra CDN

Společnost Microsoft aktuálně nabízí pouze protokoly základní analýzy, které obsahují metriky zobrazující statistiky odpovědí HTTP a statistiky odchozích dat, jak je patrné z místních přístupových polí CDN/okrajů.

### <a name="core-analytics-metrics-details"></a>Podrobnosti o základních metrikách analýzy
V následující tabulce je uveden seznam metrik dostupných v protokolech základní analýzy pro **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Akamai**a **Azure CDN Standard/Premium od společnosti Verizon**. Ne všechny metriky jsou k dispozici od všech poskytovatelů, i když tyto rozdíly jsou minimální. V tabulce se také zobrazí, zda je daná metrika dostupná od zprostředkovatele. Metriky jsou k dispozici pouze pro ty koncové body CDN, které mají provoz na nich.


|Metrika                     | Popis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Celkový počet požadavků na server během tohoto období. | Ano | Ano |Ano |
| RequestCountHttpStatus2xx | Počet všech požadavků, které vyústily v 2xx HTTP kód (například 200, 202). | Ano | Ano |Ano |
| RequestCountHttpStatus3xx | Počet všech požadavků, které vyústily v 3xx HTTP kód (například 300, 302). | Ano | Ano |Ano |
| RequestCountHttpStatus4xx | Počet všech požadavků, které vyústily v 4xx HTTP kód (například 400, 404). | Ano | Ano |Ano |
| RequestCountHttpStatus5xx | Počet všech požadavků, které vyústily v 5xx HTTP kód (například 500, 504). | Ano | Ano |Ano |
| RequestCounthttpStatusOthers | Počet všech ostatních HTTP kódů (mimo 2xx-5xx). | Ano | Ano |Ano |
| RequestCountHttpStatus200 | Počet všech požadavků, které vyústily v odpověď 200 http kód. | Ano | Ne  |Ano |
| RequestCountHttpStatus206 | Počet všech požadavků, které vyústily v odpověď 206 http kód. | Ano | Ne  |Ano |
| RequestCountHttpStatus302 | Počet všech požadavků, které vyústily v odpověď kódu 302 HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus304 | Počet všech požadavků, které vyústily v odpověď kódu 304 HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus404 | Počet všech požadavků, které vyústily v odpověď 404 http kód. | Ano | Ne  |Ano |
| RequestCountCacheHit | Počet všech požadavků, které vedly k přístupu do mezipaměti. Majetek byl doručen přímo z popu klientovi. | Ano | Ano | Ne  |
| RequestCountCacheMiss | Počet všech požadavků, které vedly k chybě mezipaměti. Chybět mezipaměti znamená, že prostředek nebyl nalezen na POP nejblíže ke klientovi, a proto byl načten z původu. | Ano | Ano | Ne |
| RequestCountCacheNoCache | Počet všech požadavků na prostředek, které jsou zabráněno ukládání do mezipaměti z důvodu konfigurace uživatele na okraji. | Ano | Ano | Ne |
| RequestCountCacheUncacheable | Počet všech požadavků na prostředky, které jsou zabráněno ukládání do mezipaměti hlavičky správce a vyprší platnost, které označují, že by neměly být uloženy do mezipaměti na POP nebo klienta HTTP. | Ano | Ano | Ne |
| RequestCountCacheOthers | Počet všech požadavků se stavem mezipaměti, které nejsou zahrnuty výše. | Ne | Ano | Ne  |
| EgressTotal | Odchozí přenos dat v GB | Ano |Ano |Ano |
| Odchozí přenos httpstatus2xx | Odchozí přenos dat* pro odpovědi se stavovými kódy 2xx HTTP v GB. | Ano | Ano | Ne  |
| Odchozí přenos httpstatus3xx | Odchozí přenos dat pro odpovědi se stavovými kódy 3xx HTTP v GB. | Ano | Ano | Ne  |
| Odchozí přenos httpstatus4xx | Odchozí přenos dat pro odpovědi se stavovými kódy 4xx HTTP v GB. | Ano | Ano | Ne  |
| Odchozí přenos httpstav5xx | Odchozí přenos dat pro odpovědi se stavovými kódy 5xx HTTP v GB. | Ano | Ano | Ne |
| EgressHttpStatusOthers | Odchozí přenos dat pro odpovědi s jinými stavovými kódy HTTP v GB. | Ano | Ano | Ne  |
| EgressCacheHit | Odchozí přenos dat pro odpovědi, které byly dodány přímo z mezipaměti CDN na CDN POP/hrany. | Ano | Ano | Ne |
| EgressCacheMiss. | Odchozí přenos dat pro odpovědi, které nebyly nalezeny na nejbližším serveru POP a načteny ze zdrojového serveru. | Ano | Ano | Ne |
| EgressCacheNoCache | Odchozí přenos dat pro datové zdroje, které jsou znemožněny ukládání do mezipaměti z důvodu konfigurace uživatele na okraji. | Ano | Ano | Ne |
| EgressCacheUncacheable | Odchozí přenos dat pro datové zdroje, které nemohou být uloženy do mezipaměti hlavičky správce a/nebo vyprší platnost. Označuje, že by neměl být uložen do mezipaměti v protokolu POP nebo klientem HTTP. | Ano | Ano | Ne |
| EgressCacheOthers | Odchozí přenosy dat pro jiné scénáře mezipaměti. | Ne | Ano | Ne |

*Odchozí přenos dat se vztahuje na přenosy dodané ze serverů CDN POP klientovi.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma protokolů základní analýzy 

Všechny protokoly jsou uloženy ve formátu JSON a každá položka má řetězcová pole podle následujícího schématu:

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

Kde *čas* představuje počáteční čas hranice hodin, pro které je statistika vykazována. Pokud metrika není podporována zprostředkovatelem CDN namísto hodnoty double nebo integer, je hodnota null. Tato hodnota null označuje nepřítomnost metriky a liší se od hodnoty 0. Existuje jedna sada těchto metrik na doménu nakonfigurované na koncovém bodu.

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
* [Základní analýzy prostřednictvím doplňkového portálu Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Protokoly azure monitoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Rozhraní REST Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







