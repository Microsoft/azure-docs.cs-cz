---
title: Glosář monitorování virtuálních ploch Windows – Azure
description: Glosář pojmů a konceptů souvisejících s Azure Monitor pro virtuální počítače s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 38f34324a0cda518645f8b9231e08bafbd75cca0
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709527"
---
# <a name="azure-monitor-for-windows-virtual-desktop-glossary"></a>Glosář Azure Monitor pro virtuální počítače s Windows

Tento článek uvádí a stručně popisuje klíčové pojmy a koncepty týkající se Azure Monitor pro virtuální počítače s Windows (Preview).

## <a name="alerts"></a>Výstrahy

Na stránce Přehled se zobrazí všechny výstrahy Active Azure Monitor, které jste nakonfigurovali v rámci předplatného a jsou klasifikovány jako [závažnost 0](#severity-0-alerts) . Informace o tom, jak nastavit výstrahy, najdete v tématu [reakce na události s výstrahami Azure monitor](../azure-monitor/alerts/tutorial-response.md).

## <a name="available-sessions"></a>Dostupné relace

Dostupné relace zobrazují počet dostupných relací ve fondu hostitelů. Služba vypočítá toto číslo vynásobením počtu virtuálních počítačů (VM) maximálním počtem povolených relací na virtuální počítač a následným odečtením celkového počtu relací.

## <a name="connection-success"></a>Úspěšné připojení

Tato položka zobrazuje stav připojení. Možnost "připojení bylo úspěšné" znamená, že připojení se může připojit k hostiteli, jak bylo potvrzeno zásobníkem na tomto virtuálním počítači. Připojení se nepovedlo znamená, že se připojení nepovedlo kontaktovat hostitele.

## <a name="daily-active-users-dau"></a>Každodenní aktivní uživatelé (DAU)

Celkový počet uživatelů, kteří spustili relaci za posledních 24 hodin.

## <a name="daily-alerts"></a>Denní upozornění

Celkový počet výstrah aktivovaných každý den

## <a name="daily-connections-and-reconnections"></a>Denní připojení a opětovná připojení

Celkový počet připojení a zahájení nebo dokončení připojení během posledních 24 hodin.

## <a name="daily-connected-hours"></a>Počet hodin připojení za den

Celkový počet hodin strávený připojením k relaci mezi uživateli za posledních 24 hodin.

## <a name="diagnostics-and-errors"></a>Diagnostika a chyby

Pokud se zobrazí chyba nebo výstraha v Azure Monitor pro virtuální počítač s Windows, je rozdělená na tři věci:

- Typ aktivity: Tato kategorie představuje způsob, jakým je tato chyba zařazená do kategorií diagnostikou virtuálních klientů Windows. Kategorie jsou aktivity správy, informační kanály, připojení, registrace hostitelů, chyby a kontrolní body. Přečtěte si další informace o těchto kategoriích při [použití Log Analytics pro funkci diagnostiky](diagnostics-log-analytics.md).

- Druh: v této kategorii se zobrazuje umístění chyby. 

     - V rámci služby Virtual Desktop systému Windows došlo k chybám označeným jako služba nebo ServiceError = TRUE.
     - Chyby označené jako "Deployment" nebo označené "ServiceError = FALSE" se nacházejí mimo službu Virtual Desktop systému Windows.
     - Další informace o značce ServiceError najdete v tématu [běžné chybové scénáře](diagnostics-role-service.md#common-error-scenarios).

- Zdroj: Tato kategorie poskytuje konkrétnější popis místa, kde došlo k chybě.

     - Diagnostika: role služby zodpovědná za aktivitu služby monitoring a Reporting Service, která uživatelům umožňuje sledovat a diagnostikovat problémy s nasazením.

     - RDBroker: role služby odpovědná za orchestraci aktivit nasazení, udržování stavu objektů, ověřování ověřování a další.

     - RDGateway: role služby zodpovědná za zpracování síťového připojení mezi koncovými uživateli a virtuálními počítači.

     - RDStack: softwarová součást, která je nainstalovaná na vašich virtuálních počítačích, aby mohla komunikovat se službou virtuální plochy Windows.

     - Klient: software spuštěný na počítači koncového uživatele, který poskytuje rozhraní pro službu Virtual Desktop systému Windows. Zobrazuje seznam publikovaných prostředků a hostuje připojení ke vzdálené ploše, jakmile provedete výběr.

Každý problém s diagnostikou nebo chyba obsahuje zprávu, která vysvětluje, co se stalo špatným. Další informace o chybách při řešení potíží najdete v tématu [identifikace a Diagnostika problémů s virtuálními počítači s Windows](diagnostics-role-service.md).

## <a name="input-delay"></a>Zpoždění vstupu

"Zpoždění vstupu" v Azure Monitor pro virtuální počítače s Windows znamená zpoždění vstupu jednotlivých procesů pro každou relaci. Na stránce výkon hostitele na adrese [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)je tento čítač výkonu nakonfigurován tak, aby odesílal zprávu službě každých 30 sekund. Tyto 30 sekundové intervaly se nazývají "ukázky" a sestavy nejhoršího případu v tomto okně. Hodnoty mediánu a p95 odrážejí medián a 95. percentil napříč všemi ukázkami.

V části **zpoždění vstupu podle hostitele** můžete vybrat řádek hostitele relace pro filtrování všech ostatních vizuálů na stránce daného hostitele. Můžete také vybrat název procesu pro filtrování mediánu vstupu v čase grafu.

Do následujících kategorií zadáte prodlevy:

- Dobrá: pod 150 milisekund.
- Přijatelné: 150-500 milisekund.
- Špatné: 500 – 2 000 milisekund (pod 2 sekundy).
- Chybné: více než 2 000 milisekund (2 sekundy a vyšší).

Další informace o tom, jak funguje čítač zpoždění vstupu, najdete v tématu [čítače výkonu zpoždění vstupu uživatele](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Měsíční aktivní uživatelé (MAU)

Celkový počet uživatelů, kteří spustili relaci za posledních 28 dní. Pokud ukládáte data po dobu 30 dnů nebo i méně, může se v průběhu období, kdy je k dispozici méně než 28 dní dostupných dat, zobrazit nižší než očekávaná hodnota MAU a připojení.

## <a name="performance-counters"></a>Čítače výkonu

Čítače výkonu zobrazují výkon hardwarových komponent, operačních systémů a aplikací.

Následující tabulka uvádí doporučené čítače výkonu a časové intervaly, které Azure Monitor používá pro virtuální počítače s Windows:

|Název čítače výkonu|Časový interval|
|---|---|
|Logický disk (C:) \\ Průměrná délka fronty disku|30 sekund|
|Logický disk (C:) \\ Střední doba disku/přenos|60 sekund|
|Logický disk (C:) \\ aktuální délka fronty disku|30 sekund|
|Paměť ( \* ) \\ dostupné v MB|30 sekund|
|Paměť ( \* ) \\ stránky chyby/s|30 sekund|
|Paměť ( \* ) \\ stránky/s|30 sekund|
|Paměť ( \* ) \\ % používaných potvrzených bajtů|30 sekund|
|Fyzický disk ( \* ) \\ Průměrná délka fronty disku|30 sekund|
|Fyzický \* disk () \\ Střední doba disku/čtení|30 sekund|
|Fyzický disk ( \* ) \\ Střední doba disku/přenos|30 sekund|
|Fyzický \* disk () \\ Střední doba disku/zápis|30 sekund|
|Informace o procesoru (_Total) \\ % času procesoru|30 sekund|
|Aktivní relace Terminálové služby ( \* ) \\|60 sekund|
|Neaktivní relace Terminálové služby ( \* ) \\|60 sekund|
|\*Celkový počet relací Terminálové služby () \\|60 sekund|
|\*Zpoždění vstupu uživatele na proces ( \* ) \\ maximálního zpoždění vstupu|30 sekund|
|\*Zpoždění vstupu uživatele na relaci ( \* ) \\ Max. zpoždění vstupu|30 sekund|
|Síť RemoteFX ( \* ) \\ aktuální doba RTT TCP|30 sekund|
|\* \\ Aktuální šířka pásma UDP sítě RemoteFX|30 sekund|

Další informace o tom, jak číst čítače výkonu, najdete v tématu [Konfigurace čítačů výkonu](../azure-monitor/agents/data-sources-performance-counters.md).

Další informace o čítačích výkonu zpoždění vstupu najdete v tématu [čítače výkonu zpoždění vstupu uživatele](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Potenciální problémy s připojením

Potenciální problémy s připojením zobrazují hostitele, uživatele, publikované prostředky a klienty s vysokou mírou selhání připojení. Když zvolíte filtr "sestava podle", můžete vyhodnotit závažnost problému tím, že zkontrolujete hodnoty v těchto sloupcích:

- Pokusy (počet pokusů o připojení)
- Prostředky (počet publikovaných aplikací nebo ploch)
- Hostitelé (počet virtuálních počítačů)
- Klienti

Pokud například vyberete filtr **podle uživatele** , můžete zaškrtnout a zobrazit pokusy o připojení jednotlivých uživatelů ve sloupci **pokusy** .

Pokud si všimnete, že problém s připojením zahrnuje několik hostitelů, uživatelů, prostředků nebo klientů, je pravděpodobný, že problém ovlivňuje celý systém. Pokud tomu tak není, jedná se o menší problém, který má nižší prioritu.

Můžete také vybrat položky a zobrazit další informace. Můžete zobrazit, kteří hostitelé, prostředky a verze klienta byly s problémem zapojeni. V zobrazení se zobrazí také všechny chyby hlášené během pokusů o připojení.

## <a name="round-trip-time-rtt"></a>Doba odezvy (RTT)

Doba odezvy (RTT) představuje odhad doby odezvy připojení mezi umístěním koncového uživatele a oblastí Azure hostitele relace. Pokud chcete zjistit, která umístění mají nejlepší latenci, vyhledejte požadované umístění v [nástroji Estimator pro virtuální počítače s Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).

## <a name="session-history"></a>Historie relací

Položka **relace** zobrazuje stav všech relací, připojených a odpojených. **Nečinné relace** zobrazují pouze odpojené relace.

## <a name="severity-0-alerts"></a>Upozornění na závažnost 0

Nejnaléhavější položky, které je třeba hned zajímat. Pokud tyto problémy neřešíte, může to způsobit, že nasazení virtuálních počítačů s Windows přestane fungovat.

## <a name="time-to-connect"></a>Čas pro připojení

Čas pro připojení je čas mezi tím, kdy uživatel spustí svou relaci, a když se počítá jako přihlášený ke službě. Vytvoření nových připojení zamýšlí trvat déle než opětovné vytvoření stávajících připojení.

## <a name="user-report"></a>Sestava uživatele

Stránka sestava uživatele umožňuje zobrazit historii připojení konkrétního uživatele a diagnostické informace. Každá sestava uživatele zobrazuje způsoby využití, zpětnou vazbu uživatelů a všechny chyby, ke kterým došlo během svých relací. Většinu menších problémů můžete vyřešit pomocí zpětné vazby od uživatelů. Pokud potřebujete dig hlubší, můžete také filtrovat informace o konkrétním ID připojení nebo časovém intervalu.

## <a name="users-per-core"></a>Počet uživatelů na jádro

Toto je počet uživatelů v jádrech virtuálních počítačů. Sledování maximálního počtu uživatelů na jádro v průběhu času vám umožní zjistit, jestli se prostředí konzistentně spouští s vysokým, nízkým nebo výkyvem počtu uživatelů na jádro. Znalost toho, kolik uživatelů je aktivních, vám pomůže efektivně zajistit prostředky a škálovat prostředí.

## <a name="windows-event-logs"></a>Protokoly událostí Windows

Protokoly událostí systému Windows jsou zdroje dat shromažďované agenty Log Analytics na virtuálních počítačích s Windows. Můžete shromažďovat události ze standardních protokolů, jako jsou systém a aplikace, i vlastní protokoly vytvořené aplikacemi, které potřebujete monitorovat.

V následující tabulce jsou uvedené požadované protokoly událostí Windows pro Azure Monitor pro virtuální počítače s Windows:

|Název události|Typ události|
|---|---|
|Aplikace|Chyba a upozornění|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin|Chyba, upozornění a informace|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|Chyba, upozornění a informace|
|Systém|Chyba a upozornění|
| Microsoft-FSLogix – aplikace/provozní|Chyba, upozornění a informace|
|Microsoft-FSLogix – aplikace/Správce|Chyba, upozornění a informace|

Další informace o protokolech událostí systému Windows naleznete v tématu [vlastnosti záznamů událostí systému Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Azure Monitor pro virtuální počítače s Windows, podívejte se na tyto články:

- [Monitorování nasazení pomocí Azure Monitor pro virtuální počítač s Windows](azure-monitor.md)
- [Řešení potíží s virtuálním počítačem s Windows Azure Monitor](troubleshoot-azure-monitor.md)

Můžete také nastavit Azure Advisor, které vám pomůžou zjistit, jak řešit běžné problémy a jak se jim vyhnout. Přečtěte si další informace o [použití Azure Advisor s virtuálním počítačem s Windows](azure-advisor.md).

Pokud potřebujete pomáhat nebo máte nějaké dotazy, Projděte si naše komunitní prostředky:

- Položte otázky nebo udělejte návrhy komunitě na [virtuálním počítači s Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
   
- Informace o tom, jak ponechávat zpětnou vazbu, najdete v tématech [Přehled řešení potíží, zpětná vazba a podpora pro virtuální počítače s Windows](troubleshoot-set-up-overview.md#report-issues).

- Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
