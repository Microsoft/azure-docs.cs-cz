---
title: "Přehled metriky v Microsoft Azure | Microsoft Docs"
description: "Přehled metriky a jejich použití v Microsoft Azure"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: johnkem
ms.openlocfilehash: 4a78236f9c6945bb982466b59690b221f35a1804
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Přehled metriky v Microsoft Azure
Tento článek popisuje, co metriky jsou ve službě Microsoft Azure jejich výhody a jak je začít používat.  

## <a name="what-are-metrics"></a>Jaké jsou metriky?
Azure monitorování umožňuje využívat telemetrie a získáte přehled o výkonu a stavu úlohy v Azure. Nejdůležitější typ Azure telemetrická data je metriky (také nazývané čítače výkonu) vysílaných prostředků nejvíce Azure. Monitorování Azure poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

## <a name="what-can-you-do-with-metrics"></a>Co se děje s metriky?
Metriky jsou cenné zdroj telemetrie a vám umožňují provádět následující úlohy:

* **Sledovat výkon** vaše prostředku (například počítač, web nebo logiku aplikace) vykreslení jeho metriky na portálu graf a Připnutí tento graf na řídicí panel.
* **Upozorňování problému** , ovlivňuje výkon prostředku, když metriky překračuje určitou mez.
* **Konfigurovat automatické akce**, jako je automatické škálování prostředku nebo když metriky překračuje určité prahovou hodnotu, která iniciovala sady runbook.
* **Provádět pokročilé analýzy** nebo generování sestav na trendy výkonu a využití vaší prostředku.
* **Archiv** historii výkon nebo stav prostředku **pro dodržování předpisů nebo auditování** účely.

## <a name="what-are-the-characteristics-of-metrics"></a>Jaké jsou charakteristiky metrik?
Metriky mít následující vlastnosti:

* Mají všechny metriky **jedné minuty frekvence**. Zobrazí hodnota metriky každou minutu z prostředku, která poskytuje téměř v reálném čase přehled o stavu a stavu prostředku.
* Metriky **k dispozici okamžitě**. Nemusíte vyjádřit výslovný souhlas nebo nastavit další diagnostiky.
* Dostanete **30 dní od historie** pro jednotlivé metriky. Můžete rychle zobrazit poslední a měsíční trendy v výkon nebo stav prostředku.
* Některé metriky můžou mít atributy dvojice název hodnota volána **dimenze**. Ty umožňují další segment a zkoumat metriky smysluplnější způsobem.

Rovněž můžete:

* Konfigurovat metriku **výstraha pravidla, které odešle oznámení nebo trvá automatizované akce** když metrika protne prahovou hodnotu, kterou jste nastavili. Při automatickém škálování je zvláštní automatizované akce, které umožňuje škálovat prostředek ke splnění příchozí požadavky nebo načte na vašem webu nebo výpočetních prostředků. Můžete nakonfigurovat pravidlo škálování nastavení škálování příchozí nebo odchozí podle metriky při překročení prahové hodnoty.

* **Trasy** všechny metriky, které umožní rychlé analýzy, vyhledávání a vlastní výstrahy na metriky data z vašich prostředků Application Insights nebo analýzy protokolů (OMS). Můžete také stream metriky do centra událostí, které umožňují směrovat je do služby Azure Stream Analytics a vlastních aplikací pro analýzu skoro v reálném čase. Nastavíte centra událostí streamování pomocí nastavení pro diagnostiku.

* **Archivovat metriky pro úložiště** pro uchování delší nebo je používat pro offline generování sestav. Při konfiguraci nastavení diagnostiky pro prostředek, můžete směrovat vaše metriky do úložiště objektů Blob Azure.

* Snadno zjistit, přístup, a **zobrazit všechny metriky** prostřednictvím portálu Azure, když vyberte prostředek a vykreslení metriky pro graf.

* **Využívat** metriky prostřednictvím nových rozhraní REST API pro Azure monitorování.

* **Dotaz** metriky pomocí rutin prostředí PowerShell nebo rozhraní REST API napříč platformami.

  ![Směrování metriky v Azure monitorování](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Metriky přístup prostřednictvím portálu
Toto je rychlý návod, jak vytvořit metriky grafu pomocí portálu Azure.

### <a name="to-view-metrics-after-creating-a-resource"></a>Chcete-li zobrazit metriky po vytvoření prostředku
1. Otevřete web Azure Portal.
2. Vytvoření webu k službě Azure App Service.
3. Po vytvoření webu, přejděte na **přehled** okno webu.
4. Můžete zobrazit nové metriky jako **monitorování** dlaždici. Potom můžete upravit na dlaždici a vybrat další metriky.

   ![Metriky pro prostředek v Azure monitorování](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Pro přístup k všechny metriky na jednom místě
1. Otevřete web Azure Portal.
2. Přejděte do nové **monitorování** karty, vyberte a poté **metriky** možnost pod ním.
3. V rozevíracím seznamu vyberte předplatné, skupinu prostředků a názvu prostředku.
4. Zobrazte seznam dostupných metriky. Pak vyberte metriku zájem a jeho vykreslení.
5. Můžete ho připnout na řídicí panel kliknutím kódu pin v pravém horním rohu.

   ![Přístup k všechny metriky na jednom místě v Azure monitorování](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Metriky na úrovni hostitele mají přístup z virtuálních počítačů (založené na Azure Resource Manager) a sadách škálování virtuálních počítačů bez jakékoli další diagnostiky. Tyto nové metriky na úrovni hostitele jsou k dispozici pro Windows a Linux instance. Tyto metriky jsou Nezaměňovat s metriky úrovni operačního systému hosta, abyste měli přístup k když zapnete Azure Diagnostics virtuálních počítačů nebo škálování virtuálních počítačů sady. Další informace o konfiguraci diagnostiky najdete v tématu [co je Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Monitorování Azure má také nové metriky grafů, prostředí, které jsou dostupné ve verzi preview. Toto prostředí umožňuje uživatelům překrytí metriky z více zdrojů na jeden graf. Uživatelé také vykreslení, segmentu a filtrovat vícerozměrných metriky pomocí této nové metrika grafů prostředí. Další informace [, klikněte sem](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Metriky přístup přes REST API
Azure metriky je přístupný prostřednictvím rozhraní API Azure monitorování. Existují dvě rozhraní API, které vám pomohou zjistit a přístup k metriky:

* Použití [Azure monitorování metrika definice rozhraní API REST](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) pro přístup k seznamu metriky a všechny dimenze, které jsou k dispozici pro službu.
* Použití [REST API služby Azure monitorování metriky](https://docs.microsoft.com/rest/api/monitor/metrics) segmentu, filtrovat a přístup k datům skutečné metriky.

> [!NOTE]
> Tento článek se zabývá metriky prostřednictvím [nové rozhraní API pro metriky](https://docs.microsoft.com/rest/api/monitor/) pro prostředky Azure. Verze rozhraní API pro nové definice metrik a metriky rozhraní API je 2017-05-01-preview. Starší verze definice metrik a metriky můžete přistupovat pomocí rozhraní API verze 2014-04-01.
>
>

Podrobnější návod pomocí rozhraní API REST Azure monitorování najdete v tématu [REST API služby Azure monitorování návod](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Export metriky
Můžete přejít na **nastavení diagnostiky** okno pod **monitorování** kartě a zobrazit možnosti exportu metrik. Můžete vybrat metriky (a diagnostických protokolů) k odeslání do úložiště objektů Blob Azure Event Hubs, nebo OMS pro použití případů, které byly dříve uvedených v tomto článku.

 ![Možnosti exportu metrik, které v Azure monitorování](./media/monitoring-overview-metrics/MetricsOverview3.png)

To můžete nakonfigurovat pomocí šablony Resource Manageru, [prostředí PowerShell](insights-powershell-samples.md), [rozhraní příkazového řádku Azure](insights-cli-samples.md), nebo [rozhraní REST API](https://msdn.microsoft.com/library/dn931943.aspx).

## <a name="take-action-on-metrics"></a>Provést akci pro metriky
K přijímání oznámení nebo provést automatické akce pro data metriky, můžete nakonfigurovat nastavení automatického škálování nebo pravidla výstrah.

### <a name="configure-alert-rules"></a>Konfigurace pravidla výstrah
Pravidla výstrah můžete nakonfigurovat na metriky. Tato pravidla výstrahy můžete zkontrolovat, pokud metriky překročila určitou mez. Existují dvě metriky výstrahy možnosti, které nabízí Azure monitorování.

Metriky výstrahy: můžete se pak upozornění e-mailem nebo fire webhooku, který slouží ke spuštění všech vlastních skriptů. Můžete taky webhooku pro konfiguraci integrace produktu třetí strany.

 ![Metriky a pravidla výstrah v monitorování Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

V blízkosti upozornění v reálném čase (preview): tyto mít možnost sledovat více metriky a prahové hodnoty pro prostředek a potom oznámíme vám prostřednictvím [akce skupiny](/monitoring-action-groups.md). Další informace o [téměř v reálném čase metrika výstrahy zde](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Škálování vašeho Azure prostředky
Některé prostředky Azure podporují, změny velikosti nebo v několika instancí pro zpracování vašich úloh. Škálování se vztahuje na služby App Service (webové aplikace), sady škálování virtuálního počítače a classic Azure Cloud Services. Můžete nakonfigurovat pravidla škálování škálování nebo v když metrika, který má dopad na vaše úloha překračuje prahovou hodnotu, která zadáte. Další informace najdete v tématu [přehled automatické škálování](monitoring-overview-autoscale.md).

 ![Metriky a škálování v Azure monitorování](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Další informace o podporovaných služeb a metriky
Můžete zobrazit podrobný seznam všech podporovaných služeb a jejich metriky [Azure monitorování metriky – podporované metriky na typ prostředku](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Další postup
Najdete pod odkazy v tomto článku. Kromě toho další informace o:  

* [Běžné metriky pro automatické škálování](insights-autoscale-common-metrics.md)
* [Postup vytvoření pravidla výstrah](insights-alerts-portal.md)
* [Analýza protokolů z úložiště Azure s analýzy protokolů](../log-analytics/log-analytics-azure-storage.md)
