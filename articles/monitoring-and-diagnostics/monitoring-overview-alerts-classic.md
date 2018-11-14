---
title: Přehled klasických upozornění v Microsoft Azure a Azure Monitor
description: Upozornění Classic se přestanou používat. Výstrahy umožňují monitorovat metriky prostředků Azure, události nebo protokoly a upozornění, když je splněna podmínka, kterou zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: e7622ffcfbaca4f83f0b5233159fc94720148365
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614136"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co jsou upozornění v Microsoft Azure classic?

> [!NOTE]
> Tento článek popisuje, jak vytvářet starší klasické metriky upozornění. Azure teď podporuje monitorování [novějších upozornění metrik v reálném čase a nové prostředí upozornění](monitoring-overview-alerts.md). 
>

Výstrahy umožňují nakonfigurovat podmínky nad daty a informováni o podmínky odpovídají data nejnovější monitorování.

## <a name="old-and-new-alerting-capabilities"></a>Staré a nové možnosti upozorňování

V posledních monitorování Azure Application Insights, Log Analytics a stavy služeb bylo samostatné možnosti výstrah. Azure přesčas, vylepšené a kombinaci uživatelského rozhraní a výstrahy různými způsoby. Stále probíhá sloučení. Výstrahy

Upozornění classic můžete zobrazit pouze na obrazovce uživatele klasických upozornění na webu Azure Portal. Získat z této obrazovky **zobrazit upozornění classic** tlačítko na obrazovce oznámení. 

 ![Upozornění možností na webu Azure portal](./media/monitoring-overview-alerts-classic/monitor-alert-screen2.png)

Nové prostředí upozornění uživatele má tyto výhody nad komfortem při upozornění classic:
-   **Lepší systému oznámení** – všechny novější upozornění použití skupin akcí, které jsou pojmenované skupiny oznámení a akce, které lze znovu použít ve více výstrah. Klasického upozornění metrik a starší upozornění Log Analytics není použití skupin akcí.
-   **A sjednocené prostředí pro vytváření** – všechny výstrahy vytvoření metriky, protokoly a aktivity přihlášení přes Azure Monitor, Log Analytics a Application Insights je na jednom místě.
-   **Zobrazení aktivuje upozornění Log Analytics na webu Azure portal** – můžete teď také viz aktivuje upozornění Log Analytics v rámci vašeho předplatného. Dříve byly tyto samostatné portálu.
-   **Oddělení aktivovaná upozornění a pravidla upozornění** – pravidla výstrah (definice podmínku, která aktivuje výstrahu) a jsou rozlišené vyvolané výstrahy (instance jeho spuštění pravidla upozornění), tak zobrazení provozu a konfigurace jsou oddělené.
-   **Lepší workflow** - nové výstrahy vytváření uživatele podél proces konfigurace pravidla upozornění, který zjednodušuje zjišťování se správnými věcmi na problém upozorněni v Průvodci prostředí.
-   **Inteligentní konsolidace výstrah** a **nastavení výstrah stavu** -novější výstrahy obsahují funkci seskupování automaticky zobrazují podobná oznámení dohromady a snížit přetížení v uživatelském rozhraní. 

Novějších upozornění metrik nabízí tyto výhody prostřednictvím klasického upozornění metrik:
-   **Vylepšené latence**: novějších upozornění metrik můžete spustit tak často, jak každou minutu. Upozornění na metriku starší vždy spustit s frekvencí 5 minut. Novější upozornění rostoucími menší prodlevou z výskytu problému oznámení nebo akce (3 až 5 minut). Starší výstrahy jsou 5 až 15 minut v závislosti na typu.  Upozornění protokolů mají obvykle 10 až 15 minut zpoždění kvůli době je trvá příjem protokolů, ale novější zpracování metody se zmenší tento čas. 
-   **Podpora pro vícerozměrné metriky**: může upozornit na jednodimenzionální metriky, které umožňuje sledovat zajímavé segmentu metriky.
-   **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidla upozornění. Novější upozornění podporují monitorování maximální minimální, průměrné a celkové hodnoty metriky.
-   **Kombinované monitorování několika metrik**: můžete sledovat několik metrik (v současné době až dvě metriky) s jedním pravidlem. Výstraha se aktivuje, pokud obě metriky porušení jejich příslušné prahové hodnoty pro zadané časové období.
-   **Lepší systému oznámení**: použít všechny novější upozornění [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md), které jsou pojmenované skupiny oznámení a akce, které lze znovu použít ve více výstrah.  Klasického upozornění metrik a starší upozornění Log Analytics není použití skupin akcí. 
-   **Metriky na základě protokolů** (public preview): data přicházející do Log Analytics můžete nyní protokolu extrahovat a převést na metrik Azure monitoru a pak zobrazí upozornění na stejně jako jiné metriky. Zobrazit [upozornění (klasická)](monitoring-overview-alerts-classic.md) pro konkrétní upozornění classic terminologie. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasických upozornění na data Azure Monitor
Existují dva typy upozornění classic k dispozici – upozornění na metriky a upozornění protokolu aktivit.

* **Klasické metriky upozornění** – Tato výstraha se aktivuje, když hodnota zadaného metrika překročí mezní hodnotu, která přiřadíte. Výstrahy generuje upozornění, když "Aktivaci upozornění" (Pokud se překročí prahovou hodnotu a je splněná podmínka výstrahy). Další oznámení vygeneruje, jestliže je se "Vyřešeno" (Pokud je znovu překročí prahovou hodnotu a už není splněná podmínka).

* **Upozornění protokolu aktivit Classic** – datové proudy upozornění na protokol, který se aktivuje při vygenerování události protokolu aktivit, že odpovídá filtru kritérií, které jste přiřadili. Tyto výstrahy obsahují pouze jeden stav "Aktivovaná," protože modul pro správu oznámení kritéria filtru, která jednoduše platí pro všechny nové události. Tyto výstrahy je možné, abyste byli informováni v případě nového incidentu Service Health, nebo když uživatele nebo aplikace provádí operace ve vašem předplatném, například "Odstranit virtuální počítač."

Pro diagnostický protokol data k dispozici prostřednictvím služby Azure Monitor směrovat data do Log Analytics (dříve OMS) a používat upozornění dotazu Log Analytics. Protokolovat Analytics teď používá [nové výstrahy – metoda](monitoring-overview-alerts.md) 

Následující diagram obsahuje souhrn zdrojů dat v Azure Monitor a, koncepčně, jak může upozornit mimo tato data.

![Vysvětlení výstrah](./media/monitoring-overview-alerts-classic/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomie služby upozornění (klasická)
Azure používá k popisu klasických upozornění a jejich funkce následující podmínky:
* **Upozornění** – definice kritéria (jeden nebo více pravidel nebo podmínky), dojde k aktivaci při splnění.
* **Aktivní** – stav, když se splní kritéria definovaná klasického upozornění.
* **Vyřešené** – stav, když kritéria definovaná klasického upozornění už není splněná po dříve splněné.
* **Oznámení** – akce na základě z klasické výstrahu aktivovala.
* **Akce** -konkrétní volání odesílat příjemce oznámení (například poslat e-mailovou adresu nebo pro příspěvky na adresu URL webhooku). Oznámení obvykle může aktivovat více akcí.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak mohu dostávat oznámení z výstrahy monitorování Azure classic?
V minulosti upozornění z různých služeb Azure použít své vlastní metody předdefinovaných oznámení. 

Azure Monitor vytvořit opakovaně použitelné oznámení seskupení volané *skupiny akcí*. Skupiny akcí zadat sadu příjemců pro oznámení a pokaždé, když se aktivuje výstrahu, která odkazuje na skupinu akcí, všichni příjemci dostávat oznámení. Skupiny akcí vám umožní znovu použít seskupení příjemce (třeba seznamu na volání engineer) napříč celou řadu výstrah objektů. Skupiny akcí oznámení podporovat odesláním na adresu URL webhooku, kromě e-mailové adresy, SMS čísel a dalších akcí.  Další informace najdete v tématu [skupiny akcí](monitoring-action-groups.md). 

Starší klasických upozornění protokolu aktivit použití skupin akcí.

Starší upozornění metrik nepoužívejte skupin akcí. Místo toho můžete nakonfigurovat následující akce: 
- Odeslání e-mailová oznámení pro správce služeb, spolupracujících správců nebo další e-mailové adresy, které zadáte.
- Voláním webhooku, která umožňuje spustit další automatizace akcí.

Webhooky umožňuje automatizaci a nápravu, například pomocí:
    - Runbook Azure Automation
    - Funkce Azure Functions
    - Azure Logic App
    - Služby třetích stran

## <a name="next-steps"></a>Další postup
Získání informací o pravidla upozornění a nakonfigurovaly pomocí:

* Další informace o [metriky](../monitoring/monitoring-data-collection.md)
* Konfigurace [klasických upozornění na metriku pomocí webu Azure portal](alert-metric-classic.md)
* Konfigurace [classic PowerShell upozornění metriky](alert-metric-classic.md)
* Konfigurace [klasické rozhraní příkazového řádku upozornění metriky (CLI)](alert-metric-classic.md)
* Konfigurace [klasické metriky upozornění monitorování rozhraní REST API Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Další informace o [protokolu aktivit](monitoring-overview-activity-logs.md)
* Konfigurace [upozornění protokolu aktivit prostřednictvím webu Azure portal](monitoring-activity-log-alerts.md)
* Konfigurace [upozornění protokolu aktivit prostřednictvím Resource Manageru](alert-activity-log.md)
* Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](monitoring-activity-log-alerts-webhook.md)
* Další informace o [skupiny akcí](monitoring-action-groups.md)
* Konfigurace [novější upozornění](alert-metric.md)
