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
ms.date: 11/05/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: f60b0b9294fa3f11889613a7d63f21e87fbea201
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894109"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Správa využití a nákladů pomocí protokolů Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak pochopit a řídit náklady na protokoly Azure Monitor. Související článek, [sledování využití a odhadované náklady](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure pro různé cenové modely.

> [!NOTE]
> Všechny ceny a náklady uvedené v tomto článku jsou třeba jenom pro účely. 

Protokoly Azure Monitor jsou určené ke škálování a podpoře shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje v podniku nebo v Azure, které jsou nasazené v Azure.  To může být hlavním důvod pro vaši organizaci, nákladovou efektivnost v konečném důsledku je základní ovladač. V tomto případě je důležité pochopit, že náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, ale také závisí na vybraném plánu a na tom, jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku si projdeme, jak můžete aktivně monitorovat monitorovaný objem dat a nárůst úložiště a definovat omezení pro řízení těchto přidružených nákladů. 

## <a name="pricing-model"></a>Cenový model

Výchozí ceny pro Log Analytics jsou Model průběžných **plateb** na základě ingestování objemu dat a volitelně i pro delší dobu uchovávání dat. Objem dat se měří jako velikost dat, která se budou ukládat. Každý Log Analytics pracovní prostor se účtuje jako samostatná služba a přispívá vám k fakturaci za vaše předplatné Azure. Množství příjmu dat může být značná v závislosti na následujících faktorech: 

  - Počet povolených řešení správy a jejich konfigurace (např. 
  - Počet monitorovaných virtuálních počítačů
  - Typ dat shromažďovaných z každého monitorovaného virtuálního počítače 
  
Kromě modelu průběžných plateb Log Analytics má vrstvy **rezervace kapacity** , které vám umožní ve srovnání s průběžnými platbami ušetřit až 25%. Cena za rezervaci kapacity vám umožní koupit rezervaci od 100 GB za den. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. Úrovně rezervace kapacity mají 31 dnů v období závazku. Během období závazku můžete přejít na úroveň rezervace kapacity vyšší úrovně (čímž se restartuje 31. období závazku), ale nemůžete přejít zpět na průběžné platby nebo na nižší úroveň rezervace kapacity až po období závazku. píše. 
[Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách Log Analytics s průběžnými platbami a rezervací kapacity. 

Všimněte si také, že některá řešení, například [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) a [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), mají svůj vlastní cenový model. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí 

Pokud protokoly Azure Monitor ještě nepoužíváte, můžete pomocí [cenové kalkulačky Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Log Analytics. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou Azure Monitor dlaždici. Posuňte se dolů na stránku Azure Monitor a v rozevíracím seznamu Typ vyberte Log Analytics.  Tady můžete zadat počet virtuálních počítačů a GB dat, která se mají z každého virtuálního počítače shromažďovat. Z typického virtuálního počítače Azure se typicky ingestují 1 až 3 GB datového měsíce. Pokud už vyhodnocujete Azure Monitor protokoly, můžete použít statistiku dat z vlastního prostředí. Níže najdete informace o tom, jak určit [Počet monitorovaných virtuálních počítačů](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) a objem dat, na které [váš pracovní prostor pracuje](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení nákladů na využití a odhadované náklady

Pokud nyní používáte protokoly Azure Monitor, je snadné pochopit, jaké náklady jsou pravděpodobně založené na nedávných vzorech použití. K tomu použijte **Log Analytics využití a odhadované náklady** na kontrolu a analýzu využití dat. Ukazuje, kolik dat je každé řešení shromažďováno, kolik dat se zachovává, a odhad nákladů na základě množství přijatých dat a dalšího uchovávání nad rámec zahrnutého množství.

![Využití a odhadované náklady](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Prozkoumat data podrobněji, klikněte na ikonu v horní části napravo od buď z grafů v **využití a odhadované náklady** stránky. Teď můžete pracovat s Tento dotaz a prozkoumejte podrobnosti o využití.  

![Zobrazení protokolů](media/manage-cost-storage/logs.png)

Na stránce **využití a odhadované náklady** si můžete prohlédnout svůj objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávají ve vašem pracovním prostoru Log Analytics.  Kliknutím na **Podrobnosti o využití** v horní části stránky zobrazíte řídicí panel využití s informacemi o trendech objemu dat podle zdroje, počítačů a nabídky. K zobrazení a nastavte denní limit nebo dobu uchování upravit, klikněte na tlačítko **Správa objemu dat**.
 
Poplatky za log Analytics se přidají do vašeho vyúčtování služeb Azure. Můžete si prohlédnout podrobnosti Azure fakturovat části fakturace na portálu Azure Portal nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Zobrazení využití Log Analytics na faktuře Azure 

Azure poskytuje skvělou užitečnou funkci centra [Azure cost management + fakturace](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Například funkce "cost Analysis" umožňuje zobrazit vaše výdaje na prostředky Azure. Přidání filtru podle typu prostředku (do Microsoft. operationalinsights/Workspace for Log Analytics) vám umožní sledovat vaše výdaje.

Lepší porozumění vašemu využití můžete získat [stažením vašeho využití z Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Ve stažených tabulkách vidíte využití podle prostředku Azure (např. Log Analytics pracovní prostor) za den. V této excelové tabulce můžete využití vašich Log Analytics pracovních prostorů najít při prvním filtrování ve sloupci měřiče měření, aby se zobrazila zpráva "Insights and Analytics" (používané některými staršími cenovými úrovněmi) a "Log Analytics" a následně jste přidali filtr do instance. ID – sloupec, který obsahuje pracovní prostor. Využití se zobrazí ve sloupci "spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou další podrobnosti, které vám pomůžou [pochopit Microsoft Azureovou fakturaci](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Pokud chcete změnit Log Analytics cenové úrovně vašeho pracovního prostoru, 

1. V Azure Portal otevřete z pracovního prostoru **využití a odhadované náklady** , kde se zobrazí seznam všech cenových úrovní dostupných pro tento pracovní prostor.

2. Přečtěte si odhadované náklady na jednotlivé cenové úrovně. Tento odhad vychází z posledních 31 dnů od použití, takže tento odhad nákladů se spoléhá na posledních 31 dní, které jsou součástí typického využití. V následujícím příkladu vidíte, jak na základě vzorů dat během posledních 31 dnů bude tento pracovní prostor méně levnější v rámci vrstvy s průběžnými platbami (#1) v porovnání s úrovní rezervace kapacity 100 GB/den (#2).  

    ![Cenové úrovně](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Po kontrole odhadovaných nákladů na základě posledních 31 dnů využití se rozhodnete změnit cenovou úroveň kliknutím na **Vybrat**.  

[Cenovou úroveň můžete také nastavit prostřednictvím Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí parametru `sku` (`pricingTier` v šabloně Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Starší cenové úrovně

Předplatná, která měl Log Analytics pracovní prostor nebo prostředek Application Insights před 2. dubna 2018 nebo jsou propojená s smlouva Enterprise, která začala před 1. února 2019, bude i nadále mít přístup k používání starších cenových úrovní: **Free**, **Standalone (za GB)** a **per Node (OMS)** .  Pracovní prostory v bezplatné cenové úrovni budou mít denní příjem dat omezený na 500 MB (kromě datových typů zabezpečení shromažďovaných v Azure Security Center) a uchovávání dat je omezeno na 7 dní. Cenová úroveň Free je určena pouze pro účely vyhodnocení. Pracovní prostory v cenové úrovni samostatného nebo jednotlivých uzlů mají uživatelsky konfigurovatelné uchovávání až po 2 roky. 

Pracovní prostory vytvořené před dubna 2016 mají přístup také k původním cenovým úrovním **Standard** a **Premium** , které mají pevnou dobu uchovávání dat 30 a 365 dnů v uvedeném pořadí. Nové pracovní prostory nelze vytvořit v cenové úrovni **Standard** nebo **Premium** a pokud je pracovní prostor přesunut z těchto úrovní, nelze jej přesunout zpět. 

Další podrobnosti o omezeních cenové úrovně jsou k dispozici [zde](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo doplňku OMS pro System Center, vyberte cenovou úroveň Log Analytics pro *jednotlivé uzly* .

## <a name="change-the-data-retention-period"></a>Změnit dobu uchování dat

Následující kroky popisují, jak nakonfigurovat jak dlouho protokol dat se uchovává ve vašem pracovním prostoru.

### <a name="default-retention"></a>Výchozí uchování

Pokud chcete nastavit výchozí dobu uchovávání pro váš pracovní prostor, 
 
1. V Azure Portal v pracovním prostoru v levém podokně vyberte **využití a odhadované náklady** .
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Správa objemu dat**.
3. V podokně s pomocí posuvníku zvyšte nebo snižte počet dní a potom klikněte na tlačítko **OK**.  Pokud používáte *bezplatné* úroveň, nebudete moci upravit dobu uchovávání dat a budete muset upgradovat na placenou úroveň cílem kontrolovat, toto nastavení.

    ![Změnit nastavení uchovávání dat pracovního prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Uchovávání lze také [nastavit prostřednictvím Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí parametru `retentionInDays`. Pokud navíc nastavíte uchovávání dat na 30 dní, můžete spustit okamžitou mazání starších dat pomocí parametru `immediatePurgeDataOn30Days`, který může být užitečný pro scénáře související s dodržováním předpisů. Tato funkce se zveřejňuje jenom prostřednictvím Azure Resource Manager. 

Dva typy dat – `Usage` a `AzureActivity`--ve výchozím nastavení uchovávají 90 dní a za tento 90 dnů se neúčtují žádné poplatky. Tyto datové typy jsou také zdarma z poplatků za příjem dat. 

### <a name="retention-by-data-type"></a>Uchovávání dat podle datového typu

Je také možné zadat různá nastavení uchovávání pro jednotlivé datové typy. Každý datový typ je dílčím prostředkem pracovního prostoru. Například tabulku SecurityEvent lze vyřešit v [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) jako:

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

Chcete-li nastavit uchovávání konkrétního datového typu (v tomto příkladu SecurityEvent) až 730 dnů, udělejte

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

Skvělý nástroj pro připojení přímo k Azure Resource Manager k nastavení možnosti uchovávání informací podle datového typu je [ARMclient](https://github.com/projectkudu/ARMClient)nástroje OSS.  Další informace o ARMclient od článků získáte v článku [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) a [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Tady je příkladu, který používá ARMClient, nastavení SecurityEvent dat na 730 dní:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Nastavení uchovávání pro jednotlivé datové typy lze použít ke snížení nákladů na uchovávání dat.  Pro data shromážděná od října 2019 (při vydání této funkce) snížení doby uchování některých datových typů může snížit náklady na uchovávání dat v průběhu času.  U dříve shromážděných dat nastavení nižšího uchování pro jednotlivý typ nebude mít vliv na náklady na uchování.  

## <a name="manage-your-maximum-daily-data-volume"></a>Správa maximálního denního objemu dat

Můžete nakonfigurovat denní limit a omezit tak denní příjem dat pro váš pracovní prostor, ale použijte pozornost jak vaším cílem by neměl být k dosažení denního limitu.  V opačném případě můžete přijít o data po zbytek dne, který může mít vliv na ostatní služby Azure a řešení, jehož funkce může záviset na aktuální data, která je k dispozici v pracovním prostoru.  V důsledku toho možnost sledovat a přijímat upozorní, když se to týká podmínky stavu prostředků podporující IT služby.  Denní limit je určený k použití jako způsob, jak spravovat neočekávané zvýšení objemu dat ze spravovaných prostředků a zůstat v rámci vašeho limitu, nebo pokud chcete omezit neplánované poplatky pro váš pracovní prostor.  

Po dosažení denního limitu pro zbytek dne zastaví kolekci fakturovatelné datových typů. Zobrazí se upozornění banner v horní části stránky pro vybraný pracovní prostor Log Analytics a operace událost je odeslána do *operace* tabulky v rámci **LogManagement** kategorie. Shromažďování dat bude pokračovat po resetování času definované v části *denní limit se nastaví na*. Doporučujeme definovat pravidlo výstrahy založené na této události operace nakonfigurovaný tak, aby upozornit po dosažení denního datového limitu. 

> [!NOTE]
> Denní limit nezastaví shromažďování dat z Azure Security Center, s výjimkou pracovních prostorů, ve kterých Azure Security Center byl nainstalován před 19. června 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifikujte jaké denního limitu pro definování dat

Kontrola [využití Log Analytics a odhadované náklady](usage-estimated-costs.md) pochopit trend příjem dat a k čemu slouží k definování denní limit objemu. Je nutné zvážit opatrně, protože nebude možné po dosažení limitu monitorování prostředků. 

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

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
   - Název signálu: vlastní prohledávání protokolů
   - Vyhledávací dotaz: operace | má-li podrobnosti "nadměrnou.
   - Na základě: počet výsledků
   - Podmínka: Větší než
   - Prahová hodnota: 0
   - Období: 5 (minuty)
   - Frekvence: 5 (minuty)
- Název pravidla upozornění: dosáhlo se denního datového limitu
- Závažnost: Upozornění (záv. 1)

Po upozornění je definován a dosáhnete limitu, výstraha se aktivuje a provádí definované ve skupině Akce odpovědi. Dokáže upozorňovat tým prostřednictvím e-mailu a textovými zprávami nebo Automatizujte akce pomocí webhooků, runbooků služby Automation nebo [integraci s externím řešení ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

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

Pokud chcete získat seznam počítačů, které se budou fakturovat jako uzly, pokud je pracovní prostor ve starší verzi na cenové úrovni uzlů, hledejte uzly, které odesílají **účtované datové typy** (některé datové typy jsou zdarma). K tomu použijte [vlastnost](log-standard-properties.md#_isbillable) `_IsBillable` a použijte pole plně kvalifikovaného názvu domény v levém krajním poli. Vrátí se seznam počítačů s fakturovanými daty:

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
| summarize billableNodes=dcount(computerName)
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
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

Všimněte si, že v klauzuli "kde IsBillable = true" filtruje z určité řešení, pro které neplatí žádné poplatky ingestování datových typů. 

Můžete přejít na trendy v datech najdete konkrétní datové typy, například pokud chcete zkoumat data z důvodu protokoly služby IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
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
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

Pokud chcete vidět, že počet účtovaných datové typy jsou odesílání dat k určitému počítači, použijte:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Objem dat podle prostředku Azure, skupiny prostředků nebo předplatného

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** fakturovaných událostí, které se __na počítač__dostanou, a to pomocí [vlastnosti](log-standard-properties.md#_resourceid)_ResourceId, která poskytuje úplnou cestu k prostředku:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** fakturovaných událostí pro __každé předplatné Azure__, analyzovat `_ResourceId` vlastnost jako:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Změnou `subscriptionId` na `resourceGroup` se zobrazí fakturovatelný příjmový objem dat skupinou prostředků Azure. 


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
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
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
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než* **Prahová hodnota** *0*.
   - **Časové období** na *180* minut a **Frekvenci upozornění** na každých *60* minut, protože se data o využití aktualizují pouze jednou za hodinu.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** na *Očekávaný větší objem dat než 100 GB během 24 hodin*.
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Pokud obdržíte upozornění, pomocí kroků v následující části můžete řešit potíže způsobující větší využití, než se čekalo.

## <a name="data-transfer-charges-using-log-analytics"></a>Poplatky za přenos dat pomocí Log Analytics

Odesílání dat do Log Analytics může mít za následek poplatky za šířku pásma dat. Jak je popsáno na [stránce ceny za Azure šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure v rámci dvou oblastí se v normální sazbě účtuje jako odchozí přenos dat. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (několik%) v porovnání s náklady na Log Analytics přijímání dat. V důsledku toho se řídí náklady na Log Analytics se musí soustředit na přijatý objem dat a máme vám Rady, jak to porozumět [.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


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

## <a name="limits-summary"></a>Souhrn omezení

Existují další limity Log Analytics, některé z nich závisí na cenové úrovni Log Analytics. Ty jsou popsány [zde](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Další kroky

- Další informace o použití vyhledávacího jazyka najdete [v tématu prohledávání protokolů v protokolu Azure monitor](../log-query/log-query-overview.md) . Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
- Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).
- Použijte [cílení na řešení](../insights/solution-targeting.md) a shromažďujte data pouze z požadované skupiny počítačů.
- Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí, Projděte si téma [Zásady filtrování Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md).
- Pokud chcete upravit nastavení shromažďování událostí, přečtěte si téma popisující [konfiguraci protokolu událostí](data-sources-windows-events.md).
- Pokud chcete upravit nastavení shromažďování syslogu, přečtěte si téma popisující [konfiguraci syslogu](data-sources-syslog.md).
