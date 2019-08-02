---
title: Správa využití a nákladů na protokoly Azure Monitor | Microsoft Docs
description: Přečtěte si, jak změnit cenový plán a spravovat zásady pro objem dat a uchovávání dat pro Log Analytics pracovní prostor v Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 5e325f7766e7b0d9764949eb3fbf9753d65db8b3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619392"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Správa využití a nákladů pomocí protokolů Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak řídit náklady v Azure Monitor nastavením doby uchovávání dat pro pracovní prostor Log Analytics.  Související informace najdete v následujícím článku.
> - [Monitorování využití a odhadované náklady](usage-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Protokoly Azure Monitor jsou určené ke škálování a podpoře shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje v podniku nebo v Azure, které jsou nasazené v Azure.  To může být hlavním důvod pro vaši organizaci, nákladovou efektivnost v konečném důsledku je základní ovladač. V tomto případě je důležité pochopit, že náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, ale také závisí na vybraném plánu a na tom, jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku jsme zkontrolujte, jak můžete aktivně monitorovat růst objemu a úložiště dat a definování omezení pro řízení těchto související náklady. 

Náklady na data může být značné, v závislosti na následujících faktorech: 

- Objem dat vygenerovaných a přijatých v pracovním prostoru 
    - Počet povolených řešení pro správu
    - Počet monitorovaných systémů
    - Typ dat shromažďovaných z každého monitorovaného prostředku 
- Doba, po kterou se rozhodnete zachovat data 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Pochopte využití a odhadované náklady v pracovním prostoru

Protokoly Azure Monitor umožňují snadno pochopit, jaké náklady jsou pravděpodobně založené na nedávných vzorech použití. K tomu použijte **Log Analytics využití a odhadované náklady** na kontrolu a analýzu využití dat. Ukazuje, kolik dat shromažďují jednotlivá řešení, kolik dat je uchovávané a odhad nákladů na základě objemu dat přijatých a jakékoli další uchování nad rámec objemu zahrnutého v ceně.

![Využití a odhadované náklady](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Prozkoumat data podrobněji, klikněte na ikonu v horní části napravo od buď z grafů v **využití a odhadované náklady** stránky. Teď můžete pracovat s Tento dotaz a prozkoumejte podrobnosti o využití.  

![Zobrazení protokolů](media/manage-cost-storage/logs.png)

Na stránce **využití a odhadované náklady** si můžete prohlédnout svůj objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávají ve vašem pracovním prostoru Log Analytics.  Kliknutím na **Podrobnosti o využití** v horní části stránky zobrazíte řídicí panel využití s informacemi o trendech objemu dat podle zdroje, počítačů a nabídky. K zobrazení a nastavte denní limit nebo dobu uchování upravit, klikněte na tlačítko **Správa objemu dat**.
 
Poplatky za log Analytics se přidají do vašeho vyúčtování služeb Azure. Můžete si prohlédnout podrobnosti Azure fakturovat části fakturace na portálu Azure Portal nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Denní limit

Můžete nakonfigurovat denní limit a omezit tak denní příjem dat pro váš pracovní prostor, ale použijte pozornost jak vaším cílem by neměl být k dosažení denního limitu.  V opačném případě můžete přijít o data po zbytek dne, který může mít vliv na ostatní služby Azure a řešení, jehož funkce může záviset na aktuální data, která je k dispozici v pracovním prostoru.  V důsledku toho možnost sledovat a přijímat upozorní, když se to týká podmínky stavu prostředků podporující IT služby.  Denní limit je určený k použití jako způsob, jak spravovat neočekávané zvýšení objemu dat ze spravovaných prostředků a zůstat v rámci vašeho limitu, nebo pokud chcete omezit neplánované poplatky pro váš pracovní prostor.  

Po dosažení denního limitu pro zbytek dne zastaví kolekci fakturovatelné datových typů. Zobrazí se upozornění banner v horní části stránky pro vybraný pracovní prostor Log Analytics a operace událost je odeslána do *operace* tabulky v rámci **LogManagement** kategorie. Shromažďování dat bude pokračovat po resetování času definované v části *denní limit se nastaví na*. Doporučujeme definovat pravidlo výstrahy založené na této události operace nakonfigurovaný tak, aby upozornit po dosažení denního datového limitu. 

> [!NOTE]
> Denní limit nezastaví shromažďování dat z Azure Security Center.

### <a name="identify-what-daily-data-limit-to-define"></a>Identifikujte jaké denního limitu pro definování dat

Kontrola [využití Log Analytics a odhadované náklady](usage-estimated-costs.md) pochopit trend příjem dat a k čemu slouží k definování denní limit objemu. Je nutné zvážit opatrně, protože nebude možné po dosažení limitu monitorování prostředků. 

### <a name="manage-the-maximum-daily-data-volume"></a>Správa maximální denní objem dat

Následující postup popisuje, jak nakonfigurovat limit pro správu objemu dat, který Log Analytics pracovní prostor ingestovat za den.  

1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. Na **využití a odhadované náklady** stránek pro vybraný pracovní prostor, klikněte na tlačítko **Správa objemu dat** z horní části stránky. 
3. Denní limit je **OFF** ve výchozím nastavení – klikněte na tlačítko **ON** ji povolit, a pak nastavit limit objemu dat v GB za den.

    ![Log Analytics konfiguraci omezení dat](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Výstraha při denním limitu

Zatímco Představujeme vizuální upozornění na webu Azure Portal, pokud je dodržena prahová hodnota pro omezení vašich dat, toto chování není nutně zarovnat spravujete provozní problémy, které vyžadují okamžitou pozornost.  Pokud chcete dostávat oznámení výstrah, můžete vytvořit nové pravidlo výstrah ve službě Azure Monitor.  Další informace najdete v tématu [jak vytvářet, zobrazovat a spravovat výstrahy](alerts-metric.md).

Abyste mohli začít, zde jsou doporučené nastavení pro upozornění:

- Cíl: Vyberte prostředek Log Analytics
- Kritéria: 
   - Název signálu: Vlastní prohledávání protokolu
   - Vyhledávací dotaz: Operace | kde detail má ' překročení kvóty '
   - Založené na: Počet výsledků
   - Podmínka: Je větší než
   - Prahová hodnota: 0
   - Hodin 5 (min.)
   - Opakování 5 (min.)
- Název pravidla výstrahy: Dosáhlo se denního limitu dat.
- Závažnost: Upozornění (závažnost 1)

Po upozornění je definován a dosáhnete limitu, výstraha se aktivuje a provádí definované ve skupině Akce odpovědi. Dokáže upozorňovat tým prostřednictvím e-mailu a textovými zprávami nebo Automatizujte akce pomocí webhooků, runbooků služby Automation nebo [integraci s externím řešení ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Změnit dobu uchování dat

Následující kroky popisují, jak nakonfigurovat jak dlouho protokol dat se uchovává ve vašem pracovním prostoru.
 
1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Správa objemu dat**.
3. V podokně s pomocí posuvníku zvyšte nebo snižte počet dní a potom klikněte na tlačítko **OK**.  Pokud používáte *bezplatné* úroveň, nebudete moci upravit dobu uchovávání dat a budete muset upgradovat na placenou úroveň cílem kontrolovat, toto nastavení.

    ![Změnit nastavení uchovávání dat pracovního prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Uchování je také možné [nastavit přes ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí `dataRetention` parametru. Pokud navíc nastavíte uchovávání dat na 30 dní, můžete spustit okamžitou mazání starších dat pomocí `immediatePurgeDataOn30Days` parametru, který může být užitečný pro scénáře související s dodržováním předpisů. Tato funkce se zveřejňuje jenom přes ARM. 

## <a name="legacy-pricing-tiers"></a>Starší cenové úrovně

Předplatná, která měla Log Analytics pracovní prostor nebo prostředek Application Insights před 2. dubna 2018 nebo jsou propojená s smlouva Enterprise, které začaly před 1. února 2019, budou mít nadále přístup k používání starší cenové úrovně: **Free**, **Standalone (za GB)** a **per Node (OMS)** .  Pracovní prostory v bezplatné cenové úrovni budou mít denní příjem dat omezený na 500 MB (kromě datových typů zabezpečení shromažďovaných v Azure Security Center) a uchovávání dat je omezeno na 7 dní. Cenová úroveň Free je určena pouze pro účely vyhodnocení. Pracovní prostory v cenové úrovni samostatného nebo jednotlivých uzlů mají uživatelsky konfigurovatelné uchovávání až po 2 roky. 

Pracovní prostory vytvořené před dubna 2016 mají také přístup k původním cenovým úrovním **Standard** a **Premium** , které mají pevnou dobu uchovávání dat na 30 a 365 dnů v uvedeném pořadí. Nové pracovní prostory nelze vytvořit v cenové úrovni **Standard** nebo **Premium** a pokud je pracovní prostor přesunut z těchto úrovní, nelze jej přesunout zpět. 

Další podrobnosti o omezeních cenové úrovně jsou k dispozici [zde](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo doplňku OMS pro System Center, vyberte cenovou úroveň Log Analytics pro *jednotlivé uzly* .


## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Pokud má váš Log Analytics pracovní prostor přístup ke starším cenovým úrovním, aby se mohlo změnit mezi staršími cenovými úrovněmi:

1. Na webu Azure Portal v podokně předplatná Log Analytics vyberte pracovní prostor.

2. V podokně pracovní prostor v rámci **Obecné**vyberte **cenová úroveň**.  

3. V části **cenová úroveň**, vyberte cenovou úroveň a potom klikněte na tlačítko **vyberte**.  
    ![Vybraná cenový plán](media/manage-cost-storage/workspace-pricing-tier-info.png)

Cenovou úroveň můžete také [nastavit přes ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí `ServiceTier` parametru. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Řešení potíží s tím, proč Log Analytics už neshromažďuje data

Pokud používáte starší verzi bezplatné cenové úrovně a v den jste poslali více než 500 MB dat, zastaví se shromažďování dat pro zbytek dne. Dosažení denního limitu je běžným důvodem Log Analytics se zastaví shromažďování dat, nebo se zdá být chybějící data.  Log Analytics, vytváří událost typu operace při shromažďování dat spustí a zastaví. Spuštěním následujícího dotazu v hledání ověřte, jestli se vám dosáhlo denního limitu a chybějících dat: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Po zastavení shromažďování dat je stav operationstatus **Upozornění**. Když se spustí shromažďování dat, stav operationstatus se **podařilo**. Následující tabulka popisuje důvody, které zastaví shromažďování dat a navrhovanou akci pokračování shromažďování dat:  

|Zastaví shromažďování důvod| Řešení| 
|-----------------------|---------|
|Dosáhlo se denního limitu starší verze bezplatné cenové úrovně. |Počkejte, až další den pro kolekci k automatickému restartu, nebo změňte na placenou cenovou úroveň.|
|Dosáhlo se denního limitu pracovního prostoru.|Počkejte na automatické restartování kolekce nebo zvyšte počet denních objemů dat popsaných v tématu Správa maximálního denního objemu dat. Doba obnovení denního limitu se zobrazí na stránce **Správa objemu dat** . |
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Bezplatná zkušební verze skončila<br> Vypršení platnosti Azure passu<br> Měsíční limit útraty dosaženo (například na předplatné MSDN nebo Visual Studio)|Převést na placené předplatné<br> Odeberte limit, nebo počkejte, dokud limit se resetuje|

Chcete-li být upozorněni na zastavení shromažďování dat, postupujte podle kroků popsaných v části *Vytvoření výstrahy denního datového zakončení* , která bude oznámena při zastavení shromažďování dat. Pomocí kroků popsaných v tématu [Vytvoření skupiny akcí](action-groups.md) nakonfigurujte akci e-mailu, Webhooku nebo Runbooku pro pravidlo výstrahy. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží způsobujících větší využití, než se čekalo

Větší využití je způsobeno jedním nebo obojím z těchto aspektů:
- Více uzlů, než se čekalo na odesílání dat do Log Analytics pracovního prostoru
- Víc dat, než se čekalo, že se posílá do Log Analytics pracovního prostoru

## <a name="understanding-nodes-sending-data"></a>Porozumění uzlům odesílajícím data

Pro pochopení počtu počítačů hlásících prezenční signály každý den během posledního měsíce použijte

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Pokud chcete získat seznam počítačů, které se budou fakturovat jako uzly, pokud je pracovní prostor ve starší verzi na cenové úrovni uzlů, hledejte uzly, které odesílají **účtované datové typy** (některé datové typy jsou zdarma). K tomu použijte `_IsBillable` [vlastnost](log-standard-properties.md#_isbillable) a použijte pole s plně kvalifikovaným názvem domény v levém krajním poli. Vrátí se seznam počítačů s fakturovanými daty:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Počet fakturovaných uzlů, které vidíte, se dají odhadnout takto: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Tyto `union withsource = tt *` dotazy můžete použít zřídka, protože kontroly napříč datovými typy jsou náročné na spouštění. Tento dotaz nahrazuje starý způsob, jak zadávat dotazy na informace o počítačích s datovým typem využití.  

Přesnější výpočet toho, co se skutečně bude účtovat, je získání počtu počítačů za hodinu, které odesílají Fakturovatelné datové typy. (Pro pracovní prostory ve starších cenových úrovních jednotlivých uzlů Log Analytics vypočítá počet uzlů, které se musí účtovat po hodinách.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Principy ingestných objemů dat

Na **využití a odhadované náklady** stránky, *příjem dat podle řešení* graf ukazuje celkový objem dat odesílaných a kolik je odesíláno každé řešení. Díky tomu můžete určit trendy, jako je například, jestli se rozrůstá celkové využití dat (nebo využití podle konkrétního řešení), zbývající konstantní nebo se snižuje. Query sloužící ke generování to je

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Všimněte si, že v klauzuli "kde IsBillable = true" filtruje z určité řešení, pro které neplatí žádné poplatky ingestování datových typů. 

Můžete přejít na trendy v datech najdete konkrétní datové typy, například pokud chcete zkoumat data z důvodu protokoly služby IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Objem dat podle počítače

Chcete-li zobrazit **Velikost** fakturovaných událostí, které jsou v jednotlivých počítačích `_BilledSize` k dispozici, použijte [vlastnost](log-standard-properties.md#_billedsize), která poskytuje velikost v bajtech:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

Vlastnost určuje, jestli se za ingestovaná data účtují poplatky. [](log-standard-properties.md#_isbillable) `_IsBillable`

Chcete-li zobrazit počet **fakturovaných událostí** zpracovaných na počítač, použijte 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Pokud chcete vidět, že počet účtovaných datové typy jsou odesílání dat k určitému počítači, použijte:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Objem dat podle prostředku Azure, skupiny prostředků nebo předplatného

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** fakturovaných událostí, které se přiřadí __na jeden počítač__, pomocí [vlastnosti](log-standard-properties.md#_resourceid)_ResourceId, která poskytuje úplnou cestu k prostředku:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** fakturovaných událostí `_ResourceId` , které se ingestují pro __každé předplatné Azure__, analyzovat vlastnost jako:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Změnou `subscriptionId` na`resourceGroup` se zobrazí fakturovatelný příjmový objem dat skupinou prostředků Azure. 


> [!NOTE]
> Některá pole datového typu použití, ale stále ve schématu, jsou zastaralá a jejich hodnoty se už neplní. Jedná se o **počítače** a také související s příjmem pole (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** a **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Dotazování na Common data types

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
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [audit registru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změňte shromažďování protokolů prostředků tak, aby se: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | Použijte [cílení na řešení](../insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů. |

### <a name="getting-security-and-automation-node-counts"></a>Načítají se počty uzlů zabezpečení a automatizace.

Pokud jste na "Za uzel (OMS)," cenové úrovně, pak se účtují na základě počtu uzlů a řešení používáte, počtu přehledy a analýzy uzly, u kterých se vám účtují se nezobrazí v tabulce na **využití a odhadované náklady**stránky.  

Pokud chcete zobrazit počet různých uzlů, které zabezpečení, můžete dotaz:

```kusto
union
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
| count
```

Pokud chcete zobrazit počet různých uzlů, které služby Automation, použijte dotaz:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Vytvořit výstrahu, když je shromažďování dat vysoké

Tato část popisuje postup vytvoření upozornění v těchto případech:
- Objem dat překračuje zadanou velikost.
- Očekává se, že objem dat překročí zadanou velikost.

Upozornění Azure podporují [upozornění protokolu](alerts-unified-log.md) využívající vyhledávací dotazy. 

Následující dotaz vrátí výsledek, pokud se za posledních 24 hodin shromáždilo více než 100 GB dat:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

Následující dotaz pomocí jednoduchého vzorce předvídá, jestli dojde k odeslání více než 100 GB dat za den: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

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

## <a name="limits-summary"></a>Souhrn omezení

Existují další limity Log Analytics, některé z nich závisí na cenové úrovni Log Analytics. Ty jsou popsány [zde](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Další postup

- Další informace o použití vyhledávacího jazyka najdete [v tématu prohledávání protokolů v protokolu Azure monitor](../log-query/log-query-overview.md) . Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
- Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).
- Použijte [cílení na řešení](../insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů.
- Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí, Projděte si téma [Zásady filtrování Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md).
- Pokud chcete upravit nastavení shromažďování událostí, přečtěte si téma popisující [konfiguraci protokolu událostí](data-sources-windows-events.md).
- Pokud chcete upravit nastavení shromažďování syslogu, přečtěte si téma popisující [konfiguraci syslogu](data-sources-syslog.md).