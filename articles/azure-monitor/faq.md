---
title: Nejčastější dotazy k monitorování Azure | Dokumenty společnosti Microsoft
description: Odpovědi na nejčastější dotazy týkající se Azure Monitoru.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: db63ce2d56eb78bf6b361d530511b6902c1cb6d5
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637776"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Nejčastější dotazy k Azure Monitoru

Tento nejčastější dotazy k Microsoftu je seznam často kladených otázek týkajících se Azure Monitoru.

## <a name="general"></a>Obecné

### <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
[Azure Monitor](overview.md) je služba v Azure, která poskytuje monitorování výkonu a dostupnosti aplikací a služeb v Azure, jiných cloudových prostředích nebo v místním prostředí. Azure Monitor shromažďuje data z více zdrojů do společné datové platformy, kde je možné analyzovat trendy a anomálie. Bohaté funkce ve službě Azure Monitor vám pomohou rychle identifikovat a reagovat na kritické situace, které mohou ovlivnit vaši aplikaci.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Jaký je rozdíl mezi Azure Monitor, Log Analytics a Application Insights?
V září 2018 Microsoft zkombinoval Azure Monitor, Log Analytics a Application Insights do jediné služby, aby poskytoval výkonné komplexní monitorování vašich aplikací a komponent, na které spoléhají. Funkce v Log Analytics a Application Insights se nezměnily, i když některé funkce byly rebranded na Azure Monitor, aby lépe odrážely jejich nový obor. Modul dat protokolu a dotazovací jazyk Log Analytics se teď označuje jako protokoly monitorování Azure. Viz [Aktualizace terminologie Azure Monitoru](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Kolik azure monitor stojí?
Funkce Azure Monitoru, které jsou automaticky povolené, jako je například shromažďování metrik a protokolů aktivit, jsou k dispozici zdarma. Náklady jsou spojeny s dalšími funkcemi, jako jsou dotazy protokolu a výstrahy. Podrobné informace o cenách najdete na [stránce s cenami Azure Monitoru.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="how-do-i-enable-azure-monitor"></a>Jak povolím Azure Monitor?
Azure Monitor je povolená v okamžiku, kdy vytvoříte nové předplatné Azure a [protokol aktivit](platform/activity-logs-overview.md) a [metriky platformy](platform/data-platform-metrics.md) se automaticky shromažďují. Vytvořte [diagnostická nastavení,](platform/diagnostic-settings.md) která shromáždí podrobnější informace o provozu vašich prostředků Azure, a přidejte [řešení monitorování](insights/solutions.md) a [přehledy,](insights/insights-overview.md) které vám poskytnou další analýzu shromážděných dat pro konkrétní služby. 

### <a name="how-do-i-access-azure-monitor"></a>Jak se dostanu k Azure Monitoru?
Přístup ke všem funkcím a datům Azure Monitoru z nabídky **Monitorování** na webu Azure Portal. V části **Monitorování** v nabídce pro různé služby Azure poskytuje přístup ke stejným nástrojům s daty filtrovanými na konkrétní prostředek. Data Azure Monitor udat návlacích je přístupná i pro různé scénáře pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZU, Prostředí PowerShell a rozhraní REST API.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Existuje místní verze Azure Monitoru?
Ne. Azure Monitor je škálovatelná cloudová služba, která zpracovává a ukládá velké množství dat, i když Azure Monitor může monitorovat prostředky, které jsou místní a v jiných cloudech.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Můžete Azure Monitor sledovat místní prostředky?
Ano, kromě shromažďování dat monitorování z prostředků Azure může Azure Monitor shromažďovat data z virtuálních počítačů a aplikací v jiných cloudech a v místním prostředí. Viz [Zdroje dat monitorování pro Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Integruje se Azure Monitor se správcem operací system centra?
Stávající skupinu pro správu Nástroje pro správu nástroje Operations Center Operations Manager můžete připojit ke službě Azure Monitor a shromažďovat data od agentů do protokolů monitorování Azure. To umožňuje použít dotazy protokolu a řešení k analýze dat shromážděných od agentů. Můžete také nakonfigurovat stávající agenty Nástroje operations manageru systému pro odesílání dat přímo do služby Azure Monitor. Viz [Připojení nástroje Operations Manager k nástroji Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Jaké IP adresy azure monitor používá?
Seznam IP adres a portů požadovaných pro agenty a další externí prostředky pro přístup k Azure Monitoru najdete v [tématu IP adresy používané application insights a Log Analytics.](app/ip-addresses.md) 

## <a name="monitoring-data"></a>Data monitorování

### <a name="where-does-azure-monitor-get-its-data"></a>Kde Azure Monitor získá svá data?
Azure Monitor shromažďuje data z různých zdrojů, včetně protokolů a metrik z platformy Azure a prostředků, vlastních aplikací a agentů spuštěných na virtuálních počítačích. Další služby, jako je Azure Security Center a Network Watcher shromažďovat data do pracovního prostoru Log Analytics, takže je možné analyzovat pomocí dat Azure Monitor. Vlastní data můžete také odeslat do Azure Monitoru pomocí rozhraní REST API pro protokoly nebo metriky. Viz [Zdroje dat monitorování pro Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Jaká data služba Azure Monitor shromažďuje? 
Azure Monitor shromažďuje data z různých zdrojů do [protokolů](platform/data-platform-logs.md) nebo [metrik](platform/data-platform-metrics.md). Každý typ dat má své vlastní relativní výhody a každý podporuje konkrétní sadu funkcí ve službě Azure Monitor. Pro každé předplatné Azure existuje jedna databáze metrik, zatímco můžete vytvořit více pracovních prostorů Log Analytics pro shromažďování protokolů v závislosti na vašich požadavcích. Viz [Datová platforma Azure Monitor](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Existuje maximální množství dat, které můžu shromažďovat ve službě Azure Monitor?
Množství metrických dat, která můžete shromažďovat, není nijak omezeno, ale tato data jsou uložena maximálně 93 dní. Viz [Uchovávání metrik](platform/data-platform-metrics.md#retention-of-metrics). Neexistuje žádné omezení množství dat protokolu, které můžete shromažďovat, ale může být ovlivněna cenovou úrovní, kterou zvolíte pro pracovní prostor Log Analytics. Viz [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Jak se dostanu k datům shromážděným službou Azure Monitor?
Přehledy a řešení poskytují vlastní prostředí pro práci s daty uloženými v Azure Monitoru. Můžete pracovat přímo s daty protokolu pomocí dotazu protokolu napsaného v Kusto dotazovací jazyk (KQL). Na webu Azure Portal můžete psát a spouštět dotazy a interaktivně analyzovat data pomocí analýzy protokolů. Analyzujte metriky na webu Azure Portal pomocí Průzkumníka metrik. Viz [Analýza dat protokolu v Azure Monitora](log-query/log-query-overview.md) a [Začínáme s Průzkumníkem metrik Azure](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Řešení a postřehy

### <a name="what-is-an-insight-in-azure-monitor"></a>Co je přehled ve Službě Azure Monitor?
Přehledy poskytují přizpůsobené možnosti monitorování pro konkrétní služby Azure. Používají stejné metriky a protokoly jako ostatní funkce v Azure Monitoru, ale můžou shromažďovat další data a poskytovat jedinečné prostředí na webu Azure Portal. Viz [Přehledy v Azure Monitoru](insights/insights-overview.md).

Přehledy na webu Azure Portal najdete v části **Přehledy** v nabídce **Monitorování** nebo v části **Monitorování** v nabídce služby.

### <a name="what-is-a-solution-in-azure-monitor"></a>Co je řešení ve službě Azure Monitor?
Řešení monitorování jsou zabalené sady logiky pro monitorování konkrétní aplikace nebo služby založené na funkcích Azure Monitoru. Shromažďují data protokolu ve službě Azure Monitor a poskytují dotazy protokolu a zobrazení pro jejich analýzu pomocí společného prostředí na webu Azure Portal. Viz [Řešení monitorování ve službě Azure Monitor](insights/solutions.md).

Pokud chcete zobrazit řešení na webu Azure Portal, klikněte v části **Přehledy** v nabídce **Monitor** na **Další.** Kliknutím na **Přidat** přidejte do pracovního prostoru další řešení.

## <a name="logs"></a>Protokoly

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Jaký je rozdíl mezi protokoly monitorování Azure a Průzkumníkem dat Azure?
Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Protokoly monitorování Azure je postavené na Azure Data Explorer a používá stejný Kusto dotazovací jazyk (KQL) s některé drobné rozdíly. Viz [Rozdíly v dotazu na protokol Azure Monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Jak načtem data protokolu?
Všechna data se načítají z pracovního prostoru Analýzy protokolů pomocí dotazu protokolu napsaného pomocí dotazovacího jazyka Kusto (KQL). Můžete napsat vlastní dotazy nebo použít řešení a přehledy, které zahrnují dotazy protokolu pro konkrétní aplikaci nebo službu. Viz [Přehled dotazů protokolu v Azure Monitoru](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Co je pracovní prostor služby Log Analytics?
Všechna data protokolu shromážděná službou Azure Monitor se ukládají v pracovním prostoru Log Analytics. Pracovní prostor je v podstatě kontejner, kde jsou data protokolu shromažďována z různých zdrojů. Můžete mít jeden pracovní prostor Log Analytics pro všechna data monitorování nebo může mít požadavky na více pracovních prostorů. Viz [Návrh nasazení protokolů azure monitoru](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Můžete přesunout existující pracovní prostor Log Analytics do jiného předplatného Azure?
Pracovní prostor můžete přesunout mezi skupinami prostředků nebo předplatnými, ale ne do jiné oblasti. Viz [Přesunutí pracovního prostoru Analýzy protokolů do jiného předplatného nebo skupiny prostředků](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Proč se v Log Analytics nezobrazují tlačítka Průzkumník a Uložit?

**Explorer dotazů**, **tlačítka** pro uložení a **nové pravidlo výstrah** nejsou k dispozici, pokud je obor [dotazu](log-query/scope.md) nastaven na určitý prostředek. Chcete-li vytvořit výstrahy, uložit nebo načíst dotaz, musí být analýza protokolů vymezena do pracovního prostoru. Pokud chcete otevřít Analýzu protokolů v kontextu pracovního prostoru, vyberte **protokoly** z nabídky **Azure Monitor.** Je vybrán poslední použitý pracovní prostor, ale můžete vybrat jakýkoli jiný pracovní prostor. Viz [Rozsah a časový rozsah protokolu protokolu v Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Proč se mi zobrazuje chyba: "Zaregistrovat poskytovatele prostředků Microsoft.Insights' pro toto předplatné povolit tento dotaz" při otevírání Log Analytics z virtuálního provozu? 
Mnoho poskytovatelů prostředků je automaticky zaregistrováno, ale možná budete muset ručně zaregistrovat některé zprostředkovatele prostředků. Obor pro registraci je vždy předplatné. Další informace najdete v tématu [Poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Proč se při otevírání analýzy protokolů z virtuálního virtuálního provozu zobrazuje žádná chybová zpráva o přístupu? 
Chcete-li zobrazit protokoly virtuálních aplikací, musíte být uděleno s oprávněním ke čtení pracovních prostorů, které ukládají protokoly virtuálních aplikací. V těchto případech vám musí správce udělit oprávnění v Azure.

## <a name="alerts"></a>Výstrahy

### <a name="what-is-an-alert-in-azure-monitor"></a>Co je výstraha ve službě Azure Monitor?
Výstrahy vás aktivně upozorní, když jsou v datech monitorování nalezeny důležité podmínky. Umožňují identifikovat a řešit problémy dříve, než si jich uživatelé systému všimnou. Existuje několik druhů výstrah:

- Metrika - hodnota metriky překračuje prahovou hodnotu.
- Dotaz protokolu - Výsledky protokolu dotazu odpovídají definovaným kritériím.
- Protokol aktivit - Událost protokolu aktivit odpovídá definovaným kritériím.
- Webový test - Výsledky testu dostupnosti splňují definovaná kritéria.


Podívejte se [na přehled výstrah v Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Co je akční skupina?
Skupina akcí je kolekce oznámení a akcí, které mohou být spuštěny výstrahou. Více výstrah může používat jednu skupinu akcí, která umožňuje využívat běžné sady oznámení a akcí. Viz [Vytvoření a správa skupin akcí na webu Azure Portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Co je pravidlo akce?
Pravidlo akce umožňuje upravit chování sady výstrah, které odpovídají určitým kritériím. To umožňuje provádět takové požadavky, jako je zakázat akce výstrah během okna údržby. Skupinu akcí můžete také použít na sadu výstrah, nikoli je použít přímo na pravidla výstrah. Viz [Pravidla akce](platform/alerts-action-rules.md).

## <a name="agents"></a>Agenti

### <a name="does-azure-monitor-require-an-agent"></a>Vyžaduje Azure Monitor agenta?
Agent je povinen pouze shromažďovat data z operačního systému a úlohy ve virtuálních počítačích. Virtuální počítače se můžou nacházet v Azure, v jiném cloudovém prostředí nebo místně. Viz [Přehled agentů Azure Monitoru](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Jaký je rozdíl mezi agenty Azure Monitoru?
Rozšíření Azure Diagnostic je pro virtuální počítače Azure a shromažďuje data do metrik Azure Monitor, Azure Storage a Azure Event Hubs. Agent Log Analytics je určen pro virtuální počítače v Azure, v jiném cloudovém prostředí nebo v místním prostředí a shromažďuje data do protokolů monitorování Azure. Agent závislostí vyžaduje agenta Analýzy protokolů a shromážděné podrobnosti procesu a závislosti. Viz [Přehled agentů Azure Monitoru](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Používá provoz agenta připojení ExpressRoute?
Provoz na Azure Monitor používá okruh Microsoft peering ExpressRoute. Popis různých typů provozu ExpressRoute naleznete v dokumentaci k [expresní](../expressroute/expressroute-faqs.md#supported-services) silnici. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Jak můžu potvrdit, že agent Analýzy protokolů je schopný komunikovat s Azure Monitorem?
V ovládacích panelech v počítači agenta vyberte **položku Zabezpečení & Nastavení**, **Microsoft Monitoring Agent** . Na kartě **Azure Log Analytics (OMS)** zelená zaškrtnutí potvrzuje, že agent je schopen komunikovat s Azure Monitor. Žlutá výstražná ikona znamená, že agent má problémy. Jedním z běžných důvodů je služba **Microsoft Monitoring Agent** byla zastavena. K restartování služby použijte správce řízení služeb.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Jak zastavím agenta Analýzy protokolů v komunikaci s Azure Monitorem?
Agenti připojení k log analytics přímo otevřete Ovládací panely a vyberte **nastavení & zabezpečení**, Microsoft Monitoring **Agent**. Na kartě **Azure Log Analytics (OMS)** odeberte všechny pracovní prostory uvedené. V nástroji System Center Operations Manager odeberte počítač ze seznamu počítačů spravovaných službou Log Analytics. Operations Manager aktualizuje konfiguraci agenta, aby již sestavy Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Kolik dat je odesláno na agenta?
Množství dat odeslaných na agenta závisí na:

* Řešení, která jste povolili
* Počet shromažďovaných protokolů a čítačů výkonu
* Objem dat v protokolech

Podrobnosti [najdete v tématu Správa využití a nákladů pomocí protokolů monitorování Azure.](platform/manage-cost-storage.md)

U počítačů, které mohou spustit agenta WireData, můžete pomocí následujícího dotazu zjistit, kolik dat se odesílá:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Kolik šířky pásma sítě používá Microsoft Management Agent (MMA) při odesílání dat do Azure Monitoru?
Šířka pásma je funkce na množství odeslaných dat. Data jsou komprimována při jejich odeslání po síti.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Jak mohu být upozorněni, když se zastaví shromažďování dat od agenta Log Analytics?

Pomocí kroků popsaných v [vytvoření nové výstrahy protokolu,](platform/alerts-metric.md) která má být upozorněna při zastavení shromažďování dat. Pro pravidlo výstrahy použijte následující nastavení:

- **Definujte podmínku výstrahy**: Zadejte pracovní prostor Analýzy protokolů jako cíl prostředku.
- **Kritéria výstrah** 
   - **Název signálu**: *Vlastní hledání protokolu*
   - **Vyhledávací dotaz**:`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logika výstrahy**: **Na základě** *počtu výsledků*, **podmínka** *větší než*, **prahová hodnota** *0*
   - **Hodnoceno na základě**: **Období (v minutách)** *30*, **Frekvence (v minutách)** *10*
- **Definice podrobností o upozornění** 
   - **Název**: *Sběr dat byl zastaven.*
   - **Závažnost :** *Upozornění*

Zadejte existující nebo novou [skupinu akcí,](platform/action-groups.md) aby se výstraha protokolu shodla s kritérii, budete upozorněni, pokud prezenční signál chybí déle než 15 minut.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Jaké jsou požadavky brány firewall pro agenty Azure Monitoru?
Podrobnosti o požadavcích na bránu firewall naleznete v tématu Požadavky na [síťovou bránu](platform/log-analytics-agent.md#network-requirements)firewall.


## <a name="visualizations"></a>Vizualizace

### <a name="why-cant-i-see-view-designer"></a>Proč nevidím Návrhář zobrazení?

Návrhář zobrazení je k dispozici pouze pro uživatele přiřazené s oprávněními přispěvatele nebo vyšší v pracovním prostoru Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problémy s konfigurací
*Mám potíže s nastavením:*

* [Aplikace .NET](app/asp-net-troubleshoot-no-data.md)
* [Sledování již spuštěné aplikace](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostika Azure](platform/diagnostics-extension-to-application-insights.md)
* [Webová aplikace Java](app/java-troubleshoot.md)

*Nedostávám ze serveru žádná data*

* [Nastavení výjimek brány firewall](app/ip-addresses.md)
* [Nastavení ASP.NET serveru](app/monitor-performance-live-website-now.md)
* [Nastavení serveru Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Mohu použít Application Insights s ...?

* [Webové aplikace na serveru IIS v sadě škálování virtuálních virtuálních zařízení Azure nebo virtuálního počítače Azure](app/azure-vm-vmss-apps.md)
* [Webové aplikace na serveru Služby IIS – místní nebo ve virtuálním počítači](app/asp-net.md)
* [Webové aplikace v Javě](app/java-get-started.md)
* [Aplikace v Node.js](app/nodejs.md)
* [Webové aplikace v Azure](app/azure-web-apps.md)
* [Cloudové služby v Azure](app/cloudservices.md)
* [Servery aplikací spuštěné v Dockeru](app/docker.md)
* [Jednostránkové webové aplikace](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Desktopová aplikace pro Windows](app/windows-desktop.md)
* [Jiné platformy](app/platforms.md)

### <a name="is-it-free"></a>Je to zdarma?

Ano, pro experimentální použití. V základním cenovém plánu může vaše aplikace každý měsíc bezplatně posílat určitý příspěvek na údaje. Bezplatný příspěvek je dostatečně velký, aby pokryl vývoj a publikování aplikace pro malý počet uživatelů. Můžete nastavit omezení, které zabrání zpracování více než zadaného množství dat.

Větší objemy telemetrie jsou účtovány Gb. Poskytujeme několik tipů, jak [omezit své poplatky](app/pricing.md).

Plán Enterprise účtuje poplatek za každý den, kdy každý uzel webového serveru odesílá telemetrii. Je vhodný, pokud chcete použít nepřetržitý export ve velkém měřítku.

[Přečtěte si cenový plán](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Kolik to stojí?

* Otevřete **stránku Využití a odhadované náklady** v prostředku Application Insights. Je tu graf nedávného využití. Pokud chcete, můžete nastavit limit objemu dat.
* Otevřete [okno Fakturace Azure a](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) podívejte se na své účty napříč všemi prostředky.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Co application insights upravit v mém projektu?
Podrobnosti závisí na typu projektu. Pro webovou aplikaci:

* Přidá tyto soubory do projektu:
  * ApplicationInsights.config
  * ai.js
* Nainstaluje tyto balíčky NuGet:
  * *Api pro přehledy aplikací* – základní rozhraní API
  * *Rozhraní API Application Insights pro webové aplikace* – používá se k odesílání telemetrie ze serveru
  * *Application Insights API pro JavaScript ové aplikace* – používá se k odesílání telemetrie z klienta
* Balíčky obsahují tato sestavení:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Vloží položky do:
  * Soubor web.config
  * packages.config
* (Pouze nové projekty – pokud [přidáte Application Insights do existujícího projektu][start], budete muset provést ručně.) Vloží výstřižky do kódu klienta a serveru, aby je inicializovali pomocí ID prostředku Application Insights. Například v aplikaci MVC je kód vložen do stránky\_předlohy Zobrazení/Sdílené/ Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak lze upgradovat ze starších verzí sady SDK?
Podívejte se na [poznámky k verzi](app/release-notes.md) sady SDK odpovídající typu aplikace.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Jak můžu změnit, kterému prostředkům Azure můj projekt odesílá data?
V Průzkumníku řešení `ApplicationInsights.config` klepněte pravým tlačítkem myši a zvolte **Aktualizovat přehledy aplikací**. Data můžete odeslat do existujícího nebo nového prostředku v Azure. Průvodce aktualizací změní klíč instrumentace v souboru ApplicationInsights.config, který určuje, kam sada SDK serveru odesílá vaše data. Pokud nezrušíte zaškrtnutí políčka Aktualizovat vše, změní se také klíč, ve kterém se zobrazuje na vašich webových stránkách.

### <a name="what-is-status-monitor"></a>Co je Monitorování stavu?

Desktopová aplikace, kterou můžete použít na webovém serveru Služby IIS a nakonfigurovat přehledy aplikací ve webových aplikacích. Neshromažďuje telemetrii: můžete ji zastavit, když nekonfigurujete aplikaci. 

[Další informace](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Jakou telemetrická data jsou shromažďována pomocí Application Insights?

Ze serverových webových aplikací:

* Požadavky HTTP
* [Závislosti](app/asp-net-dependencies.md). Volání: DATABÁZE SQL; Volání HTTP externích služeb; Azure Cosmos DB, tabulka, úložiště objektů blob a fronty. 
* [Výjimky](app/asp-net-exceptions.md) a trasování zásobníku.
* [Čítače výkonu](app/performance-counters.md) – pokud používáte [sledování stavu](app/monitor-performance-live-website-now.md), monitorování Azure [pro služby App Services](app/azure-web-apps.md), monitorování Azure pro [škálovací sadu virtuálních počítačů nebo škálovací sadu virtuálních strojů](app/azure-vm-vmss-apps.md)nebo [přehledy aplikací nasbíraného zapisovače](app/java-collectd.md).
* [Vlastní události a metriky,](app/api-custom-events-metrics.md) které kódujete.
* [Trasování protokoly,](app/asp-net-trace-logs.md) pokud nakonfigurujete příslušný kolektor.

Z [webových stránek klienta](app/javascript.md):

* [Počty zobrazení stránek](app/usage-overview.md)
* [VOLÁNÍ AJAX](app/asp-net-dependencies.md) Požadavky ze spuštěného skriptu.
* Načte data zobrazení stránky
* Počty uživatelů a relací
* [Ověřená ID uživatelů](app/api-custom-events-metrics.md#authenticated-users)

Z jiných zdrojů, pokud je nakonfigurujete:

* [Diagnostika Azure](platform/diagnostics-extension-to-application-insights.md)
* [Import do služby Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Můžu odfiltrovat nebo upravit nějakou telemetrii?

Ano, na serveru můžete napsat:

* Telemetrie Procesor filtrovat nebo přidat vlastnosti vybrané položky telemetrie před jejich odesláním z vaší aplikace.
* Telemetrie Inicializátor přidat vlastnosti pro všechny položky telemetrie.

Další informace o [ASP.NET](app/api-filtering-sampling.md) nebo [Javě](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Jak se počítají údaje o městě, zemi nebo oblasti a další geografické poloze?

Vyhledáme IP adresu (IPv4 nebo IPv6) webového klienta pomocí [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetrie prohlížeče: Shromažďujeme IP adresu odesílatele.
* Telemetrie serveru: Modul Application Insights shromažďuje IP adresu klienta. Není shromažďována, `X-Forwarded-For` pokud je nastavena.
* Další informace o tom, jak se shromažďují ip adresy a geolokační data v Application Insights, najdete v tomto [článku](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Můžete nakonfigurovat `ClientIpHeaderTelemetryInitializer` tak, aby převzala ip adresu z jiné hlavičky. V některých systémech je například přesunuta pomocí proxy serveru, `X-Originating-IP`vyrovnávání zatížení nebo cdn na . [Další informace](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Pomocí [Power BI](app/export-power-bi.md ) můžete zobrazit telemetrii požadavků na mapě.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Jak dlouho jsou data na portálu uchovávána? Je to bezpečné?
Podívejte se na [uchovávání dat a ochranu osobních údajů][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Co se stane s telemetrií Application Insight, když server nebo zařízení ztratí spojení s Azure?

Všechny naše sady SDK, včetně webové sady SDK, zahrnují "spolehlivou přepravu" nebo "robustní přepravu". Když server nebo zařízení ztratí připojení k Azure, telemetrie se [uloží místně v systému souborů](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server SDK) nebo v úložišti relací HTML5 (Web SDK). Sada SDK se bude pravidelně pokoušet odeslat tuto telemetrii, dokud ji naše služba ingestování nepovažuje za "zastaralou" (48 hodin pro protokoly, 30 minut pro metriky). Zatuchlá telemetrie bude vynechána. V některých případech, například při zaplnici místního úložiště, nedojde k opakování.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Mohly by být osobní údaje odesílány v telemetrii?

To je možné, pokud váš kód odešle taková data. Může se také stát, pokud proměnné v trasování zásobníku zahrnují osobní údaje. Váš vývojový tým by měl provádět hodnocení rizik, aby bylo zajištěno, že s osobními údaji je řádně nakládáno. [Přečtěte si další informace o uchovávání dat a ochraně osobních údajů](app/data-retention-privacy.md).

**Všechny** oktety webové adresy klienta jsou vždy nastaveny na 0 po geografické umístění atributy jsou vyhledány.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Můj instrumentační klíč je viditelný ve zdroji webové stránky. 

* To je běžná praxe v monitorování řešení.
* Nemůže být použit a ukrást vaše data.
* Může být použit ke zkreslení dat nebo aktivaci výstrah.
* Neslyšeli jsme, že by nějaký zákazník měl takové problémy.

Mohli byste:

* Pro data klienta a serveru použijte dva samostatné klíče instrumentace (samostatné prostředky Application Insights). Nebo
* Napište proxy server, který běží na serveru, a nechat webového klienta odesílat data prostřednictvím tohoto proxy serveru.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Jak se v diagnostickém vyhledávání zobrazí data POST?
Nezaznamenáváme data POST automaticky, ale můžete použít tracktrace volání: vložte data do parametru zprávy. To má delší omezení velikosti než omezení vlastností řetězce, i když nelze filtrovat na to.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Mám použít jeden nebo více prostředků Application Insights?

Použijte jeden prostředek pro všechny součásti nebo role v jednom obchodním systému. Použijte samostatné prostředky pro vývoj, testování a vydání verze a pro nezávislé aplikace.

* [Podívejte se na diskusi zde](app/separate-resources.md)
* [Příklad – cloudová služba s rolemi pracovníků a webu](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak dynamicky změním klíč instrumentace?

* [Diskuse zde](app/separate-resources.md)
* [Příklad – cloudová služba s rolemi pracovníků a webu](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Jaké jsou počty uživatelů a relací?

* Sada JavaScript SDK nastaví soubor cookie uživatele ve webovém klientovi, aby identifikovalvracející uživatele, a soubor cookie relace pro seskupení aktivit.
* Pokud není k dispozici skript na straně klienta, můžete [nastavit soubory cookie na serveru](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Pokud jeden skutečný uživatel používá vaše stránky v různých prohlížečích, nebo pomocí in-private /incognito prohlížení, nebo různé stroje, pak budou započítány více než jednou.
* Chcete-li identifikovat přihlášeného uživatele napříč počítači a prohlížeči, přidejte volání [do setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a>Povolil jsem všechno v Application Insights?
| Co byste měli vidět | Jak ji získat | Proč to chcete |
| --- | --- | --- |
| Grafy dostupnosti |[Webové testy](app/monitor-web-app-availability.md) |Vědět, že je vaše webová aplikace nahoře |
| Server app perf: doba odezvy, ... |[Přidání přehledů aplikací do projektu](app/asp-net.md) nebo [instalace sledování stavu AI na server](app/monitor-performance-live-website-now.md) (nebo napište vlastní kód pro sledování [závislostí)](app/api-custom-events-metrics.md#trackdependency) |Detekce problémů s perf |
| Telemetrie závislostí |[Instalace sledování stavu AI na server](app/monitor-performance-live-website-now.md) |Diagnostika problémů s databázemi nebo jinými externími součástmi |
| Získání trasování zásobníku z výjimek |[Vložit volání TrackException do kódu](app/asp-net-exceptions.md) (ale některé jsou hlášeny automaticky) |Zjišťování a diagnostika výjimek |
| Hledat trasování protokolu |[Přidání adaptéru pro protokolování](app/asp-net-trace-logs.md) |Diagnostikovat výjimky, problémy s perf |
| Základy používání klienta: zobrazení stránek, relace, ... |[JavaScript inicializátor na webových stránkách](app/javascript.md) |Analýza využití |
| Vlastní metriky klienta |[Sledování hovorů na webových stránkách](app/api-custom-events-metrics.md) |Zlepšete uživatelské prostředí |
| Vlastní metriky serveru |[Sledování hovorů na serveru](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Proč jsou počty v grafech Vyhledávání a Metriky nerovné?

[Vzorkování](app/sampling.md) snižuje počet položek telemetrie (požadavky, vlastní události a tak dále), které jsou ve skutečnosti odeslány z vaší aplikace na portál. Ve vyhledávání se zobrazí počet skutečně přijatých položek. V grafech metrik, které zobrazují počet událostí, se zobrazí počet původních událostí, ke kterým došlo. 

Každá položka, která `itemCount` je přenášena nese vlastnost, která ukazuje, kolik původní události, které tato položka představuje. Chcete-li sledovat vzorkování v provozu, můžete spustit tento dotaz v Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Konfigurace přehledů aplikací

[Skripty Prostředí PowerShell](app/powershell.md) můžete psát pomocí Azure Resource Monitor:

* Vytvořte a aktualizujte prostředky Application Insights.
* Nastavte cenový plán.
* Získejte klíč k přístroji.
* Přidejte upozornění na metriku.
* Přidejte test dostupnosti.

Nemůžete nastavit přehled Průzkumníkmetrik ani nastavit nepřetržitý export.

#### <a name="querying-the-telemetry"></a>Dotazování na telemetrii

Ke spouštění dotazů [analytics](app/analytics.md) použijte [rozhraní REST API.](https://dev.applicationinsights.io/)

### <a name="how-can-i-set-an-alert-on-an-event"></a>Jak mohu nastavit upozornění na událost?

Azure výstrahy jsou jenom na metriky. Vytvořte vlastní metriku, která překročí prahovou hodnotu vždy, když dojde k události. Pak nastavte upozornění na metriku. Oznámení dostanete vždy, když metrika překročí prahovou hodnotu v obou směrech. nebudete dostávat oznámení až do prvního křížení, bez ohledu na to, zda je počáteční hodnota vysoká nebo nízká; vždy existuje latence několika minut.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Existují poplatky za přenos dat mezi webovou aplikací Azure a Application Insights?

* Pokud je vaše webová aplikace Azure hostovaná v datovém centru, kde je koncový bod kolekce Application Insights, je to zdarma. 
* Pokud v hostitelském datovém centru není žádný koncový bod kolekce, bude telemetrická data vaší aplikace účtovány [odchozí poplatky Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

To nezávisí na tom, kde je váš prostředek Application Insights hostován. Záleží jen na rozložení našich koncových bodů.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Můžu poslat telemetrii na portál Application Insights?

Doporučujeme používat naše sady SDK a používat [rozhraní SDK API](app/api-custom-events-metrics.md). Existují varianty SDK pro různé [platformy](app/platforms.md). Tyto sady SDK zpracovávají ukládání do vyrovnávací paměti, komprese, omezení, opakování a tak dále. Schéma [ingestování](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) a [protokol koncového bodu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) jsou však veřejné.

### <a name="can-i-monitor-an-intranet-web-server"></a>Mohu sledovat intranetový webový server?

Ano, ale budete muset povolit provoz našich služeb buď výjimkou brány firewall, nebo přesměrováním proxy serveru.
- QuickPulse`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- Telemetrické kanály`https://dc.services.visualstudio.com:443` 


Prohlédněte si náš úplný seznam služeb a IP adres [zde](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Výjimka brány firewall

Umožněte webovému serveru odesílat telemetrická data do našich koncových bodů. 

#### <a name="gateway-redirect"></a>Přesměrování brány

Směrovat provoz ze serveru do brány v intranetu přepsáním koncových bodů ve vaší konfiguraci. Pokud tyto vlastnosti "Koncový bod" nejsou k dispozici v konfiguraci, tyto třídy budou používat výchozí hodnoty uvedené níže v příkladu ApplicationInsights.config. 

Vaše brána by měla směrovat provoz na základní adresu našeho koncového bodu. V konfiguraci nahraďte `http://<your.gateway.address>/<relative path>`výchozí hodnoty písmenem .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Příklad applicationinsights.config s výchozími koncovými body:
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
> ApplicationIdProvider je k dispozici od 2.6.0.



#### <a name="proxy-passthrough"></a>Průchod proxy serveru

Průchod proxy lze dosáhnout konfigurací úrovně počítače nebo proxy úrovně aplikace.
Další informace naleznete v článku společnosti Dotnet o [defaultproxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Příklad web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Mohu spustit webové testy dostupnosti na intranetovém serveru?

Naše [webové testy](app/monitor-web-app-availability.md) běží na místech přítomnosti, které jsou distribuovány po celém světě. Existují dvě řešení:

* Firewall dveře - Povolit požadavky na váš server z [dlouhého a proměnlivého seznamu webových testovacích agentů](app/ip-addresses.md).
* Napište vlastní kód pro odesílání pravidelných požadavků na server z intranetu. Pro tento účel můžete spustit webové testy sady Visual Studio. Tester může odeslat výsledky Application Insights pomocí TrackAvailability() ROZHRANÍ API.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak dlouho trvá, než se telemetrie shromáždí?

Většina dat Application Insights má latenci pod 5 minut. Některá data mohou trvat déle; obvykle větší soubory protokolu. Další informace naleznete v sla [s la přehledy aplikací](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery

Tento nejčastější dotazy k Microsoftu je seznam běžně kladených otázek týkajících se Azure Monitoru pro kontejnery. Máte-li jakékoli další dotazy týkající se řešení, přejděte do [diskusního fóra](https://feedback.azure.com/forums/34192--general-feedback) a pošlete své dotazy. Když je otázka často kladena, přidáme ji do tohoto článku, aby ji bylo možné rychle a snadno najít.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Co představují *jiné procesy* v zobrazení Uzel?

**Jiné procesy** jsou určeny k tomu, aby vám pomohly jasně pochopit hlavní příčinu vysokého využití prostředků v uzlu. To umožňuje rozlišovat využití mezi kontejnerizované procesy vs nekontejnerizované procesy.

Jaké jsou tyto **další procesy?** 

Jedná se o nekontejnerizované procesy, které běží na vašem uzlu.  

Jak to spočítáme?

**Ostatní procesy** = Celkové využití z využití*CAdvisor* - *z kontejnerizovaného procesu*

**Ostatní procesy** zahrnují:

- Samořízené nebo spravované nekontejnerizované procesy Kubernetes 

- Procesy za běhu kontejneru  

- Kubelet  

- Systémové procesy spuštěné na vašem uzlu 

- Jiné úlohy bez Kubernetes spuštěné na hardwaru uzlu nebo virtuálním počítači 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Při dotazování na tabulku ContainerLog se nezobrazují hodnoty vlastností Image and Name.

Pro verzi agenta ciprod12042019 a novější ve výchozím nastavení tyto dvě vlastnosti nejsou naplněny pro každý řádek protokolu minimalizovat náklady vzniklé na data protokolu shromážděny. Existují dvě možnosti dotazu na tabulku, které obsahují tyto vlastnosti s jejich hodnotami:

#### <a name="option-1"></a>Možnost 1 

Připojte se k dalším tabulkám a zahrňte tyto hodnoty vlastností do výsledků.

Upravte dotazy tak, aby zahrnovaly ```ContainerInventory``` vlastnosti Image a ImageTag z tabulky spojením na vlastnosti ContainerID. Můžete zahrnout Name vlastnost (jak se ```ContainerLog``` dříve objevila v tabulce) z tabulky KubepodInventory je ContaineName pole spojením na ContainerID vlastnost. Toto je doporučená možnost.

Následující příklad je ukázkový podrobný dotaz, který vysvětluje, jak získat tyto hodnoty polí se spojeními.

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

#### <a name="option-2"></a>2. možnost

Znovu povolte kolekci pro tyto vlastnosti pro každý řádek protokolu kontejneru.

Pokud první možnost není vhodná z důvodu změn dotazu, můžete znovu ```log_collection_settings.enrich_container_logs``` povolit shromažďování těchto polí povolením nastavení v mapě konfigurace agenta, jak je popsáno v [nastavení konfigurace shromažďování dat](insights/container-insights-agent-config.md).

> [!NOTE]
> Druhá možnost se nedoporučuje u velkých clusterů, které mají více než 50 uzlů, protože generuje volání serveru rozhraní API ze všech uzlů v clusteru k provedení tohoto obohacení. Tato možnost také zvyšuje velikost dat pro každý shromažďované řádek protokolu.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Mohu zobrazit metriky shromážděné v Grafaně?

Azure Monitor pro kontejnery podporuje zobrazení metriky uložené v pracovním prostoru Log Analytics v řídicích panelech Grafana. Poskytli jsme šablonu, kterou si můžete stáhnout z [úložiště řídicích panelů](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) grafany, abyste mohli začít a odkazovat, abyste se naučili, jak zadat další data ze sledovaných clusterů, abyste je mohli vizualizovat ve vlastních řídicích panelech Grafana. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Můžu sledovat svůj cluster modulu AKS pomocí Azure Monitoru pro kontejnery?

Azure Monitor pro kontejnery podporuje monitorování úloh kontejnerů nasazených do clusteru AKS-engine (dříve označované jako clustery modulu ACS) hostované v Azure. Další podrobnosti a přehled kroků potřebných k povolení monitorování pro tento scénář najdete [v tématu použití Azure Monitor pro kontejnery pro AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Proč se v pracovním prostoru Log Analytics nezobrazují data?

Pokud se vám nedaří zobrazit žádná data v pracovním prostoru Log Analytics v určitý čas každý den, pravděpodobně jste dosáhli výchozí limit 500 MB nebo denní limit určený k řízení množství dat, které mají být denně shromažďovány. Pokud je splněn limit pro den, shromažďování dat se zastaví a obnoví pouze následující den. Informace o využití dat a aktualizaci na jinou cenovou úroveň na základě očekávaných vzorců využití naleznete v [tématu Protokolování využití dat a nákladů](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jaké jsou stavy kontejnerů zadané v tabulce ContainerInventory?

Tabulka ContainerInventory obsahuje informace o zastavené i spuštěné kontejnery. Tabulka je naplněna pracovním postupem uvnitř agenta, který se dotazuje dockeru na všechny kontejnery (spuštěné a zastavené) a předává tato data pracovnímu prostoru Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Jak vyřeším chybějící chybu *registrace předplatného?*

Pokud se zobrazí chyba **Chybějící registrace předplatného pro Microsoft.OperationsManagement**, můžete ji vyřešit registrací poskytovatele prostředků **Microsoft.OperationsManagement** v předplatném, kde je definován pracovní prostor. Dokumentaci, jak to udělat, naleznete [zde](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Existuje podpora clusterů AKS povolených rbacem?

Řešení monitorování kontejnerů nepodporuje RBAC, ale je podporované pomocí Azure Monitor pro kontejnery. Stránka podrobností o řešení nemusí zobrazit správné informace v okně, které zobrazují data pro tyto clustery.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak povolím shromažďování protokolů pro kontejnery v oboru názvů kube systému prostřednictvím helmu?

Kolekce protokolu z kontejnerů v oboru názvů systému kube je ve výchozím nastavení zakázána. Kolekce protokolu může být povolena nastavením proměnné prostředí na omsagent. Další informace najdete na stránce [Azure Monitor pro kontejnery](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Jak lze aktualizovat omsagent na nejnovější vydanou verzi?

Informace o tom, jak inovovat agenta, naleznete v [tématu Správa agenta](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Jak povolím víceřádkové protokolování?

V současné době Azure Monitor pro kontejnery nepodporuje víceřádkové protokolování, ale jsou k dispozici řešení. Můžete nakonfigurovat všechny služby pro zápis ve formátu JSON a pak Docker/Moby bude psát jako jeden řádek.

Můžete například zabalit protokol jako objekt JSON, jak je znázorněno v příkladu níže pro ukázkovou aplikaci node.js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Tato data budou vypadat jako následující příklad v Azure Monitor u protokolů při dotazu na ně:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Podrobný přehled na problém najděte na následujícím [odkazu GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak vyřeším chyby Azure AD, když povolím živé protokoly? 

Může se zobrazit následující chyba: **Adresa URL odpovědi zadaná v požadavku neodpovídá adresám\>URL odpovědi nakonfigurovaným pro aplikaci: '<ID aplikace '**. Řešení k jeho řešení lze nalézt v článku [Jak zobrazit data kontejneru v reálném čase pomocí Azure Monitor pro kontejnery](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Proč nemohu po zaškolení upgradovat cluster?

Pokud po povolení Azure Monitor pro kontejnery pro cluster AKS, odstraníte pracovní prostor Log Analytics clusteru odesílal svá data, při pokusu o upgrade clusteru se nezdaří. Chcete-li tento obor vyřešit, budete muset zakázat monitorování a znovu povolit odkazování na jiný platný pracovní prostor ve vašem předplatném. Při pokusu o provedení upgradu clusteru znovu, by měl zpracovat a úspěšně dokončit.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Které porty a domény je třeba otevřít/whitelist pro agenta?

Podívejte se na [požadavky síťové brány firewall](insights/container-insights-onboard.md#network-firewall-requirements) pro informace o konfiguraci proxy a brány firewall požadované pro kontejnerizovaného agenta s cloudy Azure, Azure US Government a Azure China 21Vianet.

## <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
Tento nejčastější dotazy k Microsoftu je seznam často kladených otázek týkajících se Azure Monitoru pro virtuální počítače. Máte-li jakékoli další dotazy týkající se řešení, přejděte do [diskusního fóra](https://feedback.azure.com/forums/34192--general-feedback) a pošlete své dotazy. Když je otázka často kladena, přidáme ji do tohoto článku, aby ji bylo možné rychle a snadno najít.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Mohu být na palubě do existujícího pracovního prostoru?
Pokud jsou vaše virtuální počítače už připojené k pracovnímu prostoru Log Analytics, můžete tento pracovní prostor používat i při přihlašování k Azure Monitoru pro virtuální počítače za předpokladu, že se nachází v jedné z podporovaných oblastí uvedených [zde](insights/vminsights-enable-overview.md#prerequisites).


### <a name="can-i-onboard-to-a-new-workspace"></a>Mohu se nalodit na nový pracovní prostor? 
Pokud vaše virtuální počítače nejsou aktuálně připojené k existujícímu pracovnímu prostoru Log Analytics, musíte vytvořit nový pracovní prostor pro ukládání dat. Vytvoření nového výchozího pracovního prostoru se provádí automaticky, pokud nakonfigurujete jeden virtuální počítač Azure pro Azure Monitor pro virtuální počítače prostřednictvím portálu Azure.

Pokud se rozhodnete použít metodu založenou na skriptech, tyto kroky jsou popsané v [článku povolit Azure Monitor pro virtuální počítače pomocí](insights/vminsights-enable-at-scale-powershell.md) azure prostředí nebo šablony Správce prostředků. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co mám dělat, když se můj virtuální virtuální ms už hlásí do existujícího pracovního prostoru?
Pokud již shromažďujete data z virtuálních počítačů, je možné, že jste je již nakonfigurovali tak, aby vykazovala data do existujícího pracovního prostoru Log Analytics.  Pokud je tento pracovní prostor v jedné z našich podporovaných oblastí, můžete povolit Azure Monitor pro virtuální počítače do tohoto již existujícího pracovního prostoru.  Pokud pracovní prostor, který už používáte, není v jedné z našich podporovaných oblastí, nebudete v tuto chvíli moct nastoupit do Azure Monitoru pro virtuální počítače.  Aktivně pracujeme na podpoře dalších regionů.


### <a name="why-did-my-vm-fail-to-onboard"></a>Proč se mému virtuálnímu počítači nepodařilo nastoupit?
Při registraci virtuálního počítače Azure z webu Azure dojde k následujícím krokům:

* Pokud byla tato možnost vybrána, vytvoří se výchozí pracovní prostor Analýzy protokolů.
* Agent Log Analytics se nainstaluje na virtuálních počítačích Azure pomocí rozšíření virtuálního počítače, pokud je určeno, že je požadováno.  
* Agent závislostí map Azure Monitor for VMs se nainstalovaný na virtuálních počítačích Azure pomocí rozšíření, pokud je určeno, že je to nutné. 

Během procesu na palubě kontrolujeme stav každého z výše uvedených, abychom vám na portálu vrátili stav oznámení. Konfigurace pracovního prostoru a instalace agenta obvykle trvá 5 až 10 minut. Zobrazení dat monitorování na portálu trvá dalších 5 až 10 minut.  

Pokud jste iniciovali zařazování a zobrazí se zprávy oznamující, že virtuální počítač musí být na palubě, vypovolte až 30 minut, než virtuální počítač dokončí proces. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>V grafech výkonu virtuálního virtuálního virtuálního míse se nezobrazují žádná data
Naše grafy výkonu byly aktualizovány tak, aby používaly data uložená v tabulce *InsightsMetrics.*  Chcete-li zobrazit data v těchto grafech, budete muset upgradovat, abyste použili nové řešení VM Insights.  Další informace naleznete v [nejčastějších dotazech](insights/vminsights-ga-release-faq.md) k nejčastějším dotazům.

Pokud v tabulce disků nebo v některých grafech výkonu nevidíte údaje o výkonu, nemusí být čítače výkonu v pracovním prostoru nakonfigurovány. Chcete-li to vyřešit, spusťte následující [skript prostředí PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Jak se funkce Azure Monitor for VMs liší od mapy služeb?
Funkce Azure Monitor for VMs Map je založená na mapě služeb, ale má následující rozdíly:

* Zobrazení mapy je přístupné z okna virtuálního počítače a z Azure Monitor pro virtuální počítače v rámci Azure Monitor.
* Na připojení v mapě lze nyní kliknout a zobrazit zobrazení dat metrik připojení v bočním panelu pro vybrané připojení.
* K dispozici je nové rozhraní API, které se používá k vytvoření mapy pro lepší podporu složitější mapy.
* Monitorované virtuální počítače jsou teď zahrnuty v uzlu skupiny klientů a graf koblihy zobrazuje podíl monitorovaných a nemonitorovaných virtuálních počítačů ve skupině.  Lze jej také filtrovat seznam počítačů při rozšíření skupiny.
* Monitorované virtuální počítače jsou nyní zahrnuty v uzlech skupiny portů serveru a graf donut ů zobrazuje podíl monitorovaných a nemonitorovaných počítačů ve skupině.  Lze jej také filtrovat seznam počítačů při rozšíření skupiny.
* Styl mapy byl aktualizován, aby byl více konzistentní s mapou aplikací z přehledů aplikací.
* Boční panely byly aktualizovány a nemají úplnou sadu integrací, které byly podporovány v mapě služeb – správa aktualizací, sledování změn, zabezpečení a služba Service Desk. 
* Možnost výběru skupin a počítačů k mapování byla aktualizována a teď podporuje předplatná, skupiny prostředků, škálovací sady virtuálních počítačů Azure a cloudové služby.
* V funkci Azure Monitor for VM Sm nelze vytvořit nové skupiny strojů Map služby.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Proč grafy výkonu zobrazují tečkované čáry?
K tomu může dojít z několika důvodů.  V případech, kdy je mezera ve sběru dat, zobrazujeme čáry jako tečkované.  Pokud jste upravili frekvenci vzorkování dat pro povolené čítače výkonu (výchozí nastavení je shromažďovat data každých 60 sekund), můžete v grafu zobrazit tečkované čáry, pokud zvolíte úzký časový rozsah pro graf a vzorkovací frekvence je menší než velikost bloku použitá v grafu (například vzorkovací frekvence je každých 10 minut a každý segment v grafu je 5 minut).  Výběr širšího časového rozsahu, který chcete zobrazit, by měl v tomto případě způsobit, že se čáry grafu zobrazí jako plné čáry, nikoli jako tečky.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Jsou podporované skupiny pomocí Azure Monitoru pro virtuální počítače?
Ano, po instalaci agenta závislostí shromažďujeme informace z virtuálních počítače k zobrazení skupin na základě předplatného, skupiny prostředků, škálovacísady virtuálních strojů a cloudových služeb.  Pokud jste používali mapu služeb a vytvořili skupiny strojů, zobrazí se také tyto skupiny.  Skupiny počítačů se také zobrazí ve filtru skupin, pokud jste je vytvořili pro prohlížený pracovní prostor. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak zobrazím podrobnosti o tom, co řídí řádek 95 percentilu v souhrnných grafech výkonu?
Ve výchozím nastavení je seznam seřazen tak, aby zobrazoval virtuální počítače, které mají nejvyšší hodnotu pro 95 percentil pro vybranou metriku, s výjimkou grafu dostupné paměti, který zobrazuje počítače s nejnižší hodnotou pátého percentilu.  Kliknutím na graf se otevře zobrazení **Seznamu N** s vybranou příslušnou metrikou.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Jak funkce Map zpracovává duplicitní IP adresy v různých virtuálních sítích a podsítích?
Pokud duplikujete rozsahy IP adres pomocí virtuálních počítačů nebo škálovacích sad virtuálních počítačů Azure napříč podsítěmi a virtuálními sítěmi, může to způsobit, že Azure Monitor pro mapy virtuálních počítačů zobrazí nesprávné informace. Jedná se o známý problém a zkoumáme možnosti, jak tuto zkušenost zlepšit.

### <a name="does-map-feature-support-ipv6"></a>Podporuje funkce Map iPv6?
Funkce mapy v současné době podporuje pouze Protokol IPv4 a zkoumáme podporu pro protokol IPv6. Podporujeme také Protokol IPv4, který je tunelován uvnitř iPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Při načtení mapy pro skupinu zdrojů nebo jinou velkou skupinu je obtížné zobrazit mapu
I když jsme provedli vylepšení map pro zpracování velkých a složitých konfigurací, uvědomujeme si, že mapa může mít mnoho uzlů, připojení a uzlu pracujících jako cluster.  Zavázali jsme se pokračovat ve zvyšování podpory pro zvýšení škálovatelnosti.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Proč síťový graf na kartě Výkon vypadá jinak než síťový graf na stránce Přehled virtuálních her Azure?

Stránka s přehledem pro virtuální počítač Azure zobrazuje grafy na základě měření aktivity hostitele v hostovaném virtuálním počítači.  Pro síťový graf v přehledu virtuálních her Azure se zobrazí jenom síťový provoz, který se bude účtovat.  To nezahrnuje přenosy mezi virtuálními sítěmi.  Data a grafy zobrazené pro Azure Monitor pro virtuální počítače jsou založeny na datech z hostovaného virtuálního počítače a síťový graf zobrazuje veškerý přenos tcp/ip, který je příchozí a odchozí do tohoto virtuálního počítače, včetně mezivirtuální sítě.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Jak se měří doba odezvy pro data uložená v připojení VMConnection a zobrazená v panelu připojení a sešitech?

Doba odezvy je aproximace. Vzhledem k tomu, že nemáme nástroj kód aplikace, nemáme opravdu vědět, kdy žádost začíná a kdy přijde odpověď. Místo toho pozorujeme data odesílaná na připojení a pak data vrací na toto připojení. Náš agent sleduje tyto odesílá a přijímá a pokouší se je spárovat: posloupnost odešle, následuje posloupnost přijímá je interpretován jako pár požadavku a odpovědi. Časování mezi těmito operacemi je doba odezvy. Bude zahrnovat latenci sítě a dobu zpracování serveru.

Tato aproximace funguje dobře pro protokoly, které jsou založeny na požadavek/odpověď: jeden požadavek zhasne na připojení a přijde jedna odpověď. To je případ HTTP(S) (bez pipeliningu), ale není spokojen pro jiné protokoly.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Jsou jejich omezení, pokud jsem na Log Analytics zdarma cenový plán?
Pokud jste nakonfigurovali Azure Monitor s pracovním prostorem Log Analytics pomocí cenové úrovně *Free,* funkce Azure Monitor for VMs Map bude podporovat jenom pět připojených počítačů připojených k pracovnímu prostoru. Pokud máte pět virtuálních počítačů připojených k volnému pracovnímu prostoru, odpojíte jeden z virtuálních počítačů a později připojíte nový virtuální počítač, nový virtuální počítač se nemonitoruje a neodráží se na stránce Mapa.  

Za této podmínky budete vyzváni pomocí **možnosti Try Now** při otevření virtuálního počítači a vyberte **přehledy** z levého podokna, a to i poté, co byl nainstalován již na virtuálním počítači.  Však nejsou vyzváni s možnostmi, jako by normálně dojít, pokud tento virtuální počítač nebyly na palubě Azure Monitor pro virtuální počítače. 


## <a name="next-steps"></a>Další kroky
Pokud zde vaše otázka není zodpovězena, můžete se obrátit na následující fóra s dalšími otázkami a odpověďmi.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Obecnou zpětnou vazbu na Azure Monitoru najdete na [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/34192--general-feedback).
