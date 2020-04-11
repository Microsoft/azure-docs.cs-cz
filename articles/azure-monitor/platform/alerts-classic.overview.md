---
title: Přehled klasických výstrah ve službě Azure Monitor
description: Klasické výstrahy jsou zastaralé. Výstrahy umožňují sledovat metriky prostředků Azure, události nebo protokoly a být upozorněni, když je splněna podmínka, kterou zadáte.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: e9c269db870f582c176783a4654b5de251e24412
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114505"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co jsou klasická upozornění v Microsoft Azure?

> [!NOTE]
> Tento článek popisuje, jak vytvořit starší klasické metriky výstrahy. Azure Monitor teď podporuje [novější upozornění na metriky téměř v reálném čase a nové prostředí výstrah](../../azure-monitor/platform/alerts-overview.md). Klasické výstrahy jsou [vyřazeny](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement), i když stále v omezeném použití pro prostředky, které ještě nepodporují nové výstrahy. 
>

Výstrahy umožňují konfigurovat podmínky nad daty a být upozorněni, když podmínky odpovídají nejnovějším datům monitorování.

## <a name="old-and-new-alerting-capabilities"></a>Staré a nové možnosti upozorňování

V minulosti Azure Monitor, Application Insights, Log Analytics a service health měl samostatné možnosti výstrah. Přesčas, Azure lepší a kombinované uživatelské rozhraní a různé metody upozorňování. Konsolidace stále probíhá.

Klasické výstrahy můžete zobrazit jenom na obrazovce klasického uživatele výstrah na webu Azure Portal. Tuto obrazovku získáte z tlačítka **Zobrazit klasické výstrahy** na obrazovce upozornění. 

 ![Volby výstrah na webu Azure Portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Nové uživatelské prostředí výstrah má následující výhody oproti klasickému prostředí výstrah:
- **Lepší systém oznámení** – všechny novější výstrahy používají skupiny akcí, které jsou pojmenované skupiny oznámení a akcí, které lze znovu použít ve více výstrahách. Klasické metriky výstrahy a starší log Analytics výstrahy nepoužívají skupiny akcí.
- **Jednotné prostředí pro vytváření** – všechny vytváření výstrah pro metriky, protokoly a protokoly aktivit napříč Azure Monitor, Log Analytics a Application Insights je na jednom místě.
- **Zobrazit vypálené výstrahy Analýzy protokolů na webu Azure Portal** – teď můžete ve svém předplatném taky vidět vypálené výstrahy Analýzy protokolů. Dříve byly v samostatném portálu.
- **Oddělení vymažení výstrah a výstrahy pravidla** – definice podmínky, která aktivuje výstrahu) a aktivovaná výstraha (instance spuštění pravidla výstrahy) jsou diferencované, takže zobrazení provozní a konfigurace jsou odděleny.
- **Lepší pracovní postup** – Nové prostředí vytváření výstrah vede uživatele v průběhu procesu konfigurace pravidla výstrahy, což usnadňuje zjišťování správných věcí, na které je třeba upozornit.
- **Konsolidace inteligentních výstrah** a **nastavení stavu výstrah** – novější výstrahy zahrnují funkce automatického seskupení, které společně zobrazují podobné výstrahy, aby se snížilo přetížení v uživatelském rozhraní. 

Novější upozornění metriky mají následující výhody oproti klasické metriky výstrahy:
- **Vylepšená latence:** Novější upozornění metriky lze spustit stejně často jako každou minutu. Starší upozornění na metriky se vždy spouštějí s frekvencí 5 minut. Novější výstrahy mají rostoucí menší zpoždění od výskytu problému k oznámení nebo akci (3 až 5 minut). Starší výstrahy jsou 5 až 15 minut v závislosti na typu.  Výstrahy protokolu mají obvykle 10 až 15 minut zpoždění z důvodu čas potřebný k ingestování protokolů, ale novější metody zpracování snižují tento čas. 
- **Podpora vícerozměrných metrik**: Můžete upozornit na dimenzionální metriky, které vám umožní sledovat zajímavý segment metriky.
- **Větší kontrola nad podmínkami metriky**: Můžete definovat bohatší pravidla výstrah. Novější výstrahy podporují sledování maximální, minimální, průměrné a celkové hodnoty metrik.
- **Kombinované monitorování více metrik**: Pomocí jediného pravidla můžete sledovat více metrik (aktuálně až dvě metriky). Výstraha se aktivuje, pokud obě metriky překročí příslušné prahové hodnoty pro zadané časové období.
- **Lepší systém oznámení**: Všechny novější výstrahy používají [skupiny akcí](../../azure-monitor/platform/action-groups.md), které jsou pojmenované skupiny oznámení a akcí, které lze znovu použít ve více výstrahách.  Klasické metriky výstrahy a starší log Analytics výstrahy nepoužívají skupiny akcí. 
- **Metriky z protokolů** (veřejná verze Preview): Data protokolu, která se přecvádějí do Analýzy protokolů, teď můžou extrahovat a převést na metriky Azure Monitoru a pak je upozornit stejně jako ostatní metriky. Viz [Výstrahy (klasické)](alerts-classic.overview.md) pro terminologii specifické pro klasické výstrahy. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasické výstrahy na datech Azure Monitoru
K dispozici jsou dva typy klasických výstrah – upozornění na metriky a výstrahy protokolu aktivit.

* **Klasické metriky výstrahy** – tato výstraha se aktivuje, když hodnota zadané metriky překročí prahovou hodnotu, kterou přiřadíte. Výstraha vygeneruje oznámení při překročení této prahové hodnoty a splnění podmínky výstrahy. V tomto okamžiku je výstraha považována za "Aktivovaná". Generuje další oznámení, když je "Vyřešeno" - to znamená, když je prahová hodnota znovu překročena a podmínka již není splněna.

* **Upozornění na klasický protokol aktivit** – výstraha protokolu datových proudů, která se aktivuje při zadávání událostí protokolu aktivit, které odpovídá vašim kritériím filtru. Tyto výstrahy mají pouze jeden stav, "Aktivováno". Modul upozornění jednoduše použije kritéria filtru pro každou novou událost. Nevyhledává starší položky. Tyto výstrahy vás mohou upozornit, když dojde k novému incidentu stavu služby nebo když uživatel nebo aplikace provede operaci ve vašem předplatném, například "Odstranit virtuální počítač".

Pro data protokolu prostředků, která jsou k dispozici prostřednictvím Služby Azure Monitor, směrovat data do Analýzy protokolů a použít výstrahu dotazu protokolu. Log Analytics nyní používá [novou metodu výstrahy](../../azure-monitor/platform/alerts-overview.md) 

Následující diagram shrnuje zdroje dat v Azure Monitoru a koncepčně, jak můžete upozornit na tato data.

![Vysvětlení upozornění](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomie výstrah (klasické)
Azure používá následující termíny k popisu klasických výstrah a jejich funkcí:
* **Výstraha** - definice kritérií (jedno nebo více pravidel nebo podmínek), která se aktivuje při splnění.
* **Aktivní** - stav, kdy jsou splněna kritéria definovaná klasickou výstrahou.
* **Vyřešeno** - stav, kdy kritéria definovaná klasickou výstrahou již není splněna poté, co byla splněna dříve.
* **Oznámení** - akce přijatá na základě klasickévýstrahy, která se stává aktivní.
* **Akce** - konkrétní hovor odeslaný příjemci oznámení (například e-mailem adresu nebo odesláním na adresu URL webhooku). Oznámení mohou obvykle vyvolat více akcí.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak se zobrazí oznámení od klasické výstrahy Azure Monitoru?
Historicky výstrahy Azure z různých služeb používaly své vlastní integrované metody oznámení. 

Azure Monitor vytvořil opakovaně použitelné seskupení oznámení s názvem *skupiny akcí*. Skupiny akcí určují sadu příjemců pro oznámení. Kdykoli je aktivována výstraha, která odkazuje na skupinu akcí, všichni příjemci obdrží toto oznámení. Skupiny akcí umožňují znovu použít seskupení příjemců (například seznam techniků na zavolání) v mnoha objektech výstrah. Skupiny akcí podporují oznámení odesláním na adresu URL webhooku kromě e-mailových adres, čísel SMS a řady dalších akcí.  Další informace naleznete v [tématu skupiny akcí](../../azure-monitor/platform/action-groups.md). 

Starší klasické výstrahy protokolu aktivit používají skupiny akcí.

Starší upozornění metriky však nepoužívají skupiny akcí. Místo toho můžete nakonfigurovat následující akce: 
- Odešlete e-mailová oznámení správci služby, spolusprávcům nebo dalším zadaným e-mailovým adresám.
- Zavolejte webhook, který umožňuje spustit další akce automatizace.

Webhooky umožňují automatizaci a nápravu, například pomocí:
- Runbook Azure Automation
- Funkce Azure Functions
- Aplikace Azure logiky
- služba třetí strany

## <a name="next-steps"></a>Další kroky
Získejte informace o pravidlech výstrah a jejich konfiguraci pomocí:

* Další informace o [metrikách](data-platform.md)
* Konfigurace [klasických upozornění na metriky přes portál Azure](alerts-classic-portal.md)
* Konfigurace [klasického prostředí Upozornění metrik powershellu](alerts-classic-portal.md)
* Konfigurace [klasického rozhraní příkazového řádku upozornění na metriky (CLI)](alerts-classic-portal.md)
* Konfigurace [klasické metriky výstrahy Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Další informace o [protokolu aktivit](platform-logs-overview.md)
* Konfigurace [výstrah protokolu aktivit prostřednictvím portálu Azure](activity-log-alerts.md)
* Konfigurace [výstrah protokolu aktivit prostřednictvím Správce prostředků](alerts-activity-log.md)
* Kontrola [schématu webhooku výstrahy protokolu aktivit](activity-log-alerts-webhook.md)
* Další informace o [skupinách akcí](action-groups.md)
* Konfigurace [novějších výstrah](alerts-metric.md)
