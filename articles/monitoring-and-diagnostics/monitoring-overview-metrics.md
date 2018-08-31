---
title: Přehled metrik v Microsoft Azure
description: Přehled metrik a jejich použití v Microsoft Azure
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d61ac48aa7c51bc4b215a7d56b1bbedfdc613f9f
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287552"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Přehled metrik v Microsoft Azure
Tento článek popisuje, jaké metriky jsou v Microsoft Azure svoje výhody a jak je začít používat.  

## <a name="what-are-metrics"></a>Co jsou metriky?
Azure Monitor umožňuje využívat telemetrii s cílem získat přehled o výkonu a stavu vašich úloh v Azure. Metriky (také nazývané čítače výkonu) většinu služeb Azure prostředky emitovány je nejdůležitější typu Azure telemetrická data. Platforma Azure Monitor poskytuje několik způsobů, jak konfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

## <a name="what-are-the-characteristics-of-metrics"></a>Co jsou vlastnosti metrik?
Metriky mají následující vlastnosti:

* Mít všechny metriky **minutových frekvence** (Pokud není uvedeno jinak v definici metriku). Se zobrazí hodnota metriky každou minutu z vašeho prostředku poskytuje téměř v reálném čase přehled o stavu vašich prostředků.
* Metriky jsou **k dispozici okamžitě**. Není nutné vyjádřit výslovný souhlas nebo nastavit další diagnostické informace.
* Můžete přistupovat **93 dní historie** pro jednotlivé metriky. Můžete rychle prohlédnout nejnovější a měsíční trendy v výkon nebo stav prostředku.
* Některé metriky můžou mít atributy dvojice název hodnota nazývá **dimenze**. Ty umožňují dál segmentovat a prozkoumejte metriku více srozumitelným způsobem.

## <a name="what-can-you-do-with-metrics"></a>Co můžete dělat s metrikami?
Metriky umožňují provádět následující úlohy:


- Konfigurovat metriku **výstrahy, pravidla, které odešle oznámení, nebo má automatizované akce** když metriky překročí prahovou hodnotu, kterou jste nastavili. Akce jsou řízena prostřednictvím [skupiny akcí](monitoring-action-groups.md). Příklad akce zahrnují e-mail, Telefon a oznámení SMS, volání webhooků, spuštění sady runbook a další. **Automatické škálování** je speciální automatizovanou akci, která umožňuje škálování, které jste prostředek navýšení nebo snížení kapacity pro zpracování zátěže, ale náklady nebudou nižší při není pod zátěží. Můžete nakonfigurovat pravidlo nastavení automatického škálování pro horizontální snížení nebo navýšení kapacity podle metriky překročení prahové hodnoty.
- **Trasa** všechny metriky *Application Insights* nebo *Log Analytics* povolit okamžitých analýz, vyhledávání a vlastních výstrah na dat metrik z vašich prostředků. Také můžete Streamovat metriky, které *centra událostí*, umožňuje potom směrovat je do služby Azure Stream Analytics nebo vlastních aplikací pro analýzu v reálném čase. Nastavíte centra událostí streamování využívajícího službu nastavení diagnostiky.
- **Archiv** historii výkon nebo stav prostředku pro dodržování předpisů, auditování, nebo v režimu offline pro účely vykazování.  Při konfiguraci nastavení diagnostiky pro prostředek, můžete směrovat metrik do Azure Blob storage.
- Použití **webu Azure portal** ke zřízení, přístup a zobrazit všechny metriky, když vyberte prostředek a vykreslení metrik v grafu. Můžete sledovat výkon vašeho prostředku (například virtuálního počítače, web nebo aplikaci logiky) tak, že připnete graf na řídicí panel.  
- **Provádění pokročilých analýz** nebo vytváření sestav na trendy, výkon nebo využití prostředku.
- **Dotaz** metriky pomocí rutin prostředí PowerShell nebo rozhraní REST API napříč platformami.
- **Využívání** metriky přes nové rozhraní REST API pro Azure Monitor.

  ![Směrování metrik ve službě Azure Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Metriky přístup prostřednictvím portálu
Tady je rychlý návod, jak vytvořit metriku grafu pomocí webu Azure portal.

### <a name="to-view-metrics-after-creating-a-resource"></a>Pokud chcete zobrazit metriky po vytvoření prostředku
1. Otevřete web Azure Portal.
2. Můžete vytvořte web Azure App Service.
3. Po vytvoření webu, přejděte **přehled** okno webu.
4. Můžete zobrazit jako nové metriky **monitorování** dlaždici. Potom můžete upravit na dlaždici a vybrat další metriky.

   ![Metriky pro prostředek ve službě Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Pro přístup k všechny metriky na jednom místě
1. Otevřete web Azure Portal.
2. Přejděte k novému **monitorování** kartu, vyberte a pak **metriky** možnost pod ním.
3. Z rozevíracího seznamu vyberte předplatné, skupinu prostředků a název prostředku.
4. Zobrazení seznamu dostupných metrik. Vyberte metriku zajímají a vykreslit ho.
5. Můžete připnout na řídicí panel kliknutím PIN kód v pravém horním rohu.

   ![Přístup k všechny metriky na jednom místě ve službě Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Metriky na úrovni hostitele se dá dostat z virtuálních počítačů (využívající Azure Resource Manager) a škálovacích sad virtuálních počítačů bez jakékoli další konfigurace diagnostiky. Tyto nové metriky na úrovni hostitele jsou k dispozici pro Windows a Linux. Tyto metriky jsou by se zaměňovat s metrikami úrovni hostovaného operačního systému, abyste měli přístup při zapnutí Azure Diagnostics na virtuální počítače nebo virtuálního počítače škálovací sady. Další informace o konfiguraci diagnostiky, najdete v článku [co je Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Azure Monitor nabízí taky nové metriky grafické prostředí, které jsou k dispozici ve verzi preview. Toto prostředí umožňuje uživatelům překryv metriky z různých zdrojů na jednom grafu. Uživatele můžete také vykreslení, segmentu a filtrovat vícedimenzionálních metrik pomocí této nové metriky grafické prostředí. Další informace [klikněte sem](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Metriky přístup přes rozhraní REST API
Metriky Azure jsou přístupné prostřednictvím rozhraní API služby Azure Monitor. Existují dvě rozhraní API, které vám pomohou zjistit a přístup k metrikám:

* Použití [definice metrik Azure monitoru rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) pro přístup k seznamu metrik a dimenzí, které jsou k dispozici pro službu.
* Použití [rozhraní REST API služby Azure Monitor Metrics](https://docs.microsoft.com/rest/api/monitor/metrics) segmentovat, filtrovat a přístup k datům skutečné metriky.

> [!NOTE]
> Tento článek se týká metrik přes [nové rozhraní API pro metriky](https://docs.microsoft.com/rest/api/monitor/) pro prostředky Azure. Verze rozhraní API pro nové definice metrik a metriky rozhraní API je 2018-01-01. Starší verzi definice metrik a metriky můžete přistupovat pomocí rozhraní API verze 2014-04-01.
>
>

Podrobnější názorný postup pomocí rozhraní REST API služby Azure Monitor, naleznete v tématu [REST API služby Azure Monitor návod](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Export metrik
Můžete přejít na **nastavení diagnostiky** okně v části **monitorování** kartu a zobrazte možnosti exportu metriky. Můžete vybrat metriky (a diagnostické protokoly) směrovat do úložiště objektů Blob do služby Azure Event Hubs nebo ke službě Log Analytics pro případy použití, které byly již bylo zmíněno dříve v tomto článku.

 ![Možnosti exportu metriky ve službě Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

To můžete nakonfigurovat pomocí šablon Resource Manageru, [PowerShell](insights-powershell-samples.md), [rozhraní příkazového řádku Azure](insights-cli-samples.md), nebo [rozhraní REST API](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="take-action-on-metrics"></a>Provést akci na metriky
Pokud chcete dostávat oznámení nebo automatizovaných akcí na data metriky, můžete nakonfigurovat pravidla upozornění nebo nastavení automatického škálování.

### <a name="configure-alert-rules"></a>Konfigurace pravidla upozornění
Můžete nakonfigurovat pravidla upozornění na metriky. Tato pravidla výstrahy můžete zkontrolovat, pokud má metrika překročí určitou prahovou hodnotu. Existují dvě metriky výstrah možnosti nabízené službou Azure Monitor.

Upozornění na metriky: můžete pak můžete informovat prostřednictvím e-mailu nebo vyvolat webhook, který slouží ke spuštění libovolného vlastního skriptu. Webhook můžete také použít ke konfiguraci integrace produktů třetích stran.

 ![Metriky a pravidla výstrah ve službě Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

Novějších upozornění metrik, mají možnost sledovat několik metrik a prahových hodnot pro prostředek a potom oznámí prostřednictvím [skupiny akcí](monitoring-action-groups.md). Další informace o [novější upozornění zde](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Automatické škálování Azure prostředky
Některé prostředky Azure podporují, měřítka nebo v několika instancí pro zvládnutí svých úloh. Automatické škálování se vztahuje na služby App Service (webové aplikace), škálovací sady virtuálních počítačů a klasické služby Azure Cloud Services. Můžete nakonfigurovat pravidla automatického škálování pro horizontální navýšení kapacity nebo v při určitých, který má vliv na vaše úlohy metrika překročí mezní hodnoty, zadáte. Další informace najdete v tématu [přehled automatického škálování](monitoring-overview-autoscale.md).

 ![Metriky a automatické škálování ve službě Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Další informace o podporovaných služeb a metriky
Můžete zobrazit podrobný seznam všech podporovaných služeb a na jejich metriky [metrik Azure monitoru – podporované metriky na typ prostředku](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Další postup
Najdete na odkazech uvedených v tomto článku. Také se dozvíte o:  

* [Běžné metriky pro automatické škálování](insights-autoscale-common-metrics.md)
* [Vytvoření pravidla upozornění](insights-alerts-portal.md)
* [Analýza protokolů ze služby Azure storage s využitím Log Analytics](../log-analytics/log-analytics-azure-storage.md)
