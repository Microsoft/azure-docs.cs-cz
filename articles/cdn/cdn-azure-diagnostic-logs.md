---
title: Diagnostické protokoly
titleSuffix: Azure Content Delivery Network
description: Naučte se používat diagnostické protokoly Azure k ukládání základních analýz, které vám umožní exportovat metriky využití ze svého koncového bodu Azure Content Delivery Network.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573227"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Diagnostické protokoly – Azure Content Delivery Network

Pomocí diagnostických protokolů Azure můžete zobrazit základní analýzy a uložit je do jednoho nebo více cílů, včetně:

* Účet služby Azure Storage
* Pracovní prostor služby Log Analytics
* Azure Event Hubs

Tato funkce je k dispozici na koncových bodech CDN pro všechny cenové úrovně. 

Diagnostické protokoly umožňují exportovat základní metriky využití z vašeho koncového bodu CDN do různých typů zdrojů, abyste je mohli spotřebovávat vlastním způsobem. Můžete provést následující typy exportu dat:

* Exportujte data do služby Blob Storage, exportujte je do sdíleného svazku clusteru a generujte grafy v Excelu.
* Exportujte data Event Hubs a korelujte s daty z jiných služeb Azure.
* Export dat do Azure Monitor protokolů a zobrazení dat ve vlastním pracovním prostoru Log Analytics

Následující postup vyžaduje profil Azure CDN. Než budete pokračovat, přečtěte si téma [Vytvoření profilu Azure CDN a koncového bodu](cdn-create-new-endpoint.md) .

## <a name="enable-logging-with-the-azure-portal"></a>Povolení protokolování prostřednictvím webu Azure Portal

Postupujte podle těchto kroků a povolte protokolování pro Azure CDN koncový bod:

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

2. V Azure Portal přejděte na **všechny prostředky**, které  ->  **profil-CDN Profile** .

2. Vyberte koncový bod CDN, pro který chcete povolit diagnostické protokoly:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Vyberte koncový bod CDN." border="true":::

3. V části **monitorování** vyberte **protokoly diagnostiky** :

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Vyberte diagnostické protokoly." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Povolit protokolování pomocí Azure Storage

Pokud chcete použít účet úložiště pro ukládání protokolů, postupujte podle těchto kroků:

 >[!NOTE] 
 >K provedení těchto kroků je nutný účet úložiště. Další informace najdete v tématu: **[Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** .
    
1. Jako **název nastavení diagnostiky** zadejte název nastavení diagnostického protokolu.
 
2. Vyberte možnost **archivovat do účtu úložiště** a pak vyberte **CoreAnalytics**. 

3. V poli **Doba uchování (dny)** vyberte počet dnů uchování. Uchovávání nulových dnů ukládá protokoly po neomezenou dobu. 

4. Vyberte předplatné a účet úložiště pro protokoly.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Diagnostické protokoly – úložiště." border="true":::

3. Vyberte **Uložit**.

### <a name="send-to-log-analytics"></a>Odeslání do Log Analytics

Pokud chcete použít Log Analytics pro protokoly, postupujte podle těchto kroků:

>[!NOTE] 
>K provedení těchto kroků se vyžaduje pracovní prostor Log Analytics. Další informace najdete **[v tématu Vytvoření pracovního prostoru Log Analytics v Azure Portal](../azure-monitor/logs/quick-create-workspace.md)** .
    
1. Jako **název nastavení diagnostiky** zadejte název nastavení diagnostického protokolu.

2. Vyberte **Odeslat do Log Analytics** a pak vyberte **CoreAnalytics**. 

3. Vyberte předplatné a Log Analytics pracovní prostor pro protokoly.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Diagnostické protokoly – Log Analytics." border="true":::

4. Vyberte **Uložit**.

### <a name="stream-to-an-event-hub"></a>Streamovat do centra událostí

Pokud chcete použít centrum událostí pro protokoly, postupujte podle těchto kroků:

>[!NOTE] 
>K provedení těchto kroků je potřeba centrum událostí. Další informace najdete v tématu: **[rychlý Start: vytvoření centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md)** .
    
1. Jako **název nastavení diagnostiky** zadejte název nastavení diagnostického protokolu.

2. Vyberte **Stream do centra událostí** a pak vyberte **CoreAnalytics**. 

3. Vyberte obor názvů pro odběr a centrum událostí pro protokoly.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Diagnostické protokoly – centrum událostí." border="true":::

4. Vyberte **Uložit**.


## <a name="enable-logging-with-powershell"></a>Povolení protokolování prostřednictvím PowerShellu

Následující příklad ukazuje, jak povolit diagnostické protokoly pomocí rutin Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Povolení diagnostických protokolů v účtu úložiště

1. Přihlaste se k Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Pokud chcete povolit diagnostické protokoly v účtu úložiště, zadejte tyto příkazy. Nahraďte proměnné hodnotami:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Povolit protokoly diagnostiky pro Log Analytics pracovní prostor

1. Přihlaste se k Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Pokud chcete povolit protokoly diagnostiky pro Log Analytics pracovní prostor, zadejte tyto příkazy. Nahraďte proměnné hodnotami:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Povolit diagnostické protokoly pro obor názvů centra událostí

1. Přihlaste se k Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Pokud chcete povolit protokoly diagnostiky pro Log Analytics pracovní prostor, zadejte tyto příkazy. Nahraďte proměnné hodnotami:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Využívání diagnostických protokolů ze služby Azure Storage
Tato část popisuje schéma služby CDN Core Analytics, organizaci v účtu úložiště Azure a poskytuje ukázkový kód ke stažení protokolů v souboru CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Použití Průzkumník služby Microsoft Azure Storage
Pokud si chcete nástroj stáhnout, přečtěte si téma [Průzkumník služby Azure Storage](https://storageexplorer.com/). Po stažení a instalaci softwaru ho nakonfigurujte tak, aby používal stejný účet služby Azure Storage, který byl nakonfigurovaný jako cíl, do protokolů pro diagnostiku CDN.

1.  Otevřít **Průzkumník služby Microsoft Azure Storage**
2.  Vyhledání účtu úložiště
3.  Rozbalte uzel **kontejnery objektů BLOB** pod tímto účtem úložiště.
4.  Vyberte kontejner s názvem *Insights-logs-coreanalytics*.
5.  Výsledky se zobrazí v pravém podokně, počínaje první úrovní, jako *ResourceID =*. Pokračujte v výběru všech úrovní, dokud soubor nenajdete *PT1H.js*. Vysvětlení cesty najdete v tématu [Formát cesty objektu BLOB](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Každý objekt BLOB *PT1H.jsv* souboru představuje protokoly analýz na jednu hodinu pro konkrétní koncový bod CDN nebo jeho vlastní doménu.
7.  Schéma obsahu tohoto souboru JSON je popsané v části schéma základních protokolů analýzy.


#### <a name="blob-path-format"></a>Formát cesty objektu BLOB

Základní protokoly analýzy se generují každou hodinu a data se shromažďují a ukládají v jednom objektu blob Azure jako datová část JSON. Nástroj Průzkumník úložišť interpretuje "/" jako oddělovač adresářů a zobrazuje hierarchii. Cesta k objektu blob Azure se zobrazí, jako by existovala hierarchická struktura a představuje název objektu BLOB. Název objektu blob následuje po následující konvenci pojmenování:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Popis polí:**

|Hodnota|Popis|
|-------|---------|
|ID předplatného    |ID předplatného Azure ve formátu identifikátoru GUID|
|Název skupiny prostředků |Název skupiny prostředků, do které patří prostředky CDN.|
|Profile Name (Název profilu) |Název profilu CDN|
|Název koncového bodu |Název koncového bodu CDN|
|Year (Rok)|  Znázornění čtyřmístného roku, například 2017|
|Month (Měsíc)| Vyjádření čísla měsíce se dvěma číslicemi. 01 = leden... 12 = prosinec|
|Den|   Vyjádření dne v měsíci se dvěma číslicemi|
|PT1H.jsna| Skutečný soubor JSON, ve kterém jsou uložená data analýzy|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Export základních analytických dat do souboru CSV

Pro přístup k základním analýzám je k dispozici vzorový kód pro nástroj. Tento nástroj umožňuje stažení souborů JSON do samostatného formátu souboru odděleného čárkami, který se dá použít k vytváření grafů nebo jiných agregací.

Tady je postup, jak můžete použít nástroj:

1.  Navštivte odkaz na GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Stáhněte si kód.
3.  Postupujte podle pokynů pro zkompilování a konfiguraci.
4.  Spusťte nástroj.
5.  Výsledný soubor CSV zobrazuje analytická data v jednoduché ploché hierarchii.

## <a name="log-data-delays"></a>Zpoždění dat protokolu

Následující tabulka ukazuje zpoždění dat protokolu pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai** a **Azure CDN úrovně Standard/Premium z Verizon**.

Zpoždění dat protokolu Microsoft | Zpoždění dat protokolu Verizon | Zpoždění dat protokolu Akamai
--- | --- | ---
Zpožděné o 1 hodinu. | Zpožděné o 1 hodinu a může trvat až 2 hodiny, než se začne zobrazovat po dokončení šíření koncového bodu. | Zpoždění za 24 hodin; Pokud byl vytvořen před více než 24 hodinami, bude možné začít se objevovat až 2 hodiny. Pokud byl nedávno vytvořen, může trvat až 25 hodin, než se protokoly začnou zobrazovat.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy diagnostických protokolů pro CDN Core Analytics

Microsoft v současné době nabízí jenom základní protokoly analýzy, které obsahují metriky ukazující statistiku odpovědí HTTP a statistiky odchozího přenosu dat, které se zobrazují z položek POP a hran CDN.

### <a name="core-analytics-metrics-details"></a>Podrobnosti metriky Core Analytics
V následující tabulce je uveden seznam metrik dostupných v základních protokolech analýzy pro:

* **Azure CDN Standard od Microsoftu**
* **Azure CDN Standard z Akamai**
* **Azure CDN Standard/Premium z Verizon**

Ne všechny metriky jsou dostupné ze všech zprostředkovatelů, i když jsou tyto rozdíly minimální. Tabulka také zobrazuje, zda je daná metrika k dispozici od poskytovatele. Metriky jsou k dispozici pouze pro koncové body CDN, které na ně mají provoz.


|Metric                     | Popis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Celkový počet přístupů k žádosti v průběhu tohoto období. | Yes | Yes |Yes |
| RequestCountHttpStatus2xx | Počet všech požadavků, které vedly k 2xx kódu HTTP (například 200, 202). | Yes | Yes |Yes |
| RequestCountHttpStatus3xx | Počet všech požadavků, které vedly k 3xx kódu HTTP (například 300, 302). | Yes | Yes |Yes |
| RequestCountHttpStatus4xx | Počet všech požadavků, které vedly k 4xx kódu HTTP (například 400, 404). | Yes | Yes |Yes |
| RequestCountHttpStatus5xx | Počet všech požadavků, které vedly k 5xx kódu HTTP (například 500, 504). | Yes | Yes |Yes |
| RequestCountHttpStatusOthers | Počet všech ostatních kódů HTTP (mimo 2xx-5xx). | Yes | Yes |Yes |
| RequestCountHttpStatus200 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 200. | Yes | No  |Yes |
| RequestCountHttpStatus206 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 206. | Yes | No  |Yes |
| RequestCountHttpStatus302 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 302. | Yes | No  |Yes |
| RequestCountHttpStatus304 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 304. | Yes | No  |Yes |
| RequestCountHttpStatus404 | Počet všech požadavků, jejichž výsledkem je odpověď kódu HTTP 404. | Yes | No  |Yes |
| RequestCountCacheHit | Počet všech požadavků, jejichž výsledkem byl úspěšný přístup do mezipaměti. Asset byl obsluhován přímo z místního klienta. | Yes | Yes | No  |
| RequestCountCacheMiss | Počet všech požadavků, které způsobily Neúspěšné přístupy do mezipaměti. V případě neúspěšného ukládání do mezipaměti znamená, že Asset nebyl nalezen na bodu POP, který je nejblíže klientovi a byl načten ze zdroje. | Yes | Yes | No |
| RequestCountCacheNoCache | Počet všech požadavků na prostředek, jejichž ukládání do mezipaměti brání v důsledku konfigurace uživatele na hraničních zařízeních. | Yes | Yes | No |
| RequestCountCacheUncacheable | Počet všech požadavků na prostředky, které nemohou být ukládány do mezipaměti Cache-Control assetu a hlavičkou vypršení platnosti. Tento počet označuje, že by neměl být uložen do mezipaměti pro POP nebo klienta HTTP. | Yes | Yes | No |
| RequestCountCacheOthers | Počet všech požadavků s mezipamětí, na které se nevztahuje stav mezipaměti. | No | Yes | No  |
| EgressTotal | Přenos odchozích dat v GB | Yes |Yes |Yes |
| EgressHttpStatus2xx | Přenos odchozích dat * pro odpovědi se stavovým kódem HTTP 2xx v GB. | Yes | Yes | No  |
| EgressHttpStatus3xx | Přenos odchozích dat pro odpovědi se stavovým kódem HTTP 3xx v GB. | Yes | Yes | No  |
| EgressHttpStatus4xx | Přenos odchozích dat pro odpovědi se stavovým kódem HTTP 4xx v GB. | Yes | Yes | No  |
| EgressHttpStatus5xx | Přenos odchozích dat pro odpovědi se stavovým kódem HTTP 5xx v GB. | Yes | Yes | No |
| EgressHttpStatusOthers | Přenos odchozích dat pro odpovědi s dalšími kódy stavu HTTP v GB. | Yes | Yes | No  |
| EgressCacheHit | Přenos odchozích dat pro odpovědi, které byly doručeny přímo z mezipaměti CDN na pokusůch pop a hran CDN. | Yes | Yes | No |
| EgressCacheMiss. | Přenos odchozích dat pro odpovědi, které se nenašly na nejbližším serveru POP a ze zdrojového serveru se načetly. | Yes | Yes | No |
| EgressCacheNoCache | Přenos odchozích dat pro prostředky, které se neukládají do mezipaměti kvůli konfiguraci uživatele na hraničních zařízeních. | Yes | Yes | No |
| EgressCacheUncacheable | Přenos odchozích dat pro prostředky, u kterých se brání v ukládání do mezipaměti Cache-Control a hlavičkou vypršení platnosti assetu. Označuje, že by neměl být uložen do mezipaměti pro POP nebo klienta HTTP. | Yes | Yes | No |
| EgressCacheOthers | Přenosy odchozích dat pro jiné scénáře mezipaměti. | No | Yes | No |

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

Kde *Time* představuje počáteční čas hranice hodiny, pro kterou jsou statistiky hlášeny. Metrika Nepodporovaná poskytovatelem CDN místo hodnoty Double nebo integer má za následek hodnotu null. Tato hodnota null označuje absenci metriky a je odlišná od hodnoty 0. Jedna sada těchto metrik na doménu je nakonfigurovaná na koncovém bodu.

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

## <a name="additional-resources"></a>Další zdroje informací

* [Diagnostické protokoly Azure](../azure-monitor/essentials/platform-logs-overview.md)
* [Základní analýzy prostřednictvím Azure CDN doplňkového portálu](./cdn-analyze-usage-patterns.md)
* [Protokoly Azure Monitoru](../azure-monitor/logs/log-query-overview.md)
* [REST API Log Analytics Azure](/rest/api/loganalytics)