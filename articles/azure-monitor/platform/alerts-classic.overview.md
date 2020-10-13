---
title: Přehled klasických výstrah v Azure Monitor
description: Klasické výstrahy jsou zastaralé. Výstrahy umožňují sledovat metriky prostředků Azure, události nebo protokoly a být oznámeny, pokud je splněna podmínka, kterou zadáte.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: c7dfbd60b7a668b849c3150bb16e40285c345634
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844109"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Co jsou klasická upozornění v Microsoft Azure?

> [!NOTE]
> Tento článek popisuje, jak vytvořit starší klasické výstrahy metrik. Azure Monitor teď podporuje [novější výstrahy na metriky téměř v reálném čase a nové prostředí výstrah](./alerts-overview.md). Klasické výstrahy jsou [vyřazeny](./monitoring-classic-retirement.md), i když jsou v omezeném rozsahu používány pro prostředky, které ještě nepodporují nové výstrahy.
>

Výstrahy umožňují konfigurovat podmínky pro data a budou oznámeny, když se podmínky shodují s nejnovějšími daty monitorování.

## <a name="old-and-new-alerting-capabilities"></a>Staré a nové možnosti upozorňování

V posledních Azure Monitor měly Application Insights, Log Analytics a Service Health samostatné možnosti upozorňování. Přesčas, Azure zlepšil a kombinuje jak uživatelské rozhraní, tak i různé metody upozorňování. Konsolidace se pořád zpracovává.

Klasické výstrahy můžete zobrazit jenom na obrazovce uživatele klasické výstrahy na webu Azure Portal. Tuto obrazovku získáte z tlačítka **Zobrazit klasické výstrahy** na obrazovce výstrahy. 

 ![Volby výstrah v Azure Portal](media/alerts-classic.overview/monitor-alert-screen2.png)

Nové uživatelské prostředí výstrahy má oproti klasickému prostředí výstrah následující výhody:
- **Lepší systém oznámení** – všechny novější výstrahy používají skupiny akcí s názvem skupiny oznámení a akce, které se dají znovu použít v několika výstrahách. Klasické výstrahy metrik a starší výstrahy Log Analytics nepoužívají skupiny akcí.
- **Jednotné prostředí pro vytváření obsahu** – veškerá upozornění pro metriky, protokoly a protokol aktivit napříč Azure Monitor, Log Analytics a Application Insights je na jednom místě.
- **Zobrazit aktivované výstrahy Log Analytics v Azure Portal** – nyní můžete ve svém předplatném zobrazit také aktivované výstrahy Log Analytics. Dříve byly na samostatném portálu.
- **Oddělení aktivovaných výstrah a pravidel upozornění** – pravidla upozornění (definice podmínky, která aktivuje výstrahu) a aktivované výstrahy (instance pravidla výstrahy se vyvolala) jsou odlišeny, takže provozní a konfigurační zobrazení jsou oddělené.
- **Lepší pracovní postup** – nové prostředí pro vytváření výstrah vás provede uživatelem na základě procesu konfigurace pravidla upozornění, což usnadňuje zjištění správných věcí, na které se zobrazí upozornění.
- **Konsolidace inteligentních výstrah** a **Nastavení upozornění**  – novější výstrahy zahrnují funkce automatického seskupování ukazující podobná upozornění, aby se snížilo přetížení v uživatelském rozhraní. 

Novější výstrahy metriky mají oproti klasickým výstrahám metrik tyto výhody:
- **Vylepšená latence**: novější upozornění na metriku můžete spouštět stejně často, jako každou jednu minutu. Starší výstrahy metriky se vždycky spouštějí v intervalu 5 minut. Novější výstrahy zvýšily menší zpoždění od výskytu problému s oznámením nebo akcí (3 až 5 minut). Starší výstrahy mají v závislosti na typu 5 až 15 minut.  Výstrahy protokolu obvykle mají prodlevu 10 až 15 minut, protože doba potřebná k ingestování protokolů, ale novější metody zpracování zkracuje tento čas. 
- **Podpora**multidimenzionálních metrik: můžete upozorňovat na multidimenzionální metriky, které vám umožní monitorovat zajímavý segment metriky.
- **Větší kontrola nad podmínkami metriky**: můžete definovat rozsáhlejší pravidla upozornění. Novější výstrahy podporují monitorování maximálních, minimálních, průměrných a celkových hodnot metrik.
- **Kombinované monitorování více metrik**: můžete monitorovat více metrik (aktuálně až dvou metrik) s jedním pravidlem. Výstraha se aktivuje, pokud obě metriky narušují jejich příslušné prahové hodnoty za zadané časové období.
- **Lepší systém oznámení**: všechny novější výstrahy používají [skupiny akcí](./action-groups.md)s názvem skupiny oznámení a akce, které se dají znovu použít v několika výstrahách.  Klasické výstrahy metrik a starší výstrahy Log Analytics nepoužívají skupiny akcí. 
- **Metriky z protokolů** (Public Preview): data protokolu, která se budou Log Analytics, se teď dají extrahovat a převést na Azure monitor metriky a pak se na ně upozornit stejně jako na jiné metriky. Podívejte se na téma [výstrahy (Classic)]() pro terminologii specifická pro klasické výstrahy. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klasické výstrahy týkající se Azure Monitor dat
K dispozici jsou dva typy klasických výstrah – výstrahy metrik a výstrahy protokolu aktivit.

* **Klasické výstrahy metriky** – Tato výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Výstraha vygeneruje oznámení v případě překročení této prahové hodnoty a splnění podmínky upozornění. V tomto okamžiku se výstraha považuje za aktivovanou. Vygeneruje další oznámení, když je "Vyřešený" – to znamená, když je prahová hodnota znovu překročena a podmínka již není splněna.

* **Klasické výstrahy protokolu aktivit** – výstraha protokolu streamování, která se spouští na záznamu události protokolu aktivit, který odpovídá kritériím filtru. Tyto výstrahy mají pouze jeden stav "aktivováno". Modul výstrah jednoduše aplikuje kritéria filtru na každou novou událost. Nehledá starší položky. Tyto výstrahy vás můžou informovat, když dojde k novému Service Health incidentu nebo pokud uživatel nebo aplikace provádí v předplatném určitou operaci, například "odstranění virtuálního počítače".

Data protokolu prostředků jsou dostupná prostřednictvím Azure Monitor, směrovat je do Log Analytics a používat upozornění na dotaz na protokol. Log Analytics teď používá [novou metodu upozorňování](./alerts-overview.md) . 

Následující diagram shrnuje zdroje dat v Azure Monitor a koncepční, jak můžete tato data z těchto dat vyvarovat.

![Vysvětlení výstrah](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomie výstrah (klasická)
Azure používá následující výrazy k popisu klasických výstrah a jejich funkcí:
* **Výstraha** – definice kritérií (jedno nebo více pravidel nebo podmínek), která se aktivuje, když dojde k jejich splnění.
* **Aktivní** – stav při splnění kritérií definovaných pomocí klasické výstrahy.
* **Vyřešený** – stav, když kritéria definovaná klasickou výstrahou už nejsou splněné, až se dřív splní.
* **Oznámení** – akce provedená na základě klasické výstrahy se stane aktivní.
* **Action** – konkrétní volání odeslané příjemci oznámení (například e-mailová adresa nebo odeslání na adresu URL Webhooku). Oznámení mohou obvykle aktivovat více akcí.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Návody dostávat oznámení z Azure Monitor klasické výstrahy?
Historické výstrahy Azure z různých služeb používaly vlastní integrované metody oznámení. 

Azure Monitor vytvořili opakovaně použitelné seskupení oznámení nazvané *skupiny akcí*. Skupiny akcí určují sadu přijímačů pro oznámení. Kdykoli se aktivuje výstraha, která odkazuje na skupinu akcí, všichni příjemci obdrží toto oznámení. Skupiny akcí umožňují znovu použít seskupení přijímačů (například seznam techniků na základě volání) v mnoha objektech výstrah. Skupiny akcí podporují oznámení prostřednictvím publikování na adresu URL Webhooku kromě e-mailových adres, čísel SMS a mnoha dalších akcí.  Další informace najdete v tématu [skupiny akcí](./action-groups.md). 

Starší klasické výstrahy protokolu aktivit používají skupiny akcí.

Starší výstrahy metriky ale nepoužívají skupiny akcí. Místo toho můžete nakonfigurovat následující akce: 
- Odesílání e-mailových oznámení správci služby, spolusprávcům nebo dalším e-mailovým adresám, které určíte.
- Zavolejte Webhook, který umožňuje spustit další akce automatizace.

Webhooky umožňují automatizaci a nápravu, například pomocí:
- Runbook Azure Automation
- Funkce Azure
- Aplikace logiky Azure
- Služba třetí strany

## <a name="next-steps"></a>Další kroky
Získejte informace o pravidlech výstrah a jejich konfiguraci pomocí:

* Další informace o [metrikách](data-platform.md)
* Konfigurace [klasických upozornění na metriky prostřednictvím Azure Portal](alerts-classic-portal.md)
* Konfigurace [klasického prostředí PowerShell pro výstrahy metrik](alerts-classic-portal.md)
* Konfigurace [klasického rozhraní příkazového řádku pro výstrahy metriky (CLI)](alerts-classic-portal.md)
* Konfigurace [klasických výstrah metrik Azure Monitor REST API](/rest/api/monitor/alertrules)
* Další informace o [protokolu aktivit](platform-logs-overview.md)
* Konfigurace [upozornění protokolu aktivit prostřednictvím Azure Portal](activity-log-alerts.md)
* Konfigurace [upozornění protokolu aktivit prostřednictvím Správce prostředků](alerts-activity-log.md)
* Kontrola [schématu Webhooku upozornění protokolu aktivit](activity-log-alerts-webhook.md)
* Další informace o [skupinách akcí](action-groups.md)
* Konfigurace [novějších výstrah](alerts-metric.md)

