---
title: Diagnostické protokoly Azure | Dokumentace Microsoftu
description: Odběratele můžete povolit analýzy protokolů pro Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: d0b804c48f3de4d4ba29ebe0785f6dd991329a53
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409250"
---
# <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

S diagnostické protokoly Azure můžete zobrazit základní analýzy a uložit je do jedné nebo více cílů, včetně:

 - Účet služby Azure Storage
 - Azure Event Hubs
 - [Pracovní prostor služby Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Tato funkce je dostupná na koncové body CDN pro všechny cenové úrovně. 

Protokoly diagnostiky Azure umožňuje exportovat metriky základní informace o využití z koncového bodu CDN pro širokou škálu zdrojů tak, aby vám mohou využívat vlastní způsobem. Například můžete provést následující druhy export dat:

- Exportujte data do úložiště objektů blob, export do souboru CSV a generovat grafy v aplikaci Excel.
- Export dat do služby Event Hubs a je možné korelovat s daty z jiných služeb Azure.
- Exportovat data do Log Analytics a zobrazení dat v vlastní pracovní prostor Log Analytics

Následující diagram ukazuje typické CDN core analytics přehled data.

![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Obrázek 1 – CDN core analytics zobrazení*

Další informace o diagnostických protokolech najdete v tématu [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-the-azure-portal"></a>Povolit protokolování pomocí webu Azure portal

Postupujte podle těchto kroků povolit protokolování pomocí CDN core analytics:

Přihlaste se k webu [Azure Portal](http://portal.azure.com). Pokud není již jste povolili CDN pro pracovní postup [vytvoření koncového bodu a profilu Azure CDN](cdn-create-new-endpoint.md) předtím, než budete pokračovat.

1. Na webu Azure Portal, přejděte na **profil CDN**.

2. Na webu Azure Portal vyhledejte profil CDN nebo vyberte některou z řídicího panelu. Vyberte koncový bod CDN, pro kterou chcete povolit diagnostické protokoly.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Vyberte **diagnostické protokoly** v části monitorování.

   **Diagnostické protokoly** se zobrazí stránka.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Povolit protokolování pomocí služby Azure Storage

Pokud chcete použít účet úložiště k ukládání protokolů, postupujte takto:
    
1. Pro **název**, zadejte název pro nastavení diagnostických protokolů.
 
2. Vyberte **archivovat do účtu úložiště**a pak vyberte **CoreAnalytics**. 

2. Pro **uchování (dny)**, zvolte počet dní uchování. 0 dnů uchování dat po neomezenou dobu ukládá protokoly. 

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Vyberte **účtu úložiště**.

    **Vyberte účet úložiště** se zobrazí stránka.

4. Vyberte účet úložiště z rozevíracího seznamu a potom vyberte **OK**.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po provedení nastavení diagnostický protokol vyberte **Uložit**.

### <a name="logging-with-log-analytics"></a>Protokolování pomocí služby Log Analytics

Použití Log Analytics k ukládání protokolů, postupujte podle těchto kroků:

1. Z **diagnostické protokoly** stránce **odesílat do Log Analytics**. 

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Vyberte **konfigurovat** chcete nakonfigurovat protokolování pro Log Analytics. 

   **Pracovních prostorů Log Analytics** se zobrazí stránka.

    >[!NOTE] 
    >Pracovní prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Vyberte **vytvořit nový pracovní prostor**.

    **Pracovní prostor Log Analytics** se zobrazí stránka.

    >[!NOTE] 
    >Pracovní prostory OMS jsou dnes označovány jako pracovní prostory Log Analytics.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/07_Create-new.png)

4. Pro **pracovní prostor Log Analytics**, zadejte název pracovního prostoru Log Analytics. Název pracovního prostoru Log Analytics musí být jedinečný a musí obsahovat jenom písmena, číslice a spojovníky; nejsou povoleny mezery a podtržítka. 

5. Pro **předplatné**, vyberte stávající předplatné z rozevíracího seznamu. 

6. Pro **skupiny prostředků**, vytvořte novou skupinu prostředků nebo vyberte existující.

7. Pro **umístění**, vyberte umístění ze seznamu.

8. Vyberte **připnout na řídicí panel** Pokud chcete uložit konfiguraci protokolu na řídicí panel. 

9. Vyberte **OK** a dokončete tak konfiguraci.

10. Po vytvoření pracovního prostoru se vrátíte **diagnostické protokoly** stránky. Potvrďte název nového pracovního prostoru Log Analytics.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Vyberte **CoreAnalytics**a pak vyberte **Uložit**.

12. Chcete-li zobrazit nový pracovní prostor Log Analytics, vyberte **základní analýzy** ze stránky koncového bodu CDN.

    ![Portál – protokoly diagnostiky](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Váš pracovní prostor Log Analytics je teď připravený k protokolování dat o. Aby bylo možné využívat data, je nutné použít [Log Analytics řešení](#consuming-diagnostics-logs-from-a-log-analytics-workspace), uvedených dále v tomto článku.

Další informace o protokolu data zpoždění, naleznete v tématu [protokolovat data zpoždění](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Povolení protokolování prostřednictvím PowerShellu

Následující příklad ukazuje, jak povolit diagnostické protokoly prostřednictvím rutin Azure Powershellu.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Povolení diagnostických protokolů v účtu úložiště

1. Přihlášení a výběru předplatného:

    Connect-AzureRmAccount 

    Select-AzureSubscription - SubscriptionId 

2. Povolení diagnostických protokolů v účtu úložiště, zadejte tento příkaz:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Pokud chcete povolit diagnostické protokoly v pracovním prostoru Log Analytics, zadejte tento příkaz:

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Využívání diagnostické protokoly z Azure Storage
Tato část popisuje schéma CDN core analytics, jak je uspořádaný v rámci účtu úložiště Azure a poskytuje ukázkový kód pro stahování protokolů v souboru CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Pomocí Průzkumníka služby Microsoft Azure Storage
Pro přístup k základní analytických dat z účtu služby Azure storage, musíte nejdřív nástroj pro přístup k obsahu v účtu úložiště. Na trhu jsou k dispozici několik nástrojů, je ten, který doporučujeme Průzkumníka služby Microsoft Azure Storage. Stáhněte si nástroj, najdete v článku [Průzkumníka služby Azure Storage](http://storageexplorer.com/). Po stažení a instalace softwaru, nakonfigurujte ho na použití stejného účtu úložiště Azure, který byl nakonfigurovaný jako cíl, aby CDN diagnostické protokoly.

1.  Otevřít **Průzkumníka služby Microsoft Azure Storage**
2.  Najděte účet úložiště
3.  Rozbalte **kontejnery objektů Blob** uzel pod tímto účtem úložiště.
4.  Vyberte kontejner s názvem *přehledy. protokoly coreanalytics*.
5.  Zobrazit výsledky nahoru v pravém podokně, počínaje první úrovně, jako *resourceId =*. Pokračujte výběrem jednotlivých úrovních, dokud nenajdete soubor *PT1H.json*. Vysvětlení cesty, najdete v článku [formát cesty objektu Blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Každý objekt blob *PT1H.json* soubor představuje protokoly analýzy za jednu hodinu pro určitý koncový bod CDN nebo své vlastní domény.
7.  Schéma obsah tohoto souboru JSON je popsaný v části schématu protokoly analýzy jader.


#### <a name="blob-path-format"></a>Formát cesty objektu BLOB

Základní protokoly analýzy se generují každou hodinu a data se shromažďují a ukládají uvnitř jednoho Azure blob jako datovou část JSON. Protože nástroji Storage explorer interpretuje "/" jako oddělovač adresářů a zobrazuje hierarchii, cesta do objektu blob Azure se zobrazí jako kdyby je hierarchická struktura a představuje název objektu blob. Název objektu blob se řídí následujícími zásadami vytváření názvů:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Popis pole:**

|Hodnota|Popis|
|-------|---------|
|ID předplatného    |ID předplatného Azure ve formátu Guid.|
|Název skupiny prostředků |Název skupiny prostředků, do které patří prostředků CDN.|
|Název profilu |Název profilu CDN|
|Název koncového bodu |Název koncového bodu CDN|
|Rok|  Reprezentace čtyřmístný rok, například 2017|
|Měsíc| Dvěma číslicemi reprezentuje číslo měsíce. 01. ledna =... 12. prosince =|
|Den|   Dvěma číslicemi vyjádření dne v měsíci|
|PT1H.json| Skutečný soubor JSON je ukládat analytics data|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Export dat analytics core do souboru CSV

Abyste usnadnili snadnou pro přístup k základní analýzy, je k dispozici ukázkový kód pro nástroj. Tento nástroj umožňuje stahovat soubory JSON do souboru paušální oddělený čárkami ve formátu, který slouží k vytvoření grafů nebo jiných agregací.

Zde je, jak můžete pomocí nástroje:

1.  Po kliknutí odkaz githubu: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Stáhněte si kód.
3.  Postupujte podle pokynů ke kompilaci a konfigurace.
4.  Spusťte nástroj.
5.  Výsledný soubor CSV zobrazuje analytics data v hierarchii jednoduché bez stromové struktury.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Využívání diagnostické protokoly z pracovního prostoru Log Analytics
Log Analytics je služba Azure, která monitoruje cloudové a místní prostředí s cílem zachovat jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji. 

Chcete-li používat službu Log Analytics, je třeba [povolit protokolování](#enable-logging-with-azure-storage) do pracovního prostoru Azure Log Analytics, která je popsána výše v tomto článku.

### <a name="using-the-log-analytics-workspace"></a>Používání pracovního prostoru Log Analytics

 Následující diagram znázorňuje architekturu vstupy a výstupy úložiště:

![Pracovní prostor Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Obrázek 3 – úložiště Log Analytics*

Data můžete zobrazit v různých způsobů, jak pomocí řešení pro správu. Můžete získat z řešení pro správu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Řešení pro správu můžete nainstalovat z webu Azure marketplace výběrem **získat** odkaz na konci každé řešení.

### <a name="add-a-log-analytics-cdn-management-solution"></a>Přidání řešení Log Analytics pro správu CDN

Postupujte podle těchto kroků přidejte řešení pro správu Log Analytics:

1.   Přihlaste se k webu Azure portal pomocí svého předplatného Azure a přejděte na řídicí panel.
    ![Řídicí panel Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. V **nový** stránce v části **Marketplace**vyberte **monitorování a správa**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. V **monitorování a správa** stránce **zobrazit všechny**.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/15_See-all.png)

4. CDN do vyhledávacího pole Hledat.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/16_Search-for.png)

5. Vyberte **Azure CDN Core Analytics**. 

    ![Zobrazit všechno](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po výběru **vytvořit**, zobrazí se výzva k vytvoření nového pracovního prostoru Log Analytics nebo použijte již existující. 

    ![Zobrazit všechno](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Vyberte pracovní prostor, který jste vytvořili před. Potom musíte přidat účet automation.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Na následující obrazovce se zobrazí formulář účtu automation, který musíte vyplnit. 

    ![Zobrazit všechno](./media/cdn-diagnostics-log/20_Automation.png)

9. Po vytvoření účtu automation, budete chtít přidat vaše řešení. Vyberte tlačítko **Vytvořit**.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/21_Ready.png)

10. Vaše řešení je nyní přidán do pracovního prostoru. Vraťte se do řídicího panelu Azure portal.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/22_Dashboard.png)

    Vyberte pracovní prostor Log Analytics, který jste vytvořili, přejděte do pracovního prostoru. 

11. Vyberte **portál OMS** dlaždici zobrazíte nové řešení.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/23_workspace.png)

12. Na portálu by měla vypadat jako na následujícím obrázku:

    ![Zobrazit všechno](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Vyberte jednu z dlaždice zobrazíte několik zobrazení o vašich datech.

    ![Zobrazit všechno](./media/cdn-diagnostics-log/25_Interior-view.png)

    Můžete posunout doleva nebo doprava, pokud chcete zobrazit další dlaždice představující jednotlivá zobrazení k datům. 

    Vyberte jednu z dlaždice zobrazíte další podrobnosti o vašich datech.

     ![Zobrazit všechno](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Nabídky a cenové úrovně

Zobrazí se nabídky a cenové úrovně pro řešení pro správu [tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Přizpůsobení zobrazení

Zobrazení můžete přizpůsobit svá data pomocí **Návrhář zobrazení**. Pokud chcete začít návrhu, přejděte do pracovního prostoru Log Analytics a vyberte **Návrhář zobrazení** dlaždici.

![Návrhář zobrazení](./media/cdn-diagnostics-log/27_Designer.png)

Přetáhněte myší typy grafů a vyplňte data podrobnosti chcete analyzovat.

![Návrhář zobrazení](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Zpoždění data protokolu

Následující tabulka uvádí protokolu data zpoždění **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai**, a **Azure CDN Standard nebo Premium od Verizonu**.

Zpoždění data protokolu Microsoft | Verizon zpoždění data protokolu | Zpoždění data protokolu Akamai
--- | --- | ---
Opožděná. 1 hodina. | Zpožděné za 1 hodinu a může trvat až 2 hodin na spuštění vyskytující se po dokončení šíření koncový bod. | Opožděná. 24 hodin Pokud byl vytvořen před více než 24 hodin, trvá začnou zobrazovat, až 2 hodiny. Pokud byl vytvořen nedávno, může trvat až 25 hodin protokoly a začnou zobrazovat.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy diagnostických protokolů pro CDN core analytics

Společnost Microsoft aktuálně nabízí základní protokoly analýzy pouze, které obsahují metriky znázorňující statistiky odpovědi protokolu HTTP a statistiky odchozího přenosu dat jak je vidět z CDN POP a hran.

### <a name="core-analytics-metrics-details"></a>Podrobnosti o Core analytics metriky
Následující tabulka uvádí seznam metrik, které jsou k dispozici v základní analýzy protokolů pro **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai**, a **Azure CDN Standard nebo Premium od společnosti Verizon**. Některé metriky jsou k dispozici od všech zprostředkovatelů, i když tyto rozdíly jsou minimální. Tabulka také zobrazuje, zda je k dispozici od poskytovatele danou metriku. Metriky jsou k dispozici pouze ty koncové body CDN, které mají provoz na ně.


|Metrika                     | Popis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Celkový počet přístupů požadavku během tohoto období. | Ano | Ano |Ano |
| RequestCountHttpStatus2xx | Počet všech žádostí, z kterých vzniklo kód HTTP 2xx (například 200, 202). | Ano | Ano |Ano |
| RequestCountHttpStatus3xx | Počet všech žádostí, z kterých vzniklo kód HTTP 3xx (například 300, 302). | Ano | Ano |Ano |
| RequestCountHttpStatus4xx | Počet všech žádostí, z kterých vzniklo kód HTTP 4xx (například 400, 404). | Ano | Ano |Ano |
| RequestCountHttpStatus5xx | Počet všech žádostí, z kterých vzniklo kód HTTP 5xx (například 500, 504). | Ano | Ano |Ano |
| RequestCountHttpStatusOthers | Počet všechny ostatní kódy HTTP (mimo 2xx 5xx). | Ano | Ano |Ano |
| RequestCountHttpStatus200 | Počet všech žádostí, z kterých vzniklo 200 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus206 | Počet všech žádostí, z kterých vzniklo odpověď 206 kód protokolu HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus302 | Počet všech žádostí, z kterých vzniklo 302 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus304 | Počet všech žádostí, z kterých vzniklo 304 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountHttpStatus404 | Počet všech žádostí, z kterých vzniklo 404 kód odpovědi HTTP. | Ano | Ne  |Ano |
| RequestCountCacheHit | Počet všech žádostí, z kterých vzniklo mezipaměť přístupů. Asset vyřídila přímo v lokalitě POP do klienta. | Ano | Ano | Ne  |
| RequestCountCacheMiss | Počet všech žádostí, z kterých vzniklo neúspěšnému přístupu do mezipaměti. Prostředek se nenašel na nejbližší klientovi POP a proto byla načtena z původního zdroje znamená, že neúspěšnému přístupu do mezipaměti. | Ano | Ano | Ne |
| RequestCountCacheNoCache | Počet všech žádostí na prostředek, které nemají ukládat do mezipaměti kvůli konfiguraci uživatele na hraničních zařízeních. | Ano | Ano | Ne |
| RequestCountCacheUncacheable | Počet všech požadavků na prostředky, které nemají ukládat do mezipaměti podle prostředku Cache-Control a Expires hlavičky, které označují, že by neměl být uložené v mezipaměti, na místní nabídky nebo pomocí klienta HTTP. | Ano | Ano | Ne |
| RequestCountCacheOthers | Počet všech žádostí se stavem mezipaměti není pokrytá výše. | Ne | Ano | Ne  |
| EgressTotal | Přenos odchozích dat v GB | Ano |Ano |Ano |
| EgressHttpStatus2xx | Odchozí datové přenosy * pro odpovědi s stavové kódy HTTP 2xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus3xx | Přenos odchozích dat pro odpovědi s stavové kódy HTTP 3xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus4xx | Přenos odchozích dat pro odpovědi s stavové kódy HTTP 4xx v GB. | Ano | Ano | Ne  |
| EgressHttpStatus5xx | Přenos odchozích dat pro odpovědi s stavové kódy HTTP 5xx v GB. | Ano | Ano | Ne |
| EgressHttpStatusOthers | Přenos odchozích dat pro odpovědi s stavových kódech HTTP v GB. | Ano | Ano | Ne  |
| EgressCacheHit | Odchozí přenos dat pro odpovědi, které byly doručeny přímo z mezipaměti CDN v CDN POP a hran. | Ano | Ano | Ne |
| EgressCacheMiss. | Přenos odchozích dat pro odpovědi, které nebyly na serveru POP nejbližší najít a načíst ze zdrojového serveru. | Ano | Ano | Ne |
| EgressCacheNoCache | Přenos odchozích dat pro prostředky, které nemají ukládat do mezipaměti kvůli konfiguraci uživatele na hraničních zařízeních. | Ano | Ano | Ne |
| EgressCacheUncacheable | Přenos odchozích dat pro prostředky, které nemají ukládat do mezipaměti Cache-Control nebo Expires hlavičky prostředku. Označuje, že by neměl být uložené v mezipaměti, na místní nabídky nebo pomocí klienta HTTP. | Ano | Ano | Ne |
| EgressCacheOthers | Přenosy odchozích dat pro další scénáře mezipaměti. | Ne | Ano | Ne |

* Odchozí přenos dat se odkazuje na provoz klientovi předána ze serverů CDN POP.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma základní analýzy protokolů 

Všechny protokoly se ukládají ve formátu JSON a každý záznam obsahuje pole řetězce podle následujícího schématu:

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

Kde *čas* představuje čas zahájení hodinu hranic, pro které je hlášeno statistiky. Metrika není podporováno zprostředkovatelem CDN, místo hodnoty double nebo celé číslo, je hodnota null. Tato hodnota null označuje absenci metriky a se liší od hodnoty 0. Existuje jedna sada tyto metriky za konfiguraci domény na koncový bod.

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

* [Diagnostické protokoly Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Základní analýza přes doplňkový portál Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Rozhraní REST API Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







