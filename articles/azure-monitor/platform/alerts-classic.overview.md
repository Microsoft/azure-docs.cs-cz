---
title: Přehled klasických upozornění v Microsoft Azure a Azure Monitor
description: Upozornění Classic se přestanou používat. Výstrahy umožňují monitorovat metriky prostředků Azure, události nebo protokoly a upozornění, když je splněna podmínka, kterou zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/29/2018
ms.author: robb
ms.openlocfilehash: 596b7eb53d82f8eee7da439314994fd0951c7e64
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661713"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co jsou upozornění v Microsoft Azure classic?

> [!NOTE]
> Tento článek popisuje, jak vytvářet starší klasické metriky upozornění. Azure teď podporuje monitorování [novějších upozornění metrik v reálném čase a nové prostředí upozornění](../../azure-monitor/platform/alerts-overview.md). Upozornění Classic jsou [naplánované nebudou se dál nabízet](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).  
>

Výstrahy umožňují nakonfigurovat podmínky nad daty a informováni o podmínky odpovídají data nejnovější monitorování.

## <a name="old-and-new-alerting-capabilities"></a>Staré a nové možnosti upozorňování

V minulosti, které Azure Monitor, Application Insights, Log Analytics a stavy služeb samostatných upozorňování. Azure přesčas, vylepšené a kombinaci uživatelského rozhraní a výstrahy různými způsoby. Stále probíhá sloučení. Výstrahy

Upozornění classic můžete zobrazit pouze na obrazovce uživatele klasických upozornění na webu Azure Portal. Získat z této obrazovky **zobrazit upozornění classic** tlačítko na obrazovce oznámení. 

 ![Upozornění možností na webu Azure portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Nové prostředí upozornění uživatele má tyto výhody nad komfortem při upozornění classic:
-   **Lepší systému oznámení** – všechny novější upozornění použití skupin akcí, které jsou pojmenované skupiny oznámení a akce, které lze znovu použít ve více výstrah. Klasického upozornění metrik a starší upozornění Log Analytics není použití skupin akcí.
-   **A sjednocené prostředí pro vytváření** – všechny výstrahy vytvoření metriky, protokoly a aktivity přihlášení přes Azure Monitor, Log Analytics a Application Insights je na jednom místě.
-   **Zobrazení aktivuje upozornění Log Analytics na webu Azure portal** – můžete teď také viz aktivuje upozornění Log Analytics v rámci vašeho předplatného. Dříve byly tyto samostatné portálu.
-   **Oddělení aktivovaná upozornění a pravidla upozornění** – pravidla výstrah (definice podmínku, která aktivuje výstrahu) a jsou rozlišené vyvolané výstrahy (instance jeho spuštění pravidla upozornění), tak zobrazení provozu a konfigurace jsou oddělené.
-   **Lepší workflow** - nové výstrahy vytváření uživatele podél proces konfigurace pravidla upozornění, který zjednodušuje zjišťování se správnými věcmi na problém upozorněni v Průvodci prostředí.
-   **Inteligentní konsolidace výstrah** a **nastavení výstrah stavu** -novější výstrahy obsahují funkci seskupování automaticky zobrazují podobná oznámení dohromady a snížit přetížení v uživatelském rozhraní. 

Novějších upozornění metrik nabízí tyto výhody prostřednictvím klasického upozornění metrik:
-   **Vylepšené latence**: Novějších upozornění metrik můžete spustit tak často, jak každou minutu. Upozornění na metriku starší vždy spustit s frekvencí 5 minut. Novější upozornění rostoucími menší prodlevou z výskytu problému oznámení nebo akce (3 až 5 minut). Starší výstrahy jsou 5 až 15 minut v závislosti na typu.  Upozornění protokolů mají obvykle 10 do 15 minut, než z důvodu čas potřebný k ingestování protokoly, ale novější zpracování metody se zmenší tento čas. 
-   **Podpora pro vícerozměrné metriky**: Může upozornit na jednodimenzionální metriky, které umožňuje sledovat zajímavé segmentu metriky.
-   **Větší kontrolu nad metriky podmínky**: Můžete definovat bohatší pravidla upozornění. Novější upozornění podporují monitorování maximální minimální, průměrné a celkové hodnoty metriky.
-   **Kombinované monitorování několika metrik**: Můžete sledovat několik metrik (v současné době až dvě metriky) s jedním pravidlem. Výstraha se aktivuje, pokud obě metriky porušení jejich příslušné prahové hodnoty pro zadané časové období.
-   **Lepší systému oznámení**: Použít všechny novější upozornění [skupiny akcí](../../azure-monitor/platform/action-groups.md), které jsou pojmenované skupiny oznámení a akce, které lze znovu použít ve více výstrah.  Klasického upozornění metrik a starší upozornění Log Analytics není použití skupin akcí. 
-   **Metriky na základě protokolů** (public preview): Protokol data přicházející do Log Analytics se teď dá extrahovat a převést na metrik Azure monitoru a potom zobrazí upozornění na stejně jako jiné metriky. Zobrazit [upozornění (klasická)](alerts-classic.overview.md) pro konkrétní upozornění classic terminologie. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasických upozornění na data Azure Monitor
Existují dva typy upozornění classic k dispozici – upozornění na metriky a upozornění protokolu aktivit.

* **Klasické metriky upozornění** – Tato výstraha se aktivuje, když hodnota zadaného metrika překročí mezní hodnotu, která přiřadíte. Výstrahy generuje upozornění, když překročí tuto prahovou hodnotu a je splněná podmínka výstrahy. Výstrahy od tohoto okamžiku se považuje za "Aktivní". Další oznámení vygeneruje, jestliže je se "Vyřešeno" – to znamená, pokud je znovu překročí prahovou hodnotu a už není splněná podmínka.

* **Upozornění protokolu aktivit Classic** – datové proudy upozornění protokolu, která se spustí při záznam událostí protokolu aktivit, která odpovídá zadaným kritériím filtru. Tyto výstrahy obsahují pouze jeden stav "Aktivovat". Modul upozornění jednoduše platí kritéria filtru pro všechny nové události. Prohledá najít starší položky. Tyto výstrahy vás můžou informovat při výskytu nový incident Service Health, nebo když uživatel nebo aplikace provádí operace ve vašem předplatném, například "odstranit virtuální počítač."

Pro diagnostický protokol data k dispozici prostřednictvím služby Azure Monitor směrovat data do Log Analytics (dříve OMS) a používat upozornění dotazu Log Analytics. Protokolovat Analytics teď používá [nové výstrahy – metoda](../../azure-monitor/platform/alerts-overview.md) 

Následující diagram obsahuje souhrn zdrojů dat v Azure Monitor a, koncepčně, jak může upozornit mimo tato data.

![Vysvětlení výstrah](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomie služby upozornění (klasická)
Azure používá k popisu klasických upozornění a jejich funkce následující podmínky:
* **Upozornění** – definice kritéria (jeden nebo více pravidel nebo podmínky), dojde k aktivaci při splnění.
* **Aktivní** – stav, když se splní kritéria definovaná klasického upozornění.
* **Vyřešené** – stav, když kritéria definovaná klasického upozornění už není splněná po dříve splněné.
* **Oznámení** – akce na základě z klasické výstrahu aktivovala.
* **Akce** -konkrétní volání odesílat příjemce oznámení (například poslat e-mailovou adresu nebo pro příspěvky na adresu URL webhooku). Oznámení obvykle může aktivovat více akcí.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Jak mohu dostávat oznámení z výstrahy monitorování Azure classic?
V minulosti upozornění z různých služeb Azure použít své vlastní metody předdefinovaných oznámení. 

Azure Monitor vytvořit opakovaně použitelné oznámení seskupení volané *skupiny akcí*. Skupiny akcí zadat sadu příjemců pro oznámení. Pokaždé, když se aktivuje výstrahu, která odkazuje na skupinu akcí, všichni příjemci dostávat oznámení. Skupiny akcí umožňuje znovu použít seskupení příjemce (třeba seznamu na volání engineer) napříč celou řadu výstrah objektů. Skupiny akcí oznámení podporovat odesláním na adresu URL webhooku, kromě e-mailové adresy, SMS čísel a dalších akcí.  Další informace najdete v tématu [skupiny akcí](../../azure-monitor/platform/action-groups.md). 

Starší klasických upozornění protokolu aktivit použití skupin akcí.

Starší upozornění metrik nepoužívejte skupin akcí. Místo toho můžete nakonfigurovat následující akce: 
- Odeslání e-mailová oznámení pro správce služeb, spolusprávci nebo další e-mailové adresy, které zadáte.
- Voláním webhooku, která umožňuje spustit další automatizace akcí.

Webhooky umožňuje automatizaci a nápravu, například pomocí:
    - Runbook Azure Automation
    - Funkce Azure Functions
    - Azure Logic App
    - Služby třetích stran

## <a name="next-steps"></a>Další postup
Získání informací o pravidla upozornění a nakonfigurovaly pomocí:

* Další informace o [metriky](../../azure-monitor/platform/data-collection.md)
* Konfigurace [klasických upozornění na metriku pomocí webu Azure portal](alerts-classic-portal.md)
* Konfigurace [classic PowerShell upozornění metriky](alerts-classic-portal.md)
* Konfigurace [klasické rozhraní příkazového řádku upozornění metriky (CLI)](alerts-classic-portal.md)
* Konfigurace [klasické metriky upozornění monitorování rozhraní REST API Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Další informace o [protokolu aktivit](../../azure-monitor/platform/activity-logs-overview.md)
* Konfigurace [upozornění protokolu aktivit prostřednictvím webu Azure portal](../../azure-monitor/platform/activity-log-alerts.md)
* Konfigurace [upozornění protokolu aktivit prostřednictvím Resource Manageru](alerts-activity-log.md)
* Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Další informace o [skupiny akcí](../../azure-monitor/platform/action-groups.md)
* Konfigurace [novější upozornění](../../azure-monitor/platform/alerts-metric.md)
