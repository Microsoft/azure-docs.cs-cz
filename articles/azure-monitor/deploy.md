---
title: Nasazení Azure Monitoru
description: V této části najdete popis různých kroků nezbytných pro kompletní implementaci Azure Monitor pro monitorování všech prostředků ve vašem předplatném Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: c37693bc6c9ce1cc5fed6c06ecb7fe628c315176
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573577"
---
# <a name="deploy-azure-monitor"></a>Nasazení Azure Monitoru
Povolením Azure Monitor monitorování všech vašich prostředků Azure je kombinace konfigurace Azure Monitor komponent a konfigurace prostředků Azure pro generování dat monitorování pro Azure Monitor shromažďování. Tento článek popisuje různé kroky potřebné k dokončení implementace Azure Monitor s využitím společné konfigurace pro monitorování všech prostředků ve vašem předplatném Azure. Základní popisy jednotlivých kroků jsou k dispozici s odkazy na další dokumentaci pro podrobné požadavky na konfiguraci.

> [!IMPORTANT]
> Funkce Azure Monitor a jejich konfigurace se budou lišit v závislosti na vašich obchodních požadavcích, které jsou vyvážené s náklady na povolené funkce. Každý krok níže zjistí, zda jsou potenciální náklady, a před pokračováním v tomto kroku tyto náklady doporučujeme vyhodnotit. Úplné podrobnosti o cenách najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="configuration-goals"></a>Cíle konfigurace
Cílem úplné implementace Azure Monitor je shromáždit všechna dostupná data ze všech vašich cloudových prostředků a aplikací a na základě těchto dat Povolit co nejvíce funkcí Azure Monitor.

Data shromážděná pomocí Azure Monitor se odesílají buď do [Azure monitor metriky](essentials/data-platform-metrics.md) , nebo do [protokolů Azure monitor](logs/data-platform-logs.md). Každá z nich ukládá různé druhy dat a umožňuje různé druhy analýz a výstrah. Popis různých typů výstrah najdete v tématu [porovnání Azure monitor metrik a protokolů](/data-platform.md) pro porovnání dvou a [přehled výstrah v Microsoft Azure](alerts/alerts-overview.md) . 

Některá data je možné odeslat do obou metrik a protokolů, aby je bylo možné využívat pomocí různých funkcí. V těchto případech může být potřeba nakonfigurovat každou samostatně. Například data metriky jsou automaticky odesílány prostředky Azure do metrik, které podporují Průzkumníka metrik a výstrahy metriky. Musíte vytvořit nastavení diagnostiky pro každý prostředek, abyste odesílali stejná data metriky do protokolů, což vám umožní analyzovat trendy výkonu s ostatními daty protokolů pomocí Log Analytics. Níže uvedené části označují, kam se odesílají data, a zahrnuje každý krok potřebný k odeslání dat do všech možných umístění.

Můžete mít další požadavky, jako jsou monitorování prostředků mimo Azure a posílání dat mimo Azure Monitor. Požadavky, jako jsou ty, je možné dosáhnout pomocí dodatečné konfigurace funkcí popsaných v tomto článku. Další možnosti konfigurace najdete v části odkazy na dokumentaci v každém kroku.

## <a name="collect-data-from-azure-resources"></a>Shromažďování dat z prostředků Azure

> [!NOTE]
> Kompletní příručku k monitorování virtuálních počítačů pomocí Azure Monitor najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](essentials/monitor-azure-resource.md) .

Některé monitorování prostředků Azure je dostupné automaticky bez nutnosti konfigurace, ale pokud chcete shromažďovat další data monitorování, musíte provést kroky konfigurace. Následující tabulka ilustruje kroky konfigurace potřebné ke shromáždění všech dostupných dat z vašich prostředků Azure, včetně toho, kdy se data o kroku odesílají do Azure Monitor metrik a protokolů Azure Monitor. Níže uvedené části popisují jednotlivé kroky podrobněji.

[![Nasazení monitorování ](media/deploy/deploy-azure-resources.png) prostředků Azure](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Žádná konfigurace
Při vytváření předplatného Azure jsou povolené následující funkce Azure Monitor bez nutnosti konfigurace. S tímto monitorováním nejsou spojeny žádné náklady.

[Protokoly Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) – poskytuje historii přihlašovacích aktivit na úrovni tenanta a auditový záznam změn provedených v Azure Active Directory. Podrobnosti o Azure Active Directorych protokolech a jejich zobrazení v Azure Portal najdete v tématu [sestavy aktivit auditu na portálu Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) a na [sestavách aktivit přihlašování na portálu Azure Active Directory](../active-directory/reports-monitoring/concept-sign-ins.md) .

[Protokol aktivit](essentials/platform-logs-overview.md) – poskytuje přehled o skupinách pro správu a událostech na úrovni předplatného, ke kterým došlo v Azure. Události se automaticky zapisují do protokolu aktivit při vytváření nového prostředku Azure, úpravě prostředku nebo provádění významné aktivity. Při vytváření určitých událostí můžete zobrazit události v Azure Portal a vytvořit výstrahy protokolu aktivit. Podrobnosti o protokolu aktivit a způsobu jeho zobrazení v Azure Portal najdete v tématu [Azure Activity log](essentials/activity-log.md) .

[Metriky platformy](essentials/metrics-supported.md) – shromažďovány automaticky ze služeb Azure do [Azure monitor metriky](essentials/data-platform-metrics.md). Tato data se často zobrazují na stránce **Přehled** v Azure Portal pro různé služby. Podrobnosti o analýze metrik platforem v Azure Portal najdete v tématu [Začínáme s Azure Průzkumník metrik](essentials/metrics-getting-started.md) . 


### <a name="create-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics
K povolení [protokolů Azure monitor](logs/data-platform-logs.md)potřebujete aspoň jeden Log Analytics pracovní prostor, který se vyžaduje pro shromažďování takových dat jako protokolů z prostředků Azure, shromažďování dat z hostovaného operačního systému virtuálních počítačů Azure a pro většinu Azure monitor přehledů. Jiné služby, například Azure Sentinel a Azure Security Center, používají také pracovní prostor Log Analytics a můžou sdílet stejný, který používáte pro Azure Monitor. Můžete začít s jedním pracovním prostorem pro podporu tohoto monitorování, ale v tématu  [navrhování Azure Monitorch protokolů nasazení](logs/design-logs-deployment.md) získáte pokyny k používání více pracovních prostorů.

Při vytváření Log Analyticsho pracovního prostoru se neúčtují žádné náklady, ale po nastavování dat, která se mají do ní shromažďovat, se může účtovat případný poplatek. Podrobnosti najdete v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](logs/manage-cost-storage.md) .  

[V tématu Vytvoření pracovního prostoru Log Analytics v Azure Portal](logs/quick-create-workspace.md) vytvoření počátečního pracovního prostoru Log Analytics. Konfigurace přístupu najdete [v tématu Správa přístupu k datům protokolů a pracovním prostorům v Azure monitor](logs/manage-access.md) . 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Vytvoření nastavení diagnostiky pro shromáždění protokolů klientů a odběrů
I když [protokoly Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) pro vašeho tenanta a [Protokol aktivit](essentials/platform-logs-overview.md) pro vaše předplatné se shromažďují automaticky, můžete je odeslat do pracovního prostoru Log Analytics pomocí dotazů protokolu v Log Analytics analyzovat tyto události s ostatními daty protokolů. To vám také umožní vytvořit výstrahy dotazování protokolu, které jsou jediným způsobem, jak upozornit na protokoly Azure Active Directory a poskytnout složitější logiku než výstrahy protokolu aktivit.

Za odeslání protokolu aktivit do pracovního prostoru se neúčtují žádné náklady, ale v protokolech Azure Active Directory se účtuje příjem dat a uchování. 

Přečtěte si téma [integrace protokolů služby Azure AD s Azure monitor protokolů](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) a [Vytvoření nastavení diagnostiky k odesílání protokolů platforem a metrik do různých cílů](essentials/diagnostic-settings.md) , abyste vytvořili nastavení diagnostiky pro vašeho tenanta a předplatné, aby se do pracovního prostoru Log Analytics odesílaly položky protokolu. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Vytvoření nastavení diagnostiky pro shromažďování protokolů prostředků a metriky platforem
Prostředky v Azure automaticky generují [protokoly prostředků](essentials/platform-logs-overview.md) , které poskytují podrobné informace o operacích provedených v rámci prostředku. Na rozdíl od metrik platforem je ale potřeba nakonfigurovat protokoly prostředků, které se mají shromažďovat. Vytvořte nastavení diagnostiky, které se odešle do pracovního prostoru Log Analytics, aby je bylo možné kombinovat s ostatními daty použitými v protokolech Azure Monitor. Stejné nastavení diagnostiky můžete použít také k odeslání metriky platformy pro většinu prostředků do stejného pracovního prostoru, který umožňuje analyzovat data metrik pomocí dotazů protokolu s ostatními shromážděnými daty.

Pro tuto kolekci se účtují náklady, takže se před implementací na významný počet prostředků vztahují na [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/) . Podrobnosti o optimalizaci nákladů na kolekci protokolů najdete také v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](logs/manage-cost-storage.md) .

[V tématu Vytvoření nastavení diagnostiky můžete shromažďovat protokoly prostředků a metriky v Azure](essentials/diagnostic-settings.md#create-in-azure-portal) a vytvořit nastavení diagnostiky pro prostředek Azure. Vzhledem k tomu, že pro každý prostředek Azure je potřeba vytvořit diagnostické nastavení, přečtěte si téma [nasazení Azure monitor ve velkém měřítku](deploy-scale.md) , kde najdete podrobné informace o používání [zásad Azure](../governance/policy/overview.md) , aby se nastavení automaticky vytvořilo při každém vytvoření prostředku Azure.

### <a name="enable-insights-and-solutions"></a>Povolení přehledů a řešení
Přehledy a řešení poskytují specializované monitorování konkrétní služby nebo řešení. Přehledy využívají novější funkce Azure Monitor, jako jsou například sešity, takže byste měli použít přehled, pokud je k dispozici pro vaši službu. Jsou automaticky dostupné v každém předplatném Azure, ale můžou vyžadovat určitou konfiguraci pro plnou funkčnost. Obvykle budou používat metriky platformy a protokoly prostředků, které jste nakonfigurovali dříve, a mohli shromažďovat další data.

Řešení musí být přidána do každého předplatného a fungovat výhradně s daty z protokolů Azure Monitor a mohou shromažďovat další data protokolu.

Pro řešení nebo přehledy se neúčtují žádné náklady, ale můžete se vám účtovat všechna data, která shromáždí.

Seznam dostupných přehledů a řešení v Azure Monitor najdete v tématu [co je monitorované pomocí Azure monitor](monitor-reference.md) . Všechny jedinečné informace o konfiguraci a cenách najdete v dokumentaci pro každou z nich. 

## <a name="collect-data-from-virtual-machines"></a>Shromažďování dat z virtuálních počítačů

> [!NOTE]
> Kompletní příručku k monitorování virtuálních počítačů pomocí Azure Monitor najdete v tématu [monitorování virtuálních počítačů Azure pomocí Azure monitor](vm/monitor-vm-azure.md) . 

Virtuální počítače generují podobné údaje jako jiné prostředky Azure, ale potřebujete agenta, aby mohli shromažďovat data z hostovaného operačního systému. Porovnání agentů používaných v Azure Monitor najdete v tématu [přehled Azure monitor agentů](agents/agents-overview.md) . 

[Azure monitor pro virtuální počítače](vm/vminsights-overview.md) používá agenta Log Analytics a agenta závislostí ke shromažďování dat z hostovaného operačního systému virtuálních počítačů, takže tyto agenty můžete nasadit jako součást implementace tohoto přehledu. To umožňuje agentovi Log Analytics pro jiné služby, které ho používají, jako je například Azure Security Center.


[![Nasazení virtuálního počítače ](media/deploy/deploy-azure-vm.png) Azure](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>Konfigurace pracovního prostoru pro Azure Monitor pro virtuální počítače
Azure Monitor pro virtuální počítače vyžaduje pracovní prostor Log Analytics, který bude obvykle stejný jako vytvořený pro shromažďování dat z jiných prostředků Azure. Než povolíte všechny virtuální počítače, musíte do pracovního prostoru přidat řešení požadované pro Azure Monitor pro virtuální počítače.

Podrobnosti o konfiguraci Log Analyticsho pracovního prostoru pro Azure Monitor pro virtuální počítače najdete v tématu [konfigurace log Analyticsho pracovního prostoru pro Azure monitor pro virtuální počítače](vm/vminsights-configure-workspace.md) .

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>Povolit Azure Monitor pro virtuální počítače na každém virtuálním počítači
Po nakonfigurování pracovního prostoru můžete povolit každý virtuální počítač instalací agenta Log Analytics a agenta závislostí. K dispozici je několik metod pro instalaci těchto agentů, včetně Azure Policy, což umožňuje automaticky konfigurovat jednotlivé virtuální počítače při jejich vytvoření. Údaje o výkonu a podrobnosti procesu shromážděné nástrojem Azure Monitor pro virtuální počítače jsou uloženy v protokolech Azure Monitor.

V tématu [povolení Azure monitor pro virtuální počítače přehled](vm/vminsights-enable-overview.md) možností nasazení agentů do virtuálních počítačů a jejich povolení k monitorování.

### <a name="configure-workspace-to-collect-events"></a>Konfigurace pracovního prostoru pro shromažďování událostí
Azure Monitor pro virtuální počítače bude shromažďovat údaje o výkonu a podrobnosti a závislosti procesů z hostovaného operačního systému každého virtuálního počítače. Agent Log Analytics může také shromažďovat protokoly z hosta včetně protokolu událostí z Windows a syslog ze systému Linux. Načte konfiguraci pro tyto protokoly z Log Analyticsho pracovního prostoru, ke kterému je připojený. Pracovní prostor stačí nakonfigurovat jenom jednou a pokaždé, když se agent připojí, stáhne změny konfigurace. 

Podrobnosti o konfiguraci pracovního prostoru Log Analytics pro shromažďování dalších dat z vašich virtuálních počítačů agenta najdete [v tématu zdroje dat agenta v Azure monitor](agents/agent-data-sources.md) .

> [!NOTE]
> Pracovní prostor můžete také nakonfigurovat tak, aby shromáždil čítače výkonu, ale tento postup bude pravděpodobně redundantní s daty o výkonu shromažďovanými nástrojem Azure Monitor pro virtuální počítače. Údaje o výkonu shromážděné pracovním prostorem budou uloženy v tabulce *perf* , zatímco data o výkonu shromážděná pomocí Azure monitor pro virtuální počítače jsou uložena v tabulce *InsightsMetrics* . Nakonfigurujte shromažďování výkonu v pracovním prostoru pouze v případě, že požadujete čítače, které nejsou již shromážděny pomocí Azure Monitor pro virtuální počítače.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Diagnostické rozšíření a Agent telegraf
Azure Monitor pro virtuální počítače používá agenta Log Analytics, který odesílá údaje o výkonu do Log Analyticsho pracovního prostoru, ale nemá Azure Monitor metriky. Odeslání těchto dat do metrik umožní jejich analýzu pomocí Průzkumník metrik a použití s upozorněními na metriky. To vyžaduje diagnostické rozšíření v systému Windows a agenta telegraf na platformě Linux.

Podrobnosti o instalaci a konfiguraci těchto agentů najdete v tématu [instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](agents/diagnostics-extension-windows-install.md) a [shromáždění vlastních metrik pro virtuální počítač se systémem Linux pomocí agenta InfluxData telegraf](essentials/collect-custom-metrics-linux-telegraf.md) .


## <a name="monitor-applications"></a>Monitorování aplikací
Azure Monitor monitoruje vlastní aplikace pomocí [Application Insights](app/app-insights-overview.md), které je nutné nakonfigurovat pro každou aplikaci, kterou chcete monitorovat. Proces konfigurace se bude lišit v závislosti na typu monitorované aplikace a typu monitorování, které chcete provést. Data shromážděná pomocí Application Insights jsou uložená v Azure Monitor metrikách, protokoly Azure Monitor a Azure Blob Storage v závislosti na funkci. Údaje o výkonu jsou uloženy v Azure Monitor metrikách i v protokolech Azure Monitor bez nutnosti další konfigurace.

### <a name="create-an-application-resource"></a>Vytvoření prostředku aplikace
Pro každou aplikaci, kterou se chystáte monitorovat, musíte vytvořit prostředek v Application Insights. Data protokolu shromažďovaná pomocí Application Insights jsou uložená v protokolech Azure Monitor pro aplikaci založenou na pracovních prostorech. Data protokolu pro klasické aplikace se ukládají odděleně od Log Analytics pracovního prostoru, jak je popsáno v tématu [struktura dat](logs/data-platform-logs.md#data-structure).

 Když vytváříte aplikaci, musíte vybrat, jestli se má používat klasický nebo založený na pracovních prostorech. Pokud chcete vytvořit klasickou aplikaci, přečtěte si téma [vytvoření prostředku Application Insights](app/create-new-resource.md) . Pokud chcete vytvořit aplikaci založenou na pracovních prostorech, přečtěte si téma [zdroje Application Insights založené na pracovním prostoru (Preview)](app/create-workspace-resource.md) .

### <a name="configure-codeless-or-code-based-monitoring"></a>Konfigurace monitorování na základě kódu nebo kódu
Chcete-li povolit monitorování pro aplikaci, je nutné se rozhodnout, zda budete používat monitorování bez kódu nebo monitorování založené na kódu. Proces konfigurace se bude lišit v závislosti na tomto rozhodnutí a typu aplikace, kterou budete monitorovat.

**Monitorování s kódováním kódu** je nejjednodušší implementovat a lze jej nakonfigurovat po vývoji kódu. Nevyžaduje žádné aktualizace kódu. Podrobnosti o povolení monitorování v závislosti na vaší aplikaci najdete v následujících zdrojích informací.

- [Aplikace hostované v Azure Web Apps](app/azure-web-apps.md)
- [Aplikace v jazyce Java](app/java-in-process-agent.md)
- [ASP.NET aplikací hostovaných ve službě IIS na virtuálním počítači Azure nebo v sadě Azure Virtual Machine scaleing](app/azure-vm-vmss-apps.md)
- [ASP.NET aplikací hostovaných na místním virtuálním počítači IIS](app/monitor-performance-live-website-now.md)


**Monitorování založené na kódu** je více přizpůsobitelnější a shromažďuje další telemetrii, ale vyžaduje přidání závislosti do kódu v BALÍČCÍCH Application Insights SDK NuGet. Podrobnosti o povolení monitorování v závislosti na vaší aplikaci najdete v následujících zdrojích informací.

- [ASP.NET aplikace](app/asp-net.md)
- [ASP.NET Core aplikací](app/asp-net-core.md)
- [Konzolové aplikace .NET](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Jiné platformy](app/platforms.md)

### <a name="configure-availability-testing"></a>Konfigurovat testování dostupnosti
Testy dostupnosti v Application Insights jsou opakované testy, které monitorují dostupnost a rychlost odezvy vaší aplikace v pravidelných intervalech z bodů po celém světě. Pro simulaci uživatelských transakcí, ke kterým jsou přidruženy náklady, můžete vytvořit jednoduchý test pomocí příkazů pro testování nebo vytvořit sekvenci webových požadavků. 

Přehled různých druhů testů a podrobností o jejich vytváření najdete v tématu [monitorování dostupnosti jakéhokoli webu](app/monitor-web-app-availability.md) .

### <a name="configure-profiler"></a>Konfigurace profileru
Profiler v Application Insights poskytuje trasování výkonu pro aplikace .NET. Pomůže vám identifikovat "horkou" cestu kódu, která při zpracování konkrétního webového požadavku zabere nejdelší čas. Proces pro konfiguraci profileru se liší v závislosti na typu aplikace. 

Podrobnosti o konfiguraci profileru najdete v tématu [profilace produkčních aplikací v Azure pomocí Application Insights](app/profiler-overview.md) .

### <a name="configure-snapshot-debugger"></a>Konfigurace Snapshot Debugger
Snapshot Debugger v Application Insights monitoruje telemetrii výjimek z vaší aplikace .NET a shromažďuje snímky na vašich vyvolané výjimce, abyste měli k dispozici informace potřebné k diagnostice problémů v produkčním prostředí. Proces konfigurace Snapshot Debugger se liší v závislosti na typu aplikace. 

Podrobnosti o konfiguraci Snapshot Debugger najdete v tématu [ladění snímků na výjimky v aplikacích .NET](app/snapshot-debugger.md) .


## <a name="visualize-data"></a>Vizualizace dat
Přehledy a řešení budou zahrnovat vlastní sešity a zobrazení pro analýzu jejich dat. Kromě toho můžete vytvořit vlastní [vizualizace](visualizations.md) , včetně sešitů pro Azure monitor dat a řídicích panelů, abyste mohli kombinovat Azure monitor data s daty z jiných služeb v Azure.


### <a name="create-workbooks"></a>Vytváření sešitů
[Sešity](visualize/workbooks-overview.md) v Azure monitor umožňují v Azure Portal vytvářet bohatých vizuálních sestav. Pro vytváření jednotných interaktivních prostředí můžete kombinovat různé sady dat z Azure Monitor metriky a protokoly Azure Monitor. K galerii sešitů můžete přistupovat na kartě **sešity** v nabídce Azure monitor. 

Podrobnosti o vytváření vlastních sešitů najdete v tématu [Azure monitor sešitech](visualize/workbooks-overview.md) .

### <a name="create-dashboards"></a>Vytváření řídicích panelů
[Řídicí panely Azure](../azure-portal/azure-portal-dashboards.md) jsou primární technologie pro řízení řídicích panelů pro Azure a umožňují kombinovat Azure monitor data s daty z jiných služeb, aby se v infrastruktuře Azure poskytovalo jediné podokno skla. Podrobnosti o vytvoření řídicího panelu, který obsahuje data z protokolů Azure Monitor, najdete v tématu [Vytvoření a sdílení řídicích panelů Log Analytics dat](visualize/tutorial-logs-dashboards.md) . 

Podrobnosti o vytvoření řídicího panelu, který obsahuje data z Application Insights, najdete v tématu [Vytvoření vlastních řídicích panelů klíčových ukazatelů výkonu pomocí Azure Application Insights](app/tutorial-app-dashboards.md) . 

## <a name="alerts"></a>Výstrahy
Výstrahy v Azure Monitor proaktivně upozorňují na důležitá data nebo vzory identifikované v datech monitorování. Některé přehledy generují výstrahy bez konfigurace. Pro jiné scénáře musíte vytvořit [pravidla výstrah](alerts/alerts-overview.md) , která zahrnují data k analýze a kritéria pro vygenerování výstrahy, a skupiny akcí, které definují akci, která se má provést při vygenerování výstrahy. 


### <a name="create-action-groups"></a>Vytvoření skupin akcí
[Skupiny akcí](alerts/action-groups.md) jsou kolekce předvoleb oznámení používaných pravidly upozornění k určení akce, která se má provést při aktivaci výstrahy. Mezi příklady akcí patří odeslání e-mailu nebo textu, volání Webhooku nebo odeslání dat do nástroje ITSM Tool. Každé pravidlo výstrahy vyžaduje alespoň jednu skupinu akcí a jedna skupina akcí může být použita více pravidly upozornění.

Podrobnosti o vytvoření skupiny akcí a popisu různých akcí, které může obsahovat, najdete [v tématu Vytvoření a Správa skupin akcí v Azure Portal](alerts/action-groups.md) .


### <a name="create-alert-rules"></a>Vytváření pravidel upozornění
Existuje několik typů pravidel upozornění definovaných typem dat, která používají. Každá z nich má různé možnosti a jiné náklady. Základní strategii, kterou byste měli dodržovat, je použití typu pravidla výstrahy s nejnižšími náklady, která poskytuje logiku, kterou požadujete.

- [Pravidla protokolu aktivit](alerts/activity-log-alerts.md). Vytvoří výstrahu v reakci na novou událost protokolu aktivit, která odpovídá zadaným podmínkám. Těmto výstrahám se neúčtují žádné náklady, takže by měli být první volbou. Podrobnosti o vytvoření upozornění protokolu aktivit najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu aktivit pomocí Azure monitor](alerts/alerts-activity-log.md) .
- [Pravidla upozornění metrik](alerts/alerts-metric-overview.md). Vytvoří výstrahu v reakci na jednu nebo více hodnot metrik překračujících prahovou hodnotu. Upozornění na metriky jsou stavová, což znamená, že se výstraha automaticky zavře, když hodnota klesne pod prahovou hodnotu, a pošle oznámení pouze při změně stavu. Výstrahy upozorňující na metriky jsou ceny, ale výrazně méně než výstrahy protokolu. Podrobnosti o vytvoření výstrahy metriky najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](alerts/alerts-metric.md) .
- [Pravidla upozornění protokolů](alerts/alerts-unified-log.md). Vytvoří výstrahu v případě, že výsledky dotazu plánu odpovídají zadaným kritériím. Jsou to levnější z pravidel upozornění, ale umožňují nejsložitější kritéria. Podrobnosti o vytvoření výstrahy pro dotazování protokolu najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](alerts/alerts-log.md) .
- [Výstrahy aplikace](app/monitor-web-app-availability.md) umožňují provádět proaktivní testování výkonu a dostupnosti vaší webové aplikace. Můžete provést jednoduchý test nástroje test testů bez jakýchkoli nákladů, ale pro složitější testování se účtují náklady. Popis různých testů a podrobnosti o jejich vytváření najdete v tématu [monitorování dostupnosti libovolného webu](app/monitor-web-app-availability.md) .


## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [nasazení Azure monitor ve velkém měřítku pomocí Azure Policy](deploy-scale.md).