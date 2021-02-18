---
title: Azure Monitor pro stávající zákazníky Operations Manager
description: Doprovodné materiály pro stávající uživatele Operations Manager k převodu monitorování určitých úloh do Azure Monitor jako součást přechodu do cloudu.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: b1262533c3398a774b85e4143289a9b7c342aeab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593570"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor pro stávající zákazníky Operations Manager
Tento článek poskytuje pokyny pro zákazníky, kteří aktuálně používají [System Center Operations Manager](/system-center/scom/welcome) a plánují přechod [Azure monitor](overview.md) při migraci obchodních aplikací a dalších prostředků do Azure. Předpokládá, že váš konečný cíl je úplným přechodem do cloudu a nahrazuje co nejvíce Operations Manager funkcím Azure Monitor, aniž by to ohrozilo vaše podnikání a provozní požadavky. 

Konkrétní doporučení učiněná v tomto článku se změní jako Azure Monitor a Operations Manager přidání funkcí. Základní strategii je sice i nadále konzistentní.

> [!IMPORTANT]
> K implementaci několika Azure Monitor funkcí popsaných tady se účtují náklady, takže byste měli vyhodnotit jejich hodnotu před nasazením v celém prostředí.

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že už používáte [Operations Manager](/system-center/scom) a máte minimálně základní znalost [Azure monitor](overview.md). Úplné porovnání dvou najdete v tématu [Průvodce monitorováním cloudu: Přehled monitorovacích platforem](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Tento článek podrobně popisuje určité rozdíly mezi funkcemi, které vám pomůžou pochopit některá doporučení, která tady provedete. 


## <a name="general-strategy"></a>Obecná strategie
Neexistují žádné nástroje pro migraci k převedení prostředků z Operations Manager na Azure Monitor, protože platformy jsou zásadním rozdílem. Místo toho bude vaše migrace představovat [standardní Azure monitor implementace](deploy.md) , zatímco budete nadále používat Operations Manager. Při přizpůsobování Azure Monitor pro splnění požadavků na různé aplikace a komponenty a když získá více funkcí, můžete začít vyřadit různé sady Management Pack a agenty v Operations Manager.

Obecná strategie, která se doporučuje v tomto článku, je stejná jako v [Průvodci monitorováním cloudu](/azure/cloud-adoption-framework/manage/monitor/), který doporučuje strategii [monitorování hybridního cloudu](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) , která vám umožní postupný přechod do cloudu. I když se některé funkce můžou překrývat, tato strategie vám umožní zachovat stávající obchodní procesy, jako byste se seznámili s novou platformou. Z funkcí Operations Manager jenom opustit, protože ji můžete nahradit Azure Monitor. Používání více nástrojů pro monitorování přináší složitost, ale umožňuje využít výhod Azure Monitor k monitorování zatížení nové generace cloudu a současně zachovat Operations Manager schopnost monitorovat serverový software a součásti infrastruktury, které mohou být místní nebo v jiných cloudech. 


## <a name="components-to-monitor"></a>Komponenty, které se mají monitorovat
Pomáhá roztřídit různé typy úloh, které je třeba monitorovat, aby bylo možné určit odlišnou strategii monitorování pro každý z nich. [Průvodce monitorováním cloudu: formulace strategie monitorování](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) poskytuje podrobný rozpis různých vrstev ve vašem prostředí, které potřebují sledovat, jak budete postupovat od starších podnikových aplikací až po moderní aplikace v cloudu.

Před cloudem jste použili Operations Manager k monitorování všech vrstev. Při zahájení přechodu s infrastrukturou jako službou (IaaS) budete nadále používat Operations Manager pro virtuální počítače, ale začít používat Azure Monitor pro vaše cloudové prostředky. Při dalším přechodu na moderní aplikace s využitím platformy jako služby (PaaS) se můžete zaměřit na Azure Monitor a začít vyřadit Operations Manager funkce.


![Cloudové modely](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Tyto vrstvy lze zjednodušit do následujících kategorií, které jsou dále popsány ve zbývající části tohoto článku. I když se všechny úlohy monitorování ve vašem prostředí nemusí do jedné z těchto kategorií rozčlenit, musí být každý dostatečně blízko konkrétní kategorie, aby se dala použít obecná doporučení.

**Obchodní aplikace.** Aplikace, které poskytují funkce specifické pro vaši firmu. Mohou být interní nebo externí a jsou často vyvíjeny interně pomocí vlastního kódu. Starší verze aplikací se obvykle hostují na virtuálních nebo fyzických počítačích, na kterých běží Windows nebo Linux, zatímco vaše novější aplikace budou založené na aplikačních službách v Azure, jako je například Azure Web Apps a Azure Functions.

**Služby Azure.** Prostředky v Azure, které podporují vaše obchodní aplikace, které byly migrovány do cloudu. Patří sem služby, jako jsou Azure Storage, Azure SQL a Azure IoT. To zahrnuje také virtuální počítače Azure, protože jsou monitorované jako jiné služby Azure, ale aplikace a software běžící v hostovaném operačním systému těchto virtuálních počítačů vyžadují další monitorování nad rámec hostitele.

**Serverový software.** Software běžící na virtuálních a fyzických počítačích, které podporují vaše obchodní aplikace nebo zabalené aplikace, které poskytují obecné funkce pro vaši firmu. Mezi příklady patří Internet Information Server (IIS), SQL Server, Exchange a SharePoint. To zahrnuje i operační systém Windows nebo Linux na virtuálních a fyzických počítačích.

**Místní infrastruktura.** Komponenty specifické pro vaše místní prostředí, které vyžaduje monitorování. To zahrnuje tyto prostředky jako fyzické servery, úložiště a síťové součásti. Jedná se o komponenty, které jsou virtualizované při přesunu do cloudu.

## <a name="sample-walkthrough"></a>Ukázkový názorný postup
Následuje hypotetický návod k migraci z Operations Manager na Azure Monitor. To není určeno k tomu, aby reprezentovala plnou složitost skutečné migrace, ale obsahuje přinejmenším základní kroky a posloupnost. Níže uvedené části popisují všechny tyto kroky podrobněji.

Vaše prostředí před přesunutím jakýchkoli součástí do Azure vychází z virtuálních a fyzických počítačů, které jsou místní nebo se zprostředkovatelem spravované služby. Spoléhá se na Operations Manager pro monitorování obchodních aplikací, serverového softwaru a dalších součástí infrastruktury ve vašem prostředí, jako jsou například fyzické servery a sítě. Používáte standardní sady Management Pack pro serverový software, jako je IIS, SQL Server a různé softwarové software, a tyto sady Management Pack vyladíte podle svých specifických požadavků. Vytvoříte vlastní sady Management Pack pro své obchodní aplikace a další komponenty, které není možné sledovat pomocí existujících sad Management Pack, a nakonfigurujte Operations Manager pro podporu vašich obchodních procesů.

Vaše migrace do Azure začíná na IaaS a přesouvá virtuální počítače podporující obchodní aplikace do Azure. Požadavky na monitorování těchto aplikací a serverového softwaru, které jsou závislé na tom, že se nezmění a vy na těchto serverech budete nadále používat stávající sady Management Pack Operations Manager. 

Azure Monitor je pro vaše služby Azure povolený hned po vytvoření předplatného Azure. Automaticky shromažďuje metriky platforem a protokol aktivit a konfiguruje protokoly prostředků, které se mají shromažďovat, abyste mohli interaktivně analyzovat veškerou dostupnou telemetrii pomocí dotazů protokolu. Ve virtuálních počítačích povolíte Azure Monitor pro virtuální počítače, abyste mohli analyzovat data monitorování napříč celým prostředím společně a zjišťovat vztahy mezi počítači a procesy. Tím, že na svých místních fyzických a virtuálních počítačích rozšíříte Azure Monitor, povolíte na nich servery s povoleným použitím ARC Azure. 

U každé z vašich obchodních aplikací povolíte Application Insights. Identifikuje různé součásti každé aplikace, začne shromažďovat data o využití a výkonu a identifikuje všechny chyby, ke kterým dochází v kódu. Vytvoříte testy dostupnosti pro proaktivní testování externích aplikací a upozorní vás na případné problémy s výkonem nebo dostupností. I když vám Application Insights poskytuje výkonné funkce, které v Operations Manager nemáte, budete se nadále spoléhat na vlastní sady Management Pack, které jste pro své obchodní aplikace vyvinuli, protože zahrnují scénáře monitorování, které ještě nejsou zahrnuté v Azure Monitor. 

Jak se seznámíte s Azure Monitor, začnete vytvářet pravidla výstrah, která budou moci nahradit některé funkce Management Pack a začít vyvíjet obchodní procesy, aby používaly novou monitorovací platformu. To vám umožní začít odebírat počítače a sady Management Pack z Operations Manager skupiny pro správu. Můžete dál používat sady Management Pack pro důležitý serverový software a místní infrastrukturu, ale dál sledovat nové funkce v Azure Monitor, které vám umožní vyřadit další funkce.

## <a name="monitor-azure-services"></a>Monitorování služeb Azure
Služby Azure ve skutečnosti vyžadují Azure Monitor shromažďování telemetrie a je to povolený okamžik, kdy vytvoříte předplatné Azure. [Protokol aktivit](essentials/activity-log.md) se automaticky shromáždí pro předplatné a [metriky platformy](essentials/data-platform-metrics.md) se automaticky shromažďují z libovolných prostředků Azure, které vytvoříte. Můžete okamžitě začít používat [Průzkumníka metrik](essentials/metrics-getting-started.md), který je podobný zobrazení výkonu v konzoli Operations Console, ale poskytuje interaktivní analýzu a [Pokročilé agregace](essentials/metrics-charts.md) dat. [Vytvořte výstrahu metriky](alerts/alerts-metric.md) , která bude upozorněna na překročení prahové hodnoty, nebo [přidat graf na řídicí panel Azure](essentials/metrics-charts.md#pinning-to-dashboards) pro přehlednost.

[![Průzkumník metrik](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Vytvořte nastavení diagnostiky](essentials/diagnostic-settings.md) pro každý prostředek Azure a odešlete metriky a [protokoly prostředků](essentials/resource-logs.md), které poskytují podrobné informace o interní operaci každého prostředku, do Log Analytics pracovního prostoru. Díky tomu máte k dispozici veškerou telemetrii pro vaše prostředky a můžete použít [Log Analytics](logs/log-analytics-overview.md) k interaktivní analýze dat protokolů a výkonu pomocí pokročilého dotazovacího jazyka, který nemá v Operations Manager žádný ekvivalent. Můžete také vytvořit [výstrahy dotazování protokolu](alerts/alerts-log-query.md), které můžou pomocí složité logiky určit podmínky upozornění a korelovat data napříč několika prostředky.

[![Analýza protokolů](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

[Přehledy](monitor-reference.md) v Azure monitor jsou podobné sadám Management Pack v tom, že poskytují jedinečné monitorování konkrétní služby Azure. Přehledy jsou aktuálně k dispozici pro několik služeb, včetně sítě, úložiště a kontejnerů, a další jsou neustále přidávány.

[![Příklad přehledu](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Přehledy jsou založené na [sešitech](visualize/workbooks-overview.md) v Azure monitor, které kombinují metriky a dotazy protokolů do propracovaných interaktivních sestav. Vytvořte si vlastní sešity pro kombinování dat z více služeb, podobně jako při vytváření vlastních zobrazení a sestav v konzoli Operations Console.

### <a name="azure-management-pack"></a>Management Pack Azure
[Azure Management Pack](https://www.microsoft.com/download/details.aspx?id=50013) umožňuje Operations Manager zjišťovat prostředky Azure a monitorovat jejich stav na základě konkrétní sady scénářů monitorování. Tento Management Pack vyžaduje, abyste u každého prostředku v Azure provedli další konfiguraci, ale může být užitečné zajistit si přehled o prostředcích Azure v konzoli Operations Console, dokud nevyvíjíte obchodní procesy, které se budou soustředit na Azure Monitor.

[![Management Pack Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Sadu Azure Management Pack můžete použít, pokud chcete mít v konzole Operations Console přehled o určitých prostředcích Azure a integrovat základní výstrahy s vašimi stávajícími procesy. Ve skutečnosti používá data shromažďovaná v Azure Monitor. Měli byste se Azure Monitor, i když budete mít dlouhodobě kompletní monitorování vašich prostředků Azure. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Monitorování softwaru a místní infrastruktury serveru
Když přesunete počítače do cloudu, požadavky na monitorování se pro software nemění. Už nemusíte monitorovat fyzické součásti, protože jsou virtualizované, ale hostovaný operační systém a jeho úlohy mají stejné požadavky bez ohledu na jejich prostředí.

[Azure monitor pro virtuální počítače](vm/vminsights-overview.md) je primární funkcí v Azure monitor pro monitorování virtuálních počítačů a jejich hostovaných operačních systémů a úloh. Podobně jako u Operations Manager Azure Monitor pro virtuální počítače používá Agent ke shromažďování dat z hostovaného operačního systému virtuálních počítačů. Jedná se o stejné údaje o výkonu a událostech, které se obvykle používají v sadách Management Pack pro účely analýzy a upozorňování. K identifikaci a upozornění na problémy pro obchodní aplikace a serverový software běžící v těchto počítačích nejsou k dispozici žádná předplatná pravidla. Musíte vytvořit vlastní pravidla výstrah, která budou aktivním upozorněním na zjištěné problémy.

[![Azure Monitor pro virtuální počítače výkon](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor také neměří stav různých aplikací a služeb spuštěných na virtuálním počítači. Výstrahy metriky se můžou automaticky vyřešit, když hodnota klesne pod prahovou hodnotu, ale Azure Monitor aktuálně nemá možnost definovat kritéria stavu pro aplikace a služby běžící na počítači, ani neposkytuje souhrn stavu pro seskupení stavu souvisejících součástí.

> [!NOTE]
> Nová [funkce stavu hosta pro Azure monitor pro virtuální počítače](vm/vminsights-health-overview.md) je teď ve verzi Public Preview a upozorní na základě stavu sady metrik výkonu. Je to zpočátku omezené na určitou sadu čítačů výkonu, které souvisejí s hostovaným operačním systémem, a ne aplikace nebo jiné úlohy spuštěné ve virtuálním počítači.
> 
> [![Stav hosta Azure Monitor pro virtuální počítače](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

Monitorování softwaru na vašich počítačích v hybridním prostředí obvykle používá kombinaci Azure Monitor pro virtuální počítače a Operations Manager v závislosti na požadavcích jednotlivých počítačů a na vaší splatnosti při vývoji provozních procesů kolem Azure Monitor. Agent pro správu Microsoft (dále jen agent Log Analytics v Azure Monitor) se používá v obou platformách, aby se jeden počítač mohl monitorovat současně.

> [!NOTE]
> V budoucnu se Azure Monitor pro virtuální počítače přechodem na [agenta Azure monitor](agents/azure-monitor-agent-overview.md), který je aktuálně ve verzi Public Preview. Bude kompatibilní s Microsoft Monitoring Agent, aby se stejný virtuální počítač dál mohl monitorovat pomocí obou platforem.

Nadále používejte Operations Manager pro funkce, které se ještě nedají poskytnout Azure Monitor. Patří sem sady Management Pack pro důležitý serverový software, jako je IIS, SQL Server nebo Exchange. Můžete mít také vytvořené vlastní sady Management Pack pro místní infrastrukturu, u kterých není možné získat přístup k Azure Monitor. I nadále používejte Operations Manager, pokud je pevně integrovaná do provozních procesů, dokud nebudete moct přejít na modernizaci vaší provozní operace, kde Azure Monitor a jiné služby Azure se můžou rozšířit nebo nahradit. 

Pomocí Azure Monitor virtuálních počítačů Vylepšete aktuální monitorování, i když ho hned nenahradíte Operations Manager. Příklady funkcí, které jsou jedinečné pro Azure Monitor, jsou následující:

- Zjišťování a monitorování vztahů mezi virtuálními počítači a jejich externími závislostmi.
- Prohlédněte si agregovaná data o výkonu napříč několika virtuálními počítači v interaktivních grafech a sešitech.
- Pomocí [dotazů protokolu](logs/log-query-overview.md) můžete interaktivně analyzovat telemetrii z vašich virtuálních počítačů s daty z dalších prostředků Azure.
- Vytvořte [pravidla upozornění protokolů](alerts/alerts-log-query.md) založená na komplexní logice napříč několika virtuálními počítači.

[![Mapa Azure Monitor pro virtuální počítače](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Kromě virtuálních počítačů Azure může Azure Monitor pro virtuální počítače monitorovat počítače v místním prostředí a v jiných cloudech pomocí [serverů s podporou ARC Azure](../azure-arc/servers/overview.md). Servery s podporou ARC umožňují spravovat počítače se systémem Windows a Linux hostované mimo Azure, ve vaší podnikové síti nebo v jiném cloudovém poskytovateli konzistentním se správou nativních virtuálních počítačů Azure.



## <a name="monitor-business-applications"></a>Monitorování obchodních aplikací
K monitorování obchodních aplikací pomocí Operations Manager obvykle potřebujete vlastní sady Management Pack, které využívají agenty nainstalované na každém virtuálním počítači. Application Insights v Azure Monitor sleduje webové aplikace bez ohledu na to, jestli jsou v Azure, v jiných cloudech nebo v místním prostředí, takže se dá použít pro všechny vaše aplikace bez ohledu na to, jestli se přenesly do Azure.

Pokud je vaše monitorování obchodní aplikace omezené na funkčnost poskytovanou [šablonou výkonu aplikace .NET]() v Operations Manager, můžete s nejpravděpodobnější migrací do Application Insights bez ztráty funkčnosti. Application Insights budou zahrnovat značný počet dalších funkcí, včetně následujících:

- Automatické zjišťování a monitorování součástí aplikace
- Shromážděte podrobné údaje o využití a výkonu aplikací, jako je doba odezvy, míry selhání a sazby za požadavky.
- Shromažďovat data prohlížeče, jako jsou zobrazení stránek a výkon zatížení.
- Zjištění výjimek a přechod do trasování zásobníku a souvisejících požadavků.
- Proveďte pokročilou analýzu pomocí funkcí, jako je [distribuované trasování](app/distributed-tracing.md) a [inteligentní zjišťování](app/proactive-diagnostics.md).
- Použijte [Průzkumníka metrik](essentials/metrics-getting-started.md) pro interaktivní analýzu dat výkonu.
- Pomocí [dotazů protokolu](logs/log-query-overview.md) můžete interaktivně analyzovat shromážděnou telemetrii společně s daty shromážděnými pro služby Azure a Azure monitor pro virtuální počítače.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Existují však určité scénáře, kdy možná budete muset dál používat Operations Manager kromě Application Insights, dokud nebudete schopni dosáhnout požadovaných funkcí. Příklady, kdy možná budete potřebovat pokračovat v Operations Manager zahrnuje následující:

-  [Testy dostupnosti](app/monitor-web-app-availability.md), které umožňují monitorovat a upozorňovat na dostupnost a odezvu vašich aplikací, vyžadují příchozí požadavky z IP adres agentů webového testu. Pokud vaše zásada takovému přístupu nepovolí, možná budete muset v Operations Manager dál používat [monitorování dostupnosti webových aplikací](/system-center/scom/web-application-availability-monitoring-template) .
- V Operations Manager můžete nastavit libovolný interval cyklického dotazování pro testy dostupnosti, přičemž mnoho zákazníků kontroluje každých 60-120 sekund. Application Insights má minimální interval dotazování 5 minut, který může být pro některé zákazníky příliš dlouhý.
- Významné množství monitorování v Operations Manager provádí shromažďováním událostí generovaných aplikacemi a spouštěním skriptů v místním agentovi. Nejedná se o standardní možnosti v Application Insights, takže můžete vyžadovat vlastní práci, abyste dosáhli svých podnikových požadavků. To může zahrnovat vlastní pravidla upozornění, která používají data událostí uložená v pracovním prostoru Log Analytics a skripty spouštěné v hostu virtuálních počítačů pomocí [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).
- V závislosti na jazyku, ve kterém je vaše aplikace napsaná, může být omezení [instrumentace, kterou můžete používat s Application Insights](app/platforms.md).

Po základní strategii v ostatních částech této příručky můžete dál používat Operations Manager pro vaše obchodní aplikace, ale využít výhod dalších funkcí poskytovaných Application Insights. Vzhledem k možnosti nahrazení důležitých funkcí Azure Monitor můžete začít vyřadit vlastní sady Management Pack.


## <a name="next-steps"></a>Další kroky

- Podrobné porovnání Azure Monitor a System Center Operations Manager a další informace o návrhu a implementaci hybridního monitorovacího prostředí najdete v [Průvodci monitorováním cloudu](/azure/cloud-adoption-framework/manage/monitor/) .
- Přečtěte si další informace o [monitorování prostředků Azure v Azure monitor](essentials/monitor-azure-resource.md).
- Přečtěte si další informace o [monitorování virtuálních počítačů Azure v Azure monitor](vm/monitor-vm-azure.md).
- Přečtěte si další informace o [Azure monitor pro virtuální počítače](vm/vminsights-overview.md).
- Přečtěte si další informace o [Application Insights](app/app-insights-overview.md).