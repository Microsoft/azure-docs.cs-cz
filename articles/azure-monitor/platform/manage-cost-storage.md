---
title: Správa nákladů a využití pro Azure Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak změnit cenový tarif a spravovat zásady objemu a uchovávání dat pro váš pracovní prostor Log Analytics v Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 262c81dbf2c094b6a823a8320a0657f2767bc20c
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332315"
---
# <a name="manage-usage-and-costs-for-log-analytics"></a>Správa nákladů a využití pro Log Analytics

> [!NOTE]
> Tento článek popisuje, jak řídit své náklady v Log Analytics tak, že nastavíte dobu uchování dat.  Přečtěte si následující články související informace.
> - [Analýza využití dat v Log Analytics](manage-cost-storage.md) popisuje, jak analyzovat a upozornění na data využití.
> - [Monitorování využití a odhadované náklady](usage-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Log Analytics je navržená tak, aby škálování a podporu shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje ve vašem podniku nebo nasazené v Azure.  To může být hlavním důvod pro vaši organizaci, nákladovou efektivnost v konečném důsledku je základní ovladač. Za tímto účelem je důležité pochopit, že náklady na pracovní prostor Log Analytics není jen na základě objemu shromážděných dat, je také závislý na vybraného plánu a jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku jsme zkontrolujte, jak můžete aktivně monitorovat růst objemu a úložiště dat a definování omezení pro řízení těchto související náklady. 

Náklady na data může být značné, v závislosti na následujících faktorech: 

- Objem dat generovaný a ingestuje se do pracovního prostoru 
    - Počet řešení pro správu povolena
    - Počet systémů, monitoruje
    - Typ data shromážděná z jednotlivých sledovaných prostředků 
- Dobu se rozhodnete uchovávat data 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Vysvětlení využití a odhadované náklady na váš pracovní prostor
Log Analytics umožňuje snadno pochopit, jaké jsou náklady pravděpodobně možné na základě nedávné způsobů využití.  Chcete-li to provést, použijte **využití Log Analytics a odhadované náklady** můžete zkontrolovat a analyzovat data využití. Ukazuje, kolik dat shromažďují jednotlivá řešení, kolik dat je uchovávané a odhad nákladů na základě objemu dat přijatých a jakékoli další uchování nad rámec objemu zahrnutého v ceně.

![Využití a odhadované náklady](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Prozkoumat data podrobněji, klikněte na ikonu v horní části napravo od buď z grafů v **využití a odhadované náklady** stránky. Teď můžete pracovat s Tento dotaz a prozkoumejte podrobnosti o využití.  

![Zobrazení protokolů](media/manage-cost-storage/logs.png)

Z **využití a odhadované náklady** stránce můžete zkontrolovat objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávají ve vašem pracovním prostoru Log Analytics.  Klikněte na tlačítko **podrobnosti o použití** z horní části stránky, chcete-li zobrazit řídicí panel s informacemi o využití na trendy objemu dat podle zdroje, počítačů a nabídky. K zobrazení a nastavte denní limit nebo dobu uchování upravit, klikněte na tlačítko **Správa objemu dat**.
 
Poplatky za log Analytics se přidají do vašeho vyúčtování služeb Azure. Můžete si prohlédnout podrobnosti Azure fakturovat části fakturace na portálu Azure Portal nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Denní limit
Můžete nakonfigurovat denní limit a omezit tak denní příjem dat pro váš pracovní prostor, ale použijte pozornost jak vaším cílem by neměl být k dosažení denního limitu.  V opačném případě můžete přijít o data po zbytek dne, který může mít vliv na ostatní služby Azure a řešení, jehož funkce může záviset na aktuální data, která je k dispozici v pracovním prostoru.  V důsledku toho možnost sledovat a přijímat upozorní, když se to týká podmínky stavu prostředků podporující IT služby.  Denní limit je určena pro použití jako způsob, jak spravovat neočekávané nárůst objemu dat ze spravovaných prostředků a zůstat v rámci svého limitu, nebo pokud chcete jednoduše omezení neplánovaných poplatků pro váš pracovní prostor.  

Po dosažení denního limitu pro zbytek dne zastaví kolekci fakturovatelné datových typů. Zobrazí se upozornění banner v horní části stránky pro vybraný pracovní prostor Log Analytics a operace událost je odeslána do *operace* tabulky v rámci **LogManagement** kategorie. Shromažďování dat bude pokračovat po resetování času definované v části *denní limit se nastaví na*. Doporučujeme definovat pravidlo výstrahy založené na této události operace nakonfigurovaný tak, aby upozornit po dosažení denního datového limitu. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifikujte jaké denního limitu pro definování dat 
Kontrola [využití Log Analytics a odhadované náklady](usage-estimated-costs.md) pochopit trend příjem dat a k čemu slouží k definování denní limit objemu. Je nutné zvážit opatrně, protože nebude možné po dosažení limitu monitorování prostředků. 

### <a name="manage-the-maximum-daily-data-volume"></a>Správa maximální denní objem dat 
Následující kroky popisují, jak nakonfigurovat limit Správa objemu dat, která Log Analytics bude ingestovat za den.  

1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. Na **využití a odhadované náklady** stránek pro vybraný pracovní prostor, klikněte na tlačítko **Správa objemu dat** z horní části stránky. 
3. Denní limit je **OFF** ve výchozím nastavení – klikněte na tlačítko **ON** ji povolit, a pak nastavit limit objemu dat v GB za den.<br><br> ![Konfigurace omezení dat log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Pošle upozornění, když denní limit dosažen
Zatímco Představujeme vizuální upozornění na webu Azure Portal, pokud je dodržena prahová hodnota pro omezení vašich dat, toto chování není nutně zarovnat spravujete provozní problémy, které vyžadují okamžitou pozornost.  Pokud chcete dostávat oznámení výstrah, můžete vytvořit nové pravidlo výstrah ve službě Azure Monitor.  Další informace najdete v tématu [vytvoření, zobrazení a Správa výstrah](alerts-metric.md).      

Abyste mohli začít, zde jsou doporučené nastavení pro upozornění:

* Cíl: Vyberte prostředek Log Analytics
* Kritéria: 
   * Název signálu: Vlastní protokol hledání
   * Vyhledávací dotaz: Operace | má-li podrobnosti "nadměrnou.
   * Na základě: Počet výsledků
   * Podmínka: Větší než
   * Prahová hodnota: 0
   * Období: 5 (minuty)
   * Frekvence: 5 (minuty)
* Název pravidla upozornění: Dosáhlo se denního datového limitu
* Závažnost: Upozornění (záv. 1)

Po upozornění je definován a dosáhnete limitu, výstraha se aktivuje a provádí definované ve skupině Akce odpovědi. Dokáže upozorňovat tým prostřednictvím e-mailu a textovými zprávami nebo Automatizujte akce pomocí webhooků, runbooků služby Automation nebo [integraci s externím řešení ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Změnit dobu uchování dat 
Následující kroky popisují, jak nakonfigurovat jak dlouho protokol dat se uchovává ve vašem pracovním prostoru.
 
1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Správa objemu dat**.
5. V podokně s pomocí posuvníku zvyšte nebo snižte počet dní a potom klikněte na tlačítko **OK**.  Pokud používáte *bezplatné* úroveň, nebudete moci upravit dobu uchovávání dat a budete muset upgradovat na placenou úroveň cílem kontrolovat, toto nastavení.<br><br> ![Změna nastavení uchovávání dat pracovního prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Starší verze cenové úrovně

Zákazníci se smlouvou Enterprise podepsaná před 1. červencem 2018 nebo kteří už vytvořili pracovní prostor Log Analytics v rámci předplatného, budete mít dál přístup k *Free* plánu. Pokud vaše předplatné není vázaný na existující registraci smlouvy Enterprise, *Free* úroveň není k dispozici, když vytvoříte pracovní prostor v rámci nového předplatného po 2. dubna 2018.  Je omezený na 7denní doba uchování dat *Free* vrstvy.  Pro starší *samostatné* nebo *na jeden uzel* úrovně, jakož i aktuálním 2018 jednu cenovou úroveň, shromážděných dat je k dispozici za posledních 31 dní. *Free* úroveň má denního limitu příjmu 500 MB, a pokud najdete konzistentně překročení částky povolený objem pracovního prostoru lze změnit na jiný plán, ke shromažďování dat nad tento limit. 

> [!NOTE]
> Chcete-li používat nároky z nákupu OMS E1 Suite, sadu E2 OMS nebo doplňku OMS pro System Center, zvolte Log Analytics *na jeden uzel* cenovou úroveň.

## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Pokud váš pracovní prostor Log Analytics má přístup ke starší verzi cenové úrovně, chcete-li změnit mezi starší verze cenové úrovně:

1. Na webu Azure Portal v podokně předplatná Log Analytics vyberte pracovní prostor.

2. V podokně pracovní prostor v rámci **Obecné**vyberte **cenová úroveň**.  

3. V části **cenová úroveň**, vyberte cenovou úroveň a potom klikněte na tlačítko **vyberte**.  
    ![Vybraná cenový plán](media/manage-cost-storage/workspace-pricing-tier-info.png)

Pokud chcete přesunout do aktuální cenová úroveň pracovního prostoru, budete muset [změnit cenový model ve službě Azure Monitor monitorování vašeho předplatného](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/usage-estimated-costs#moving-to-the-new-pricing-model) který změní cenovou úroveň všech pracovních prostorů v tomto předplatném.

> [!NOTE]
> Pokud je váš pracovní prostor propojený s účtem Automation, musíte před tím, než budete moci vybrat cenovou úroveň *Standalone (za GB)*, odstranit všechna řešení **Automation and Control** a zrušit propojení s účtem Automation. V okně pracovního prostoru v části **Obecné** klikněte na **Řešení**. Zobrazí se řešení a můžete je odstranit. Propojení s účtem Automation zrušíte kliknutím na název účtu Automation v okně **Cenová úroveň**.


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Řešení potíží způsobujících Log Analytics je už shromažďování dat
Pokud jsou na starší verzi cenové úrovně Free a odeslali více než 500 MB dat za den, zastaví shromažďování dat pro zbytek dne. Dosažení denního limitu je běžným důvodem Log Analytics se zastaví shromažďování dat, nebo se zdá být chybějící data.  Log Analytics, vytváří událost typu operace při shromažďování dat spustí a zastaví. Spuštěním následujícího dotazu ve službě search zkontrolujte, jestli jsou dosažení denního limitu a chybějící data: 

`Operation | where OperationCategory == 'Data Collection Status' `

Když se zastaví shromažďování dat, je upozornění stav OperationStatus. Při shromažďování dat začne, stav OperationStatus je proběhlo úspěšně. Následující tabulka popisuje důvody, které zastaví shromažďování dat a navrhovanou akci pokračování shromažďování dat:  

|Zastaví shromažďování důvod| Řešení| 
|-----------------------|---------|
|Dosáhlo se denního limitu starších verzí cenové úrovně Free |Počkejte, až další den pro kolekci k automatickému restartu, nebo změňte na placenou cenovou úroveň.|
|Dosáhlo se denního limitu vašeho pracovního prostoru|Počkejte kolekce k automatickému restartu, nebo zvýšit denní limit objemu dat je popsáno v [spravovat maximální denní objem dat](#manage-the-maximum-daily-volume). Čas obnovení denního limitu se zobrazují na **Správa objemu dat** stránky. |
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Bezplatná zkušební verze skončila<br> Vypršení platnosti Azure passu<br> Měsíční limit útraty dosaženo (například na předplatné MSDN nebo Visual Studio)|Převést na placené předplatné<br> Odeberte limit, nebo počkejte, dokud limit se resetuje|

Která vás upozorní, když se zastaví shromažďování dat, použijte postup popsaný v *vytvořit denním datovém limitu* upozornění, která vás upozorní, když se zastaví shromažďování dat a postupujte podle kroků, pomocí kroků popsaných v přidání akcí, které výstrahy, pravidla konfigurace e-mailu webhook nebo runbook akce pro pravidlo upozornění. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží způsobujících větší využití, než se čekalo
Větší využití je způsobeno jedním nebo obojím z těchto aspektů:
- Do Log Analytics se odesílá více dat, než se čekalo.
- Do Log Analytics odesílá data více uzlů, než se čekalo.

### <a name="data-volume"></a>Objem dat 
Na **využití a odhadované náklady** stránky, *příjem dat podle řešení* graf ukazuje celkový objem dat odesílaných a kolik je odesíláno každé řešení. Díky tomu můžete určit trendy, jako je například, jestli se rozrůstá celkové využití dat (nebo využití podle konkrétního řešení), zbývající konstantní nebo se snižuje. Query sloužící ke generování to je

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Všimněte si, že v klauzuli "kde IsBillable = true" filtruje z určité řešení, pro které neplatí žádné poplatky ingestování datových typů. 

Můžete přejít na trendy v datech najdete konkrétní datové typy, například pokud chcete zkoumat data z důvodu protokoly služby IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Uzlů odesílajících data

Chcete-li pochopit počet počítačů (uzlů), vytváření sestav dat každý den během posledního měsíce, použijte

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

Zobrazíte seznam počítačů odesílajících **účtuje datové typy** (některé typy dat jsou zdarma), využívat [_IsBillable](log-standard-properties.md#isbillable) vlastnost:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

Pomocí těchto `union withsource = tt *` střídmě dotazy jsou nákladné ke spuštění kontrol napříč typres dat data. 

To je možné rozšířit na vrátí počet počítačů za hodinu, které odesílají účtuje datové typy:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

Pokud chcete zobrazit **velikost** účtovaných událostí může ingestovat počítače, použijte `_BilledSize` vlastnost, která poskytuje velikost v bajtech:

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

Tento dotaz nahrazuje starý způsob dotazování to s datovým typem využití. 

Pokud chcete zobrazit **počet** událostí může ingestovat počítače, použijte

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Pokud chcete zobrazit počet účtovaných událostí může ingestovat počítače, použijte 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Pokud chcete vidět, že počet účtovaných datové typy jsou odesílání dat k určitému počítači, použijte:

`union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

> [!NOTE]
> Některá pole datového typu využití, zatímco stále ve schématu, jsou zastaralé a bude, že jejich hodnoty jsou již nejsou naplněny. Jedná se o **počítače** a také související s příjmem pole (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** a **AverageProcessingTimeMs**.

Pokud chcete dostat hlouběji do zdroje dat pro konkrétní datový typ, tady jsou některé užitečné příklady dotazů:

+ Řešení **zabezpečení**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Řešení **pro správu protokolů**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Datový typ **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Datový typ **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Datový typ **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datový typ **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tipy pro snížení objemu dat

Některé návrhy pro snížení objemu shromažďovaných protokolů zahrnují:

| Zdroj velkého objemu dat | Postup snížení objemu dat |
| -------------------------- | ------------------------- |
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [audit registru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změňte shromažďování protokolů prostředků tak, aby se: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | Použijte [cílení na řešení](../insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů. |

### <a name="getting-node-counts"></a>Získávání počty uzlu 

Pokud jste na "Za uzel (OMS)," cenové úrovně, pak se účtují na základě počtu uzlů a řešení používáte, počtu přehledy a analýzy uzly, u kterých se vám účtují se nezobrazí v tabulce na **využití a odhadované náklady**stránky.  

Pokud chcete zobrazit počet různých uzlů, které zabezpečení, můžete dotaz:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Pokud chcete zobrazit počet různých uzlů, které služby Automation, použijte dotaz:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Vytvoření upozornění při větším než očekávaném shromažďování dat
Tato část popisuje postup vytvoření upozornění v těchto případech:
- Objem dat překračuje zadanou velikost.
- Očekává se, že objem dat překročí zadanou velikost.

Upozornění Azure podporují [upozornění protokolu](alerts-unified-log.md) využívající vyhledávací dotazy. 

Následující dotaz vrátí výsledek, pokud se za posledních 24 hodin shromáždilo více než 100 GB dat:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Následující dotaz pomocí jednoduchého vzorce předvídá, jestli dojde k odeslání více než 100 GB dat za den: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Pokud chcete upozornit na jiný objem dat, změňte v dotazech hodnotu 100 na počet GB, na který chcete upozornit.

Pokud chcete být upozorňováni při větším než očekávaném shromažďování dat, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).

Při vytváření upozornění pro první dotaz (více než 100 GB dat během 24 hodin) nastavte:  

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *1440* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Při vytváření upozornění pro druhý dotaz (předpověď, že během 24 hodin bude shromážděno více než 100 GB dat) nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *180* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Očekávaný větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Pokud obdržíte upozornění, pomocí kroků v následující části můžete řešit potíže způsobující větší využití, než se čekalo.

## <a name="next-steps"></a>Další postup
* V tématu [Prohledávání protokolů v Log Analytics](../log-query/log-query-overview.md) zjistíte, jak používat jazyk vyhledávání. Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
* Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).
* Použijte [cílení na řešení](../insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů.
* Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí zabezpečení, přečtěte si téma popisující [zásady filtrování v Azure Security Center](../../security-center/security-center-enable-data-collection.md).
* Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md).
* Pokud chcete upravit nastavení shromažďování událostí, přečtěte si téma popisující [konfiguraci protokolu událostí](data-sources-windows-events.md).
* Pokud chcete upravit nastavení shromažďování syslogu, přečtěte si téma popisující [konfiguraci syslogu](data-sources-syslog.md).

