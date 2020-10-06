---
title: Nejčastější dotazy k Azure Monitor | Microsoft Docs
description: Odpovědi na nejčastější dotazy týkající se Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/15/2020
ms.openlocfilehash: b524b0d8f24f011065772495bc2bb283a3c90d4a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760249"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor nejčastějších dotazech

Toto je seznam nejčastějších dotazů k Azure Monitor. Pokud máte nějaké další dotazy, navštivte [diskuzní fórum](https://docs.microsoft.com/answers/questions/topics/single/24223.html) a Vystavte své dotazy. V případě častého dotazu přidáme Tento článek do tohoto článku, aby ho bylo možné rychle a snadno najít.


## <a name="general"></a>Obecné

### <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
[Azure monitor](overview.md) je služba v Azure, která poskytuje monitorování výkonu a dostupnosti pro aplikace a služby v Azure, v jiných cloudových prostředích nebo v místním prostředí. Azure Monitor shromažďuje data z více zdrojů do běžné datové platformy, kde se dá analyzovat pro trendy a anomálie. Bohatých funkcí v Azure Monitor pomáhají rychle identifikovat a reagovat na kritické situace, které by mohly ovlivnit vaši aplikaci.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Jaký je rozdíl mezi Azure Monitor, Log Analytics a Application Insights?
V září 2018 společnost Microsoft kombinovaná Azure Monitor, Log Analytics a Application Insights do jediné služby, která poskytuje výkonné komplexní monitorování vašich aplikací a komponent, na kterých se spoléhají. Funkce v Log Analytics a Application Insights se nezměnily, ale některé funkce se přestavily na Azure Monitor, aby lépe odrážely jejich nový rozsah. Datový stroj protokolu a dotazovací jazyk Log Analytics se teď označují jako Azure Monitor protokoly. Viz [Azure monitor aktualizace terminologie](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Co Azure Monitor náklady?
Funkce Azure Monitor, které jsou automaticky povoleny, například shromažďování metrik a protokolů aktivit, jsou poskytovány bez nákladů. Existují náklady spojené s dalšími funkcemi, jako jsou dotazy protokolu a výstrahy. Podrobné informace o cenách najdete na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Návody povolit Azure Monitor?
Azure Monitor je povolený okamžik, kdy vytvoříte nové předplatné Azure, a automaticky se shromažďují [metriky](platform/data-platform-metrics.md) [protokolů aktivit](./platform/platform-logs-overview.md) a platforem. Vytvořte [nastavení diagnostiky](platform/diagnostic-settings.md) pro shromažďování podrobnějších informací o provozu prostředků Azure a přidejte [řešení monitorování](insights/solutions.md) a [přehledy](insights/insights-overview.md) , které poskytují další analýzy shromážděných dat pro konkrétní služby. 

### <a name="how-do-i-access-azure-monitor"></a>Azure Monitor Návody Access?
Přístup ke všem funkcím Azure Monitor a datům z nabídky **monitor** v Azure Portal. Část **monitorování** v nabídce různých služeb Azure poskytuje přístup ke stejným nástrojům, které obsahují data filtrovaná pro konkrétní prostředek. Data Azure Monitor jsou také přístupná pro celou řadu scénářů pomocí rozhraní příkazového řádku, PowerShellu a REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Existuje místní verze Azure Monitor?
Ne. Azure Monitor je škálovatelná cloudová služba, která zpracovává a ukládá velké objemy dat, i když Azure Monitor může monitorovat místní a jiné cloudové prostředky.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Může Azure Monitor monitorovat místní prostředky?
Ano, kromě shromažďování dat monitorování z prostředků Azure Azure Monitor můžou shromažďovat data z virtuálních počítačů a aplikací v jiných cloudech i v místním prostředí. Další informace najdete v tématu [zdroje dat monitorování pro Azure monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Je Azure Monitor integrace s System Center Operations Manager?
Stávající skupinu pro správu System Center Operations Manager můžete připojit, abyste Azure Monitor mohli shromažďovat data z agentů do protokolů Azure Monitor. To vám umožní analyzovat data shromážděná z agentů pomocí dotazů protokolu a řešení. Stávající agenty System Center Operations Manager můžete také nakonfigurovat tak, aby odesílaly data přímo do Azure Monitor. Azure Monitor najdete v tématu [připojení Operations Manager](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Jaké IP adresy Azure Monitor použít?
Seznam IP adres a portů vyžadovaných agentům a jiným externím prostředkům pro přístup k Azure Monitor najdete v části [IP adresy, které používá Application Insights a Log Analytics](app/ip-addresses.md) . 

## <a name="monitoring-data"></a>Data monitorování

### <a name="where-does-azure-monitor-get-its-data"></a>Kde Azure Monitor získá data?
Azure Monitor shromažďuje data z nejrůznějších zdrojů, včetně protokolů a metrik z platformy a prostředků Azure, vlastních aplikací a agentů běžících na virtuálních počítačích. Další služby, například Azure Security Center a Network Watcher shromažďují data do Log Analyticsho pracovního prostoru, aby je bylo možné analyzovat pomocí Azure Monitor dat. Můžete také odesílat vlastní data Azure Monitor pomocí REST API pro protokoly nebo metriky. Další informace najdete v tématu [zdroje dat monitorování pro Azure monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Jaká data shromažďuje Azure Monitor? 
Azure Monitor shromažďuje data z nejrůznějších zdrojů do [protokolů](platform/data-platform-logs.md) nebo [metrik](platform/data-platform-metrics.md). Každý typ dat má své vlastní relativní výhody a každá z nich podporuje konkrétní sadu funkcí v Azure Monitor. Pro každé předplatné Azure existuje jedna databáze metrik, zatímco můžete vytvořit několik pracovních prostorů Log Analytics pro shromažďování protokolů v závislosti na vašich požadavcích. Viz [Azure monitor datovou platformu](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Je možné shromažďovat v Azure Monitor maximální množství dat?
Množství dat metrik, která můžete shromažďovat, není nijak omezené, ale tato data se ukládají maximálně po 93 dnech. Podívejte se [na uchovávání metrik](platform/data-platform-metrics.md#retention-of-metrics). Množství dat protokolu, která můžete shromažďovat, není nijak omezené, ale může to mít vliv na cenovou úroveň, kterou zvolíte pro pracovní prostor Log Analytics. Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Návody přístup k datům shromažďovaným pomocí Azure Monitor?
Přehledy a řešení poskytují vlastní prostředí pro práci s daty uloženými v Azure Monitor. Můžete pracovat přímo s daty protokolu pomocí dotazu protokolu napsaného v Kusto Query Language (KQL). V Azure Portal můžete zapisovat a spouštět dotazy a interaktivně analyzovat data pomocí Log Analytics. Analyzujte metriky v Azure Portal pomocí Průzkumník metrik. Přečtěte si téma [Analýza dat protokolu v Azure monitor](log-query/log-query-overview.md) a [začínáme s Azure Průzkumník metrik](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Řešení a přehledy

### <a name="what-is-an-insight-in-azure-monitor"></a>Co je přehled v Azure Monitor?
Přehledy poskytují přizpůsobené možnosti monitorování pro konkrétní služby Azure. Používají stejné metriky a protokoly jako jiné funkce v Azure Monitor, ale mohou shromažďovat další data a v Azure Portal poskytovat jedinečné prostředí. Podívejte [se na přehledy v Azure monitor](insights/insights-overview.md).

Pokud chcete zobrazit přehledy v Azure Portal, přečtěte si část **Přehled** v nabídce **monitor** nebo v části **monitorování** v nabídce služby.

### <a name="what-is-a-solution-in-azure-monitor"></a>Co je řešení v Azure Monitor?
Řešení monitorování jsou zabalené sady logiky pro monitorování konkrétní aplikace nebo služby na základě funkcí Azure Monitor. Shromažďují data protokolu v Azure Monitor a poskytují dotazy protokolů a zobrazení pro jejich analýzu pomocí společného prostředí v Azure Portal. Viz [řešení monitorování v Azure monitor](insights/solutions.md).

Pokud chcete zobrazit řešení v Azure Portal, klikněte na tlačítko **Další** v části **Přehled** v nabídce **monitorování** . Kliknutím na **Přidat** přidejte do pracovního prostoru další řešení.

## <a name="logs"></a>Protokoly

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Jaký je rozdíl mezi protokoly Azure Monitor a Azure Průzkumník dat?
Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Protokoly Azure Monitor jsou postavené na Azure Průzkumník dat a používají stejný dotazovací jazyk Kusto (KQL) s některými drobnými rozdíly. Viz [rozdíly v jazyce pro dotazování protokolu Azure monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Návody načíst data protokolu?
Všechna data se načítají z Log Analyticsho pracovního prostoru pomocí dotazu protokolu napsaného pomocí dotazovacího jazyka KQL (Kusto Query Language). Můžete napsat vlastní dotazy nebo využít řešení a přehledy, které obsahují dotazy protokolu pro konkrétní aplikaci nebo službu. Další informace najdete [v tématu Přehled dotazů protokolu v Azure monitor](log-query/log-query-overview.md).

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Můžu odstranit data z Log Analyticsho pracovního prostoru?
Data se z pracovního prostoru odeberou podle [doby jejich uchování](platform/manage-cost-storage.md#change-the-data-retention-period). Můžete odstranit konkrétní data z důvodů ochrany osobních údajů nebo dodržování předpisů. Další informace najdete v tématu [Jak exportovat a odstranit soukromá data](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) .


### <a name="what-is-a-log-analytics-workspace"></a>Co je pracovní prostor služby Log Analytics?
Všechna data protokolu shromážděná pomocí Azure Monitor jsou uložena v pracovním prostoru Log Analytics. Pracovní prostor je v podstatě kontejner, ve kterém jsou data protokolu shromažďována z nejrůznějších zdrojů. Můžete mít jeden Log Analytics pracovní prostor pro všechna data monitorování nebo mohou mít požadavky na více pracovních prostorů. Přečtěte si téma [navrhování Azure Monitorch protokolů nasazení](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Můžete přesunout existující Log Analytics pracovní prostor do jiného předplatného Azure?
Pracovní prostor můžete přesouvat mezi skupinami prostředků nebo předplatnými, ale ne do jiné oblasti. Přečtěte si téma [přesunutí log Analyticsho pracovního prostoru do jiného předplatného nebo skupiny prostředků](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Proč se mi nedá zobrazit Průzkumník dotazů a tlačítka Uložit v Log Analytics?

Tlačítka **Průzkumník dotazů**, **Uložit** a **Nová pravidla výstrahy** nejsou k dispozici, pokud je [obor dotazu](log-query/scope.md) nastaven na konkrétní prostředek. Pokud chcete vytvářet výstrahy, ukládat nebo načítat dotaz, Log Analytics musí být vymezené na pracovní prostor. Pokud chcete otevřít Log Analytics v kontextu pracovního prostoru, v nabídce **Azure monitor** vyberte **protokoly** . Vybere se poslední použitý pracovní prostor, ale můžete vybrat libovolný jiný pracovní prostor. Viz [Rozsah dotazů protokolu a rozsah času ve Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Proč se mi zobrazuje chyba: "zaregistrovat poskytovatele prostředků" Microsoft. Insights "pro toto předplatné povolíte tento dotaz" při otevírání Log Analytics z virtuálního počítače? 
Mnoho poskytovatelů prostředků se registruje automaticky, ale možná budete muset některé poskytovatele prostředků zaregistrovat ručně. Obor pro registraci je vždy předplatné. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Proč se při otevírání Log Analytics z virtuálního počítače zobrazuje chybová zpráva o přístupu? 
Chcete-li zobrazit protokoly virtuálních počítačů, je třeba udělit oprávnění ke čtení pro pracovní prostory, ve kterých jsou uloženy protokoly virtuálních počítačů. V těchto případech vám správce musí udělit oprávnění v Azure.

## <a name="metrics"></a>Metriky

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Proč se v Průzkumníkovi metrik nezobrazuje metriky z hostovaného operačního systému virtuálního počítače Azure?
[Metriky platformy](insights/monitor-azure-resource.md#monitoring-data) se shromažďují automaticky pro prostředky Azure. Abyste mohli shromažďovat metriky z hostovaného operačního systému virtuálního počítače, musíte udělat nějakou konfiguraci. Pro virtuální počítač s Windows nainstalujte diagnostické rozšíření a nakonfigurujte Azure Monitor jímku, jak je popsáno v tématu [instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](platform/diagnostics-extension-windows-install.md). Pro Linux nainstalujte agenta telegraf, jak je popsáno v tématu [shromáždění vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta telegraf InfluxData](platform/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Výstrahy

### <a name="what-is-an-alert-in-azure-monitor"></a>Co je výstraha v Azure Monitor?
Výstrahy proaktivně upozorňují na skutečnost, že jsou ve vašich datech monitorování zjištěny důležité podmínky. Umožňují identifikovat a řešit problémy předtím, než si ji uživatelé vašeho systému všimnete. Existuje několik typů výstrah:

- Metrika – hodnota metriky překračuje prahovou hodnotu.
- Dotaz protokolu – výsledky dotazu protokolu odpovídají definovaným kritériím.
- Protokol aktivit – událost protokolu aktivit odpovídá definovaným kritériím.
- Webový test – výsledky definovaných kritérií shody testů dostupnosti.


Přečtěte si téma [Přehled výstrah v Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Co je skupina akcí?
Skupina akcí je kolekce oznámení a akcí, které mohou být aktivovány výstrahou. Několik výstrah může používat jednu skupinu akcí, která vám umožní využít běžné sady oznámení a akcí. Viz [Vytvoření a Správa skupin akcí v Azure Portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Co je pravidlo akce?
Pravidlo akce umožňuje upravit chování sady výstrah, které odpovídají určitým kritériím. To vám umožní provádět tyto požadavky jako zakázat akce výstrah během časového období údržby. Můžete také použít skupinu akcí pro sadu výstrah místo jejich použití přímo na pravidla výstrahy. Viz [pravidla akcí](platform/alerts-action-rules.md).

## <a name="agents"></a>Agenti

### <a name="does-azure-monitor-require-an-agent"></a>Vyžaduje Azure Monitor agenta?
Agent se vyžaduje jenom ke shromažďování dat z operačního systému a úloh na virtuálních počítačích. Virtuální počítače se můžou nacházet v Azure, jiném cloudovém prostředí nebo místně. Přečtěte si téma [Přehled agentů Azure monitor](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Jaký je rozdíl mezi agenty Azure Monitor?
Diagnostické rozšíření Azure je pro virtuální počítače Azure a shromažďuje data pro Azure Monitor metriky, Azure Storage a Azure Event Hubs. Agent Log Analytics je pro virtuální počítače v Azure, jiné cloudové prostředí nebo místní a shromažďuje data do protokolů Azure Monitor. Agent závislostí vyžaduje agenta Log Analytics a shromážděných podrobností procesu a závislostí. Přečtěte si téma [Přehled agentů Azure monitor](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Používá můj agent provozu moje připojení ExpressRoute?
Provoz do Azure Monitor využívá okruh ExpressRoute partnerského vztahu Microsoftu. Popis různých typů provozu ExpressRoute najdete v [dokumentaci k ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) . 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Jak potvrzuji, že agent Log Analytics může komunikovat s Azure Monitor?
V části Ovládací panely na počítači agenta vyberte **nastavení zabezpečení &**, * * Microsoft Monitoring Agent. Na kartě **Azure Log Analytics (OMS)** se ikona zeleného zaškrtnutí potvrdí, že agent může komunikovat s Azure monitor. Žlutá ikona upozornění znamená, že u agenta dochází k problémům. Jednou z běžných příčin je, že se služba **Microsoft Monitoring Agent** zastavila. Pro restartování služby použijte Správce řízení služeb.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Návody zastavit Log Analytics agenta komunikaci s Azure Monitor?
Pro agenty připojené k Log Analytics přímo otevřete ovládací panely a vyberte **nastavení & zabezpečení** **Microsoft Monitoring Agent**. Na kartě **Azure Log Analytics (OMS)** odeberte všechny uvedené pracovní prostory. V System Center Operations Manager odeberte počítač ze seznamu spravovaných počítačů s Log Analytics. Operations Manager aktualizuje konfiguraci agenta tak, aby se už nehlásila Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Kolik dat se odesílá na agenta?
Množství dat odeslaných na agenta závisí na:

* Řešení, která jste povolili
* Počet shromažďovaných protokolů a čítačů výkonu
* Objem dat v protokolech

Podrobnosti najdete v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](platform/manage-cost-storage.md) .

Pro počítače, na kterých je možné spustit agenta WireData, použijte následující dotaz k zobrazení množství dat, která jsou odesílána:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Kolik šířky pásma sítě používá agent MMA (Microsoft Management Agent) při odesílání dat do Azure Monitor?
Šířka pásma je funkce na množství odesílaných dat. Data se komprimují při posílání přes síť.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Jak se mi upozorní při zastavení shromažďování dat z Log Analyticsho agenta?

Použijte postup popsaný v tématu [Vytvoření nového upozornění protokolu](platform/alerts-metric.md) , které se upozorní na zastavení shromažďování dat. Pro pravidlo upozornění použijte následující nastavení:

- **Definovat podmínku výstrahy**: Zadejte svůj pracovní prostor Log Analytics jako cíl prostředku.
- **Kritéria výstrahy** 
   - **Název signálu**: *prohledávání vlastního protokolu*
   - **Vyhledávací dotaz**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logika výstrahy**: **na základě** *počtu výsledků*, **podmínky** *větší než*, **prahová hodnota** *0*
   - **Vyhodnoceno na základě**: **perioda (v minutách)** *30*, **frekvence (v minutách)** *10*
- **Definice podrobností o upozornění** 
   - **Název**: *shromažďování dat bylo zastaveno* .
   - **Závažnost**: *Upozornění*

Zadejte existující nebo novou [skupinu akcí](platform/action-groups.md) , aby když výstraha protokolu odpovídá kritériím, zobrazí se oznámení v případě, že chybí prezenční signál po dobu více než 15 minut.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Jaké jsou požadavky brány firewall pro agenty Azure Monitor?
Podrobnosti o požadavcích na bránu firewall najdete v tématu [požadavky na bránu firewall sítě](platform/log-analytics-agent.md#network-requirements).


## <a name="visualizations"></a>Vizualizace

### <a name="why-cant-i-see-view-designer"></a>Proč nemůžu zobrazit návrháře zobrazení?

Návrhář zobrazení je k dispozici pouze pro uživatele, kteří jsou přiřazeni k oprávněním přispěvatele nebo vyšší v pracovním prostoru Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problémy s konfigurací
*Mám potíže s nastavením mých:*

* [Aplikace v .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorování již spuštěné aplikace](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostika Azure](platform/diagnostics-extension-to-application-insights.md)
* [Webová aplikace Java](app/java-troubleshoot.md)

*Nedaří se mi získat žádná data ze serveru:*

* [Nastavení výjimek brány firewall](app/ip-addresses.md)
* [Nastavení serveru ASP.NET](app/monitor-performance-live-website-now.md)
* [Nastavení serveru Java](app/java-agent.md)

*Kolik prostředků Application Insights mám nasadit:*

* [Jak navrhnout nasazení Application Insights: jeden versus mnoho prostředků Application Insights?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Můžu použít Application Insights s...?

* [Webové aplikace na serveru služby IIS na virtuálním počítači Azure nebo v sadě Azure Virtual Machine scaleing](app/azure-vm-vmss-apps.md)
* [Webové aplikace na serveru služby IIS místně nebo na virtuálním počítači](app/asp-net.md)
* [Webové aplikace v Javě](app/java-get-started.md)
* [Aplikace Node.js](app/nodejs.md)
* [Webové aplikace v Azure](app/azure-web-apps.md)
* [Cloud Services v Azure](app/cloudservices.md)
* [Aplikační servery běžící v Docker](app/docker.md)
* [Jednostránkové webové aplikace](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Desktopová aplikace pro Windows](app/windows-desktop.md)
* [Jiné platformy](app/platforms.md)

### <a name="is-it-free"></a>Je to zdarma?

Ano, pro experimentální použití. V cenovém plánu Basic může vaše aplikace odeslat určitý příspěvek na data každý měsíc zdarma. Bezplatná náhrada je dostatečně velká pro pokrytí vývoje a publikování aplikace pro malý počet uživatelů. Můžete nastavit limit, který zabrání zpracování více než zadaného množství dat.

Větší objemy telemetrie se účtují za GB. Nabízíme několik tipů, jak [omezit vaše poplatky](app/pricing.md).

Plán organizace se zaúčtuje za každý den, kdy každý uzel webového serveru odesílá telemetrii. Je vhodný v případě, že chcete průběžný export použít ve velkém měřítku.

[Přečtěte si Cenový tarif](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Kolik to stojí?

* Otevřete **stránku využití a odhadované náklady** v prostředku Application Insights. K dispozici je graf nedávného využití. Pokud chcete, můžete nastavit limit datového svazku.
* Otevřete okno [fakturace Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) , ve kterém se zobrazí vaše účty napříč všemi prostředky.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Co Application Insights v projektu upravovat?
Podrobnosti závisí na typu projektu. Pro webovou aplikaci:

* Přidá tyto soubory do projektu:
  * ApplicationInsights.config
  * ai.js
* Nainstaluje tyto balíčky NuGet:
  * *Rozhraní API pro Application Insights* – základní rozhraní API
  * *Application Insights API pro webové aplikace* – slouží k posílání telemetrie ze serveru.
  * *Rozhraní API pro Application Insights pro aplikace JavaScriptu* – používá se k posílání telemetrie z klienta
* Balíčky zahrnují tato sestavení:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. Platform
* Vloží položky do:
  * Soubor web.config
  * packages.config
* (Jenom nové projekty – Pokud [přidáte Application Insights do existujícího projektu][start], musíte to provést ručně.) Vloží fragmenty kódu do klienta a kód serveru pro inicializaci s ID prostředku Application Insights. Například v aplikaci MVC je kód vložen do zobrazení stránky předloha/Shared/ \_ layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Návody upgradovat ze starších verzí sady SDK?
Přečtěte si [poznámky k verzi](app/release-notes.md) pro sadu SDK odpovídající vašemu typu aplikace.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Jak můžu změnit, na který prostředek Azure můj projekt odesílá data?
V Průzkumník řešení klikněte pravým tlačítkem myši `ApplicationInsights.config` a vyberte možnost **aktualizovat Application Insights**. Data můžete odeslat do existujícího nebo nového prostředku v Azure. Průvodce aktualizací změní klíč instrumentace v ApplicationInsights.config, který určuje, kde sada SDK serveru odesílá vaše data. Pokud nevyberete možnost Aktualizovat vše, změní se také klíč, ve kterém se zobrazí na webových stránkách.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Můžu `providers('Microsoft.Insights', 'components').apiVersions[0]` v nasazeních Azure Resource Manager použít?

Tuto metodu nedoporučujeme používat pro naplnění verze rozhraní API. Nejnovější verze může představovat verze Preview, které mohou obsahovat zásadní změny. I s novějšími verzemi bez verze Preview nejsou verze rozhraní API vždycky zpětně kompatibilní se stávajícími šablonami, nebo v některých případech nemusí být verze rozhraní API k dispozici pro všechny odběry.

### <a name="what-is-status-monitor"></a>Co je Monitorování stavu?

Desktopová aplikace, kterou můžete použít na webovém serveru služby IIS a které vám pomůžou nakonfigurovat Application Insights ve webových aplikacích. Neshromažďuje telemetrii: když aplikaci nekonfigurujete, můžete ji zastavit. 

[Další informace](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Jaká telemetrie se shromažďuje pomocí Application Insights?

Z webových aplikací serveru:

* Požadavky HTTP
* [Závislosti:](app/asp-net-dependencies.md) Volání: databáze SQL; Volání HTTP do externích služeb; Azure Cosmos DB, tabulka, úložiště objektů BLOB a fronta. 
* [Výjimky](app/asp-net-exceptions.md) a trasování zásobníku.
* [Čítače výkonu](app/performance-counters.md) – pokud používáte [monitorování stavu](app/monitor-performance-live-website-now.md), [monitorování Azure pro App Services](app/azure-web-apps.md), [monitorování Azure pro virtuální počítače nebo sadu škálování virtuálního počítače](app/azure-vm-vmss-apps.md)nebo [Application Insights shromážděného zapisovače](app/java-collectd.md).
* [Vlastní události a metriky](app/api-custom-events-metrics.md) , které kódujete.
* [Protokoly trasování](app/asp-net-trace-logs.md) , pokud konfigurujete příslušný kolektor.

Z [klientských webových stránek](app/javascript.md):

* [Počty zobrazení stránek](app/usage-overview.md)
* [Volání AJAX](app/asp-net-dependencies.md) Požadavky vytvořené ze spuštěného skriptu
* Data načtení zobrazení stránky
* Počty uživatelů a relací
* [ID ověřených uživatelů](app/api-custom-events-metrics.md#authenticated-users)

Z jiných zdrojů, pokud je nakonfigurujete:

* [Diagnostika Azure](platform/diagnostics-extension-to-application-insights.md)
* [Import do analýz](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Můžu odfiltrovat nebo upravit určitou telemetrii?

Ano, na serveru, který můžete zapsat:

* Procesor telemetrie pro filtrování nebo přidání vlastností vybraných položek telemetrie před jejich odesláním z vaší aplikace
* Inicializátor telemetrie pro přidání vlastností do všech položek telemetrie

Další informace najdete v [ASP.NET](app/api-filtering-sampling.md) nebo [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Jak se počítají města, země/oblast a další data geografického umístění?

IP adresu (IPv4 nebo IPv6) webového klienta vyhledáme pomocí [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetrie prohlížeče: shromažďujeme IP adresu odesílatele.
* Telemetrie serveru: modul Application Insights shromažďuje IP adresu klienta. Není shromažďována, pokud `X-Forwarded-For` je nastavena.
* Další informace o tom, jak se shromažďují údaje o IP adrese a geografickém umístění v Application Insights najdete v tomto [článku](./app/ip-collection.md).


Můžete nakonfigurovat, `ClientIpHeaderTelemetryInitializer` aby se IP adresa převzala z jiného záhlaví. V některých systémech je například přesouvá server proxy, nástroj pro vyrovnávání zatížení nebo síť CDN `X-Originating-IP` . [Další informace](https://apmtips.com/posts/2016-07-05-client-ip-address/).

[Pomocí Power BI](app/export-power-bi.md ) můžete zobrazit telemetrii žádostí na mapě.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Jak dlouho se data na portálu uchovávají? Je zabezpečení?
Podívejte se na [uchovávání dat a ochranu osobních údajů][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Co se stane s telemetriemi Application Insights, když server nebo zařízení ztratí spojení s Azure?

Všechny naše sady SDK, včetně webové sady SDK, zahrnují "spolehlivý přenos" nebo "robustní přenos". Když server nebo zařízení ztratí spojení s Azure, telemetrie se [uloží lokálně na systém souborů](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (sady SDK serveru) nebo v ÚLOŽIŠTI relace HTML5 (webová sada SDK). Sada SDK se bude pravidelně pokoušet o odeslání této telemetrie, dokud naše služba pro příjem dat nepovažuje za "zastaralou" (48 – hodiny pro protokoly, 30 minut pro metriky). Zahozená zastaralá telemetrie se vynechá. V některých případech, například v případě, že je místní úložiště plný, nebude k opakování docházet.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Můžou být do telemetrie odesílána osobní data?

To je možné, pokud váš kód odesílá taková data. K tomu může dojít také v případě, že proměnné v trasování zásobníku obsahují osobní údaje. Váš vývojový tým by měl provádět posouzení rizik, aby se zajistilo správné zpracování osobních údajů. [Přečtěte si další informace o uchovávání dat a ochraně osobních údajů](app/data-retention-privacy.md).

Po vyhledání atributů geografického umístění jsou **všechny** oktety klientské webové adresy vždycky nastavené na 0.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Můj klíč instrumentace je viditelný ve zdroji webové stránky. 

* To je běžný postup při monitorování řešení.
* Nedá se použít ke krádeži vašich dat.
* Dá se použít k zkosení dat nebo aktivaci výstrah.
* Neslyšeli jsme, že u každého zákazníka byly takové problémy.

Mohli byste:

* Pro data klienta a serveru použijte dva samostatné klíče instrumentace (samostatné Application Insights prostředky). Nebo
* Napíšete proxy server, který běží na serveru, a webový klient bude odesílat data prostřednictvím tohoto proxy serveru.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Návody najdete v tématu vystavování dat v diagnostickém vyhledávání?
Data neodesíláme automaticky, ale můžete použít volání TrackTrace: vložte data do parametru zprávy. To má delší omezení velikosti než limity vlastností řetězce, ale nemůžete je filtrovat.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Mám použít jeden nebo více prostředků Application Insights?

Pro všechny komponenty nebo role v jednom podnikovém systému použijte jeden prostředek. Použijte samostatné prostředky pro vývoj, testování a vydání a pro nezávislé aplikace.

* [Podívejte se na diskuzi tady](app/separate-resources.md)
* [Příklad – cloudová služba s pracovními a webovými rolemi](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Návody dynamicky měnit klíč instrumentace?

* [Diskuze sem](app/separate-resources.md)
* [Příklad – cloudová služba s pracovními a webovými rolemi](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Jaké jsou počty uživatelů a relací?

* Sada JavaScript SDK nastavuje na webovém klientovi soubor cookie pro uživatele, identifikaci vracení uživatelů a soubor cookie relace pro seskupení aktivit.
* Pokud není k dispozici žádný skript na straně klienta, můžete [nastavit soubory cookie na serveru](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Pokud se vaše lokalita používá v různých prohlížečích nebo v rámci anonymním nebo v různých počítačích, bude se tato síť počítat více než jednou.
* Chcete-li identifikovat přihlášeného uživatele v počítačích a prohlížečích, přidejte volání [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Jsem povolil vše v Application Insights?
| Co byste měli vidět | Jak ho získat | Proč to chcete |
| --- | --- | --- |
| Grafy dostupnosti |[Webové testy](app/monitor-web-app-availability.md) |Informace o tom, že vaše webová aplikace je zapnutá |
| Výkon aplikace serveru: doby odezvy,... |[Přidejte do svého projektu Application Insights](app/asp-net.md) nebo [nainstalujte AI monitorování stavu na server](app/monitor-performance-live-website-now.md) (nebo zapište vlastní kód pro [sledování závislostí](app/api-custom-events-metrics.md#trackdependency)). |Zjistit problémy s výkonem |
| Telemetrie závislostí |[Nainstalovat AI Monitorování stavu na serveru](app/monitor-performance-live-website-now.md) |Diagnostika problémů s databázemi nebo jinými externími komponentami |
| Získat trasování zásobníku z výjimek |[Vložení volání TrackException do kódu](app/asp-net-exceptions.md) (ale některé jsou hlášeny automaticky) |Detekovat a diagnostikovat výjimky |
| Hledat trasování protokolu |[Přidat adaptér protokolování](app/asp-net-trace-logs.md) |Diagnostikujte výjimky, problémy s výkonem |
| Základy použití klientů: zobrazení stránky, relace,... |[Inicializátor JavaScriptu na webových stránkách](app/javascript.md) |Analýza využití |
| Vlastní metriky klienta |[Sledování hovorů na webových stránkách](app/api-custom-events-metrics.md) |Vylepšení uživatelského prostředí |
| Vlastní metriky serveru |[Sledování hovorů na serveru](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Proč se počty v grafech hledání a metrik nerovnají?

[Vzorkování](app/sampling.md) snižuje počet položek telemetrie (požadavky, vlastní události atd.), které jsou ve skutečnosti odesílány z vaší aplikace na portál. V části Hledat se zobrazí počet položek, které byly skutečně přijaty. V grafech metrik, které zobrazují počet událostí, se zobrazí počet původních událostí, ke kterým došlo. 

Každá odeslaná položka nese `itemCount` vlastnost, která ukazuje, kolik původních událostí položka představuje. Pokud chcete sledovat vzorkování v provozu, můžete spustit tento dotaz v Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Konfigurace Application Insights

Pomocí Azure Sledování prostředků můžete [psát skripty PowerShellu](app/powershell.md) :

* Vytváření a aktualizace prostředků Application Insights.
* Nastavte cenový plán.
* Získejte klíč instrumentace.
* Přidejte upozornění metriky.
* Přidejte test dostupnosti.

Nemůžete nastavit sestavu Průzkumníka metrik ani nastavit průběžný export.

#### <a name="querying-the-telemetry"></a>Dotazování telemetrie

Pro spouštění [analytických](./log-query/log-query-overview.md) dotazů použijte [REST API](https://dev.applicationinsights.io/) .

### <a name="how-can-i-set-an-alert-on-an-event"></a>Jak můžu nastavit upozornění na událost?

Výstrahy Azure jsou jenom v metrikách. Vytvořte vlastní metriku, která při každém výskytu události přeprotíná prahovou hodnotu hodnoty. Pak u metriky nastavte výstrahu. Obdržíte oznámení vždy, když metrika v obou směrech vyřadí prahovou hodnotu. nebudete dostávat žádné oznámení do prvního křížení bez ohledu na to, jestli je počáteční hodnota vysoká nebo nízká. vždy se jedná o latenci během několika minut.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Účtují se poplatky za přenos dat mezi webovou aplikací Azure a Application Insights?

* Pokud je webová aplikace Azure hostovaná v datovém centru, kde je koncový bod kolekce Application Insights, neúčtují se žádné poplatky. 
* Pokud se v datovém centru hostitele nenachází koncový bod kolekce, bude telemetrie vaší aplikace účtovat [odchozí poplatky za Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Nezáleží na tom, kde je váš prostředek Application Insights hostovaný. Záleží jenom na distribuci našich koncových bodů.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Můžu na portálu Application Insights odeslat telemetrii?

Doporučujeme používat naše sady SDK a používat [rozhraní API SDK](app/api-custom-events-metrics.md). Existují varianty sady SDK pro různé [platformy](app/platforms.md). Tyto sady SDK zpracovávají ukládání do vyrovnávací paměti, kompresi, omezování, opakování a tak dále. [Schéma](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) příjmu a [protokol koncového bodu](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) jsou ale veřejné.

### <a name="can-i-monitor-an-intranet-web-server"></a>Můžu monitorovat intranetový webový server?

Ano, ale budete muset pro naše služby povolený provoz buď pomocí výjimek brány firewall, nebo přesměrování proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


[Tady si můžete](app/ip-addresses.md)projít úplný seznam služeb a IP adres.

#### <a name="firewall-exception"></a>Výjimka brány firewall

Umožněte webovému serveru odesílat telemetrii do našich koncových bodů. 

#### <a name="gateway-redirect"></a>Přesměrování brány

Přesměrujte provoz z vašeho serveru do brány v intranetu přepsáním koncových bodů ve vaší konfiguraci. Pokud tyto vlastnosti Endpoint nejsou v konfiguraci k dispozici, budou tyto třídy používat výchozí hodnoty uvedené níže v příkladu ApplicationInsights.config. 

Brána by měla směrovat provoz na základní adresu našeho koncového bodu. V konfiguraci nahraďte výchozí hodnoty hodnotou `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Příklad ApplicationInsights.config s výchozími koncovými body:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider je k dispozici od verze v 2.6.0.



#### <a name="proxy-passthrough"></a>Průchozí proxy

Passthrough proxy můžete dosáhnout konfigurací úrovně počítače nebo proxy serveru na úrovni aplikace.
Další informace najdete v článku dotnet na [defaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Příklad Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Můžu webové testy dostupnosti spustit na intranetovém serveru?

Naše [webové testy](app/monitor-web-app-availability.md) jsou spouštěny v bodech přítomnosti, které jsou distribuovány po celém světě. Existují dvě řešení:

* Dvířka brány firewall – umožňují žádosti na server z [dlouhého a nepřípustného seznamu webových testovacích agentů](app/ip-addresses.md).
* Napište svůj vlastní kód pro posílání pravidelných požadavků na server v rámci intranetu. Pro tento účel můžete spustit webové testy sady Visual Studio. Tester by mohl odeslat výsledky do Application Insights pomocí rozhraní API TrackAvailability ().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak dlouho trvá shromažďování telemetrie?

Většina dat Application Insights má latenci kratší než 5 minut. Některá data mohou trvat déle; obvykle jsou větší soubory protokolu. Další informace najdete v tématu věnovaném [smlouvě SLA Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>Odpovědi HTTP 502 a 503 nejsou vždy zachyceny Application Insights

Application Insights se nezachycují chyby "502 špatné brány" a "503 služba nedostupná". Pokud se pro monitorování používá jenom JavaScript na straně klienta, je očekávané chování, protože odpověď na chybu se vrátí před stránku obsahující hlavičku HTML a vykresluje se fragment kódu jazyka JavaScript monitorování. 

Pokud byla odpověď 502 nebo 503 odeslána ze serveru se zapnutým monitorováním na straně serveru, budou chyby shromažďovány Application Insights SDK. 

Existují však i případy, kdy je na webovém serveru aplikace povoleno monitorování na straně serveru, že chyba 502 nebo 503 nebude zachycena Application Insights. Mnohé moderní webové servery neumožňují klientovi komunikovat přímo, ale místo toho používají řešení, jako jsou reverzní proxy, k předávání informací mezi klientem a front-end webovými servery. 

V tomto scénáři by bylo možné klientovi vrátit odpověď 502 nebo 503 z důvodu problému ve vrstvě reverzního proxy serveru a to by nebylo zachyceno předem Application Insights. K detekci problémů v této vrstvě možná budete muset přesměrovat protokoly ze reverzního proxy serveru na Log Analytics a vytvořit vlastní pravidlo pro kontrolu odpovědí 502/503. Další informace o běžných příčinách chyb 502 a 503 najdete v [článku věnovaném řešení potíží s Azure App Service pro "502 Bad Gateway" a "503 Služba není k dispozici"](../app-service/troubleshoot-http-502-http-503.md).     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Co je OpenTelemetry

Nové Open Source standard pro pozorování. Další informace najdete na adrese [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Proč Microsoft/Azure Monitor investici v OpenTelemetry?

Domníváme se, že zákazníkům lépe obsluhuje tři důvody:
   1. Povolit podporu pro více zákaznických scénářů.
   2. Instrumentace bez obav od zamčení dodavatele.
   3. Zvyšte transparentnost a zapojení zákazníků.

Také zarovnává se strategií Microsoftu, aby vyrovnala [Open Source](https://opensource.microsoft.com/).

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Jakou další hodnotu mi OpenTelemetry?

Kromě výše uvedených důvodů je OpenTelemetry efektivnějším způsobem v měřítku a poskytuje konzistentní návrh a konfigurace napříč jazyky.

### <a name="how-can-i-test-out-opentelemetry"></a>Jak můžu otestovat OpenTelemetry?

Zaregistrujte se a připojte se k naší Azure Monitor Application Insights na [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>Co znamená GA v kontextu OpenTelemetry?

Komunita OpenTelemetry definuje všeobecně dostupné ( [GA).](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302) OpenTelemetry "GA" ale neznamená paritu funkcí existujícími sadami Application Insights SDK. Azure Monitor bude pro zákazníky, kteří vyžadují funkce, jako jsou [předem agregované metriky](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), [živé metriky](app/live-stream.md), [adaptivní vzorkování](app/sampling.md#adaptive-sampling), [Profiler](app/profiler-overview.md)a [Snapshot Debugger](app/snapshot-debugger.md) , dál doporučit naše aktuální Application Insights sady SDK, dokud nedosáhne zralosti sady OpenTelemetry SDK.

### <a name="can-i-use-preview-builds-in-production-environments"></a>Můžu používat buildy Preview v produkčním prostředí?

Nedoporučuje se. Další informace najdete v tématu věnovaném [dodatečným podmínkám použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Jaký je rozdíl mezi OpenTelemetry SDK a automatickou instrumentací?

Specifikace OpenTelemetry definuje [sadu SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk). Zkratka "SDK" je balíček pro konkrétní jazyk, který shromažďuje data telemetrie napříč různými komponentami vaší aplikace a odesílá data Azure Monitor prostřednictvím vývozce.

Koncept automatické instrumentace (někdy označovaný jako vkládání v bajtech, bez kódu nebo založené na agentech) odkazuje na schopnost instrumentovat aplikaci beze změny kódu. Další informace najdete například v [souboru Readme pro automatickou instrumentaci OpenTelemetry Java](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) .

### <a name="whats-the-opentelemetry-collector"></a>Co je kolekce OpenTelemetry?

Kolektor OpenTelemetry je popsaný v [souboru Readme pro GitHub](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector). V současné době společnost Microsoft nevyužívá kolektor OpenTelemetry a závisí na přímých vývozcůch, které odesílají Application Insights Azure Monitor.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Jaký je rozdíl mezi OpenCensus a OpenTelemetry?

[OpenCensus](https://opencensus.io/) je ukazatel na [OpenTelemetry](https://opentelemetry.io/). Microsoft pomáhá společně s [OpenTracing](https://opentracing.io/) a OpenCensus vytvořit OpenTelemetry, což je standardní standard pro dodržování standardů na světě. Stávající provozní prostředí [Python SDK](app/opencensus-python.md) pro Azure monitor je založené na OpenCensus, ale nakonec budou všechny sady Azure monitor SDK založené na OpenTelemetry.


## <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery

### <a name="health-feature-is-in-private-preview"></a>Funkce Health je v privátní verzi Preview.

Plánujeme vytvořit řadu změn pro přidání funkcí a řešení vašich názorů. Funkce Health přejde na konec června 2020 na verzi Private Preview a další informace najdete v [oznámení o aktualizacích Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/).

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Co *jiné procesy* reprezentují v rámci zobrazení uzlu?

**Další procesy** jsou určené k tomu, aby vám pomohla jasně pochopit hlavní příčinu vysokého využití prostředků na vašem uzlu. To umožňuje rozlišovat využití mezi kontejnerové procesy vs bez kontejnerových procesů.

Jaké jsou tyto **Další procesy**? 

Jedná se o nekontejnerové procesy, které běží na vašem uzlu.  

Jak to Vypočítejte?

**Jiné procesy**  =  *Celkové využití z CAdvisor*  -  *Využití z kontejneru procesu*

Mezi **Další procesy** patří:

- Samostatné spravované nebo spravované procesy Kubernetes bez kontejnerů 

- Běhové procesy kontejneru  

- Kubelet  

- Systémové procesy spuštěné v uzlu 

- Jiné úlohy bez Kubernetes spuštěné v hardwaru nebo na virtuálním počítači s uzlem 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Nezobrazuje se při dotazování tabulky ContainerLog vyplněné hodnoty vlastností image a Name.

U agenta verze ciprod12042019 a novějších se ve výchozím nastavení tyto dvě vlastnosti neplní pro každou řádek protokolu, aby se minimalizovaly náklady vzniklé shromážděnými daty protokolů. Existují dvě možnosti, jak zadat dotaz na tabulku, která obsahuje tyto vlastnosti s jejich hodnotami:

#### <a name="option-1"></a>Možnost 1 

Připojte další tabulky, abyste tyto hodnoty vlastností zahrnuli do výsledků.

Upravte dotazy tak, aby zahrnovaly vlastnosti Image a ImageTag z ```ContainerInventory``` tabulky připojením k vlastnosti ContainerID. Můžete zahrnout vlastnost Name (jak se dřív zobrazila v ```ContainerLog``` tabulce) z pole ContaineName tabulky KubepodInventory spojením s vlastností ContainerID. Toto je doporučená možnost.

Následující příklad je ukázkový podrobný dotaz, který vysvětluje, jak tyto hodnoty polí získat pomocí spojení.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Možnost 2

Znovu povolit shromažďování pro tyto vlastnosti pro každý řádek protokolu kontejneru.

Pokud první možnost není vhodná v důsledku změn dotazů, můžete shromažďování těchto polí znovu povolit povolením nastavení ```log_collection_settings.enrich_container_logs``` v mapě konfigurace agenta, jak je popsáno v [nastavení konfigurace shromažďování dat](insights/container-insights-agent-config.md).

> [!NOTE]
> Druhá možnost se nedoporučuje u velkých clusterů, které mají více než 50 uzlů, protože generují volání serveru rozhraní API z každého uzlu v clusteru, aby bylo možné toto rozšíření provést. Tato možnost také zvyšuje velikost dat pro všechny shromážděné řádky protokolu.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Můžu zobrazit metriky shromážděné v Grafana?

Azure Monitor for Containers podporuje zobrazování metrik uložených v pracovním prostoru Log Analytics v řídicích panelech Grafana. K dispozici je šablona, kterou si můžete stáhnout z [úložiště řídicích panelů](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, abyste mohli začít s odkazem na pomoc s postupem, jak se dotazovat na další data z monitorovaných clusterů, aby je bylo možné vizualizovat ve vlastních řídicích panelech Grafana. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Můžu monitorovat cluster AKS s Azure Monitor pro kontejnery?

Azure Monitor for Containers podporuje monitorování úloh kontejneru nasazených do AKS (dřív označovaných jako ACS-Engine) clusterů hostovaných v Azure. Další podrobnosti a přehled kroků požadovaných k povolení monitorování pro tento scénář najdete v tématu [použití Azure monitor pro kontejnery pro AKS modul](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Proč se mi nezobrazují data v pracovním prostoru Log Analytics?

Pokud v pracovním prostoru Log Analytics v průběhu času nevidíte žádná data, možná jste dosáhli výchozího limitu 500 MB nebo denního limitu pro kontrolu množství dat, která se mají shromažďovat denně. Pokud je limit splněn pro daný den, shromažďování dat se zastaví a obnoví pouze následující den. Pokud chcete zkontrolovat využití dat a aktualizovat ji na jinou cenovou úroveň v závislosti na předpokládaných vzorcích použití, přečtěte si téma [využití dat protokolu a náklady](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jaké jsou stavy kontejneru zadané v tabulce ContainerInventory?

Tabulka ContainerInventory obsahuje informace o zastavených a spuštěných kontejnerech. Tabulka je vyplněna pracovním postupem v rámci agenta, který se dotáže do Docker pro všechny kontejnery (spuštěné a zastaveno) a předává tato data Log Analytics pracovním prostoru.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Návody vyřešit chybu *registrace chybějícího předplatného* ?

Pokud se zobrazí chyba **chybějící registrace odběru pro Microsoft. OperationsManagement**, můžete ji vyřešit registrací poskytovatele prostředků **Microsoft. OperationsManagement** v předplatném, ve kterém je tento pracovní prostor definovaný. Dokumentaci k tomu, jak to udělat, najdete [tady](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Podporuje AKS clustery s podporou RBAC?

Řešení pro monitorování kontejnerů nepodporuje RBAC, ale podporuje se Azure Monitor pro kontejnery. Na stránce s podrobnostmi řešení se nemusí zobrazovat správné informace v oknech, které zobrazují data pro tyto clustery.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Návody Povolit shromažďování protokolů pro kontejnery v oboru názvů Kube-System prostřednictvím Helm?

Kolekce protokolů z kontejnerů v oboru názvů Kube-System je ve výchozím nastavení zakázaná. Shromažďování protokolů lze povolit nastavením proměnné prostředí v omsagent. Další informace najdete na stránce [Azure monitor pro kontejnery](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) na webu GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Návody aktualizovat omsagent na nejnovější vydanou verzi?

Informace o tom, jak upgradovat agenta, najdete v tématu [Správa agentů](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Návody povolit víceřádkové protokolování?

V současné době Azure Monitor pro kontejnery nepodporuje víceřádkové protokolování, ale k dispozici jsou alternativní řešení. Můžete nakonfigurovat všechny služby pro zápis ve formátu JSON a potom Docker/Moby je zapíše jako jeden řádek.

Můžete například zabalit protokol jako objekt JSON, jak je znázorněno v následujícím příkladu pro ukázkovou node.js aplikaci:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Tato data budou vypadat jako v následujícím příkladu v Azure Monitor pro protokoly při dotazování na ni:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Podrobný přehled tohoto problému najdete v následujícím [odkazu na GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Návody vyřešit chyby Azure AD, když povolíte živé protokoly? 

Může se zobrazit následující chyba: **Adresa URL odpovědi zadaná v požadavku neodpovídá adresám URL odpovědí nakonfigurovaným pro aplikaci: ' <ID aplikace \> '**. Řešení, které se má vyřešit, najdete v článku [jak zobrazit data kontejneru v reálném čase s Azure monitor pro kontejnery](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Proč není možné upgradovat cluster po registraci?

Pokud po povolení Azure Monitor pro kontejnery pro cluster AKS odstraníte pracovní prostor Log Analytics, který cluster odeslal do, při pokusu o upgrade clusteru se nezdaří. Pokud chcete tento problém obejít, budete muset zakázat monitorování a pak ho znovu povolit odkazování na jiný platný pracovní prostor v rámci vašeho předplatného. Pokud se pokusíte upgradovat cluster znovu, měl by se zpracovat a dokončit úspěšně.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Které porty a domény potřebuji pro agenta otevřít nebo povolit?

Podívejte se na [požadavky na bránu firewall sítě](insights/container-insights-onboard.md#network-firewall-requirements) pro informace o konfiguraci proxy serveru a brány firewall vyžadované pro kontejnerový agent s Azure, vládou USA a Azure Čína 21Vianet cloudy.


## <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače

### <a name="can-i-onboard-to-an-existing-workspace"></a>Můžu se připojit k existujícímu pracovnímu prostoru?
Pokud jsou vaše virtuální počítače už připojené k pracovnímu prostoru Log Analytics, můžete tento pracovní prostor i nadále používat při připojování k Azure Monitor pro virtuální počítače za předpokladu, že je v některé z [podporovaných oblastí](insights/vminsights-configure-workspace.md#supported-regions).


### <a name="can-i-onboard-to-a-new-workspace"></a>Můžu se připojit k novému pracovnímu prostoru? 
Pokud vaše virtuální počítače nejsou aktuálně připojené k existujícímu pracovnímu prostoru Log Analytics, je potřeba vytvořit nový pracovní prostor pro ukládání vašich dat. Vytvoření nového výchozího pracovního prostoru se provádí automaticky, pokud nakonfigurujete jeden virtuální počítač Azure pro Azure Monitor pro virtuální počítače prostřednictvím Azure Portal.

Pokud se rozhodnete použít metodu založenou na skriptu, tyto kroky jsou pokryté v článku [povolení Azure monitor pro virtuální počítače pomocí Azure PowerShell nebo šablony Správce prostředků](./insights/vminsights-enable-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Jak mám dělat, když už můj virtuální počítač hlásí stávající pracovní prostor?
Pokud už data z virtuálních počítačů shromažďujete, je možné, že jste ji už nakonfigurovali k nahlášení dat do existujícího pracovního prostoru Log Analytics.  Pokud je tento pracovní prostor v některé z našich podporovaných oblastí, můžete Azure Monitor pro virtuální počítače do tohoto existujícího pracovního prostoru povolit.  Pokud pracovní prostor, který už používáte, není v některé z našich podporovaných oblastí, nebudete moct Azure Monitor pro virtuální počítače v tuto chvíli zaregistrovat.  Aktivně pracujeme na podpoře dalších oblastí.


### <a name="why-did-my-vm-fail-to-onboard"></a>Proč se můj virtuální počítač nepovedlo zařadit do provozu?
Při připojování virtuálního počítače Azure z Azure Portal dojde k následujícím krokům:

* Pokud je vybraná tato možnost, vytvoří se výchozí pracovní prostor Log Analytics.
* Agent Log Analytics je nainstalovaný na virtuálních počítačích Azure pomocí rozšíření virtuálního počítače, pokud je to potřeba.  
* Agent závislostí Azure Monitor pro virtuální počítače se nainstaluje na virtuální počítače Azure s použitím rozšíření, pokud je stanovený jako povinný. 

Během procesu připojení zkontrolujeme stav každé z výše uvedených a vrátíte na portálu stav oznámení. Konfigurace pracovního prostoru a instalace agenta obvykle trvá 5 až 10 minut. Zobrazení dat monitorování na portálu trvá dalších 5 až 10 minut.  

Pokud jste zahájili registraci a viděli zprávy indikující, že virtuální počítač musí být zaregistrované, počkejte až 30 minut, než virtuální počítač dokončí proces. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>V grafech výkonu pro virtuální počítač nejsou vidět žádná nebo žádná data
Naše grafy výkonu byly aktualizovány tak, aby používaly data uložená v tabulce *InsightsMetrics* .  Pokud chcete zobrazit data v těchto grafech, budete muset upgradovat, aby používala nové řešení VM Insights.  Další informace najdete v našich [dotazech GA](insights/vminsights-ga-release-faq.md) .

Pokud nevidíte údaje o výkonu v tabulce disků nebo v některém z grafů výkonu, nemusí být v pracovním prostoru nakonfigurovány čítače výkonu. Pokud ho chcete vyřešit, spusťte následující [skript PowerShellu](./insights/vminsights-enable-powershell.md).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Jak se Azure Monitor pro virtuální počítače funkce map liší od Service Map?
Funkce mapy Azure Monitor pro virtuální počítače je založena na Service Map, ale má následující rozdíly:

* Zobrazení mapy je dostupné z okna virtuálního počítače a z Azure Monitor pro virtuální počítače v části Azure Monitor.
* Připojení na mapě teď lze kliknout a zobrazit zobrazení dat metriky připojení na bočním panelu pro vybrané připojení.
* K dispozici je nové rozhraní API, které slouží k vytváření map pro lepší podporu složitějších map.
* Monitorované virtuální počítače jsou teď součástí uzlu skupiny klientů a prstencový graf zobrazuje poměr monitorovaných a nemonitorovaných virtuálních počítačů ve skupině.  Můžete ji také použít k filtrování seznamu počítačů při rozbalení skupiny.
* Monitorované virtuální počítače jsou teď zahrnuté do uzlů skupiny portů serveru a prstencový graf zobrazuje poměr monitorovaných a nemonitorovaných počítačů ve skupině.  Můžete ji také použít k filtrování seznamu počítačů při rozbalení skupiny.
* Styl mapy byl aktualizován tak, aby byl s mapou aplikace z Application Insights lépe konzistentní.
* Postranní panely byly aktualizovány a nemají kompletní sadu integrace, které byly podporovány v Service Map-Update Management, Change Tracking, zabezpečení a oddělení služeb. 
* Možnost výběru skupin a počítačů na mapě byla aktualizována a teď podporuje předplatná, skupiny prostředků, službu Azure Virtual Machine Scale Sets a cloudové služby.
* Ve funkci mapování Azure Monitor pro virtuální počítače nelze vytvořit nové Service Map skupiny počítačů.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Proč moje grafy výkonu zobrazují tečkované řádky?
K tomu může dojít z několika důvodů.  V případech, kdy existuje mezera v kolekci dat, můžeme řádky považovat za tečky.  Pokud jste upravili četnost vzorkování dat pro povolené čítače výkonu (výchozí nastavení je shromažďování dat každých 60 sekund), tečkované čáry v grafu se zobrazí, pokud vyberete úzký časový rozsah grafu a frekvence vzorkování je menší než velikost intervalu použitá v grafu (například frekvence vzorkování je každých 10 minut a každé pole v grafu je 5 minut).  Když zvolíte širší časový rozsah, který se má zobrazit, musí se čáry grafu zobrazovat jako plné čáry místo teček v tomto případě.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Jsou skupiny podporované pomocí Azure Monitor pro virtuální počítače?
Ano, Jakmile nainstalujete agenta závislostí, shromáždíme informace z virtuálních počítačů pro zobrazení skupin na základě předplatného, skupiny prostředků, sady škálování virtuálních počítačů a cloudových služeb.  Pokud jste používali Service Map a vytvořili jste skupiny počítačů, zobrazí se také.  Skupiny počítačů se zobrazí také ve filtru skupiny, pokud jste je vytvořili pro pracovní prostor, který si prohlížíte. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Návody se zobrazí podrobnosti o tom, co řídí řádek 95. percentilu v agregovaných grafech výkonu?
Ve výchozím nastavení je seznam seřazený tak, aby se zobrazily virtuální počítače s nejvyšší hodnotou 95. percentilu pro vybranou metriku, s výjimkou dostupného grafu paměti, který zobrazuje počítače s nejnižší hodnotou 5. percentilu.  Když kliknete na graf, otevře se zobrazení **horních seznamů**  s odpovídající vybranou metrikou.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkce map zpracovává duplicitní IP adresy napříč různými virtuální sítě a podsítěmi?
Pokud duplikujete rozsahy IP adres buď pomocí virtuálních počítačů, nebo virtuálních počítačů Azure pro škálování v podsítích a virtuální sítě, může to způsobit, že Azure Monitor pro virtuální počítače mapa zobrazí nesprávné informace. Jedná se o známý problém a chystáme se prozkoumat možnosti pro zlepšení tohoto prostředí.

### <a name="does-map-feature-support-ipv6"></a>Podporuje funkce mapy protokol IPv6?
Funkce map aktuálně podporuje jenom protokol IPv4 a prozkoumáme podporu pro protokol IPv6. Podporujeme také protokol IPv4, který je tunelem v rámci protokolu IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Když nahrajem mapu pro skupinu prostředků nebo jinou velkou skupinu, může se mapa obtížně zobrazit.
I když jsme vylepšili mapování na zpracování rozsáhlých a složitých konfigurací, uvědomujeme si, že mapa může mít spoustu uzlů, připojení a uzel funguje jako cluster.  Jsme se zavázali, že budete pokračovat v vylepšení podpory pro zvýšení škálovatelnosti.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Proč se síťový graf na kartě výkon liší od síťového grafu na stránce Přehled virtuálního počítače Azure?

Stránka s přehledem pro virtuální počítač Azure zobrazuje grafy založené na měření aktivity hostitele v hostovaném virtuálním počítači.  V článku Přehled síťového grafu na virtuálním počítači Azure se zobrazí jenom síťový provoz, který se bude fakturovat.  To nezahrnuje přenos mezi virtuálními sítěmi.  Data a grafy zobrazené pro Azure Monitor pro virtuální počítače jsou založené na datech z virtuálního počítače hosta a v síťovém grafu se zobrazují všechny přenosy protokolu TCP/IP, které jsou příchozí a odchozí pro tento virtuální počítač, včetně mezi virtuálními sítěmi.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Jak je měřena doba odezvy pro data uložená v VMConnection a zobrazená na panelu připojení a sešitech?

Doba odezvy je aproximace. Vzhledem k tomu, že kód aplikace neznáte, nevíte ve skutečnosti, kdy je zahájena žádost a kdy odpověď dorazí. Místo toho sledujeme data odesílaná na připojení a potom se data znovu přidávají do tohoto připojení. Náš agent udržuje přehled o těchto odesláních a pokusůch o jejich párování: sekvence odeslání, následovaná posloupností přijetí, je interpretována jako pár požadavků a odpovědí. Časování mezi těmito operacemi je doba odezvy. Bude zahrnovat latenci sítě a dobu zpracování serveru.

Tato aproximace funguje dobře u protokolů, které jsou založené na požadavcích a odpovědích: jedna žádost se na připojení doprovází a přijde jedna odpověď. Jedná se o případ HTTP (S) (bez použití kanálů), ale nevyhovuje pro jiné protokoly.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Existují nějaká omezení, pokud mám Log Analytics cenového plánu zdarma?
Pokud jste nakonfigurovali Azure Monitor s pracovním prostorem Log Analytics pomocí *bezplatné* cenové úrovně, funkce Azure monitor pro virtuální počítače map bude podporovat jenom pět připojených počítačů připojených k pracovnímu prostoru. Pokud máte pět virtuálních počítačů připojených k bezplatnému pracovnímu prostoru, odpojíte jeden z virtuálních počítačů a později se připojíte k novému VIRTUÁLNÍmu počítači, nový virtuální počítač se nemonitoruje a neprojeví se na stránce Mapa.  

V rámci této podmínky budete po otevření virtuálního počítače vyzváni v možnosti **vyzkoušet nyní** a v levém podokně vyberte **přehledy** , i když už je nainstalovaný na virtuálním počítači.  Nebudete ale mít k možnost, která by normálně probíhala, pokud se tento virtuální počítač nepřipojil k Azure monitor pro virtuální počítače. 


## <a name="next-steps"></a>Další kroky
Pokud tady na váš dotaz neodpovíte, můžete na následující fóra najít další otázky a odpovědi.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Pokud chcete získat obecnou zpětnou vazbu na Azure Monitor, navštivte [fórum pro názory](https://feedback.azure.com/forums/34192--general-feedback).
