---
title: Azure diagnostické protokoly | Microsoft Docs
description: Zákazníka můžete povolit analýzy protokolů pro Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.openlocfilehash: 98a7fc5c4607115811e17a7cf6acd4e867663833
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261300"
---
# <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

S Azure diagnostické protokoly můžete zobrazit analýzu základní a uložit je do jedné nebo více cílů, včetně:

 - Účet služby Azure Storage
 - Azure Event Hubs
 - [Pracovní prostor analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Tato funkce je dostupná na koncové body CDN pro všechny cenové úrovně. 

Azure diagnostics protokoly umožňují, aby můžete využívat požadovaným způsobem exportu metriky základní informace o využití z koncový bod CDN do různých zdrojů. Například můžete provést následující typy dat exportu:

- Exportujte data do úložiště objektů blob, exportovat do souboru CSV a generovat grafy v aplikaci Excel.
- Exportovat data do centra událostí a korelovat s daty z jiné služby Azure.
- Exportovat data do analýzy protokolů a zobrazení dat v vlastní pracovní prostor analýzy protokolů

Následující diagram znázorňuje typické zobrazení základní analýza CDN data.

![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Obrázek 1 – Zobrazit analýzu základní CDN*

Další informace o diagnostických protokolů najdete v tématu [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Povolit protokolování na portálu Azure

Postupujte podle těchto kroků povolit protokolování s CDN základní analýza:

Přihlaste se k [portálu Azure](http://portal.azure.com). Pokud jste si již povolili CDN pro pracovní postup [vytvoření profilu Azure CDN a koncový bod](cdn-create-new-endpoint.md) než budete pokračovat.

1. Na portálu Azure přejděte do **profil CDN**.

2. Na portálu Azure vyhledejte profil CDN nebo vyberte jednu z řídicího panelu. Pak vyberte koncový bod CDN, pro který chcete povolit diagnostické protokoly.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Vyberte **protokolů diagnostiky** v části monitorování.

   **Protokolů diagnostiky** se zobrazí stránka.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Povolit protokolování s Azure Storage

Účet úložiště k ukládání protokolů, postupujte podle těchto kroků:
    
1. Pro **název**, zadejte název pro nastavení protokolů diagnostiky.
 
2. Vyberte **archivu do účtu úložiště**, pak vyberte **CoreAnalytics**. 

2. Pro **uchovávání dat (dny)**, zvolte počet dní uchovávání informací. Uchování nulový počet dnů po neomezenou dobu ukládá protokoly. 

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Vyberte **účet úložiště**.

    **Vyberte účet úložiště** se zobrazí stránka.

4. V rozevíracím seznamu vyberte účet úložiště, a pak vyberte **OK**.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po provedení požadovaných nastavení protokolů diagnostiky, vyberte **Uložit**.

### <a name="logging-with-log-analytics"></a>Protokolování s analýzy protokolů

Analýzy protokolů k ukládání protokolů, postupujte podle těchto kroků:

1. Z **protokolů diagnostiky** vyberte **odeslat k analýze protokolů**. 

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Vyberte **konfigurace** konfigurace analýzy protokolů protokolování. 

   **Pracovních prostorů OMS** se zobrazí stránka.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Vyberte **vytvořit nový pracovní prostor**.

    **Pracovním prostorem OMS** se zobrazí stránka.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/07_Create-new.png)

4. Pro **pracovním prostorem OMS**, zadejte název pracovní prostor OMS. Název pracovní prostor OMS musí být jedinečný a obsahovat pouze písmena, číslice a pomlčky; nejsou povoleny mezery a podtržítka. 

5. Pro **předplatné**, vyberte z rozevíracího seznamu existujícímu předplatnému. 

6. Pro **skupiny prostředků**, vytvořte novou skupinu prostředků nebo vyberte nějaký existující.

7. Pro **umístění**, vyberte umístění ze seznamu.

8. Vyberte **připnout na řídicí panel** Pokud chcete uložit nastavení protokolu na řídicí panel. 

9. Vyberte **OK** k dokončení konfigurace.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/08_Workspace-resource.png)

10. Po vytvoření pracovního prostoru se vrátíte na **diagnostické protokoly** stránky. Zkontrolujte název nové pracovní prostor analýzy protokolů.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Vyberte **CoreAnalytics**, pak vyberte **Uložit**.

12. Chcete-li zobrazit nový pracovní prostor analýzy protokolů, vyberte **základní analýza** z stránku koncového bodu CDN.

    ![Portál – protokolů diagnostiky](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Pracovní prostor analýzy protokolů je nyní připraven k protokolovat data. Aby bylo možné využívat data, je nutné použít [řešení protokolu analýzy](#consuming-diagnostics-logs-from-a-log-analytics-workspace), zahrnuté později v tomto článku.

Další informace o zpoždění dat protokolu najdete v tématu [protokolu zpoždění data](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Povolení protokolování v prostředí PowerShell

Následující příklad ukazuje postup povolení diagnostických protokolů prostřednictvím rutin prostředí PowerShell Azure.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Povolení diagnostických protokolů v účtu úložiště

1. Přihlaste se a vybrat odběr, který:

    Připojit AzureRmAccount 

    Select-AzureSubscription - SubscriptionId 

2. Pokud chcete povolit diagnostických protokolů v účtu úložiště, zadejte tento příkaz:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. K povolení protokolů diagnostiky v pracovním prostoru analýzy protokolů, zadejte tento příkaz:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Použití protokolů diagnostiky ze služby Azure Storage
Tato část popisuje schéma CDN základní analýza, jak je organizovaná uvnitř účet úložiště Azure a poskytuje ukázkový kód pro stažení protokolů v souboru CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Pomocí Průzkumníka úložiště Microsoft Azure
Než se dostanete k základní analytická data z účtu úložiště Azure, musíte nejprve nástroj pro přístup k obsahu v účtu úložiště. Na trhu jsou k dispozici několik nástrojů, i když je ten, který doporučujeme Microsoft Azure Storage Explorer. Si můžete stáhnout nástroj [Azure Storage Explorer](http://storageexplorer.com/). Po stažení a instalaci softwaru, nakonfigurujte ji používat stejný účet úložiště Azure, který byl nakonfigurovaný jako cíl do protokolů diagnostiky CDN.

1.  Otevřete **Microsoft Azure Storage Explorer**
2.  Najděte účet úložiště
3.  Rozbalte **kontejnery objektů Blob** uzel v rámci tohoto účtu úložiště.
4.  Vyberte kontejner s názvem *insights-logs-coreanalytics*.
5.  Zobrazit výsledky nahoru v pravém podokně počínaje první úroveň jako *resourceId =*. Pokračujte výběrem jednotlivých úrovních, vyhledejte soubor *PT1H.json*. Najdete zde *formát cesty objektu Blob* Poznámka vysvětlení cesty.
6.  Každý objekt blob *PT1H.json* soubor představuje analýzy protokolů pro jednu hodinu pro konkrétní koncový bod CDN nebo jeho vlastní doménu.
7.  Schéma obsah tohoto souboru JSON je popsaná v části schéma protokoly analýzy jádra.


> [!NOTE]
> **Formát cesty objektu BLOB**
> 
> Základní analýza protokolů jsou generovány každou hodinu a data se shromažďují a uložený v jednom Azure blob, které jako datové části JSON. Protože nástroj Průzkumník úložišť interpretuje '/' jako oddělovač adresářů a zobrazí v hierarchii, cesta k objekt blob systému Azure se zobrazí jako kdyby je hierarchická struktura a představuje název objektu blob. Následuje název objektu blob podle následující konvence: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Popis polí:**

|Hodnota|Popis|
|-------|---------|
|ID předplatného    |ID předplatného Azure ve formátu Guid.|
|Název skupiny prostředků |Název skupiny prostředků, do které patří prostředky CDN.|
|Název profilu |Název profilu CDN|
|Název koncového bodu |Název koncového bodu CDN|
|Rok|  Reprezentace čtyřmístný rok, například 2017|
|Měsíc| Reprezentace dvoumístné číslo měsíce. 01 = leden... 12 = prosinec|
|Den|   Dvouciferné znázornění dne v měsíci|
|PT1H.json| Skutečný soubor JSON se uloží analytická data|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Export základní analytická data do souboru CSV

Chcete-li snadný přístup k základní analýza, je k dispozici ukázkový kód pro nástroj. Tento nástroj umožňuje stahování souborů JSON do souboru nestrukturované oddělený čárkami ve formátu, který můžete použít k vytvoření grafu nebo jiných agregací.

Zde je, jak můžete použít nástroj:

1.  Po kliknutí na odkaz githubu: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Stáhněte si kód.
3.  Postupujte podle pokynů pro kompilaci a konfigurace.
4.  Spusťte nástroj.
5.  Výsledný soubor CSV zobrazuje analytická data v jednoduchých ploché hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Použití protokolů diagnostiky z pracovního prostoru analýzy protokolů
Analýzy protokolů je služba Azure, která monitoruje své cloudové a místní prostředí k udržování své dostupnosti a výkonu. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji. 

Chcete-li použít analýzy protokolů, je nutné [povolit protokolování](#enable-logging-with-azure-storage) do pracovního prostoru analýzy protokolů Azure, které je popsané výše v tomto článku.

### <a name="using-the-log-analytics-workspace"></a>Použití pracovního prostoru analýzy protokolů

 Následující diagram znázorňuje architekturu vstupy a výstupy úložiště:

![Pracovní prostor Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Obrázek 3 - úložiště analýzy protokolů*

Data můžete zobrazit v mnoha různými způsoby pomocí řešení pro správu. Můžete získat řešení pro správu z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Řešení pro správu můžete nainstalovat z Azure marketplace. výběrem **ho získat** odkaz na konci každé řešení.

### <a name="add-a-log-analytics-cdn-management-solution"></a>Přidat řešení pro správu protokolu analýzy CDN

Použijte následující postup přidání řešení pro správu protokolu Analytics:

1.   Přihlaste se k portálu Azure pomocí svého předplatného Azure a přejděte na řídicí panel.
    ![Řídicí panel Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. V **nový** v části **Marketplace**, vyberte **monitorování + správu**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. V **monitorování + správu** vyberte **zobrazit všechny**.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/15_See-all.png)

4. Vyhledejte CDN do vyhledávacího pole.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/16_Search-for.png)

5. Vyberte **Azure CDN základní analýza**. 

    ![Zobrazit všechno](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po výběru **vytvořit**, se zobrazí výzva k vytvoření nové pracovní prostor analýzy protokolů nebo použijte existující. 

    ![Zobrazit všechno](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Vyberte pracovní prostor, který jste vytvořili před. Pak je potřeba přidat účet automation.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Na následující obrazovce se zobrazí formulář účet automation, který je nutné vyplnit. 

    ![Zobrazit všechno](./media/cdn-diagnostics-log/20_Automation.png)

9. Po vytvoření účtu automation, jste připraveni přidat řešení. Vyberte tlačítko **Vytvořit**.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/21_Ready.png)

10. Řešení teď přidaná do pracovního prostoru. Vraťte se do řídicího panelu portálu Azure.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/22_Dashboard.png)

    Vyberte pracovní prostor analýzy protokolů, které jste vytvořili pro přejděte do pracovního prostoru. 

11. Vyberte **portálu OMS** dlaždice zobrazíte nové řešení.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/23_workspace.png)

12. Portálu by teď měl vypadat jako následující obrazovka:

    ![Zobrazit všechno](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Vyberte jednu z dlaždice zobrazíte několik zobrazení na vaše data.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/25_Interior-view.png)

    Můžete se posunete doleva nebo doprava zobrazíte další dlaždice představující jednotlivé zobrazení do data. 

    Vyberte jednu z dlaždice, které chcete zobrazit další podrobnosti o vaše data.

     ![Zobrazit všechno](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Nabídky a cenové úrovně

Můžete zobrazit nabídky a cenové úrovně pro řešení pro správu [zde](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Přizpůsobení zobrazení

Zobrazení lze přizpůsobit do vašich dat pomocí **Návrhář zobrazení**. Pokud chcete začít návrh, přejděte do pracovního prostoru analýzy protokolů a vyberte **Návrhář zobrazení** dlaždici.

![Návrhář zobrazení](./media/cdn-diagnostics-log/27_Designer.png)

Přetažení myší podrobnosti typy grafů a vyplňte v datech je chcete analyzovat.

![Návrhář zobrazení](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Zpoždění data protokolu

Následující tabulka uvádí protokolu zpoždění data pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Akamai**, a **Azure CDN Standard nebo Premium od společnosti Verizon**.

Zpoždění data protokolu Microsoft | Verizon protokolu data zpoždění | Akamai protokolu data zpoždění
--- | --- | ---
Zpožděn 1 hodina. | Zpožděn 1 hodinu a může trvat až 2 hodin zahájíte zobrazování po dokončení šíření koncový bod. | Zpoždění po 24 hodinách; Pokud byla vytvořena více než 24 hodinami, trvá spustit zobrazování až 2 hodin. Pokud byl nedávno vytvořen, může trvat až 25 hodin pro protokoly spuštění, které jsou uvedeny.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy protokolů diagnostiky pro CDN základní analýza

Microsoft teď nabízí základní Analýza protokolů pouze obsahujících metriky zobrazuje statistiku odpovědi HTTP a statistiky odchozí jak je vidět z CDN POP nebo okrajů.

### <a name="core-analytics-metrics-details"></a>Základní analýza metriky podrobnosti
Následující tabulka uvádí seznam metriky, které jsou k dispozici v základní analýzy protokolů pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od společnosti Akamai**, a **Azure CDN Standard nebo Premium od společnosti Verizon**. Ne všechny metriky jsou k dispozici od všech poskytovatelů, i když tyto rozdíly jsou minimální. Tabulka zobrazuje také, zda daná metrika je k dispozici od zprostředkovatele. Podle metrik, které jsou k dispozici pro pouze tyto koncové body CDN mající provoz na ně.


|Metrika                     | Popis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Celkový počet přístupů k žádosti o během této doby. | Ano | Ano |Ano |
| RequestCountHttpStatus2xx | Počet všech požadavků, které 2xx kód HTTP (například 200, 202). | Ano | Ano |Ano |
| RequestCountHttpStatus3xx | Počet všech požadavků, které 3xx kód HTTP (například 300, 302). | Ano | Ano |Ano |
| RequestCountHttpStatus4xx | Počet všech požadavků, které 4xx kód HTTP (například 400, 404). | Ano | Ano |Ano |
| RequestCountHttpStatus5xx | Počet všech požadavků, jejichž výsledkem kód HTTP 5xx (například 500, 504). | Ano | Ano |Ano |
| RequestCountHttpStatusOthers | Počet všechny ostatní kódy HTTP (mimo 2xx 5xx). | Ano | Ano |Ano |
| RequestCountHttpStatus200 | Počet všech požadavků, jejichž výsledkem 200 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus206 | Počet všech požadavků, jejichž výsledkem 206 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus302 | Počet všech požadavků, jejichž výsledkem 302 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus304 | Počet všech požadavků, jejichž výsledkem 304 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus404 | Počet všech požadavků, jejichž výsledkem odpovědi kódu HTTP 404. | Ano | Ne  |Ano |
| RequestCountCacheHit | Počet všech požadavků, jejichž výsledkem mezipaměti přístupů. Asset zpracování přímo z POP do klienta. | Ano | Ano | Ne  |
| RequestCountCacheMiss | Počet všech požadavků, které vedly k neúspěšnému přístupu do mezipaměti. K neúspěšnému přístupu do mezipaměti znamená asset nebyla nalezena na serveru POP nejbližší klientovi a proto byla načtena z tohoto počátku. | Ano | Ano | Ne |
| RequestCountCacheNoCache | Počet všech požadavků na prostředek, které nebudou moci ukládat do mezipaměti z důvodu konfigurace uživatele na hranici. | Ano | Ano | Ne |
| RequestCountCacheUncacheable | Počet všech požadavků na prostředky, které nebudou moci ukládat do mezipaměti asset Cache-Control a Expires hlavičky, které označují, že by neměl být uložené v mezipaměti, na serveru POP nebo klienta HTTP. | Ano | Ano | Ne |
| RequestCountCacheOthers | Počet všech požadavků stavem mezipaměti, které nejsou pokryty výše. | Ne | Ano | Ne  |
| EgressTotal | Odchozí přenosy dat v GB | Ano |Ano |Ano |
| EgressHttpStatus2xx | Odchozí datové přenosy * pro odpovědi s stavové kódy HTTP 2xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus3xx | Přenos odchozích dat pro odpovědi s stavové kódy HTTP 3xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus4xx | Přenos odchozích dat pro odpovědi s stavové kódy HTTP 4xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus5xx | Přenos odchozích dat pro odpovědi s stavové kódy HTTP 5xx v GB. | Ano | Ano | Ne |
| EgressHttpStatusOthers | Přenos odchozích dat pro odpovědi s ostatních stavových kódech HTTP v GB. | Ano | Ano | Ne  |
| EgressCacheHit | Odchozí přenos dat pro odpovědi, které byly doručeny přímo z mezipaměti CDN na CDN POP nebo okraje. | Ano | Ano | Ne |
| EgressCacheMiss. | Přenos odchozích dat pro odpovědi, které nebyly na nejbližší server POP najít a načíst ze zdrojového serveru. | Ano | Ano | Ne |
| EgressCacheNoCache | Přenos odchozích dat pro prostředky, které nebudou moci ukládat do mezipaměti z důvodu konfigurace uživatele na hranici. | Ano | Ano | Ne |
| EgressCacheUncacheable | Odchozí datové přenosy pro prostředky, které nebudou moci ukládat do mezipaměti Cache-Control nebo Expires hlavičky assetu. Označuje, že by neměl být uložené v mezipaměti, na serveru POP nebo klienta HTTP. | Ano | Ano | Ne |
| EgressCacheOthers | Odchozí datové přenosy s dalšími scénáři mezipaměti. | Ne | Ano | Ne |

* Odchozí přenosy dat odkazuje na provoz klientovi předána ze serverů CDN POP.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma základní analýzy protokolů 

Všechny protokoly se ukládají ve formátu JSON a každá položka má polí s řetězcem podle schématu. následující:

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

Kde *čas* představuje čas zahájení hodinu hranic, pro který je hlášen statistik. Pokud metriky není podporována zprostředkovatelem CDN, místo hodnotu double nebo celé číslo, nebude hodnotu null. Tato hodnota null ukazuje na nepřítomnost metriky a se liší od hodnoty 0. Není jednu sadu tyto metriky v každé doméně nakonfigurovaný v koncovém bodě.

Příklad vlastnosti:

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

## <a name="additional-resources"></a>Další zdroje informací:

* [Azure diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Základní analýza prostřednictvím doplňkovém portálu Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Analýza protokolů Azure rozhraní REST API](https://docs.microsoft.com/rest/api/loganalytics)







