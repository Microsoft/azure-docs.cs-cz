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
ms.date: 03/03/2021
ms.author: bwren
ms.openlocfilehash: 4d546401baa7edc7725e3fdb23065009895f9c1e
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027427"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Správa využití a nákladů pomocí protokolů Azure Monitoru    

> [!NOTE]
> Tento článek popisuje, jak pochopit a řídit náklady na protokoly Azure Monitor. Související článek, [sledování využití a odhadované náklady](../usage-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure pro různé cenové modely. Všechny ceny a náklady uvedené v tomto článku jsou třeba jenom pro účely. 

Protokoly Azure Monitor jsou určené ke škálování a podpoře shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje v podniku nebo v Azure, které jsou nasazené v Azure.  I když to může být primárním ovladačem pro vaši organizaci, náklady na efektivitu jsou nakonec základní ovladače. V tomto případě je důležité pochopit, že náklady na Log Analytics pracovní prostor nejsou založené jenom na objemu shromažďovaných dat, ale také závisí na vybraném plánu a na tom, jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku si projdeme, jak můžete aktivně monitorovat monitorovaný objem dat a nárůst úložiště a definovat omezení pro řízení těchto přidružených nákladů. 

## <a name="pricing-model"></a>Cenový model

Výchozí ceny pro Log Analytics jsou Model průběžných **plateb** na základě ingestování objemu dat a volitelně i pro delší dobu uchovávání dat. Objem dat se měří jako velikost dat, která se uloží v GB (10 ^ 9 bajtů). Každý Log Analytics pracovní prostor se účtuje jako samostatná služba a přispívá vám k fakturaci za vaše předplatné Azure. Množství příjmu dat může být značná v závislosti na následujících faktorech: 

  - Počet povolených řešení pro správu a jejich konfiguraci
  - Počet monitorovaných virtuálních počítačů
  - Typ dat shromažďovaných z každého monitorovaného virtuálního počítače 
  
Kromě modelu průběžných plateb Log Analytics má vrstvy **rezervace kapacity** , které vám umožní ve srovnání s průběžnými platbami ušetřit až 25%. Cena za rezervaci kapacity vám umožní koupit rezervaci od 100 GB za den. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. Úrovně rezervace kapacity mají 31 dnů v období závazku. Během období závazku můžete přejít na úroveň rezervace kapacity vyšší úrovně (která bude restartovala 31. období závazku), ale nemůžete přejít zpět na průběžné platby nebo na nižší úroveň rezervace kapacity až po dokončení období závazku. Faktura za úrovně rezervace kapacity se provádí každý den. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách Log Analytics s průběžnými platbami a rezervací kapacity. 

U všech cenových úrovní je velikost dat události počítána z řetězcové reprezentace vlastností, které jsou uloženy v Log Analytics pro tuto událost, bez ohledu na to, zda jsou data odesílána z agenta nebo přidána během procesu příjmu. To zahrnuje všechna [vlastní pole](custom-fields.md) , která jsou přidána, když jsou shromažďována data a uložena v Log Analytics. Při výpočtu velikosti události jsou vyloučeny některé vlastnosti společné pro všechny typy dat, včetně některých [log Analyticsch standardních vlastností](./log-standard-columns.md). To zahrnuje `_ResourceId` , `_SubscriptionId` , `_ItemId` , `_IsBillable` `_BilledSize` a `Type` . Všechny ostatní vlastnosti uložené v Log Analytics jsou zahrnuté do výpočtu velikosti události. Některé datové typy jsou zcela bezplatné poplatky za příjem dat, například AzureActivity, prezenční signál a typy využití. Chcete-li zjistit, zda byla událost vyloučena z fakturace pro příjem dat, můžete použít `_IsBillable` vlastnost, jak je uvedeno [níže](#data-volume-for-specific-events). Použití je hlášeno v GB (1,0 E9 bajtů). 

Všimněte si také, že některá řešení, jako je [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Správa konfigurace a konfigurace](https://azure.microsoft.com/pricing/details/automation/) [Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/) , mají své vlastní cenové modely. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics vyhrazené clustery

Log Analytics vyhrazené clustery jsou kolekce pracovních prostorů do jednoho spravovaného clusteru Azure Průzkumník dat, který podporuje pokročilé scénáře, jako jsou [klíče spravované zákazníky](customer-managed-keys.md).  Log Analytics vyhrazené clustery používají cenový model rezervace kapacity, který musí být nakonfigurovaný aspoň 1000 GB/měsíc. Tato úroveň kapacity má ve srovnání s cenami za průběžné platby 25% slevu. Veškeré využití nad úrovní rezervace se bude účtovat podle tarifu průběžných plateb. Rezervace kapacity clusteru má po zvýšení úrovně rezervace 31 dní období závazku. Během období závazku nelze úroveň rezervace kapacity snížit, ale je možné ji kdykoli zvýšit. Když jsou pracovní prostory přidruženy k clusteru, účtování příjmu dat pro tyto pracovní prostory se provádí na úrovni clusteru pomocí nakonfigurované úrovně rezervace kapacity. Přečtěte si další informace o [vytváření clusterů Log Analytics](customer-managed-keys.md#create-cluster) a [jejich přiřazování k pracovním prostorům](customer-managed-keys.md#link-workspace-to-cluster). Informace o cenách rezervací kapacity najdete na [stránce s cenami Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

Úroveň rezervace kapacity clusteru je konfigurována prostřednictvím programu programově s Azure Resource Manager pomocí `Capacity` parametru v `Sku` . `Capacity`Hodnota je určena v jednotkách GB a může mít hodnoty 1000 GB/den nebo více v přírůstcích po 100 GB za den. Tato podrobná [Azure monitor klíč spravovaný zákazníkem](customer-managed-keys.md#create-cluster). Pokud váš cluster potřebuje rezervaci nad 2000 GB za den, kontaktujte nás na adrese [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Existují dva režimy fakturace pro použití v clusteru. Tyto parametry mohou být zadány `billingType` parametrem při [konfiguraci clusteru](customer-managed-keys.md#customer-managed-key-operations). Tyto dva režimy jsou: 

1. **Cluster**: v tomto případě (což je výchozí nastavení) se fakturace pro ingestovaná data provádí na úrovni clusteru. Množství zpracovaných dat z každého pracovního prostoru přidruženého ke clusteru se agreguje za účelem výpočtu denního vyúčtování clusteru. Všimněte si, že přidělení na základě uzlů z [Azure Security Center](../../security-center/index.yml) se aplikují na úrovni pracovního prostoru před touto agregací agregovaných dat napříč všemi pracovními prostory v clusteru. 

2. **Pracovní prostory**: náklady na rezervaci kapacity pro váš cluster se úměrně připočítají k pracovním prostorům v clusteru (po zaúčtování pro přidělení podle uzlu z [Azure Security Center](../../security-center/index.yml) pro každý pracovní prostor.) Pokud je celkový objem dat zpracovaných v pracovním prostoru za den menší než rezervace kapacity, pak se každý pracovní prostor fakturuje za jeho ingestovaná data na základě sazby za nevyužitou kapacitu na GB tím, že je vyúčtováním zlomku kapacity rezervace a nevyužité části rezervace kapacity se účtují do prostředku clusteru. Pokud celkový objem dat, který se během dne ingestuje do pracovního prostoru, je větší než rezervace kapacity, pak se pro každý pracovní prostor účtuje zlomek kapacity na základě jeho zlomku a v každém pracovním prostoru se podílem přijatých dat nad rámec rezervace kapacity. K prostředku clusteru se nic neúčtuje, pokud je celkový objem dat ingestný do pracovního prostoru za den nad rezervací kapacity.

V možnostech fakturace clusteru se uchovávání dat účtuje podle pracovního prostoru. Všimněte si, že při vytváření clusteru začíná fakturace clusteru bez ohledu na to, jestli byly pracovní prostory přidružené ke clusteru. Všimněte si také, že pracovní prostory přidružené k clusteru už nemají cenovou úroveň.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Odhad nákladů na správu prostředí 

Pokud protokoly Azure Monitor ještě nepoužíváte, můžete pomocí [cenové kalkulačky Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Log Analytics. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou Azure Monitor dlaždici. Posuňte se dolů na stránku Azure Monitor a v rozevíracím seznamu Typ vyberte Log Analytics.  Tady můžete zadat počet virtuálních počítačů a GB dat, která se mají z každého virtuálního počítače shromažďovat. Z typického virtuálního počítače Azure se typicky ingestují 1 až 3 GB datového měsíce. Pokud už vyhodnocujete Azure Monitor protokoly, můžete použít statistiku dat z vlastního prostředí. Níže najdete informace o tom, jak určit [Počet monitorovaných virtuálních počítačů](#understanding-nodes-sending-data) a objem dat, na které [váš pracovní prostor pracuje](#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení nákladů na využití a odhadované náklady

Pokud nyní používáte protokoly Azure Monitor, je snadné pochopit, jaké náklady jsou pravděpodobně založené na nedávných vzorech použití. K tomu použijte  **Log Analytics využití a odhadované náklady** na kontrolu a analýzu využití dat. Ukazuje, kolik dat je každé řešení shromažďováno, kolik dat se zachovává, a odhad nákladů na základě množství přijatých dat a dalšího uchovávání nad rámec zahrnutého množství.

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Využití a odhadované náklady":::

Pokud chcete svá data prozkoumat podrobněji, klikněte na ikonu v pravém horním rohu obou grafů na stránce **využití a odhadované náklady** . Nyní můžete s tímto dotazem pracovat a prozkoumat podrobnější informace o jeho využití.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Zobrazení protokolů":::

Na stránce **využití a odhadované náklady** si můžete prohlédnout svůj objem dat za měsíc. Zahrnuje to všechna fakturovatelná a uchovávaná data v pracovním prostoru Log Analytics.  
 
Do faktury Azure se přidají poplatky za Log Analytics. Podrobnosti o fakturaci Azure můžete zobrazit v části fakturace Azure Portal nebo v [portál fakturace Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Zobrazení využití Log Analytics na faktuře Azure 

Azure poskytuje skvělou užitečnou funkci centra [Azure cost management + fakturace](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) . Například funkce "cost Analysis" umožňuje zobrazit vaše výdaje na prostředky Azure. Nejdřív přidejte filtr podle "typ prostředku" (do Microsoft. operationalinsights/Workspace pro Log Analytics a Microsoft. operationalinsights/cluster pro Log Analytics clustery), abyste mohli sledovat výdaje na Log Analytics. Pak u možnosti "seskupit podle" vyberte kategorii měřičů "nebo" měřič ".  Všimněte si, že jiné služby, například Azure Security Center a Sentinel Azure, účtují své využití také pomocí Log Analytics prostředků pracovního prostoru. Chcete-li zobrazit mapování na název služby, můžete místo grafu vybrat zobrazení tabulky. 

Další vysvětlení vašeho využití můžete získat [stažením informací o využití z webu Azure Portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Ve stažené tabulce uvidíte využití jednotlivých prostředků Azure (např. pracovního prostoru služby Log Analytics) po dnech. V této excelové tabulce můžete využití vašich Log Analytics pracovních prostorů najít prvním filtrováním ve sloupci měřiče měření, ve kterém se zobrazí "Log Analytics", "Insight and Analytics" (používané některými staršími cenovými úrovněmi) a "Azure Monitor" (používané cenovými úrovněmi rezervace kapacity) a pak přidat filtr do sloupce ID instance, který je "obsahuje pracovní prostor" nebo "obsahuje cluster" (druhý k zahrnutí Log Analyticsho využití clusteru). Využití se zobrazí ve sloupci "spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou také další podrobnosti, které vám pomůžou [porozumět informacím na faktuře za Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Změna cenové úrovně

Pokud chcete změnit Log Analytics cenové úrovně vašeho pracovního prostoru, 

1. V Azure Portal otevřete z pracovního prostoru **využití a odhadované náklady** , kde se zobrazí seznam všech cenových úrovní dostupných pro tento pracovní prostor.

2. Přečtěte si odhadované náklady na jednotlivé cenové úrovně. Tento odhad vychází z posledních 31 dnů od použití, takže tento odhad nákladů se spoléhá na posledních 31 dní, které jsou součástí typického využití. V následujícím příkladu vidíte, jak na základě vzorů dat během posledních 31 dnů bude tento pracovní prostor méně levnější v rámci vrstvy s průběžnými platbami (#1) v porovnání s úrovní rezervace kapacity 100 GB/den (#2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Cenové úrovně":::
    
3. Po kontrole odhadovaných nákladů na základě posledních 31 dnů využití se rozhodnete změnit cenovou úroveň kliknutím na **Vybrat**.  

[Cenovou úroveň můžete také nastavit prostřednictvím Azure Resource Manager](./resource-manager-workspace.md) pomocí `sku` parametru ( `pricingTier` v Azure Resource Manager šabloně). 

## <a name="legacy-pricing-tiers"></a>Starší cenové úrovně

Předplatná, která měl Log Analytics pracovní prostor nebo prostředek Application Insights před 2. dubna 2018 nebo jsou propojená s smlouva Enterprise, která začala před 1. února 2019, bude i nadále mít přístup k používání starších cenových úrovní: **Free**, **Standalone (za GB)** a **per Node (OMS)**.  Pracovní prostory v bezplatné cenové úrovni budou mít denní příjem dat omezený na 500 MB (kromě datových typů zabezpečení shromažďovaných v [Azure Security Center](../../security-center/index.yml)) a uchovávání dat je omezeno na 7 dní. Cenová úroveň Free je určena pouze pro účely vyhodnocení. Pracovní prostory v cenové úrovni samostatného nebo počtu uzlů mají uživatelsky konfigurovatelné uchovávání dat od 30 do 730 dnů.

Využití na samostatné cenové úrovni se účtuje podle povrstveného objemu dat. Je hlášen ve službě **Log Analytics** a měřič se nazývá "Analýza dat". 

Cenové úrovně na jednotlivé uzly se účtují za monitorované virtuální počítače (uzel) na základě členitosti hodin. U každého monitorovaného uzlu má pracovní prostor přiděleno 500 MB dat za den, který se neúčtuje. Tato alokace se počítá s hodinovou členitou a je agregovaná na úrovni pracovního prostoru každý den. Data ingestovaná nad agregovaným denním přidělením dat se účtují za GB jako nadlimitní využití dat. Všimněte si, že na faktuře bude služba **Insight and Analytics** Log Analytics využití, pokud je pracovní prostor v cenové úrovni podle počtu uzlů. Použití je hlášeno na třech měřičích:

1. Uzel: Toto je využití v počtu monitorovaných uzlů (virtuálních počítačů) v jednotkách uzlu * měsíců.
2. Nadlimitní využití dat na uzel: Jedná se o počet GB dat, která se převezmou nad agregovaným přidělením dat.
3. Data zahrnutá na uzel: Jedná se o množství zpracovaných dat, která byla pokrytá agregovaným přidělením dat. Tento měřič se používá také v případě, že je pracovní prostor ve všech cenových úrovních a zobrazuje množství dat, která jsou pokrytá Azure Security Center.

> [!TIP]
> Pokud má váš pracovní prostor přístup k cenové úrovni **na jednotlivých uzlech** , ale zjistíte, jestli by byl méně nákladně v úrovni průběžných plateb, můžete [pomocí následujícího dotazu](#evaluating-the-legacy-per-node-pricing-tier) snadno získat doporučení. 

Pracovní prostory vytvořené před dubna 2016 mají přístup také k původním cenovým úrovním **Standard** a **Premium** , které mají pevnou dobu uchovávání dat 30 a 365 dnů v uvedeném pořadí. Nové pracovní prostory nelze vytvořit v cenové úrovni **Standard** nebo **Premium** a pokud je pracovní prostor přesunut z těchto úrovní, nelze jej přesunout zpět. Měřiče příjmu dat pro tyto starší úrovně se nazývají "Analyzovaná data".

K dispozici je také některé chování mezi používáním starších Log Analytics vrstev a způsobu, jakým se účtují využití [Azure Security Center](../../security-center/index.yml). 

1. Pokud je pracovní prostor ve starší verzi úrovně Standard nebo Premium, Azure Security Center se bude účtovat jenom pro Log Analytics příjem dat, ne na uzel.
2. Pokud je pracovní prostor ve starší verzi na vrstvu uzlu, Azure Security Center se bude účtovat pomocí aktuálního [cenového modelu založeného na uzlu Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/). 
3. V jiných cenových úrovních (včetně rezervací kapacity), pokud Azure Security Center bylo povoleno před 19. června 2017, Azure Security Center bude účtováno pouze za Log Analytics přijímání dat. Jinak se Azure Security Center bude účtovat pomocí aktuálního cenového modelu založeného na uzlu Azure Security Center.

Další podrobnosti o omezeních cenové úrovně jsou k dispozici v [limitech, kvótách a omezeních předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Žádná z cenových úrovní starší verze neobsahuje regionální ceny.  

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo OMS Add-On pro System Center, vyberte cenovou úroveň Log Analytics *na jednotlivých uzlech* .

## <a name="log-analytics-and-security-center"></a>Log Analytics a Security Center

[Azure Security Center](../../security-center/index.yml) fakturace je úzce spjata s Log Analytics fakturace. Security Center poskytuje 500 MB/s pro následující podmnožinu [datových typů zabezpečení](/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) a datové typy aktualizace a UpdateSummary, pokud je povolené Update Management řešení neběží v pracovním prostoru nebo cílení řešení. Pokud je pracovní prostor ve starší verzi na cenové úrovni pro jednotlivé uzly, Security Center a Log Analytics alokace se zkombinují a společně se aplikují na všechna fakturovatelná ingestovaná data.  

## <a name="change-the-data-retention-period"></a>Změna doby uchovávání dat

Následující postup popisuje, jak nakonfigurovat, jak dlouho budou data protokolu uchovávána ve vašem pracovním prostoru. Uchovávání dat na úrovni pracovního prostoru můžete nakonfigurovat z 30 na 730 dní (2 roky) pro všechny pracovní prostory, pokud nepoužíváte starší verzi bezplatné cenové úrovně. Uchovávání pro jednotlivé datové typy lze nastavit na hodnotu nižší než 4 dny. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách pro delší dobu uchovávání dat.  Pokud chcete uchovávat data déle než 730 dní, zvažte použití [exportu dat Log Analytics pracovního prostoru](logs-data-export.md).

### <a name="workspace-level-default-retention"></a>Výchozí uchování na úrovni pracovního prostoru

Pokud chcete nastavit výchozí dobu uchovávání pro váš pracovní prostor, 
 
1. V Azure Portal v pracovním prostoru v levém podokně vyberte **využití a odhadované náklady** .
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Uchovávání dat**.
3. Pomocí posuvníku v podokně zvyšte nebo snižte počet dnů a pak klikněte na **OK**.  Pokud jste na *bezplatné* úrovni, nebudete moct upravit dobu uchovávání dat a abyste mohli řídit toto nastavení, musíte upgradovat na placenou úroveň.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Změnit nastavení uchovávání dat pracovního prostoru":::

Když je doba uchování nižší, před odebráním dat starším než je nové nastavení uchování se odklade několik dní. 

Stránka pro **uchovávání dat** umožňuje nastavení uchovávání dat: 30, 31, 60, 90, 120, 180, 270, 365, 550 a 730 dnů. Pokud je vyžadováno jiné nastavení, které lze nakonfigurovat pomocí [Azure Resource Manager](./resource-manager-workspace.md) pomocí `retentionInDays` parametru. Když nastavíte uchovávání dat na 30 dní, můžete spustit okamžitou mazání starších dat pomocí `immediatePurgeDataOn30Days` parametru (neodstraníme dobu odkladu na několik dní). To může být užitečné pro scénáře související s dodržováním předpisů, které jsou nezbytné k okamžitému odstranění dat. Tato funkce okamžitého vyprázdnění se zveřejňuje jenom přes Azure Resource Manager. 

Pracovní prostory s dobou uchování 30 dnů můžou uchovávat data po dobu 31 dnů. Pokud je nutné, aby data byla uchovávána pouze po dobu 30 dnů, použijte Azure Resource Manager k nastavení uchování na 30 dní a s `immediatePurgeDataOn30Days` parametrem.  

Ve výchozím nastavení se standardně uchovávají dva datové typy-- `Usage` a--a za `AzureActivity` Toto 90 dne se neúčtují žádné poplatky za 90 dní. Pokud se uchování v pracovním prostoru zvyšuje nad 90 dnů, bude se také zvyšovat doba uchování těchto datových typů.  Tyto datové typy jsou také zdarma z poplatků za příjem dat. 

Datové typy z prostředků Application Insights založených na pracovních prostorech ( `AppAvailabilityResults` , `AppBrowserTimings` ,, `AppDependencies` `AppExceptions` , `AppEvents` , `AppMetrics` , `AppPageViews` , `AppPerformanceCounters` , `AppRequests` `AppSystemEvents` a `AppTraces` ) se ve výchozím nastavení uchovávají i po dobu 90 dnů a za toto 90 dne se neúčtují žádné poplatky. Jejich uchování je možné upravit pomocí funkce uchování podle datového typu. 

Upozorňujeme, že rozhraní Log Analytics [Purge API](/rest/api/loganalytics/workspacepurge/purge) nemá vliv na fakturaci uchovávání a je určené k použití ve velmi omezených případech. Aby se snížila doba uchovávání informací, je nutné dobu uchování snížit buď pro pracovní prostor, nebo pro konkrétní datové typy. 

### <a name="retention-by-data-type"></a>Uchovávání dat podle datového typu

Je také možné zadat různá nastavení uchovávání pro jednotlivé datové typy od 4 do 730 dnů (s výjimkou pracovních prostorů ve starší verzi bezplatné cenové úrovně), která přepíší výchozí dobu uchování na úrovni pracovního prostoru. Každý datový typ je dílčím prostředkem pracovního prostoru. Například tabulku SecurityEvent lze vyřešit v [Azure Resource Manager](../../azure-resource-manager/management/overview.md) jako:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Všimněte si, že datový typ (tabulka) rozlišuje velká a malá písmena.  Chcete-li získat aktuální nastavení uchovávání dat pro konkrétní datový typ (v tomto příkladu SecurityEvent), použijte:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> Uchování je vráceno pouze pro datový typ, pokud bylo uchovávání pro něj explicitně nastaveno.  Datové typy, které nemají explicitně nastavené uchovávání (a tedy dědí uchování v pracovním prostoru), nebudou vracet žádné z těchto volání. 

Pokud chcete získat aktuální nastavení uchovávání dat pro všechny datové typy v pracovním prostoru, které mají svou sadu pro uchovávání dat typu pro jednotlivé datové typy, stačí vynechat konkrétní datový typ, například:

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

Můžete nakonfigurovat denní limit a omezit každodenní ingestování pro váš pracovní prostor, ale pečlivě používejte, protože by váš cíl neměl mít denní limit.  Jinak ztratíte data zbývajícího dne, což může mít vliv na další služby a řešení Azure, jejichž funkce můžou záviset na dostupnosti dat v pracovním prostoru.  V důsledku toho ztratíte možnost sledovat a přijímat upozornění při ovlivnění stavu prostředků podporujících IT služby.  Denní limit je určený k použití jako způsob, jak spravovat **neočekávané zvýšení** objemu dat ze spravovaných prostředků a zůstat v rámci vašeho limitu, nebo pokud chcete omezit neplánované poplatky pro váš pracovní prostor. Není vhodné pro pracovní prostor nastavit denní limit tak, aby se ho dosáhlo každý den.

Každý pracovní prostor má denní limit, který se aplikuje na jinou hodinu dne. Nulová hodina se zobrazí na stránce **denní limit** (viz níže). Tuto hodinu resetování nelze nakonfigurovat. 

Brzy po dosažení denního limitu se kolekce fakturovatelných datových typů zastaví pro zbytek dne. Latence vyplývající z použití denního limitu znamená, že se limit nepoužívá přesně na určenou denní úroveň Cap. V horní části stránky se zobrazí banner s upozorněním pro vybraný Log Analytics pracovní prostor a událost operace se odešle do tabulky *Operation* v kategorii **LogManagement** . Shromažďování dat se obnoví po uplynutí doby obnovení definované v rámci *denního limitu*. Doporučujeme definovat pravidlo výstrahy na základě této události operace, která se nakonfiguruje na upozorňování po dosažení denního limitu dat (viz [níže](#alert-when-daily-cap-reached)). 

> [!NOTE]
> Denní limit nemůže zastavit shromažďování dat přesně na zadané úrovni zakončení a je očekáváno několik přebytečných dat, zejména v případě, že pracovní prostor přijímá velké objemy dat. [Níže](#view-the-effect-of-the-daily-cap) najdete dotaz, který je užitečný při studiu chování denního limitu. 

> [!WARNING]
> Denní limit nebrání shromažďování datových typů WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus, Update a UpdateSummary, s výjimkou pracovních prostorů, ve kterých Azure Security Center byl nainstalován před 19. června 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Určete, který denní limit dat se má definovat.

Přečtěte si [Log Analytics využití a odhadované náklady](../usage-estimated-costs.md) , abyste porozuměli trendům příjmu dat a jaký je denní zakončení pro definování. Měli byste se považovat za pečlivě, protože jste ji vyhráli? po dosažení limitu by bylo možné monitorovat vaše prostředky. 

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

Následující postup popisuje, jak nakonfigurovat limit pro správu objemu dat, který Log Analytics pracovní prostor ingestovat za den.  

1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. Na stránce **využití a odhadované náklady** pro vybraný pracovní prostor klikněte v horní části stránky na možnost **datový limit** . 
3. Denní limit je ve výchozím nastavení **vypnutý** ? klikněte na **zapnout** a pak nastavte limit počtu dat v GB za den.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics konfiguraci omezení dat":::
    
Denní limit se dá nakonfigurovat přes ARM nastavením `dailyQuotaGb` parametru v části podle `WorkspaceCapping` popsaných v [pracovních prostorech – vytvořit nebo aktualizovat](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

### <a name="view-the-effect-of-the-daily-cap"></a>Zobrazení účinku denního limitu

Chcete-li zobrazit efekt denního limitu, je důležité se zařadit do datových typů zabezpečení, které nejsou zahrnuté do denního limitu, a nastavit hodinu pro váš pracovní prostor. Hodina resetování denního limitu se zobrazí na stránce **denní limit** .  Následující dotaz lze použít ke sledování datových svazků, které jsou předmětem denního limitu mezi denním obnovením limitu. V tomto příkladu je hodina resetu pracovního prostoru 14:00.  Budete ho muset pro svůj pracovní prostor aktualizovat.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

(V datových typech použití `Quantity` jsou jednotky v MB.)

### <a name="alert-when-daily-cap-reached"></a>Výstraha při denním limitu

Když při splnění prahové hodnoty limitu dat prezentujeme Azure Portal vizuální sestavování, toto chování se nemusí nutně sjednotit na to, jak budete spravovat provozní problémy, které vyžadují okamžitou pozornost.  Chcete-li dostávat oznámení o výstrahách, můžete v Azure Monitor vytvořit nové pravidlo výstrahy.  Další informace najdete v tématu [jak vytvářet, zobrazovat a spravovat výstrahy](../alerts/alerts-metric.md).

Pokud chcete začít, tady je doporučené nastavení výstrahy dotazování `Operation` tabulky pomocí `_LogOperation` funkce. 

- Cíl: Vyberte prostředek Log Analytics
- Měřítk 
   - Název signálu: prohledávání vlastního protokolu
   - Vyhledávací dotaz: `_LogOperation | where Operation == "Data Collection Status" | where Detail contains "OverQuota"`
   - Podle: počet výsledků
   - Podmínka: je větší než
   - Prahová hodnota: 0
   - Období: 5 (minuty)
   - Frekvence: 5 (minuty)
- Název pravidla výstrahy: dosáhlo se denního limitu dat.
- Závažnost: upozornění (závažnost 1)

Po definování výstrahy a dosažení limitu se aktivuje výstraha a bude provedena odpověď definovaná ve skupině akcí. Může váš tým informovat prostřednictvím e-mailu a textových zpráv nebo automatizovat akce pomocí webhooků, runbooků pro automatizaci nebo [integrací s externím řešením ITSM](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts). 

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
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Chcete-li získat seznam uzlů, které odesílají data (a objem dat odesílaných každým), je možné použít následující dotaz:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Uzly účtované starší verzí na cenové úrovni uzlů

[Starší verze cenové úrovně na úrovni jednotlivých uzlů](#legacy-pricing-tiers) pro uzly s hodinovou členitost a také nepočítají uzly pouze odesílající sadu datových typů zabezpečení. Denní počet uzlů by byl blízko následujícího dotazu:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Počet jednotek ve vašem vyúčtování je v jednotkách typu počet měsíců, které jsou v dotazu reprezentovány `billableNodeMonthsPerDay` . Pokud má pracovní prostor nainstalované řešení Update Management, přidejte do seznamu klauzule WHERE ve výše uvedeném dotazu datové typy Update a UpdateSummary. V případě, že se používá cílení řešení, které není ve výše uvedeném dotazu zastoupeno, je nakonec něco dalšího složitosti v samotném algoritmu pro účtování. 


> [!TIP]
> Tyto dotazy můžete použít `find` zřídka, protože kontroly napříč datovými typy jsou [náročné na prostředky](./query-optimization.md#query-performance-pane) , které je potřeba provést. Pokud nepotřebujete výsledky **na počítač** , zadejte dotaz na datový typ použití (viz níže).

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

Všimněte si, že klauzule `where _IsBillable = true` filtruje datové typy z určitých řešení, pro které se neúčtují žádné poplatky za ingestování. [Přečtěte si další informace](./log-standard-columns.md#_isbillable) o `_IsBillable` .

### <a name="data-volume-by-solution"></a>Objem dat podle řešení

Dotaz použitý k zobrazení objemu fakturovatelných dat podle řešení za poslední měsíc (s výjimkou posledního částečného dne) je:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

Klauzule WITH `TimeGenerated` je určena pouze k zajištění toho, aby se možnosti dotazování v Azure Portal vypadaly až za výchozí 24 hodin. Při použití datového typu použití `StartTime` a `EndTime` představují časové intervaly, pro které jsou zobrazeny výsledky. 

### <a name="data-volume-by-type"></a>Objem dat podle typu

Další podrobnosti můžete prozkoumat a zobrazit tak trendy dat pro datový typ:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Nebo pokud chcete zobrazit tabulku podle řešení a typu za poslední měsíc,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Objem dat podle počítače

`Usage`Datový typ neobsahuje informace na úrovni počítače. Chcete-li zobrazit **Velikost** zpracovaných dat na jeden počítač, použijte `_BilledSize` [vlastnost](./log-standard-columns.md#_billedsize), která poskytuje velikost v bajtech:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [Vlastnost](./log-standard-columns.md#_isbillable) určuje, jestli se za ingestovaná data účtují poplatky. 

Chcete-li zobrazit **počet** fakturovaných událostí zpracovaných na počítač, použijte 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> Tyto dotazy můžete použít `find` zřídka, protože kontroly napříč datovými typy jsou [náročné na prostředky](./query-optimization.md#query-performance-pane) , které je potřeba provést. Pokud nepotřebujete výsledky **na počítač** , pak na něj zadejte dotaz na datový typ použití.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Objem dat podle prostředku Azure, skupiny prostředků nebo předplatného

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** zpracovaných dat __na jeden počítač__, použít [vlastnost](./log-standard-columns.md#_resourceid)_ResourceId, která poskytuje úplnou cestu k prostředku:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Pro data z uzlů hostovaných v Azure můžete získat **Velikost** zpracovaných dat __na předplatné Azure__, získat tuto `_SubscriptionId` vlastnost jako:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| summarize BillableDataBytes = sum(BillableDataBytes) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Pokud chcete získat objem dat podle skupiny prostředků, můžete analyzovat `_ResourceId` :

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

V případě potřeby můžete také `_ResourceId` plně analyzovat v případě potřeby i.

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Tyto dotazy můžete použít `find` zřídka, protože kontroly napříč datovými typy jsou [náročné na prostředky](./query-optimization.md#query-performance-pane) , které je potřeba provést. Pokud nepotřebujete výsledky v rámci předplatného, skupiny prostředků nebo názvu prostředku, pak dotaz na datový typ použití.

> [!WARNING]
> Některá pole datového typu použití, ale stále ve schématu, jsou zastaralá a jejich hodnoty se už neplní. Jedná se o **počítač** a pole související s ingestování (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** a **AverageProcessingTimeMs**).


### <a name="querying-for-common-data-types"></a>Dotazování na Common data types

Pokud chcete dig hlouběji ke zdroji dat pro určitý datový typ, tady jsou některé užitečné příklady dotazů:

+ Prostředky **Application Insights založené na pracovním prostoru**
  - Další informace [o správě využití a nákladů pro Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
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

## <a name="tips-for-reducing-data-volume"></a>Tipy pro snížení objemu dat

Mezi návrhy na snížení objemu shromažďovaných protokolů patří:

| Zdroj velkého objemu dat | Postup snížení objemu dat |
| -------------------------- | ------------------------- |
| Přehledy o kontejnerech         | [Nakonfigurujte službu Container Insights](../containers/container-insights-cost.md#controlling-ingestion-to-reduce-cost) tak, aby shromáždila pouze data, která požadujete. |
| Události zabezpečení            | Vyberte [běžné nebo minimální události zabezpečení](../../security-center/security-center-enable-data-collection.md#data-collection-tier). <br> Změňte zásady auditu zabezpečení tak, aby se shromažďovaly jenom potřebné události. Zaměřte se hlavně na potřebu shromažďovat události pro <br> - [audit platformy Filtering Platform](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [audit registru](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [audit systému souborů](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [audit objektu jádra](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [audit manipulace s popisovačem](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> – audit vyměnitelného úložiště |
| Čítače výkonu       | Změňte [konfiguraci čítačů výkonu](../agents/data-sources-performance-counters.md) tak, aby se: <br> – Snížila četnost shromažďování dat <br> – Snížil počet čítačů výkonu |
| Protokoly událostí                 | Změňte [konfiguraci protokolů událostí](../agents/data-sources-windows-events.md) tak, aby se: <br> – Snížil počet shromažďovaných protokolů událostí <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace*. |
| Syslog                     | Změňte [konfiguraci syslogu](../agents/data-sources-syslog.md) tak, aby se: <br> – Snížil počet zařízení, ze kterých se shromažďují data <br> – Shromažďovaly pouze požadované úrovně událostí Například zrušte shromažďování událostí úrovně *Informace* a *Ladění*. |
| AzureDiagnostics           | Změnit [kolekci protokolů prostředků](../essentials/diagnostic-settings.md#create-in-azure-portal) na: <br> – Snížil počet prostředků, které odesílají protokoly do Log Analytics <br> – Shromažďovaly pouze požadované protokoly |
| Data řešení z počítačů, které řešení nepotřebují | K shromažďování dat z požadovaných skupin počítačů použijte [cílení na řešení](../insights/solution-targeting.md) . |
| Application Insights | Zkontrolovat možnosti pro [https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume](managing Application Insights data volume) |
| [Analýza SQL](../insights/azure-sql.md) | K ladění nastavení auditování použijte [set-AzSqlServerAudit](/powershell/module/az.sql/set-azsqlserveraudit) . |
| Azure Sentinel | Zkontrolujte všechny [zdroje dat Sentinel](../../sentinel/connect-data-sources.md) , které jste nedávno povolili jako zdroje dalšího objemu dat. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Získávání uzlů, které se účtují v cenové úrovni podle počtu uzlů

Pokud chcete získat seznam počítačů, které se budou fakturovat jako uzly, pokud je pracovní prostor ve starší verzi na cenové úrovni uzlů, hledejte uzly, které odesílají **účtované datové typy** (některé datové typy jsou zdarma). K tomu použijte `_IsBillable` [vlastnost](./log-standard-columns.md#_isbillable) a použijte pole s plně kvalifikovaným názvem domény v levém krajním poli. Vrátí počet počítačů s fakturovanými daty za hodinu (což je členitost, při které se uzly počítají a účtují):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Načítají se počty uzlů zabezpečení a automatizace.

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
// Pricing details available at https://azure.microsoft.com/en-us/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
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
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Tento dotaz nepředstavuje přesnou replikaci toho, jak se vypočítává využití, ale ve většině případů bude fungovat s doporučeními cenové úrovně.  

> [!NOTE]
> Pokud chcete použít nároky, které pocházejí z nákupu sady OMS E1 Suite, OMS E2 Suite nebo OMS Add-On pro System Center, vyberte cenovou úroveň Log Analytics *na jednotlivých uzlech* .

## <a name="create-an-alert-when-data-collection-is-high"></a>Vytvořit výstrahu, když je shromažďování dat vysoké

V této části se dozvíte, jak vytvořit výstrahu, kterou datový svazek za posledních 24 hodin překročil zadanou velikost pomocí Azure Monitor [výstrahy protokolu](../alerts/alerts-unified-log.md). 

Chcete-li upozornit, zda byl objem fakturovatelných dat zobrazený za posledních 24 hodin větší než 50 GB, postupujte podle následujících kroků: 

- **Definujte podmínku upozornění** – Jako cíl prostředku zadejte svůj pracovní prostor služby Log Analytics.
- **Kritéria upozornění** – Zadejte následující:
   - **Název signálu** – Vyberte **Vlastní prohledávání protokolu**.
   - **Vyhledávací dotaz** na `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Pokud chcete jiné 
   - **Logika upozornění** je **Založená na** *počtu výsledků* a **Podmínka** je *Větší než***Prahová hodnota***0*.
   - **Časové období** *1440* minut a **frekvence upozornění** každé *1440* minut, které se spustí jednou denně.
- **Definujte podrobnosti upozornění** – Zadejte následující:
   - **Název** pro *fakturovatelný objem dat větší než 50 GB za 24 hodin*
   - **Závažnost** na *Upozornění*.

Zadejte existující nebo vytvořte novou [Skupinu akcí](../alerts/action-groups.md), abyste dostali upozornění, když upozornění protokolu splní kritéria.

Když obdržíte výstrahu, postupujte podle kroků uvedených v předchozích částech o postupu při řešení potíží s tím, proč je použití vyšší, než se očekávalo.

## <a name="data-transfer-charges-using-log-analytics"></a>Poplatky za přenos dat pomocí Log Analytics

Odesílání dat do Log Analytics může mít za následek poplatky za šířku pásma, ale to je omezené na Virtual Machines, kde je agent Log Analytics nainstalovaný a který se nepoužije při použití nastavení diagnostiky nebo s jinými konektory, které jsou integrované do Azure Sentinel. Jak je popsáno na [stránce ceny za Azure šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure v rámci dvou oblastí se v normální sazbě účtuje jako odchozí přenos dat. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (několik%) v porovnání s náklady na Log Analytics přijímání dat. V důsledku toho se řídí náklady na Log Analytics se musí soustředit na přijatý [objem dat](#understanding-ingested-data-volume). 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Řešení potíží s tím, proč Log Analytics už neshromažďuje data

Pokud používáte starší verzi bezplatné cenové úrovně a v den jste poslali více než 500 MB dat, zastaví se shromažďování dat pro zbytek dne. Dosažení denního limitu je běžný důvod, proč Log Analytics zastaví shromažďování dat, nebo se zdá, že data chybí.  Log Analytics vytvoří událost typu operace při spuštění a zastavení shromažďování dat. Spuštěním následujícího dotazu v hledání ověřte, jestli se vám dosáhlo denního limitu a chybějících dat: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Když se shromažďování dat zastaví, stav OperationStatus je **Upozornění**. Když se shromažďování dat spustí, stav OperationStatus je **Úspěch**. Následující tabulka popisuje důvody, proč se shromažďování dat zastaví, a navrhovanou akci pro pokračování shromažďování dat:  

|Kolekce důvodů – zastavení| Řešení| 
|-----------------------|---------|
|Dosáhlo se denního limitu pracovního prostoru.|Počkejte na automatické restartování kolekce nebo zvyšte počet denních objemů dat popsaných v tématu Správa maximálního denního objemu dat. Doba obnovení denního limitu se zobrazí na stránce **denní limit** . |
| V pracovním prostoru se dosáhlo [míry objemu příjmu dat](../service-limits.md#log-analytics-workspaces) . | Výchozí limit objemu a rychlosti příjmu dat odesílaných z prostředků Azure využívajících nastavení diagnostiky je přibližně 6 GB za sekundu na pracovní prostor. Tato hodnota je přibližná, protože skutečná velikost se může u jednotlivých datových typů lišit v závislosti na délce protokolu a jeho kompresním poměru. Tento limit se nevztahuje na data odesílaná z agentů nebo rozhraní API kolektoru dat. Pokud do jednoho pracovního prostoru odesíláte data vyšší rychlostí, některá data se zahodí a po dobu, kdy bude pokračovat překročení prahové hodnoty, se každých 6 hodin bude odesílat událost do tabulky Operation ve vašem pracovním prostoru. Pokud váš objem příjmu dat dál překračuje limit přenosové rychlosti nebo pokud očekáváte, že ho brzy dosáhnete, můžete požádat o zvětšení pracovního prostoru, a to odesláním e-mailu na adresu LAIngestionRate@microsoft.com nebo vytvořením žádosti o podporu. Událost, která indikuje dosažení limitu rychlosti příjmu dat, najdete dotazem `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Dosáhlo se denního limitu starší verze bezplatné cenové úrovně. |Počkejte prosím, než se automaticky restartuje kolekce, nebo se změní na placenou cenovou úroveň.|
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Zkušební verze skončila.<br> Platnost Azure Pass vypršela.<br> Dosáhlo se limitu měsíčního útraty (například na předplatném MSDN nebo Visual Studio).|Přechod na placené předplatné<br> Odebrat limit nebo počkat na obnovení limitu|

Chcete-li být upozorněni na zastavení shromažďování dat, postupujte podle kroků popsaných v části *Vytvoření výstrahy denního datového zakončení* , která bude oznámena při zastavení shromažďování dat. Pomocí kroků popsaných v tématu [Vytvoření skupiny akcí](../alerts/action-groups.md) nakonfigurujte akci e-mailu, Webhooku nebo Runbooku pro pravidlo výstrahy. 

## <a name="limits-summary"></a>Souhrn omezení

Existují další limity Log Analytics, některé z nich závisí na cenové úrovni Log Analytics. Ty jsou zdokumentovány v [limitech, kvótách a omezeních předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Další kroky

- Další informace o použití vyhledávacího jazyka najdete [v tématu prohledávání protokolů v protokolu Azure monitor](../logs/log-query-overview.md) . Pomocí vyhledávacích dotazů můžete na datech o využití provádět další analýzy.
- Pokud chcete být upozorňováni při splnění kritérií vyhledávání, postupujte podle kroků popsaných v tématu týkajícím se [vytvoření nového upozornění protokolu](../alerts/alerts-metric.md).
- K shromažďování dat z požadovaných skupin počítačů použijte [cílení na řešení](../insights/solution-targeting.md) .
- Pokud chcete nakonfigurovat efektivní zásadu shromažďování událostí, Projděte si téma [Zásady filtrování Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Změňte [konfiguraci čítačů výkonu](../agents/data-sources-performance-counters.md).
- Pokud chcete upravit nastavení shromažďování událostí, zkontrolujte [konfiguraci protokolu událostí](../agents/data-sources-windows-events.md).
- Pokud chcete upravit nastavení kolekce syslog, zkontrolujte [konfiguraci syslogu](../agents/data-sources-syslog.md).
- Pokud chcete upravit nastavení kolekce syslog, zkontrolujte [konfiguraci syslogu](../agents/data-sources-syslog.md).
