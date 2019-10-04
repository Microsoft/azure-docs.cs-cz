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
ms.date: 10/01/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: fa0bd847596a601875d5662da1c000a5b1388eef
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960269"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Správa využití a nákladů pomocí protokolů Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak pochopit a řídit náklady na protokoly Azure Monitor. Související článek, [sledování využití a odhadované náklady](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure pro různé cenové modely.

Protokoly Azure Monitor jsou určené ke škálování a podpoře shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje v podniku nebo v Azure, které jsou nasazené v Azure.  I když to může být primárním ovladačem pro vaši organizaci, náklady na efektivitu jsou nakonec základní ovladače. V tomto případě je důležité pochopit, že náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, ale také závisí na vybraném plánu a na tom, jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku si projdeme, jak můžete aktivně monitorovat monitorovaný objem dat a nárůst úložiště a definovat omezení pro řízení těchto přidružených nákladů. 

## <a name="pricing-model"></a>Cenový model

Výchozí ceny pro Log Analytics jsou Model průběžných **plateb** na základě ingestování objemu dat a volitelně i pro delší dobu uchovávání dat. Každý Log Analytics pracovní prostor se účtuje jako samostatná služba a přispívá vám k fakturaci za vaše předplatné Azure. Množství příjmu dat může být značná v závislosti na následujících faktorech: 

  - Počet povolených řešení správy a jejich konfigurace (např. 
  - Počet monitorovaných virtuálních počítačů
  - Typ dat shromažďovaných z každého monitorovaného virtuálního počítače 
  
Kromě modelu průběžných plateb jsme zavedli **rezervace kapacity** pro Log Analytics, které vám umožní v porovnání s průběžnými platbami ušetřit až 25%. Cena za rezervaci kapacity vám umožní koupit rezervaci od 100 GB za den. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách Log Analytics s průběžnými platbami a rezervací kapacity. 

Všimněte si, že některá řešení, například [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) a [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), mají svůj vlastní cenový model. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí 

Pokud protokoly Azure Monitor ještě nepoužíváte, můžete pomocí [cenové kalkulačky Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Log Analytics. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou Azure Monitor dlaždici. Posuňte se dolů na stránku Azure Monitor a v rozevíracím seznamu Typ vyberte Log Analytics.  Tady můžete zadat počet virtuálních počítačů a GB dat, která se mají z každého virtuálního počítače shromažďovat. Typcially 1 až 3 GB datového měsíce se ingestují z typických virtuálních počítačů Azure. Pokud už vyhodnocujete Azure Monitor protokoly, můžete použít statistiku dat z vlastního prostředí. Níže najdete informace o tom, jak určit [Počet monitorovaných virtuálních počítačů](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) a objem dat, na které [váš pracovní prostor pracuje](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení nákladů na využití a odhadované náklady

Pokud nyní používáte protokoly Azure Monitor, je snadné pochopit, jaké náklady jsou pravděpodobně založené na nedávných vzorech použití. K tomu použijte **Log Analytics využití a odhadované náklady** na kontrolu a analýzu využití dat. Zobrazuje množství dat shromažďovaných jednotlivými řešeními, množství uchovávaných dat a odhad nákladů na základě množství přijatých dat a dalších uchování nad rámec zahrnutého množství.

![Využití a odhadované náklady](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Pokud chcete svá data prozkoumat podrobněji, klikněte na ikonu v pravém horním rohu obou grafů na stránce **využití a odhadované náklady** . Nyní můžete s tímto dotazem pracovat a prozkoumat podrobnější informace o jeho využití.  

![Zobrazení protokolů](media/manage-cost-storage/logs.png)

Na stránce **využití a odhadované náklady** si můžete prohlédnout svůj objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávaná v pracovním prostoru Log Analytics.  Kliknutím na **Podrobnosti o využití** v horní části stránky zobrazíte řídicí panel využití s informacemi o trendech objemu dat podle zdroje, počítačů a nabídky. Chcete-li zobrazit a nastavit denní limit nebo upravit dobu uchování, klikněte na položku **Správa objemu dat**.
 
Do faktury Azure se přidají poplatky za Log Analytics. Podrobnosti o fakturaci Azure můžete zobrazit v části fakturace Azure Portal nebo v [portál fakturace Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Zobrazení využití Log Analytics na faktuře Azure 

Azure poskytuje skvělou užitečnou funkci centra [Azure cost management + fakturace](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Například funkce "cost Analysis" umožňuje zobrazit vaše výdaje na prostředky Azure. Přidání filtru podle typu prostředku (do Microsoft. operationalinsights/Workspace for Log Analytics) vám umožní sledovat vaše výdaje.

Lepší porozumění vašemu využití najdete [na webu Azure Portal stažením vašeho využití](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Ve stažených tabulkách vidíte využití podle prostředku Azure (např. Log Analytics pracovní prostor) za den. V této excelové tabulce můžete využití vašich Log Analytics pracovních prostorů najít při prvním filtrování ve sloupci měřiče měření, aby se zobrazila zpráva "Insights and Analytics" (používané některými staršími cenovými úrovněmi) a "Log Analytics" a následně jste přidali filtr do instance. ID – sloupec, který obsahuje pracovní prostor. Využití se zobrazí ve sloupci "spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou další podrobnosti, které vám pomůžou [pochopit Microsoft Azureovou fakturaci](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="manage-your-maximum-daily-data-volume"></a>Správa maximálního denního objemu dat

Můžete nakonfigurovat denní limit a omezit každodenní ingestování pro váš pracovní prostor, ale pečlivě používejte, protože by váš cíl neměl mít denní limit.  Jinak ztratíte data zbývajícího dne, což může mít vliv na další služby a řešení Azure, jejichž funkce můžou záviset na dostupnosti dat v pracovním prostoru.  Výsledkem je, že schopnost sledovat a přijímat výstrahy, když jsou ovlivněny stavové stavy prostředků, které podporují IT služby.  Denní limit je určený k použití jako způsob, jak spravovat neočekávané zvýšení objemu dat ze spravovaných prostředků a zůstat v rámci vašeho limitu, nebo pokud chcete omezit neplánované poplatky pro váš pracovní prostor.  

Po dosažení denního limitu se pro zbytek dne zastaví shromažďování fakturovatelných datových typů. V horní části stránky se zobrazí banner s upozorněním pro vybraný Log Analytics pracovní prostor a událost operace se odešle do tabulky *Operation* v kategorii **LogManagement** . Shromažďování dat se obnoví po uplynutí doby obnovení definované v rámci *denního limitu*. Doporučujeme definovat pravidlo výstrahy na základě této události operace, která je nakonfigurována tak, aby po dosažení denního limitu dat upozornila na oznámení. 

> [!NOTE]
> Denní limit nezastaví shromažďování dat z Azure Security Center, s výjimkou pracovních prostorů, ve kterých Azure Security Center byl nainstalován před 19. června 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Určete, který denní limit dat se má definovat.

Přečtěte si [Log Analytics využití a odhadované náklady](usage-estimated-costs.md) , abyste porozuměli trendům příjmu dat a jaký je denní zakončení pro definování. Mělo by se brát v úvahu opatrně, protože po dosažení limitu už nebudete moct monitorovat vaše prostředky. 

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

Následující postup popisuje, jak nakonfigurovat limit pro správu objemu dat, který Log Analytics pracovní prostor ingestovat za den.  

1. V pracovním prostoru v levém podokně vyberte **využití a odhadované náklady** .
2. Na stránce **využití a odhadované náklady** pro vybraný pracovní prostor klikněte v horní části stránky na možnost **Správa objemu dat** . 
3. Denní limit je ve výchozím nastavení **vypnutý** – Pokud ho chcete povolit, klikněte na **zapnout** a pak nastavte limit počtu dat v GB za den.

    ![Log Analytics konfiguraci omezení dat](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Výstraha při denním limitu

Když při splnění prahové hodnoty limitu dat prezentujeme Azure Portal vizuální sestavování, toto chování se nemusí nutně sjednotit na to, jak budete spravovat provozní problémy, které vyžadují okamžitou pozornost.  Chcete-li dostávat oznámení o výstrahách, můžete v Azure Monitor vytvořit nové pravidlo výstrahy.  Další informace najdete v tématu [jak vytvářet, zobrazovat a spravovat výstrahy](alerts-metric.md).

Pokud chcete začít, tady je doporučené nastavení pro tuto výstrahu:

- Cíl: Vyberte prostředek Log Analytics
- Měřítk 
   - Název signálu: prohledávání vlastního protokolu
   - Vyhledávací dotaz: operace | kde detail má ' překročení kvóty '
   - Podle: počet výsledků
   - Podmínka: je větší než
   - Prahová hodnota: 0
   - Období: 5 (minuty)
   - Frekvence: 5 (minuty)
- Název pravidla výstrahy: dosáhlo se denního limitu dat.
- Závažnost: upozornění (závažnost 1)

Po definování výstrahy a dosažení limitu se aktivuje výstraha a bude provedena odpověď definovaná ve skupině akcí. Může váš tým informovat prostřednictvím e-mailu a textových zpráv nebo automatizovat akce pomocí webhooků, runbooků pro automatizaci nebo [integrací s externím řešením ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Změnit dobu uchovávání dat

Následující postup popisuje, jak nakonfigurovat, jak dlouho budou data protokolu uchovávána ve vašem pracovním prostoru.

### <a name="default-retention"></a>Výchozí uchování

Pokud chcete nastavit výchozí dobu uchovávání pro váš pracovní prostor, 
 
1. Na webu Azure Portal v pracovním prostoru vyberte v levém podokně **využití a odhadované náklady** .
2. Na stránce **využití a odhadované náklady** klikněte v horní části stránky na možnost **Správa objemu dat** .
3. V podokně přesunutím posuvníku zvyšte nebo snižte počet dní a potom klikněte na tlačítko **OK**.  Pokud jste na *bezplatné* úrovni, nebudete moct upravit dobu uchovávání dat a abyste mohli řídit toto nastavení, musíte upgradovat na placenou úroveň.

    ![Změnit nastavení uchovávání dat pracovního prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Uchování je také možné [nastavit přes ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí parametru `retentionInDays`. Pokud navíc nastavíte uchovávání dat na 30 dní, můžete spustit okamžité vymazání starších dat pomocí parametru `immediatePurgeDataOn30Days`, který může být užitečný pro scénáře související s dodržováním předpisů. Tato funkce se zveřejňuje jenom přes ARM. 

Dva datové typy--`Usage` a `AzureActivity`--jsou ve výchozím nastavení uchovány po dobu 90 dnů a za toto 90 dne se neúčtují žádné poplatky. Tyto datové typy jsou také zdarma z poplatků za příjem dat. 

### <a name="retention-by-data-type"></a>Uchovávání dat podle datového typu

Je také možné zadat různá nastavení uchovávání pro jednotlivé datové typy. Každý datový typ je dílčím prostředkem pracovního prostoru. Například tabulku SecurityEvent můžete vyřešit v [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) jako:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Všimněte si, že datový typ (tabulka) rozlišuje velká a malá písmena.  Chcete-li získat aktuální nastavení pro uchování dat určitého datového typu (v tomto příkladu SecurityEvent), použijte:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Chcete-li pro všechny typy dat v pracovním prostoru získat aktuální nastavení pro uchování dat, stačí vynechat konkrétní datový typ, například:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Pokud chcete nastavit uchování určitého datového typu (v tomto příkladu SecurityEvent) na 730 dne, udělejte to.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Datové typy `Usage` a `AzureActivity` nelze nastavit s vlastním uchováváním. Budou platit až do maximálního počtu výchozích uchovávání pracovních prostorů nebo 90 dnů. 

Skvělý nástroj pro připojení přímo k ARM k nastavení uchovávání informací podle datového typu je [ARMclient](https://github.com/projectkudu/ARMClient)nástroje OSS.  Další informace o ARMclient od článků získáte v článku [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) a [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/). 

> [!NOTE]
> Nastavení uchovávání pro jednotlivé datové typy lze použít ke snížení nákladů na uchovávání dat.  Pro data shromážděná od října 2019 (při vydání této funkce) snížení doby uchování některých datových typů může snížit náklady na uchovávání dat v průběhu času.  U dříve shromážděných dat nastavení nižšího uchování pro jednotlivý typ nebude mít vliv na náklady na uchování.  

## <a name="legacy-pricing-tiers"></a>Starší cenové úrovně

Předplatná, která má Log Analytics pracovní prostor nebo prostředek Application Insights před 2. dubna 2018 nebo jsou propojeny s smlouva Enterprise, které začaly před 1. února 2019, budou mít nadále přístup k používání starších verzí cenové úrovně: **Free**, **Standalone (za GB)** a **per Node (OMS)** .  Pracovní prostory v bezplatné cenové úrovni budou mít denní příjem dat omezený na 500 MB (kromě datových typů zabezpečení shromažďovaných v Azure Security Center) a uchovávání dat je omezeno na 7 dní. Cenová úroveň Free je určena pouze pro účely vyhodnocení. Pracovní prostory v cenové úrovni samostatného nebo jednotlivých uzlů mají uživatelsky konfigurovatelné uchovávání až po 2 roky. 

Pracovní prostory vytvořené před dubna 2016 mají přístup také k původním cenovým úrovním **Standard** a **Premium** , které mají pevnou dobu uchovávání dat 30 a 365 dnů v uvedeném pořadí. Nové pracovní prostory nelze vytvořit v cenové úrovni **Standard** nebo **Premium** a pokud je pracovní prostor přesunut z těchto úrovní, nelze jej přesunout zpět. 

Další podrobnosti o omezeních cenové úrovně jsou k dispozici [zde](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo doplňku OMS pro System Center, vyberte cenovou úroveň Log Analytics pro *jednotlivé uzly* .


## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Pokud má váš Log Analytics pracovní prostor přístup ke starším cenovým úrovním, aby se mohlo změnit mezi staršími cenovými úrovněmi:

1. V Azure Portal v podokně Log Analytics odběry vyberte pracovní prostor.

2. V podokně pracovního prostoru v části **Obecné**vyberte **cenová úroveň**.  

3. V části **cenová úroveň**vyberte cenovou úroveň a pak klikněte na **Vybrat**.  
    Cenový tarif @no__t 0Selected @ no__t-1

[Cenovou úroveň můžete také nastavit přes ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí parametru `sku` (`pricingTier` v šabloně ARM). 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Řešení potíží s tím, proč Log Analytics už neshromažďuje data

Pokud používáte starší verzi bezplatné cenové úrovně a v den jste poslali více než 500 MB dat, zastaví se shromažďování dat pro zbytek dne. Dosažení denního limitu je běžný důvod, proč Log Analytics zastaví shromažďování dat, nebo se zdá, že data chybí.  Log Analytics vytvoří událost typu operace při spuštění a zastavení shromažďování dat. Spuštěním následujícího dotazu v hledání ověřte, jestli se vám dosáhlo denního limitu a chybějících dat: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Po zastavení shromažďování dat je stav operationstatus **Upozornění**. Když se spustí shromažďování dat, stav operationstatus se **podařilo**. Následující tabulka popisuje důvody, proč se shromažďování dat zastaví, a navrhovanou akci pro pokračování shromažďování dat:  

|Kolekce důvodů – zastavení| Řešení| 
|-----------------------|---------|
|Dosáhlo se denního limitu starší verze bezplatné cenové úrovně. |Počkejte prosím, než se automaticky restartuje kolekce, nebo se změní na placenou cenovou úroveň.|
|Dosáhlo se denního limitu pracovního prostoru.|Počkejte na automatické restartování kolekce nebo zvyšte počet denních objemů dat popsaných v tématu Správa maximálního denního objemu dat. Doba obnovení denního limitu se zobrazí na stránce **Správa objemu dat** . |
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Zkušební verze skončila.<br> Platnost Azure Pass vypršela.<br> Dosáhlo se limitu měsíčního útraty (například na předplatném MSDN nebo Visual Studio).|Převod na placené předplatné<br> Odebrat limit nebo počkat na obnovení limitu|

Chcete-li být upozorněni na zastavení shromažďování dat, postupujte podle kroků popsaných v části *Vytvoření výstrahy denního datového zakončení* , která bude oznámena při zastavení shromažďování dat. Pomocí kroků popsaných v tématu [Vytvoření skupiny akcí](action-groups.md) nakonfigurujte akci e-mailu, Webhooku nebo Runbooku pro pravidlo výstrahy. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží s tím, proč je využití vyšší než očekávané

Vyšší využití je způsobeno jedním z těchto:
- Více uzlů, než se čekalo na odesílání dat do Log Analytics pracovního prostoru
- Víc dat, než se čekalo, že se posílá do Log Analytics pracovního prostoru

## <a name="understanding-nodes-sending-data"></a>Porozumění uzlům odesílajícím data

Pro pochopení počtu počítačů hlásících prezenční signály každý den během posledního měsíce použijte

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Pokud chcete získat seznam počítačů, které se budou fakturovat jako uzly, pokud je pracovní prostor ve starší verzi na cenové úrovni uzlů, hledejte uzly, které odesílají **účtované datové typy** (některé datové typy jsou zdarma). K tomu použijte [vlastnost](log-standard-properties.md#_isbillable) `_IsBillable` a použijte pole plně kvalifikovaného názvu domény v levém poli. Vrátí se seznam počítačů s fakturovanými daty:

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
> Tyto dotazy `union withsource = tt *` můžete obsloužit zřídka, protože vyhledávání napříč datovými typy je náročné na spouštění. Tento dotaz nahrazuje starý způsob, jak zadávat dotazy na informace o počítačích s datovým typem využití.  

Přesnější výpočet toho, co se skutečně bude účtovat, je získání počtu počítačů za hodinu, které odesílají Fakturovatelné datové typy. (Pro pracovní prostory ve starších cenových úrovních jednotlivých uzlů Log Analytics vypočítá počet uzlů, které se musí účtovat po hodinách.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Principy ingestných objemů dat

Na stránce **využití a odhadované náklady** zobrazuje ingestování *dat na řešení* celkový objem odeslaných dat a množství, které jednotlivé řešení odesílají. Díky tomu můžete určit trendy, jako je například to, jestli celkové využití dat (nebo využití konkrétního řešení) roste, zbývající stabilní nebo klesající. Dotaz použitý k vygenerování je

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Všimněte si, že klauzule "kde Fakturovatelné = true" filtruje datové typy z určitých řešení, pro které se neúčtují žádné poplatky za ingestování. 

Další podrobnosti můžete zobrazit tak, abyste viděli trendy dat pro konkrétní typy dat, například pokud chcete prostudovat data z důvodu protokolů IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Objem dat podle počítače

Chcete-li zobrazit **Velikost** fakturovaných událostí, které jsou v jednotlivých počítačích k dispozici, použijte [vlastnost](log-standard-properties.md#_billedsize)`_BilledSize`, která poskytuje velikost v bajtech:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

[Vlastnost](log-standard-properties.md#_isbillable) `_IsBillable` určuje, zda se za příjem dat účtují poplatky.

Chcete-li **Zobrazit počet fakturovaných událostí** zpracovaných na počítač, použijte 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Pokud chcete zobrazit počty pro Fakturovatelné datové typy, které odesílají data do konkrétního počítače, použijte:

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

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** fakturovaných událostí pro __každé předplatné Azure__, analyzovat vlastnost `_ResourceId` jako:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Změna `subscriptionId` na `resourceGroup` zobrazí fakturovatelný příjmový objem dat skupinou prostředků Azure. 


> [!NOTE]
> Některá pole datového typu použití, ale stále ve schématu, jsou zastaralá a jejich hodnoty se už neplní. Jedná se o **počítač** a pole související s ingestování (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** a **AverageProcessingTimeMs**).

### <a name="querying-for-common-data-types"></a>Dotazování na Common data types

Pokud chcete dig hlouběji ke zdroji dat pro určitý datový typ, tady jsou některé užitečné příklady dotazů:

+ Řešení **zabezpečení**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Řešení **správy protokolů**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Datový typ **výkonu**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Datový typ **události**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Datový typ **SYSLOG**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Datový typ **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Tipy pro snížení objemu dat

Mezi návrhy na snížení objemu shromažďovaných protokolů patří:

| Zdroj vysokého objemu dat | Jak snížit objem dat |
| -------------------------- | ------------------------- |
| Události zabezpečení            | Vybrat [běžné nebo minimální události zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Změňte zásady auditu zabezpečení tak, aby byly shromažďovány pouze potřebné události. Zejména zkontrolujte nutnost shromažďování událostí pro <br> - [filtrovacích platforem pro audit](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> [registr auditu](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10)) - <br> [systém souborů auditu](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10)) - <br> - [objekt jádra auditu](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> [manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10)) - <br> – audit vyměnitelného úložiště |
| Čítače výkonu       | Změnit [konfiguraci čítače výkonu](data-sources-performance-counters.md) na: <br> – Snížení frekvence shromažďování <br> – Snížení počtu čítačů výkonu |
| Protokoly událostí                 | Změnit [konfiguraci protokolu událostí](data-sources-windows-events.md) na: <br> – Snižte počet shromážděných protokolů událostí. <br> – Shromažďují se jenom požadované úrovně událostí. Neshromažďovat například události na úrovni *informací* |
| Syslog                     | Změňte [konfiguraci syslogu](data-sources-syslog.md) na: <br> – Snižte počet shromážděných zařízení. <br> – Shromažďují se jenom požadované úrovně událostí. Například neshromážděte *informace o* událostech úrovně info a *Debug* |
| AzureDiagnostics           | Změnit kolekci protokolů prostředků na: <br> – Snižte počet prostředků, které odesílají protokoly do Log Analytics <br> -Shromažďovat pouze požadované protokoly |
| Data řešení z počítačů, které nepotřebují řešení | K shromažďování dat z požadovaných skupin počítačů použijte [cílení na řešení](../insights/solution-targeting.md) . |

### <a name="getting-security-and-automation-node-counts"></a>Načítají se počty uzlů zabezpečení a automatizace.

Pokud se nacházíte na cenové úrovni per Node (OMS), bude se vám účtovat na základě počtu uzlů a řešení, která používáte, a v tabulce na stránce **využití a odhadované náklady** se zobrazí počet uzlů Insights a analýz, pro které se vám účtují poplatky.  

Pokud chcete zobrazit počet různých uzlů zabezpečení, můžete použít dotaz:

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

Pro zobrazení počtu různých uzlů automatizace použijte dotaz:

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

Tato část popisuje, jak vytvořit výstrahu v těchto případech:
- Objem dat překračuje zadanou velikost.
- Hodnota data Volume je předpokládaná, aby překročila zadanou velikost.

Výstrahy Azure podporují [výstrahy protokolu](alerts-unified-log.md) , které používají vyhledávací dotazy. 

Následující dotaz má za následek, že za posledních 24 hodin se shromáždilo více než 100 GB dat:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

Následující dotaz používá jednoduchý vzorec k předpovídání, že za jeden den bude odesláno více než 100 GB dat: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

Pokud chcete upozornit na jiný datový svazek, změňte 100 v dotazech na počet GB, na které chcete upozornit.

Pomocí kroků popsaných v tématu [Vytvoření nového upozornění protokolu](alerts-metric.md) se dozvíte, že shromažďování dat je vyšší, než se očekávalo.

Při vytváření výstrahy pro první dotaz – Pokud je během 24 hodin více než 100 GB dat, nastavte:  

- **Definovat podmínku upozornění** zadejte jako cíl prostředku Log Analytics pracovní prostor.
- **Kritéria výstrahy** určují následující:
   - **Název signálu** – výběr **vlastního prohledávání protokolu**
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Logika výstrah** je **založena na** *počtu výsledků* a **Podmínka** je *větší než* **prahová hodnota** *0* .
   - **Časové období** *1440* minut a **četnosti upozornění** každých *60* minut, protože data o využití se aktualizují jenom jednou za hodinu.
- Určete **Podrobnosti výstrahy** zadejte následující:
   - **Název** na *objem dat větší než 100 GB za 24 hodin*
   - **Závažnost** na *Upozornění*

Zadejte existující nebo vytvořte novou [skupinu akcí](action-groups.md) , aby se při upozornění protokolu na kritéria shodovala oznámení.

Při vytváření výstrahy pro druhý dotaz – Pokud je předpovězena, že během 24 hodin bude více než 100 GB dat, nastavte:

- **Definovat podmínku upozornění** zadejte jako cíl prostředku Log Analytics pracovní prostor.
- **Kritéria výstrahy** určují následující:
   - **Název signálu** – výběr **vlastního prohledávání protokolu**
   - **Vyhledávací dotaz** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Logika výstrah** je **založena na** *počtu výsledků* a **Podmínka** je *větší než* **prahová hodnota** *0* .
   - **Časové období** *180* minut a **četnosti upozornění** každých *60* minut, protože data o využití se aktualizují jenom jednou za hodinu.
- Určete **Podrobnosti výstrahy** zadejte následující:
   - **Název** pro *datový svazek, který se očekává jako větší než 100 GB za 24 hodin*
   - **Závažnost** na *Upozornění*

Zadejte existující nebo vytvořte novou [skupinu akcí](action-groups.md) , aby se při upozornění protokolu na kritéria shodovala oznámení.

Když obdržíte výstrahu, postupujte podle kroků v následující části, pokud chcete vyřešit, proč je použití vyšší, než se očekávalo.

## <a name="data-transfer-charges-using-log-analytics"></a>Poplatky za přenos dat pomocí Log Analytics

Odesílání dat do Log Analytics může mít za následek poplatky za šířku pásma dat. Jak je popsáno na [stránce ceny za Azure šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure v rámci dvou oblastí se v normální sazbě účtuje jako odchozí přenos dat. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (několik%) v porovnání s náklady na Log Analytics přijímání dat. V důsledku toho se řídí náklady na Log Analytics se musí soustředit na přijatý objem dat a máme vám Rady, jak to porozumět [.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   

## <a name="limits-summary"></a>Souhrn omezení

Existují další limity Log Analytics, některé z nich závisí na cenové úrovni Log Analytics. Ty jsou popsány [zde](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Další kroky

- Další informace o použití vyhledávacího jazyka najdete [v tématu prohledávání protokolů v protokolu Azure monitor](../log-query/log-query-overview.md) . Pomocí vyhledávacích dotazů můžete na základě dat o využití provádět další analýzu.
- Pomocí kroků popsaných v tématu [Vytvoření nového upozornění protokolu](alerts-metric.md) se dozvíte, že jsou splněna kritéria hledání.
- K shromažďování dat z požadovaných skupin počítačů použijte [cílení na řešení](../insights/solution-targeting.md) .
- Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí, Projděte si téma [Zásady filtrování Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Změna [Konfigurace čítače výkonu](data-sources-performance-counters.md).
- Pokud chcete upravit nastavení shromažďování událostí, zkontrolujte [konfiguraci protokolu událostí](data-sources-windows-events.md).
- Pokud chcete upravit nastavení kolekce syslog, zkontrolujte [konfiguraci syslogu](data-sources-syslog.md).
