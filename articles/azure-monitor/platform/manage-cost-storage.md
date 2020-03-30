---
title: Správa využití a nákladů pro protokoly monitorování Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak změnit cenový plán a spravovat zásady objemu dat a uchovávání informací pro váš pracovní prostor Log Analytics ve službě Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 6911afa5dfcd14f9e5d1068acbcb2355200c5545
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479803"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Správa využití a nákladů pomocí protokolů azure monitoru

> [!NOTE]
> Tento článek popisuje, jak pochopit a řídit vaše náklady na protokoly monitorování Azure. Související článek [Sledování využití a odhadované náklady](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) popisuje, jak zobrazit využití a odhadované náklady napříč několika funkcemi monitorování Azure pro různé cenové modely.

> [!NOTE]
> Všechny ceny a náklady uvedené v tomto článku jsou například pouze pro účely. 

Protokoly monitorování Azure jsou navržené tak, aby škálovaly a podporovaly shromažďování, indexování a ukládání obrovského množství dat denně z libovolného zdroje ve vašem podniku nebo nasazených v Azure.  I když to může být primární ovladač pro vaši organizaci, efektivita nákladů je nakonec základní ovladač. Za tímto účelem je důležité si uvědomit, že náklady na pracovní prostor Log Analytics nejsou založeny pouze na objemu shromážděných dat, je také závislá na vybraném plánu a jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku zkontrolujeme, jak můžete proaktivně sledovat nárůst objemu ingovaných dat a úložiště, a definujeme limity pro řízení těchto souvisejících nákladů. 

## <a name="pricing-model"></a>Cenový model

Výchozí ceny pro Log Analytics je model **průběžných plateb** založený na objemu dat, který je ingedován, a volitelně pro delší uchovávání dat. Objem dat se měří jako velikost dat, která budou uložena. Každý pracovní prostor Log Analytics se účtuje jako samostatná služba a přispívá k účtu za vaše předplatné Azure. Množství požití dat může být značné v závislosti na následujících faktorech: 

  - Počet povolených řešení pro správu a jejich konfigurace (např. 
  - Počet monitorovaných virtuálních mích
  - Typ dat shromážděných z každého sledovaného virtuálního montova. 
  
Kromě modelu průběžných plateb má služba Log Analytics úrovně **rezervace kapacity,** které umožňují uložit až 25 % ve srovnání s průběžnou cenou průběžných plateb. Cena za rezervaci kapacity vám umožní zakoupit rezervaci od 100 GB/den. Veškeré využití nad úrovní rezervace bude účtováno podle sazby průběžných plateb. Úrovně rezervace kapacity mají 31denní období závazku. Během období závazku můžete přejít na vyšší úroveň rezervace kapacity (která restartuje 31denní období závazku), ale nemůžete se vrátit zpět na úroveň průběžných plateb nebo na nižší úroveň rezervace kapacity až po uplynutí období závazku. Dokončení. 
[Další informace](https://azure.microsoft.com/pricing/details/monitor/) o průběžných platbách analýzy protokolů a cenách rezervací kapacity. 

Ve všech cenových úrovních se objem dat vypočítá z řetězcové reprezentace dat, která jsou připravena k uložení. Do výpočtu velikosti události není zahrnuto několik [vlastností společných pro všechny datové typy,](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) včetně `_ResourceId`, `_ItemId`, `_IsBillable` a `_BilledSize`.

Všimněte si také, že některá řešení, jako je [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) a Azure [Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), mají svůj vlastní cenový model. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí 

Pokud ještě nepoužíváte protokoly monitorování Azure, můžete pomocí [cenové kalkulačky Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Log Analytics. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou dlaždici Azure Monitor. Posuňte se dolů po stránce na Azure Monitor a v rozevíracím seznamu Typ vyberte Log Analytics.  Tady můžete zadat počet virtuálních připojení a GB dat, která očekáváte, že budete shromažďovat z každého virtuálního účtu. Obvykle 1 až 3 GB datového měsíce se ingestuje z typického virtuálního počítače Azure. Pokud už vyhodnocujete protokoly azure monitoru, můžete použít statistiky dat z vlastního prostředí. Níže naleznete postup určení [počtu monitorovaných virtuálních počítačů](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) a [objemu dat, která pracovní prostor ingestuje](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení využití a odhad nákladů

Pokud teď používáte protokoly monitorování Azure, je snadné pochopit, jaké náklady jsou pravděpodobně založeny na nedávných vzorcích využití. Chcete-li to provést, použijte **využití analýzy protokolů a odhadované náklady** ke kontrole a analýze využití dat. To ukazuje, kolik dat je shromažďováno jednotlivými řešeními, kolik dat je uchováno a odhad nákladů na základě množství pozůstalých dat a jakékoli další uchovávání nad rámec zahrnuté částky.

![Využití a odhadované náklady](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Chcete-li data prozkoumat podrobněji, klikněte na ikonu v pravém horním rohu některého z grafů na stránce **Využití a Odhadované náklady.** Nyní můžete pracovat s tímto dotazem a prozkoumat další podrobnosti o vašem použití.  

![Zobrazení protokolů](media/manage-cost-storage/logs.png)

Na stránce **Využití a Odhadované náklady** můžete zkontrolovat objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávaná v pracovním prostoru Log Analytics.  Kliknutím na **Podrobnosti o využití** v horní části stránky zobrazíte řídicí panel využití s informacemi o trendech objemu dat podle zdroje, počítačů a nabídky. Chcete-li zobrazit a nastavit denní limit nebo upravit dobu uchování, klepněte na tlačítko **Správa objemu dat**.
 
Poplatky za analýzu protokolů se přidají k vaší vyúčtování Azure. Podrobnosti o účtu Azure najdete v části Fakturace na webu Azure Portal nebo na [fakturačním portálu Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Zobrazení využití analýzy protokolů na vaší vyúčtování Azure 

Azure poskytuje velké množství užitečných funkcí v centru [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Například funkce "Analýza nákladů" umožňuje zobrazit vaše výdaje za prostředky Azure. Přidání filtru podle typu prostředku (do microsoft.operationalinsights/workspace pro Log Analytics) vám umožní sledovat výdaje.

Větší pochopení vašeho využití lze získat [stažením využití z webu Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Ve stažené tabulce uvidíte využití na prostředek Azure (např. pracovní prostor Log Analytics) za den. V této tabulce aplikace Excel lze využití z pracovních prostorů Analýzy protokolů nalézt tak, že nejprve přefiltrujete ve sloupci Kategorie měřiče a zobrazíse funkce Přehledy a analýzy (používané některými staršími cenovými úrovněmi) a "Log Analytics" a potom přidáte filtr na "Instance" ID", který je "obsahuje pracovní prostor". Využití je zobrazeno ve sloupci "Spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou další podrobnosti, které vám pomohou [pochopit vaši fakturu za Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Chcete-li změnit cenovou úroveň Log Analytics vašeho pracovního prostoru, 

1. Na webu Azure Portal otevřete **možnost Využití a odhadované náklady** z vašeho pracovního prostoru, kde se zobrazí seznam jednotlivých cenových úrovní, které jsou k dispozici pro tento pracovní prostor.

2. Zkontrolujte odhadované náklady pro každou z cenových úrovní. Tento odhad je založen na posledních 31 dnech používání, takže tento odhad nákladů závisí na tom, že posledních 31 dní je reprezentativní pro typické použití. V níže uvedeném příkladu můžete vidět, jak by tento pracovní prostor na základě datových vzorů za posledních 31 dní stál méně na úrovni průběžných plateb (#1) ve srovnání s úrovní 100 GB/den rezervace kapacity (#2).  

    ![Cenové úrovně](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Pokud se po kontrole odhadovaných nákladů na základě posledních 31 dnů používání rozhodnete změnit cenovou úroveň, klikněte na **vybrat**.  

Cenovou [úroveň](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) můžete nastavit taky přes `sku` Azure`pricingTier` Resource Manager pomocí parametru (v šabloně Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Starší cenové úrovně

Předplatná, kteří měli pracovní prostor Log Analytics nebo prostředek Application Insights před **2.** **Free** **Standalone (Per GB)**  Pracovní prostory v cenové úrovni Free budou mít denní ingestování dat omezené na 500 MB (s výjimkou typů dat zabezpečení shromážděných službou Azure Security Center) a uchovávání dat je omezeno na 7 dní. Cenová úroveň Free je určena pouze pro účely hodnocení. Pracovní prostory v cenových úrovních Samostatné nebo Podle uzlu mají uživatelem konfigurovatelné uchovávání až 2 roky. 

Pracovní prostory vytvořené před dubnem 2016 mají také přístup k původním cenovým úrovním **Standard** a **Premium,** které mají pevné uchovávání dat 30 a 365 dní. Nové pracovní prostory nelze vytvořit v cenové úrovně **Standard** nebo **Premium** a pokud je pracovní prostor přesunut z těchto úrovní, nelze jej přesunout zpět. 

Další podrobnosti o omezení cenové úrovně jsou k dispozici [zde](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Chcete-li použít nároky, které pocházejí z nákupu OMS E1 Suite, OMS E2 Suite nebo OMS Add-On pro System Center, zvolte Log Analytics *per Node* cenové úrovně.

## <a name="change-the-data-retention-period"></a>Změna doby uchovávání dat

Následující kroky popisují, jak nakonfigurovat, jak dlouho jsou data protokolu uchovávána ve vašem pracovním prostoru.

### <a name="default-retention"></a>Výchozí uchovávání informací

Chcete-li nastavit výchozí uchovávání informací pro pracovní prostor, 
 
1. Na webu Azure Portal vyberte z pracovního prostoru **využití a odhadované náklady** v levém podokně.
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Správa objemu dat**.
3. V podokně posunutím jezdce zvětšete nebo snižte počet dní a klepněte na tlačítko **OK**.  Pokud jste na *volné* úrovni, nebudete moci upravit dobu uchovávání dat a budete muset upgradovat na placenou úroveň, abyste mohli řídit toto nastavení.

    ![Změna nastavení uchovávání dat v pracovním prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Uchovávání informací lze také [nastavit prostřednictvím Správce prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí parametru. `retentionInDays` Navíc pokud nastavíte uchovávání dat na 30 dní, můžete aktivovat `immediatePurgeDataOn30Days` okamžité vymazání starších dat pomocí parametru, což může být užitečné pro scénáře související s dodržováním předpisů. Tato funkce je vystavena jenom prostřednictvím Správce prostředků Azure. 

Dva datové `Usage` typy `AzureActivity` – a -- jsou ve výchozím nastavení uchovávány po dobu 90 dnů a za toto 90denní uchovávání se neúčtují žádné poplatky. Tyto datové typy jsou také bez poplatků za přijím dat. 

### <a name="retention-by-data-type"></a>Uchovávání podle typu dat

Je také možné zadat různá nastavení uchovávání pro jednotlivé datové typy. Každý datový typ je dílčím zdrojem pracovního prostoru. Například tabulku SecurityEvent lze ve [Správci prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) adresovat jako:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Všimněte si, že datový typ (tabulka) je malá a velká písmena.  Chcete-li získat aktuální nastavení uchovávání podle typu dat určitého datového typu (v tomto příkladu SecurityEvent), použijte:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Chcete-li získat aktuální nastavení uchovávání podle typu dat pro všechny datové typy v pracovním prostoru, stačí vynechat konkrétní datový typ, například:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Chcete-li nastavit uchovávání určitého datového typu (v tomto příkladu SecurityEvent) na 730 dní,

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Datové `Usage` `AzureActivity` typy a nelze nastavit s vlastní uchovávání. Budou trvat maximálně výchozí uchovávání pracovního prostoru nebo 90 dní. 

Skvělý nástroj pro připojení přímo ke Správci prostředků Azure pro nastavení uchovávání podle typu dat je nástroj OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Další informace o ARMclient z článků [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) a [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Zde je příklad pomocí ARMClient, nastavení SecurityEvent data na 730 dnů uchovávání:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Nastavení uchovávání u jednotlivých datových typů lze použít ke snížení nákladů na uchovávání dat.  U dat shromážděných od října 2019 (kdy byla tato funkce vydána) může snížení uchování u některých datových typů časem snížit náklady na uchovávání dat.  U dat shromážděných dříve nemá nastavení nižšího uchovávání informací pro jednotlivé typy vliv na náklady na uchovávání.  

## <a name="manage-your-maximum-daily-data-volume"></a>Správa maximálního denního objemu dat

Můžete nakonfigurovat denní limit a omezit denní požití pro váš pracovní prostor, ale buďte opatrní, protože vaším cílem by nemělo být dosažení denního limitu.  V opačném případě ztratíte data pro zbytek dne, což může mít vliv na další služby Azure a řešení, jejichž funkce může záviset na aktuální data jsou k dispozici v pracovním prostoru.  V důsledku toho je ovlivněna vaše schopnost sledovat a přijímat výstrahy, pokud jsou ovlivněny zdravotní podmínky prostředků podporujících služby IT.  Denní limit je určen k použití jako způsob, jak spravovat neočekávané zvýšení objemu dat ze spravovaných prostředků a zůstat v limitu, nebo pokud chcete omezit neplánované poplatky za váš pracovní prostor.  

Po dosažení denního limitu se shromažďování fakturovatelných datových typů zastaví po zbytek dne. V horní části stránky pro vybraný pracovní prostor Analýzy protokolů se zobrazí výstražný nápis a událost operace je odeslána do tabulky *Operace* v části **Správa protokolu.** Shromažďování dat pokračuje po obnovení času definovaného v *části Denní limit bude nastavena na*. Doporučujeme definovat pravidlo výstrahy na základě této události operace, nakonfigurované tak, aby upozorňovalo na dosažení denního datového limitu. 

> [!NOTE]
> Denní limit nezastaví shromažďování dat z Azure Security Center, s výjimkou pracovních prostorů, ve kterých azure security center byla nainstalována před 19 června 2017. 

> [!NOTE]
> Latence vlastní použití denního limitu může znamenat, že limit není použit přesně jako zadaná denní úroveň limitu. 

### <a name="identify-what-daily-data-limit-to-define"></a>Určení denního datového limitu, který chcete definovat

Zkontrolujte [využití analýzy protokolů a odhadované náklady,](usage-estimated-costs.md) abyste pochopili trend přihlašování dat a co je denní limit svazku, který chcete definovat. To by mělo být považováno za opatrně, protože nebudete moci sledovat své zdroje po dosažení limitu. 

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

Následující kroky popisují, jak nakonfigurovat limit pro správu objemu dat, které bude pracovní prostor Analýzy protokolů ingestovat za den.  

1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. Na stránce **Využití a odhadované náklady** pro vybraný pracovní prostor klikněte v horní části stránky na Správa objemu **dat.** 
3. Denní limit je ve výchozím nastavení **vypnutý,** klikněte **na ZAPNUTO,** abyste ho povolili, a pak nastavte limit svazku dat v GB/den.

    ![Služba Log Analytics konfiguruje datový limit](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Výstraha při dosažení denního limitu

I když prezentujeme vizuální upozornění na webu Azure Portal, když je splněna prahová hodnota limitu dat, toto chování nemusí nutně odpovídat tomu, jak spravujete provozní problémy vyžadující okamžitou pozornost.  Chcete-li dostávat upozornění, můžete vytvořit nové pravidlo výstrahy v Azure Monitor.  Další informace naleznete v [tématu vytváření, zobrazení a správy výstrah](alerts-metric.md).

Chcete-li začít, zde jsou doporučená nastavení výstrahy:

- Cíl: Vyberte zdroj Log Analytics.
- Kritéria: 
   - Název signálu: Vlastní hledání protokolu
   - Vyhledávací dotaz: Operace | kde detail má 'OverQuota'
   - Na základě: Počet výsledků
   - Podmínka: Větší než
   - Práh: 0
   - Období: 5 (minuty)
   - Frekvence: 5 (minuty)
- Název pravidla výstrahy: Bylo dosaženo denního limitu dat
- Závažnost: Varování (Sev 1)

Jakmile je výstraha definována a je dosaženo limitu, aktivuje se výstraha a provede odpověď definovanou ve skupině akcí. Může informovat váš tým prostřednictvím e-mailu a textových zpráv nebo automatizovat akce pomocí webhooků, runbooků Automation nebo [integrace s externím řešením ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží způsobujících větší využití, než se čekalo

Větší využití je způsobeno jedním nebo obojím z těchto aspektů:
- Více uzlů, než bylo očekáváno odesílání dat do pracovního prostoru Log Analytics
- Do pracovního prostoru Log Analytics je odesíláno více dat, než se očekávalo (pravděpodobně kvůli tomu, že začínáte používat nové řešení nebo změnu konfigurace existujícího řešení)

## <a name="understanding-nodes-sending-data"></a>Principy odesílání dat uzlů

Chcete-li porozumět počtu uzlů, které hlásí prezenční signály od agenta každý den v posledním měsíci, použijte

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Získat počet uzlů odesílání dat za posledních 24 hodin pomocí dotazu: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Chcete-li získat seznam uzlů odesílajících všechna data (a množství dat odeslaných každým z nich), lze použít následující dotaz:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!NOTE]
> Používejte `union withsource = tt *` tyto dotazy střídmě jako prohledávání napříč datovými typy jsou nákladné spustit. Tento dotaz nahrazuje starý způsob dotazování na informace na počítač datovým typem Usage.  

## <a name="understanding-ingested-data-volume"></a>Principy objemu ingedu dat

Na stránce **Využití a odhadované náklady** zobrazuje graf při *lání dat na řešení* celkový objem odeslaných dat a kolik odesílá každé řešení. To vám umožní určit trendy, například zda celkové využití dat (nebo využití konkrétním řešením) roste, zůstává stabilní nebo klesá. 

### <a name="data-volume-for-specific-events"></a>Objem dat pro konkrétní události

Chcete-li se podívat na velikost ingestovaných dat pro určitou sadu událostí, můžete zadat dotaz na konkrétní tabulku (v tomto příkladu) `Event`a pak omezit dotaz na události, které vás zajímají (v tomto příkladu ID události 5145 nebo 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Všimněte si, že klauzule `where IsBillable = true` filtruje datové typy z určitých řešení, pro které není účtován poplatek za ingestování. 

### <a name="data-volume-by-solution"></a>Objem dat podle řešení

Dotaz použitý k zobrazení fakturovatelného objemu dat podle řešení za poslední měsíc (s výjimkou posledního částečného dne) je:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Klauzule `TimeGenerated` s je pouze zajistit, že prostředí dotazů na portálu Azure bude hledět zpět za výchozí 24 hodin. Při použití datového `StartTime` typu `EndTime` Využití a představují časové intervaly, pro které jsou prezentovány výsledky. 

### <a name="data-volume-by-type"></a>Objem dat podle typu

Můžete přejít k podrobnostem a zobrazit trendy dat podle typu dat:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Nebo chcete-li zobrazit tabulku podle řešení a zadejte za poslední měsíc,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Objem dat podle počítače

Datový `Usage` typ neobsahuje informace na úrovni completer. Chcete-li zobrazit **velikost** ingtovaných dat `_BilledSize` na počítač, použijte [vlastnost](log-standard-properties.md#_billedsize), která poskytuje velikost v bajtů:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Vlastnost](log-standard-properties.md#_isbillable) určuje, zda budou z ingestovaná data účtovány poplatky.

Chcete-li zobrazit **počet** fakturovatelných událostí požitých v počítači, použijte 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Objem dat podle prostředků, skupiny prostředků nebo předplatného Azure

Pro data z uzlů hostovaných v Azure můžete získat **velikost** ingdeed data __na počítač__, použijte [vlastnost](log-standard-properties.md#_resourceid)_ResourceId , která poskytuje úplnou cestu k prostředku:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Pro data z uzlů hostovaných v Azure můžete získat **velikost** ingdeed dat `_ResourceId` na předplatné __Azure__, analyzovat vlastnost jako:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Změna `subscriptionId` `resourceGroup` na zobrazí fakturovatelný svazek dat podle skupiny prostředků Azure. 

> [!NOTE]
> Některá pole datového typu Využití, zatímco stále ve schématu, byly zastaralé a budou jejich hodnoty již naplněny. Jedná **se o počítač,** stejně jako pole související s požitím (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** a **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Dotazování na běžné datové typy

Chcete-li hlouběji do zdroje dat pro konkrétní datový typ, zde jsou některé užitečné příklady dotazů:

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

Některé návrhy na snížení objemu protokolů shromážděných patří:

| Zdroj velkého objemu dat | Postup snížení objemu dat |
| -------------------------- | ------------------------- |
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [audit registru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změňte shromažďování protokolů prostředků tak, aby se: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | Pomocí [cílení na řešení](../insights/solution-targeting.md) můžete shromažďovat data pouze z požadovaných skupin počítačů. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Získání uzlů, jak se účtuje v cenové úrovni Per Node

Chcete-li získat seznam počítačů, které se budou účtovat jako uzly, pokud je pracovní prostor v cenové vrstvě Starší verze uzlu, vyhledejte uzly, které odesílají **fakturované datové typy** (některé datové typy jsou zdarma). Chcete-li to `_IsBillable` provést, použijte [vlastnost](log-standard-properties.md#_isbillable) a použijte pole zcela vlevo plně kvalifikovaného názvu domény. To vrátí počet počítačů s fakturovanými daty za hodinu (což je rozlišovací schopnost, při které se sčítají a účtují uzly):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Získání počtu uzlů zabezpečení a automatizace

Pokud jste na cenové úrovni "Na uzel (OMS)", pak se vám budou účtovat poplatky na základě počtu uzlů a řešení, které používáte, počet uzlů Přehledy a Analytics, kterým se účtují, se zobrazí v tabulce na stránce **Využití a Odhadované náklady.**  

Chcete-li zobrazit počet různých uzlů zabezpečení, můžete použít dotaz:

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

Chcete-li zobrazit počet různých uzlů automatizace, použijte dotaz:

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

## <a name="create-an-alert-when-data-collection-is-high"></a>Vytvoření výstrahy při vysokém shromažďování dat

Tato část popisuje postup vytvoření upozornění v těchto případech:
- Objem dat překračuje zadanou velikost.
- Očekává se, že objem dat překročí zadanou velikost.

Upozornění Azure podporují [upozornění protokolu](alerts-unified-log.md) využívající vyhledávací dotazy. 

Následující dotaz vrátí výsledek, pokud se za posledních 24 hodin shromáždilo více než 100 GB dat:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

Následující dotaz pomocí jednoduchého vzorce předvídá, jestli dojde k odeslání více než 100 GB dat za den: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Pokud chcete upozornit na jiný objem dat, změňte v dotazech hodnotu 100 na počet GB, na který chcete upozornit.

Pokud chcete být upozorňováni při větším než očekávaném shromažďování dat, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).

Při vytváření upozornění pro první dotaz (více než 100 GB dat během 24 hodin) nastavte:  

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor služby Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než***Prahová hodnota***0*.
   - **Časové období** na *1440* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Při vytváření upozornění pro druhý dotaz (předpověď, že během 24 hodin bude shromážděno více než 100 GB dat) nastavte:

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor služby Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`.
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než***Prahová hodnota***0*.
   - **Časové období** na *180* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Očekávaný větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Pokud obdržíte upozornění, pomocí kroků v následující části můžete řešit potíže způsobující větší využití, než se čekalo.

## <a name="data-transfer-charges-using-log-analytics"></a>Poplatky za přenos dat pomocí analýzy protokolů

Odesílání dat do služby Log Analytics může být účtovánpoplatek za šířku pásma dat. Jak je popsáno na [stránce s cenami šířky pásma Azure](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure umístěnými ve dvou oblastech se účtuje jako odchozí přenos dat normální masovou rychlostí. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (málo %) v porovnání s náklady na přihlašování dat služby Log Analytics. V důsledku toho řízení nákladů pro Log Analytics musí zaměřit na svazek ingestovaných dat a máme pokyny, které vám pomohou pochopit, že [zde](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Řešení potíží s tím, proč služba Log Analytics již neshromažďuje data

Pokud jste na starší cenové úrovni Free a odeslali více než 500 MB dat za den, shromažďování dat se zastaví po zbytek dne. Dosažení denního limitu je běžným důvodem, proč služba Log Analytics přestane shromažďovat data nebo že data chybí.  Log Analytics vytvoří událost typu Operace při shromažďování dat začíná a zastaví. Spusťte následující dotaz ve vyhledávání a zkontrolujte, zda dosahujete denního limitu a chybějících dat: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Když se zastaví shromažďování dat, OperationStatus je **Upozornění**. Při spuštění shromažďování dat OperationStatus je **succeeded**. Následující tabulka popisuje důvody, proč se shromažďování dat zastaví, a navrhovanou akci pro obnovení shromažďování dat:  

|Důvod kolekce zastaví| Řešení| 
|-----------------------|---------|
|Denní limit starší cenové úrovně Zdarma dosažený |Počkejte až do následujícího dne kolekce automaticky restartovat nebo změnit na placenou cenovou úroveň.|
|Bylo dosaženo denního limitu pracovního prostoru|Počkejte na automatické restartování kolekce nebo zvyšte denní limit objemu dat popsaný v části Správa maximálního denního objemu dat. Denní doba obnovení limitu se zobrazuje na stránce **Správa objemu dat.** |
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Bezplatná zkušební verze ukončena<br> Vypršela platnost předplatného Azure Pass<br> Byl dosažen limit měsíčního útraty (například u předplatného MSDN nebo Visual Studia).|Přechod na placené předplatné<br> Odebrat limit nebo počkat, až se limit resetuje|

Chcete-li být upozorněni, když se shromažďování dat zastaví, použijte kroky popsané v části Vytvořit upozornění *na omezení denních dat,* které mají být upozorňovány, když se shromažďování dat zastaví. Pomocí kroků popsaných v [části Vytvoření skupiny akcí](action-groups.md) nakonfigurujte pro pravidlo výstrahy e-mail, webhook nebo akci sady runbook. 

## <a name="limits-summary"></a>Souhrn limitů

Existují některé další omezení Analýzy protokolů, z nichž některé závisí na cenové úrovni Log Analytics. Ty jsou [zde](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)zdokumentovány .


## <a name="next-steps"></a>Další kroky

- Informace o tom, jak používat vyhledávací jazyk, najdete [v tématu Hledání protokolů v protokolech Azure Monitor.](../log-query/log-query-overview.md) Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
- Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).
- Pomocí [cílení na řešení](../insights/solution-targeting.md) můžete shromažďovat data pouze z požadovaných skupin počítačů.
- Chcete-li nakonfigurovat účinnou zásadu shromažďování událostí, přečtěte si [zásady filtrování Centra zabezpečení Azure](../../security-center/security-center-enable-data-collection.md).
- Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md).
- Chcete-li změnit nastavení kolekce událostí, zkontrolujte [konfiguraci protokolu událostí](data-sources-windows-events.md).
- Chcete-li upravit nastavení kolekce syslogu, zkontrolujte [konfiguraci syslogu](data-sources-syslog.md).