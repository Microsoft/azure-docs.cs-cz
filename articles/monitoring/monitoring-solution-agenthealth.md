---
title: Řešení Agent Health v OMS | Dokumentace Microsoftu
description: Tento článek vám objasní, jak pomocí tohoto řešení monitorovat stav agentů odesílajících sestavy přímo do OMS nebo nástroje System Center Operations Manager.
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
ms.openlocfilehash: 8a6275748c82fbb448a767af690121abbd1f669a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346920"
---
#  <a name="agent-health-solution-in-oms"></a>Řešení Agent Health v OMS
Řešení Agent Health v OMS vám pomůže rozpoznat, kteří z agentů, odesílajících sestavy přímo do pracovního prostoru OMS nebo do skupiny pro správu nástroje System Center Operations Manager připojené k OMS, nereagují a odesílají provozní data.  Můžete také sledovat, kolik agentů je nasazených a jak jsou geograficky distribuováni, a provádět další dotazy, abyste si udrželi přehled o distribuci agentů nasazených v Azure, dalších cloudových prostředích nebo místně.    

## <a name="prerequisites"></a>Požadavky
Před nasazením tohoto řešení potvrďte, že aktuálně podporujete [agenty systému Windows](../log-analytics/log-analytics-windows-agent.md) odesílající sestavy to pracovního prostoru OMS nebo do [skupiny pro správu nástroje Operations Manager](../log-analytics/log-analytics-om-agents.md) integrované s pracovním prostorem OMS.    

## <a name="solution-components"></a>Součásti řešení
Toto řešení se skládá z následujících prostředků, které se přidají do vašeho pracovního prostoru, a přímo připojených agentů nebo skupiny pro správu připojené k nástroji Operations Manager.

### <a name="management-packs"></a>Sady Management Pack
Pokud je vaše skupina pro správu System Center Operations Manageru připojená k pracovnímu prostoru OMS, do Operations Manageru se nainstalují následující sady Management Pack.  Tyto sady Management Pack se po přidání tohoto řešení nainstalují také na přímo připojené počítače s Windows. U těchto sad Management Pack není nutné nic konfigurovat ani spravovat.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Další informace o způsobu, jakým se aktualizují sady pro správu řešení, najdete v tématu [Připojení Operations Manageru ke službě Log Analytics](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Konfigurace
Přidejte řešení Agent Health do pracovního prostoru OMS pomocí procesu popsaného v tématu o [přidání řešení](../log-analytics/log-analytics-add-solutions.md). Není nutná žádná další konfigurace.


## <a name="data-collection"></a>Shromažďování dat
### <a name="supported-agents"></a>Podporovaní agenti
Následující tabulka popisuje připojené zdroje, které toto řešení podporuje.

| Připojený zdroj | Podporováno | Popis |
| --- | --- | --- |
| Agenti systému Windows | Ano | Události prezenčního signálu se shromažďují z přímých agentů systému Windows.|
| Skupina pro správu nástroje System Center Operations Manager | Ano | Události prezenčního signálu se shromažďují z agentů odesílajících sestavy do skupiny pro správu každých 60 sekund a pak se předávají do Log Analytics. Přímé připojení z agentů nástroje Operations Manager ke službě Log Analytics není potřeba. Data událostí prezenčního signálu se předávají ze skupiny pro správu do úložiště Log Analytics.|

## <a name="using-the-solution"></a>Použití řešení
Když přidáte řešení do pracovního prostoru OMS, na řídicí panel OMS se přidá dlaždice **Agent Health**. Tato dlaždice ukazuje celkový počet agentů a počet nereagujících agentů za posledních 24 hodin.<br><br> ![Dlaždice řešení Agent Health na řídicím panelu](./media/monitoring-solution-agenthealth/agenthealth-solution-tile-homepage.png)

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
| Počet nainstalovaných bran | Počet serverů s nainstalovanou bránou OMS a seznam těchto serverů.|

![Ukázka řídicího panelu řešení Agent Health](./media/monitoring-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Řešení vytváří v úložišti OMS jeden typ záznamu.  

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
| Verze | Verze agenta OMS nebo agenta nástroje Operations Manager.|
| SCAgentChannel | Hodnota je *Direct* (Přímý) nebo *SCManagementServer* (Server pro správu nástroje SCOM).|
| IsGatewayInstalled | Pokud je nainstalovaná brána OMS, hodnota je *true*, jinak je hodnota *false*.|
| ComputerIP | IP adresa počítače.|
| RemoteIPCountry | Zeměpisné umístění, kde je počítač nasazený.|
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager.|
| SourceComputerId | Jedinečné ID počítače.|
| RemoteIPLongitude | Zeměpisná délka zeměpisného umístění počítače.|
| RemoteIPLatitude | Zeměpisná šířka zeměpisného umístění počítače.|

Každý agent odesílající sestavy na server pro správu nástroje Operations Manager bude odesílat dva prezenční signály a hodnota vlastnosti SCAgentChannel bude zahrnovat **Direct** i **SCManagementServer** v závislosti na tom, jaká řešení a jaké zdroje dat Log Analytics jste ve svém předplatném OMS povolili. Jestli si vzpomínáte, data z řešení se buď odesílají přímo ze serveru pro správu nástroje Operations Manager do webové služby OMS, nebo se kvůli objemu dat shromážděných v agentovi odesílají přímo z agenta do webové služby OMS. U událostí prezenčního signálu, které mají hodnotu **SCManagementServer**, je hodnota ComputerIP IP adresou serveru pro správu, protože ten data ve skutečnosti odesílá.  U prezenčních signálů, které mají vlastnost SCAgentChannel nastavenou na hodnotu **Direct**, to je veřejná IP adresa agenta.  

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
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Počet nainstalovaných bran OMS |




## <a name="next-steps"></a>Další postup

* Podrobnosti o generování upozornění ze služby Log Analytics najdete v tématu [Upozornění v Log Analytics](../log-analytics/log-analytics-alerts.md).
