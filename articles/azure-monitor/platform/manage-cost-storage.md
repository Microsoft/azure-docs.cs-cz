---
title: Správa využití a nákladů na protokoly Azure Monitor
description: Přečtěte si, jak změnit cenový plán a spravovat zásady pro objem dat a uchovávání dat pro Log Analytics pracovní prostor v Azure Monitor.
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
ms.date: 05/12/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: ea289dbdf22f76c8ea716acf87b0b1a2da6ef0f9
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196595"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Správa využití a nákladů pomocí protokolů Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak pochopit a řídit náklady na protokoly Azure Monitor. Související článek, [sledování využití a odhadované náklady](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure pro různé cenové modely. Všechny ceny a náklady uvedené v tomto článku jsou třeba jenom pro účely. 

Protokoly Azure Monitor jsou určené ke škálování a podpoře shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje v podniku nebo v Azure, které jsou nasazené v Azure.  I když to může být primárním ovladačem pro vaši organizaci, náklady na efektivitu jsou nakonec základní ovladače. V tomto případě je důležité pochopit, že náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, ale také závisí na vybraném plánu a na tom, jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku si projdeme, jak můžete aktivně monitorovat monitorovaný objem dat a nárůst úložiště a definovat omezení pro řízení těchto přidružených nákladů. 

## <a name="pricing-model"></a>Cenový model

Výchozí ceny pro Log Analytics jsou Model průběžných **plateb** na základě ingestování objemu dat a volitelně i pro delší dobu uchovávání dat. Objem dat se měří jako velikost dat, která se budou ukládat. Každý Log Analytics pracovní prostor se účtuje jako samostatná služba a přispívá vám k fakturaci za vaše předplatné Azure. Množství příjmu dat může být značná v závislosti na následujících faktorech: 

  - Počet povolených řešení pro správu a jejich konfiguraci
  - Počet monitorovaných virtuálních počítačů
  - Typ dat shromažďovaných z každého monitorovaného virtuálního počítače 
  
Kromě modelu průběžných plateb Log Analytics má vrstvy **rezervace kapacity** , které vám umožní ve srovnání s průběžnými platbami ušetřit až 25%. Cena za rezervaci kapacity vám umožní koupit rezervaci od 100 GB za den. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. Úrovně rezervace kapacity mají 31 dnů v období závazku. Během období závazku můžete přejít na úroveň rezervace kapacity vyšší úrovně (která bude restartovala 31. období závazku), ale nemůžete přejít zpět na průběžné platby nebo na nižší úroveň rezervace kapacity až po dokončení období závazku. Faktura za úrovně rezervace kapacity se provádí každý den. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách Log Analytics s průběžnými platbami a rezervací kapacity. 

Ve všech cenových úrovních se objem dat počítá z řetězcové reprezentace dat, která je připravená k uložení. Některé [vlastnosti společné pro všechny datové typy](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) nejsou zahrnuté do výpočtu velikosti události, včetně `_ResourceId` , `_ItemId` `_IsBillable` a `_BilledSize` .

Všimněte si také, že některá řešení, jako je [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Správa konfigurace a konfigurace](https://azure.microsoft.com/pricing/details/automation/) [Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/) , mají své vlastní cenové modely. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics vyhrazené clustery

Log Analytics vyhrazené clustery jsou kolekce pracovních prostorů do jednoho spravovaného clusteru Azure Průzkumník dat, který podporuje pokročilé scénáře, jako jsou [klíče spravované zákazníky](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys).  Log Analytics vyhrazené clustery podporují jenom cenový model rezervace kapacity od 1000 GB za den s 25% slevou ve srovnání s cenami za průběžné platby. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. Rezervace kapacity clusteru má po zvýšení úrovně rezervace 31 dní období závazku. Během období závazku nelze úroveň rezervace kapacity snížit, ale je možné ji kdykoli zvýšit. Přečtěte si další informace o [vytváření clusterů Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) a [jejich přiřazování k pracovním prostorům](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource).  

Úroveň rezervace kapacity clusteru je nakonfigurována prostřednictvím programově Azure Resource Manager s použitím `Capacity` parametru v `Sku` . `Capacity`Hodnota je určena v jednotkách GB a může mít hodnoty 1000 GB/den nebo více v přírůstcích po 100 GB za den. [Zde](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource)je podrobně popsán. Pokud váš cluster potřebuje rezervaci nad 2000 GB za den, kontaktujte nás na adrese [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Vzhledem k tomu, že faktura pro ingestovaná data probíhá na úrovni clusteru, pracovní prostory přidružené k tomuto clusteru už nemají cenovou úroveň. Množství zpracovaných dat z každého pracovního prostoru přidruženého ke clusteru se agreguje za účelem výpočtu denního vyúčtování clusteru. Všimněte si, že přidělení na základě uzlů z [Azure Security Center](https://docs.microsoft.com/azure/security-center/) se aplikují na úrovni pracovního prostoru před touto agregací agregovaných dat napříč všemi pracovními prostory v clusteru. Uchovávání dat se pořád účtuje na úrovni pracovního prostoru. Všimněte si, že při vytváření clusteru začíná fakturace clusteru bez ohledu na to, jestli byly pracovní prostory přidružené ke clusteru. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí 

Pokud protokoly Azure Monitor ještě nepoužíváte, můžete pomocí [cenové kalkulačky Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Log Analytics. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou Azure Monitor dlaždici. Posuňte se dolů na stránku Azure Monitor a v rozevíracím seznamu Typ vyberte Log Analytics.  Tady můžete zadat počet virtuálních počítačů a GB dat, která se mají z každého virtuálního počítače shromažďovat. Z typického virtuálního počítače Azure se typicky ingestují 1 až 3 GB datového měsíce. Pokud už vyhodnocujete Azure Monitor protokoly, můžete použít statistiku dat z vlastního prostředí. Níže najdete informace o tom, jak určit [Počet monitorovaných virtuálních počítačů](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) a objem dat, na které [váš pracovní prostor pracuje](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení nákladů na využití a odhadované náklady

Pokud nyní používáte protokoly Azure Monitor, je snadné pochopit, jaké náklady jsou pravděpodobně založené na nedávných vzorech použití. K tomu použijte **Log Analytics využití a odhadované náklady** na kontrolu a analýzu využití dat. Ukazuje, kolik dat je každé řešení shromažďováno, kolik dat se zachovává, a odhad nákladů na základě množství přijatých dat a dalšího uchovávání nad rámec zahrnutého množství.

![Využití a odhadované náklady](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Pokud chcete svá data prozkoumat podrobněji, klikněte na ikonu v pravém horním rohu obou grafů na stránce **využití a odhadované náklady** . Nyní můžete s tímto dotazem pracovat a prozkoumat podrobnější informace o jeho využití.  

![Zobrazení protokolů](media/manage-cost-storage/logs.png)

Na stránce **využití a odhadované náklady** si můžete prohlédnout svůj objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávaná v pracovním prostoru Log Analytics.  Kliknutím na **Podrobnosti o využití** v horní části stránky zobrazíte řídicí panel využití s informacemi o trendech objemu dat podle zdroje, počítačů a nabídky. Chcete-li zobrazit a nastavit denní limit nebo upravit dobu uchování, klikněte na položku **Správa objemu dat**.
 
Do faktury Azure se přidají poplatky za Log Analytics. Podrobnosti o fakturaci Azure můžete zobrazit v části fakturace Azure Portal nebo v [portál fakturace Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Zobrazení využití Log Analytics na faktuře Azure 

Azure poskytuje skvělou užitečnou funkci centra [Azure cost management + fakturace](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Například funkce "cost Analysis" umožňuje zobrazit vaše výdaje na prostředky Azure. Nejdřív přidejte filtr podle "typ prostředku" (do Microsoft. operationalinsights/Workspace pro Log Analytics a Microsoft. operationalinsights/Workspace for Log Analytics clusters) vám umožní sledovat výdaje na Log Analytics. Pak u možnosti "seskupit podle" vyberte kategorii měřičů "nebo" měřič ".  Všimněte si, že jiné služby, například Azure Security Center a Sentinel Azure, účtují své využití také pomocí Log Analytics prostředků pracovního prostoru. Chcete-li zobrazit mapování na název služby, můžete místo grafu vybrat zobrazení tabulky. 

Lepší porozumění vašemu využití můžete získat [stažením vašeho využití z Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Ve stažených tabulkách vidíte využití podle prostředku Azure (např. Log Analytics pracovní prostor) za den. V této excelové tabulce můžete využití vašich Log Analytics pracovních prostorů najít prvním filtrováním ve sloupci měřiče měření, ve kterém se zobrazí "Log Analytics", "přehledy a analýzy (používané některými staršími cenovými úrovněmi) a" Azure Monitor "(používané cenovými úrovněmi rezervací kapacity) a pak přidání filtru do sloupce" ID instance ", který je" obsahuje pracovní prostor "nebo" obsahuje cluster "(druhý k zahrnutí Log Analytics využití clusteru). Využití se zobrazí ve sloupci "spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou další podrobnosti, které vám pomůžou [pochopit Microsoft Azureovou fakturaci](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Pokud chcete změnit Log Analytics cenové úrovně vašeho pracovního prostoru, 

1. V Azure Portal otevřete z pracovního prostoru **využití a odhadované náklady** , kde se zobrazí seznam všech cenových úrovní dostupných pro tento pracovní prostor.

2. Přečtěte si odhadované náklady na jednotlivé cenové úrovně. Tento odhad vychází z posledních 31 dnů od použití, takže tento odhad nákladů se spoléhá na posledních 31 dní, které jsou součástí typického využití. V následujícím příkladu vidíte, jak na základě vzorů dat během posledních 31 dnů bude tento pracovní prostor méně levnější v rámci vrstvy s průběžnými platbami (#1) v porovnání s úrovní rezervace kapacity 100 GB/den (#2).  

    ![Cenové úrovně](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Po kontrole odhadovaných nákladů na základě posledních 31 dnů využití se rozhodnete změnit cenovou úroveň kliknutím na **Vybrat**.  

[Cenovou úroveň můžete také nastavit prostřednictvím Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí `sku` parametru ( `pricingTier` v Azure Resource Manager šabloně). 

## <a name="legacy-pricing-tiers"></a>Starší cenové úrovně

Předplatná, která měl Log Analytics pracovní prostor nebo prostředek Application Insights před 2. dubna 2018 nebo jsou propojená s smlouva Enterprise, která začala před 1. února 2019, bude i nadále mít přístup k používání starších cenových úrovní: **Free**, **Standalone (za GB)** a **per Node (OMS)**.  Pracovní prostory v bezplatné cenové úrovni budou mít denní příjem dat omezený na 500 MB (kromě datových typů zabezpečení shromažďovaných v Azure Security Center) a uchovávání dat je omezeno na 7 dní. Cenová úroveň Free je určena pouze pro účely vyhodnocení. Pracovní prostory v cenové úrovni samostatného nebo počtu uzlů mají uživatelsky konfigurovatelné uchovávání dat od 30 do 730 dnů.

Cenové úrovně na jednotlivé uzly se účtují za monitorované virtuální počítače (uzel) na základě členitosti hodin. U každého monitorovaného uzlu má pracovní prostor přiděleno 500 MB dat za den, který se neúčtuje. Toto přidělení je agregované na úrovni pracovního prostoru. Data ingestovaná nad agregovaným denním přidělením dat se účtují za GB jako nadlimitní využití dat. Všimněte si, že na faktuře bude služba **Insight and Analytics** Log Analytics využití, pokud je pracovní prostor v cenové úrovni podle počtu uzlů. 

> [!TIP]
> Pokud má váš pracovní prostor přístup k cenové úrovni **na jednotlivých uzlech** , ale zjistíte, jestli by byl méně nákladně v úrovni průběžných plateb, můžete [pomocí následujícího dotazu](#evaluating-the-legacy-per-node-pricing-tier) snadno získat doporučení. 

Pracovní prostory vytvořené před dubna 2016 mají přístup také k původním cenovým úrovním **Standard** a **Premium** , které mají pevnou dobu uchovávání dat 30 a 365 dnů v uvedeném pořadí. Nové pracovní prostory nelze vytvořit v cenové úrovni **Standard** nebo **Premium** a pokud je pracovní prostor přesunut z těchto úrovní, nelze jej přesunout zpět. 

Další podrobnosti o omezeních cenové úrovně jsou k dispozici [zde](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo doplňku OMS pro System Center, vyberte cenovou úroveň Log Analytics pro *jednotlivé uzly* .

## <a name="change-the-data-retention-period"></a>Změna doby uchovávání dat

Následující postup popisuje, jak nakonfigurovat, jak dlouho budou data protokolu uchovávána ve vašem pracovním prostoru. Uchovávání dat je možné nakonfigurovat z 30 na 730 dní (2 roky) pro všechny pracovní prostory, pokud nepoužíváte starší verzi bezplatné cenové úrovně. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách pro delší dobu uchovávání dat. 

### <a name="default-retention"></a>Výchozí uchování

Pokud chcete nastavit výchozí dobu uchovávání pro váš pracovní prostor, 
 
1. V Azure Portal v pracovním prostoru v levém podokně vyberte **využití a odhadované náklady** .
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Správa objemu dat**.
3. V podokně přesunutím posuvníku zvyšte nebo snižte počet dní a potom klikněte na tlačítko **OK**.  Pokud jste na *bezplatné* úrovni, nebudete moct upravit dobu uchovávání dat a abyste mohli řídit toto nastavení, musíte upgradovat na placenou úroveň.

    ![Změnit nastavení uchovávání dat pracovního prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)

Když je doba uchování nižší, před odebráním nejstarší dat se zobrazí několik dní po dobu odkladu. 
    
Uchovávání lze také [nastavit prostřednictvím Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) pomocí `retentionInDays` parametru. Pokud navíc nastavíte uchovávání dat na 30 dní, můžete spustit okamžitou mazání starších dat pomocí `immediatePurgeDataOn30Days` parametru, který může být užitečný pro scénáře související s dodržováním předpisů. Tato funkce se zveřejňuje jenom prostřednictvím Azure Resource Manager. 


Dva datové typy-- `Usage` a `AzureActivity` --se ve výchozím nastavení uchovávají po dobu 90 dnů a za tento 90 dne se neúčtují žádné poplatky. Tyto datové typy jsou také zdarma z poplatků za příjem dat. 

Datové typy z prostředků Application Insights založených na pracovních prostorech ( `AppAvailabilityResults` , `AppBrowserTimings` ,, `AppDependencies` `AppExceptions` , `AppEvents` , `AppMetrics` , `AppPageViews` , `AppPerformanceCounters` , `AppRequests` `AppSystemEvents` a `AppTraces` ) se ve výchozím nastavení uchovávají i po dobu 90 dnů a za toto 90 dne se neúčtují žádné poplatky. Jejich uchování je možné upravit pomocí funkce uchování podle datového typu. 


### <a name="retention-by-data-type"></a>Uchovávání dat podle datového typu

Je také možné zadat různá nastavení uchovávání pro jednotlivé datové typy od 30 do 730 dnů (s výjimkou pracovních prostorů ve starší verzi bezplatné cenové úrovně). Každý datový typ je dílčím prostředkem pracovního prostoru. Například tabulku SecurityEvent lze vyřešit v [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) jako:

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

Platné hodnoty pro `retentionInDays` jsou od 30 do 730.

`Usage` `AzureActivity` Datové typy a nelze nastavit s vlastním uchováváním. Budou platit až do maximálního počtu výchozích uchovávání pracovních prostorů nebo 90 dnů. 

Skvělý nástroj pro připojení přímo k Azure Resource Manager k nastavení možnosti uchovávání informací podle datového typu je [ARMclient](https://github.com/projectkudu/ARMClient)nástroje OSS.  Další informace o ARMclient od článků získáte v článku [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) a [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Tady je příklad použití ARMClient a nastavení dat SecurityEvent na 730 dne:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Nastavení uchovávání pro jednotlivé datové typy lze použít ke snížení nákladů na uchovávání dat.  Pro data shromážděná od října 2019 (při vydání této funkce) snížení doby uchování některých datových typů může snížit náklady na uchovávání dat v průběhu času.  U dříve shromážděných dat nastavení nižšího uchování pro jednotlivý typ nebude mít vliv na náklady na uchování.  

## <a name="manage-your-maximum-daily-data-volume"></a>Správa maximálního denního objemu dat

Můžete nakonfigurovat denní limit a omezit každodenní ingestování pro váš pracovní prostor, ale pečlivě používejte, protože by váš cíl neměl mít denní limit.  Jinak ztratíte data zbývajícího dne, což může mít vliv na další služby a řešení Azure, jejichž funkce můžou záviset na dostupnosti dat v pracovním prostoru.  Výsledkem je, že schopnost sledovat a přijímat výstrahy, když jsou ovlivněny stavové stavy prostředků, které podporují IT služby.  Denní limit je určený k použití jako způsob, jak spravovat neočekávané zvýšení objemu dat ze spravovaných prostředků a zůstat v rámci vašeho limitu, nebo pokud chcete omezit neplánované poplatky pro váš pracovní prostor.  

Brzy po dosažení denního limitu se kolekce fakturovatelných datových typů zastaví pro zbytek dne. (Latence vyplývající z použití denního limitu může znamenat, že se limit nepoužije přesně na určenou denní úroveň Cap.) V horní části stránky se zobrazí banner s upozorněním pro vybraný Log Analytics pracovní prostor a událost operace se odešle do tabulky *Operation* v kategorii **LogManagement** . Shromažďování dat se obnoví po uplynutí doby obnovení definované v rámci *denního limitu*. Doporučujeme definovat pravidlo výstrahy na základě této události operace, která je nakonfigurována tak, aby po dosažení denního limitu dat upozornila na oznámení. 

> [!WARNING]
> Denní limit nezastaví shromažďování dat z Azure Security Center, s výjimkou pracovních prostorů, ve kterých Azure Security Center byl nainstalován před 19. června 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Určete, který denní limit dat se má definovat.

Přečtěte si [Log Analytics využití a odhadované náklady](usage-estimated-costs.md) , abyste porozuměli trendům příjmu dat a jaký je denní zakončení pro definování. Měli byste se považovat za pečlivě, protože jste ji vyhráli? po dosažení limitu by bylo možné monitorovat vaše prostředky. 

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

Následující postup popisuje, jak nakonfigurovat limit pro správu objemu dat, který Log Analytics pracovní prostor ingestovat za den.  

1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. Na stránce **využití a odhadované náklady** pro vybraný pracovní prostor klikněte v horní části stránky na možnost **Správa objemu dat** . 
3. Denní limit je ve výchozím nastavení **vypnutý** ? klikněte na **zapnout** a pak nastavte limit počtu dat v GB za den.

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

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Řešení potíží způsobujících větší využití, než se čekalo

Větší využití je způsobeno jedním nebo obojím z těchto aspektů:
- Více uzlů, než se čekalo na odesílání dat do Log Analytics pracovního prostoru
- Více dat, než se čekalo do Log Analytics pracovního prostoru (možná z důvodu zahájení použití nového řešení nebo změny konfigurace stávajícího řešení)

## <a name="understanding-nodes-sending-data"></a>Porozumění uzlům odesílajícím data

Chcete-li pochopit počet uzlů, které hlásí prezenční signály od agenta každý den za poslední měsíc, použijte

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Hodnota získat počet uzlů odesílajících data za posledních 24 hodin používá dotaz: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Chcete-li získat seznam uzlů, které odesílají data (a objem dat odesílaných každým), je možné použít následující dotaz:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Tyto dotazy můžete použít `union *` zřídka, protože kontroly napříč datovými typy jsou [náročné na prostředky](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) , které je potřeba provést. Pokud nepotřebujete výsledky **na počítač** , zadejte dotaz na datový typ použití (viz níže).

## <a name="understanding-ingested-data-volume"></a>Principy ingestných objemů dat

Na stránce **využití a odhadované náklady** zobrazuje ingestování *dat na řešení* celkový objem odeslaných dat a množství, které jednotlivé řešení odesílají. Díky tomu můžete určit trendy, jako je například to, jestli celkové využití dat (nebo využití konkrétního řešení) roste, zbývající stabilní nebo klesající. 

### <a name="data-volume-for-specific-events"></a>Objem dat pro konkrétní události

Chcete-li se podívat na velikost přijatých dat pro určitou sadu událostí, můžete zadat dotaz na konkrétní tabulku (v tomto příkladu `Event` ) a pak dotaz omezit na události, které vás zajímají (v tomto příkladě ID události 5145 nebo 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Všimněte si, že klauzule `where IsBillable = true` filtruje datové typy z určitých řešení, pro které se neúčtují žádné poplatky za ingestování. 

### <a name="data-volume-by-solution"></a>Objem dat podle řešení

Dotaz použitý k zobrazení objemu fakturovatelných dat podle řešení za poslední měsíc (s výjimkou posledního částečného dne) je:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Klauzule WITH `TimeGenerated` je určena pouze k zajištění toho, aby se možnosti dotazování v Azure Portal vypadaly až za výchozí 24 hodin. Při použití datového typu použití `StartTime` a `EndTime` představují časové intervaly, pro které jsou zobrazeny výsledky. 

### <a name="data-volume-by-type"></a>Objem dat podle typu

Další podrobnosti můžete prozkoumat a zobrazit tak trendy dat pro datový typ:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Nebo pokud chcete zobrazit tabulku podle řešení a typu za poslední měsíc,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Objem dat podle počítače

`Usage`Datový typ neobsahuje informace na úrovni počítače. Chcete-li zobrazit **Velikost** zpracovaných dat na jeden počítač, použijte `_BilledSize` [vlastnost](log-standard-properties.md#_billedsize), která poskytuje velikost v bajtech:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Vlastnost](log-standard-properties.md#_isbillable) určuje, jestli se za ingestovaná data účtují poplatky. 

Chcete-li zobrazit **počet** fakturovaných událostí zpracovaných na počítač, použijte 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

> [!TIP]
> Tyto dotazy můžete použít `union  *` zřídka, protože kontroly napříč datovými typy jsou [náročné na prostředky](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) , které je potřeba provést. Pokud nepotřebujete výsledky **na počítač** , pak na něj zadejte dotaz na datový typ použití.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Objem dat podle prostředku Azure, skupiny prostředků nebo předplatného

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** zpracovaných dat __na jeden počítač__, použít [vlastnost](log-standard-properties.md#_resourceid)_ResourceId, která poskytuje úplnou cestu k prostředku:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** zpracovaných dat __na předplatné Azure__, analyzovat `_ResourceId` vlastnost jako:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Změnou `subscriptionId` na se `resourceGroup` zobrazí fakturovatelný příjmový objem dat skupinou prostředků Azure. 

> [!TIP]
> Tyto dotazy můžete použít `union  *` zřídka, protože kontroly napříč datovými typy jsou [náročné na prostředky](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) , které je potřeba provést. Pokud nepotřebujete výsledky v rámci předplatného, skupiny prostředků nebo názvu prostředku, pak dotaz na datový typ použití.

> [!WARNING]
> Některá pole datového typu použití, ale stále ve schématu, jsou zastaralá a jejich hodnoty se už neplní. Jedná se o **počítač** a pole související s ingestování (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** a **AverageProcessingTimeMs**).


### <a name="querying-for-common-data-types"></a>Dotazování na Common data types

Pokud chcete dig hlouběji ke zdroji dat pro určitý datový typ, tady jsou některé užitečné příklady dotazů:

+ Prostředky **Application Insights založené na pracovním prostoru**
  - [Další informace](https://docs.microsoft.com/azure/azure-monitor/app/pricing#data-volume-for-workspace-based-application-insights-resources)
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

Mezi návrhy na snížení objemu shromažďovaných protokolů patří:

| Zdroj velkého objemu dat | Postup snížení objemu dat |
| -------------------------- | ------------------------- |
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [audit registru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změňte shromažďování protokolů prostředků tak, aby se: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | K shromažďování dat z požadovaných skupin počítačů použijte [cílení na řešení](../insights/solution-targeting.md) . |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Získávání uzlů, které se účtují v cenové úrovni podle počtu uzlů

Pokud chcete získat seznam počítačů, které se budou fakturovat jako uzly, pokud je pracovní prostor ve starší verzi na cenové úrovni uzlů, hledejte uzly, které odesílají **účtované datové typy** (některé datové typy jsou zdarma). K tomu použijte `_IsBillable` [vlastnost](log-standard-properties.md#_isbillable) a použijte pole s plně kvalifikovaným názvem domény v levém krajním poli. Vrátí počet počítačů s fakturovanými daty za hodinu (což je členitost, při které se uzly počítají a účtují):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Vyhodnocení starší cenové úrovně na úrovni jednotlivých uzlů

Rozhodnutí o tom, jestli jsou pracovní prostory s přístupem k starší verzi na úrovni **jednotlivých uzlů** v této úrovni nebo v aktuální úrovni **rezervace kapacity** pro průběžné **platby** často pro zákazníky obtížné vyhodnotit.  To zahrnuje porozumění vzájemnému poměru mezi pevnými náklady na uzel na základě cenové úrovně jednotlivých uzlů a jeho zahrnutým přidělením dat 500 MB/uzel/den a náklady spojené s příjmem dat na úrovni průběžných plateb (za GB). 

K usnadnění tohoto posouzení se dá použít následující dotaz k vytvoření doporučení pro optimální cenovou úroveň na základě vzorů používání pracovního prostoru.  Tento dotaz prohlíží monitorované uzly a data ingestovaná do pracovního prostoru za posledních 7 dnů. každý den vyhodnocuje, která cenová úroveň byla optimální. Chcete-li použít dotaz, je nutné zadat

1. zda pracovní prostor používá Azure Security Center nastavením `workspaceHasSecurityCenter` na `true` nebo `false` , 
2. Aktualizujte ceny, pokud máte specifické slevy a
3. Zadejte počet dní, po který se má vyhledat a analyzovat `daysToEvaluate` . To je užitečné v případě, že dotaz trvá příliš dlouho, než se podíváme na 7 dní dat. 

Tady je dotaz doporučení cenové úrovně:

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union withsource = tt * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Tento dotaz nepředstavuje přesnou replikaci toho, jak se vypočítává využití, ale ve většině případů bude fungovat s doporučeními cenové úrovně.  

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo doplňku OMS pro System Center, vyberte cenovou úroveň Log Analytics pro *jednotlivé uzly* .

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

## <a name="data-transfer-charges-using-log-analytics"></a>Poplatky za přenos dat pomocí Log Analytics

Odesílání dat do Log Analytics může mít za následek poplatky za šířku pásma dat. Jak je popsáno na [stránce ceny za Azure šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure v rámci dvou oblastí se v normální sazbě účtuje jako odchozí přenos dat. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (několik%) v porovnání s náklady na Log Analytics přijímání dat. V důsledku toho se řídí náklady na Log Analytics se musí soustředit na přijatý objem dat a máme vám Rady, jak to porozumět [.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


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
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Zkušební verze skončila.<br> Platnost Azure Pass vypršela.<br> Dosáhlo se limitu měsíčního útraty (například na předplatném MSDN nebo Visual Studio).|Přechod na placené předplatné<br> Odebrat limit nebo počkat na obnovení limitu|

Chcete-li být upozorněni na zastavení shromažďování dat, postupujte podle kroků popsaných v části *Vytvoření výstrahy denního datového zakončení* , která bude oznámena při zastavení shromažďování dat. Pomocí kroků popsaných v tématu [Vytvoření skupiny akcí](action-groups.md) nakonfigurujte akci e-mailu, Webhooku nebo Runbooku pro pravidlo výstrahy. 

## <a name="limits-summary"></a>Souhrn omezení

Existují další limity Log Analytics, některé z nich závisí na cenové úrovni Log Analytics. Ty jsou popsány [zde](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Další kroky

- Další informace o použití vyhledávacího jazyka najdete [v tématu prohledávání protokolů v protokolu Azure monitor](../log-query/log-query-overview.md) . Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
- Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](alerts-metric.md).
- K shromažďování dat z požadovaných skupin počítačů použijte [cílení na řešení](../insights/solution-targeting.md) .
- Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí, Projděte si téma [Zásady filtrování Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Změňte [konfiguraci čítačů výkonu](data-sources-performance-counters.md).
- Pokud chcete upravit nastavení shromažďování událostí, zkontrolujte [konfiguraci protokolu událostí](data-sources-windows-events.md).
- Pokud chcete upravit nastavení kolekce syslog, zkontrolujte [konfiguraci syslogu](data-sources-syslog.md).