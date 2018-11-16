---
title: Řešení Agent Health v Azure | Dokumentace Microsoftu
description: Tento článek je určený k vám pomůžou porozumět způsobu použití tohoto řešení monitorovat stav agentů odesílajících sestavy přímo do Log Analytics nebo System Center Operations Manager.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: 5823e6e3bf2614bf834a9ab64c3403829d58a779
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715217"
---
#  <a name="agent-health-solution-in-azure"></a>Řešení Agent Health v Azure
Řešení Agent Health v Azure vám pomůže pochopit, u všech agentů odesílajících sestavy přímo do pracovního prostoru Log Analytics nebo skupinu pro správu System Center Operations Manager připojení k Log Analytics, která jsou reagovat a odešlete provozní data.  Můžete také sledovat, kolik agentů je nasazených a jak jsou geograficky distribuováni, a provádět další dotazy, abyste si udrželi přehled o distribuci agentů nasazených v Azure, dalších cloudových prostředích nebo místně.    

## <a name="prerequisites"></a>Požadavky
Před nasazením tohoto řešení potvrďte, že aktuálně podporujete [agentů Windows](../../log-analytics/log-analytics-windows-agent.md) sestavy do pracovního prostoru Log Analytics nebo odesílajících sestavy do [skupinu pro správu Operations Manageru](../../log-analytics/log-analytics-om-agents.md) integrované s váš pracovní prostor.    

## <a name="solution-components"></a>Součásti řešení
Toto řešení se skládá z následujících prostředků, které se přidají do vašeho pracovního prostoru, a přímo připojených agentů nebo skupiny pro správu připojené k nástroji Operations Manager.

### <a name="management-packs"></a>Sady Management Pack
Pokud vaší skupině pro správu System Center Operations Manageru je připojený k pracovnímu prostoru Log Analytics, nainstaluje se následující sady management Pack v nástroji Operations Manager.  Tyto sady Management Pack se po přidání tohoto řešení nainstalují také na přímo připojené počítače s Windows. U těchto sad Management Pack není nutné nic konfigurovat ani spravovat.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Konfigurace
Přidejte řešení Agent Health do pracovního prostoru Log Analytics pomocí postupu popsaného v [přidat řešení](solutions.md). Není nutná žádná další konfigurace.


## <a name="data-collection"></a>Shromažďování dat
### <a name="supported-agents"></a>Podporovaní agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows | Ano | Události prezenčního signálu se shromažďují z přímých agentů systému Windows.|
| Skupina pro správu nástroje System Center Operations Manager | Ano | Události prezenčního signálu se shromažďují z agentů odesílajících sestavy do skupiny pro správu každých 60 sekund a pak se předávají do Log Analytics. Přímé připojení z agentů nástroje Operations Manager ke službě Log Analytics není potřeba. Data událostí prezenčního signálu se předávají ze skupiny pro správu do úložiště Log Analytics.|

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení do pracovního prostoru Log Analytics **stav agenta** se přidá dlaždice na řídicí panel. Tato dlaždice ukazuje celkový počet agentů a počet nereagujících agentů za posledních 24 hodin.<br><br> ![Dlaždice řešení Agent Health na řídicím panelu](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kliknutím na dlaždici **Agent Health** otevřete řídicí panel **Agent Health**.  Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec obsahuje seznam prvních deseti událostí podle počtu, které splňují kritéria sloupce pro zadaný časový rozsah. Výběrem možnosti **Zobrazit všechno** v pravé dolní části každého sloupce nebo kliknutím na záhlaví sloupce můžete spustit prohledávání protokolu, které vám poskytne úplný seznam.

| Sloupec | Popis |
|--------|-------------|
| Počet agentů v průběhu času | Trend vývoje počtu linuxových agentů a agentů systému Windows za posledních sedm dnů.|
| Počet nereagujících agentů | Seznam agentů, kteří za posledních 24 hodin neodeslali prezenční signál.|
| Distribuce podle typu operačního systému | Rozdělení agentů systému Windows a linuxových agentů ve vašem prostředí.|
| Distribuce podle verze agenta | Rozdělení různých verzí agentů nainstalovaných ve vašem prostředí a jejich počet.|
| Distribuce podle kategorie agenta | Rozdělení různých kategorií agentů, kteří odesílají události prezenčního signálu: přímí agenti, agenti nástroje Operations Manager nebo server pro správu nástroje Operations Manager.|
| Distribuce podle skupiny pro správu | Rozdělení různých skupin pro správu nástroje SCOM ve vašem prostředí.|
| Geografické umístění agentů | Rozdělení různých zemí, ve kterých máte agenty, a celkový počet agentů nainstalovaných v každé zemi.|
| Počet nainstalovaných bran | Počet serverů, které mají nainstalovanou bránu Log Analytics a seznam těchto serverů.|

![Ukázka řídicího panelu řešení Agent Health](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Toto řešení vytvoří jeden typ záznamu v pracovním prostoru Log Analytics.  

### <a name="heartbeat-records"></a>Záznamy prezenčního signálu
Vytvoří se záznam typu **Prezenční signál**.  Vlastnosti záznamů tohoto typu uvádí následující tabulka.  

| Vlastnost | Popis |
| --- | --- |
| Typ | *Heartbeat* (Prezenční signál)|
| Kategorie | Hodnota je *Direct Agent* (Přímý agent), *SCOM Agent* (Agent nástroje SCOM) nebo *SCOM Management Server* (Server pro správu nástroje SCOM).|
| Počítač | Název počítače.|
| OSType | Operační systém Windows nebo Linux.|
| OSMajorVersion | Hlavní verze operačního systému.|
| OSMinorVersion | Podverze operačního systému.|
| Verze | Verze agenta log Analytics nebo agenta nástroje Operations Manager.|
| SCAgentChannel | Hodnota je *Direct* (Přímý) nebo *SCManagementServer* (Server pro správu nástroje SCOM).|
| IsGatewayInstalled | Pokud je nainstalovaná brána Log Analytics, je hodnota *true*, v opačném případě je hodnota *false*.|
| ComputerIP | IP adresa počítače.|
| RemoteIPCountry | Zeměpisné umístění, kde je počítač nasazený.|
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager.|
| SourceComputerId | Jedinečné ID počítače.|
| RemoteIPLongitude | Zeměpisná délka zeměpisného umístění počítače.|
| RemoteIPLatitude | Zeměpisná šířka zeměpisného umístění počítače.|

Každý agent generování sestav použít server pro správu nástroje Operations Manager bude odesílat dva prezenční signály a hodnota vlastnosti SCAgentChannel bude zahrnovat **přímé** a **SCManagementServer** v závislosti na tom, jaký protokol Zdroje dat Analytics a řešení, které jste povolili ve vašem předplatném. Pokud jste si možná Vzpomínáte, data z řešení se buď odesílají přímo ze serveru pro správu Operations Manageru ke službě Log Analytics nebo z důvodu objem dat shromážděných v agentovi, se odesílají přímo z agenta ke službě Log Analytics. U událostí prezenčního signálu, které mají hodnotu **SCManagementServer**, je hodnota ComputerIP IP adresou serveru pro správu, protože ten data ve skutečnosti odesílá.  U prezenčních signálů, které mají vlastnost SCAgentChannel nastavenou na hodnotu **Direct**, to je veřejná IP adresa agenta.  

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky prohledávání protokolu pro záznamy shromážděné tímto řešením.

| Dotaz | Popis |
|:---|:---|
| Heartbeat &#124; distinct Computer |Celkový počet agentů |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Počet nereagujících agentů za posledních 24 hodin |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Počet nereagujících agentů za posledních 15 minut |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Online počítače (za posledních 24 hodin) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Celkový počet agentů, kteří byli v průběhu posledních 30 minut offline (za posledních 24 hodin) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Získání trendu vývoje počtu agentů v průběhu času podle typu operačního systému|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribuce podle typu operačního systému |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribuce podle verze agenta |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribuce podle kategorie agenta |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribuce podle skupiny pro správu |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geografické umístění agentů |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Počet nainstalovaných bran Log Analytics |




## <a name="next-steps"></a>Další postup

* Podrobnosti o generování upozornění ze služby Log Analytics najdete v tématu [Upozornění v Log Analytics](../../monitoring-and-diagnostics/monitoring-overview-alerts.md). 
