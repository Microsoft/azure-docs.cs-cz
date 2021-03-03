---
title: Historie verzí agenta ochrany heslem – Azure Active Directory
description: Verze dokumentů a historie změn chování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32ad7199360ca0acc8674f7a4e34bd206f8b335f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648760"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historie verzí agenta ochrany hesel Azure AD

## <a name="121720"></a>1.2.172.0

Datum vydání: Únor 22 2021

To bylo skoro dva roky od vydání verze GA místních agentů ochrany hesel Azure AD. Nyní je k dispozici nová aktualizace – viz změnit popis níže. Děkujeme všem, kdo na produkt poslal svůj názor. 

* Agent řadiče domény a software agenta proxy teď vyžadují, aby se nainstaloval .NET 4.7.2.
  * Pokud rozhraní .NET 4.7.2 ještě není nainstalované, Stáhněte a spusťte instalační program, který najdete v [instalačním programu .NET Framework 4.7.2 offline pro Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Modul AzureADPasswordProtection PowerShell je teď nainstalovaný také softwarem agenta DC.
* Byly přidány dvě nové rutiny PowerShellu související se stavem: Test-AzureADPasswordProtectionDCAgent a test-AzureADPasswordProtectionProxy.
* Knihovna DLL filtru hesla agenta AzureADPasswordProtection se teď načte a spustí na počítačích, kde je lsass.exe nakonfigurovaná tak, aby běžela v režimu PPL.
* Oprava chyb pro algoritmus hesla, který povoluje zakázaná hesla, méně než pět znaků, aby byla nesprávně přijata.
  * Tato chyba se vztahuje jenom v případě, že je nakonfigurovaná zásada minimální délky hesel místních AD tak, aby na prvním místě povolovala méně než pět znaků hesla.
* Další drobné opravy chyb.

Noví instalační programy budou automaticky upgradovat starší verze softwaru. Pokud jste nainstalovali agenta řadiče domény i proxy software v jednom počítači (doporučený pouze pro testovací prostředí), je nutné současně provést upgrade.

Podporuje spouštění starších a novějších verzí agenta řadiče domény a softwaru proxy v rámci domény nebo doménové struktury, i když doporučujeme upgradovat všechny agenty na nejnovější verzi jako osvědčený postup. Je podporováno jakékoli pořadí upgradů agenta – noví agenti řadiče domény mohou komunikovat prostřednictvím starších agentů proxy a starší agenti řadiče domény mohou komunikovat prostřednictvím novějších agentů proxy.

## <a name="121250"></a>1.2.125.0

Datum vydání: březen 22 2019

* Oprava drobných chyb překlepů ve zprávách protokolu událostí
* Aktualizace smlouvy EULA na konečnou verzi obecné dostupnosti

> [!NOTE]
> Build 1.2.125.0 je sestavení obecné dostupnosti. Děkujeme vám, že jste znovu dostali zpětnou vazbu k produktu.

## <a name="121160"></a>1.2.116.0

Datum vydání: 3/13/2019

* Rutiny Get-AzureADPasswordProtectionProxy a Get-AzureADPasswordProtectionDCAgent nyní hlásí verzi softwaru a aktuálního tenanta Azure s těmito omezeními:
  * Verze softwaru a data tenanta Azure jsou k dispozici pouze pro agenty DC a proxy servery se spuštěnou verzí 1.2.116.0 nebo novější.
  * Data tenanta Azure se nemusí nahlásit, dokud nedošlo k opětovné registraci (nebo obnovení) proxy serveru nebo doménové struktury.
* Služba proxy teď vyžaduje, aby bylo nainstalované rozhraní .NET 4,7.
  * Pokud rozhraní .NET 4,7 ještě není nainstalované, Stáhněte a spusťte instalační program, který najdete v [instalačním programu .NET Framework 4,7 offline pro Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * V systémech jádra serveru může být nutné předat příznak/q instalačnímu programu rozhraní .NET 4,7 a získat tak jeho úspěšnost.
* Služba proxy teď podporuje automatický upgrade. Automatický upgrade používá službu Microsoft Azure AD Connect agent aktualizační službu, která je nainstalovaná souběžně se službou proxy serveru. Automatický upgrade je ve výchozím nastavení zapnutý.
* Automatický upgrade může být povolený nebo zakázaný pomocí rutiny Set-AzureADPasswordProtectionProxyConfiguration. Pomocí rutiny Get-AzureADPasswordProtectionProxyConfiguration se dá zadat dotaz na aktuální nastavení.
* Binární soubor služby agenta řadiče domény byl přejmenován na AzureADPasswordProtectionDCAgent.exe.
* Binární soubor služby pro proxy službu byl přejmenován na AzureADPasswordProtectionProxy.exe. Pokud používáte bránu firewall jiného výrobce, může být nutné změnit pravidla brány firewall.
  * Poznámka: Pokud se konfigurační soubor proxy serveru HTTP používá v předchozí instalaci proxy serveru, bude nutné po tomto upgradu přejmenovat (od *proxyservice.exe.config* do *AzureADPasswordProtectionProxy.exe.config*).
* Všechny kontroly funkcí s časovým omezením byly odebrány z agenta řadiče domény.
* Drobné opravy chyb a vylepšení protokolování.

## <a name="12650"></a>1.2.65.0

Datum vydání: 1. února 2019

Provedeny

* Agent serveru a proxy služby se teď v jádru serveru podporují. Požadavky na operační systém Mininimum se nezměnily před: Windows Server 2012 pro agenty DC a Windows Server 2012 R2 pro proxy servery.
* Rutiny Register-AzureADPasswordProtectionProxy a Register-AzureADPasswordProtectionForest nyní podporují režimy ověřování Azure založené na kódu zařízení.
* Rutina Get-AzureADPasswordProtectionDCAgent bude ignorovat pozměněný nebo neplatný spojovací body služby. Tato změna opravuje chybu, při které by se ve výstupu někdy ve výstupu zobrazovaly řadiče domény vícekrát.
* Rutina Get-AzureADPasswordProtectionSummaryReport bude ignorovat pozměněný nebo neplatný spojovací body služby. Tato změna opravuje chybu, při které by se ve výstupu někdy ve výstupu zobrazovaly řadiče domény vícekrát.
* Modul PowerShell proxy je teď registrovaný z%ProgramFiles%\WindowsPowerShell\Modules.. Proměnná prostředí PSModulePath počítače se už nemění.
* K podpoře při zjišťování registrovaných proxy serverů v doménové struktuře nebo doméně byla přidána nová rutina Get-AzureADPasswordProtectionProxy.
* Agent DC používá novou složku ve sdílené složce SYSVOL pro replikaci zásad hesel a dalších souborů.

   Umístění staré složky:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nové umístění složky:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Tato změna byla provedena, aby se zabránilo falešně pozitivním upozorněním na osamocený objekt zásad skupiny.)

   > [!NOTE]
   > V rámci staré složky a nové složky se neprovede žádná migrace ani sdílení dat. Starší verze agenta řadiče domény budou nadále používat staré umístění, dokud neproběhne upgrade na tuto verzi nebo novější. Jakmile všichni agenti řadiče domény používají verzi 1.2.65.0 nebo novější, je možné, že se původní složka SYSVOL odstraní ručně.

* Agent řadiče domény a proxy služby nyní zjišťují a odstraňují pozměněné kopie příslušných spojovacích bodů služby.
* Každý agent řadiče domény bude ve své doméně pravidelně odstraňovat pozměněné a zastaralé spojovací body služby, a to pro spojovací body agenta řadiče domény i proxy služby. I když je časové razítko prezenčního signálu starší než sedm dní, považují se za zastaralé oba spojovací body agenta DC i proxy služby.
* Agent řadiče domény nyní obnoví certifikát doménové struktury podle potřeby.
* Služba proxy teď certifikát proxy serveru obnoví podle potřeby.
* Aktualizace algoritmu ověřování hesla: globální seznam zakázaných hesel a seznam zakázaných hesel specifických pro zákazníka (Pokud je nakonfigurovaný) se sloučí před ověřením hesla. Toto heslo může být teď odmítnuté (pokud obsahuje tokeny ze seznamu globálních i specifických pro zákazníka) (pouze v případě selhání nebo auditu). Dokumentace protokolu událostí byla aktualizována tak, aby odrážela tuto skutečnost; Viz téma [monitorování ochrany heslem Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Opravy výkonu a odolnosti
* Vylepšené protokolování

> [!WARNING]
> Časově omezená funkce: Služba agenta DC v této verzi (1.2.65.0) zastaví zpracování požadavků na ověření hesla od 1. září 2019.  Služby agenta DC v předchozích verzích (viz seznam níže) zastaví zpracování od 1. července 2019. Služba agenta DC ve všech verzích bude protokolovat události 10021 do protokolu událostí správce během dvou měsíců, které provedou tyto termíny. Všechna omezení s časovým limitem budou v nadcházející verzi GA odebrána. Služba agenta proxy serveru není časově omezená v jakékoli verzi, ale je třeba ji upgradovat na nejnovější verzi, aby bylo možné využívat všechny následné opravy chyb a další vylepšení.

## <a name="12250"></a>1.2.25.0

Datum vydání: 1. listopadu 2018

Řeší

* Agent řadiče domény a proxy služba by už neměly selhat kvůli chybám důvěryhodnosti certifikátů.
* Agent řadiče domény a proxy služba mají opravy pro počítače kompatibilní se standardem FIPS.
* Služba proxy bude nyní fungovat správně v síťovém prostředí pouze TLS 1,2.
* Drobné opravy výkonu a odolnosti
* Vylepšené protokolování

Provedeny

* Minimální požadovaná úroveň operačního systému pro službu proxy serveru je teď Windows Server 2012 R2. Minimální požadovaná úroveň operačního systému pro službu agenta řadiče domény zůstává v systému Windows Server 2012.
* Služba proxy teď vyžaduje rozhraní .NET verze 4.6.2.
* Algoritmus ověřování hesla používá tabulku normalizace rozšířené znakové sady. Tato změna může mít za následek odmítnutí hesel, která byla přijata v předchozích verzích.

## <a name="12100"></a>1.2.10.0

Datum vydání: srpna 17 2018

Řeší

* Register-AzureADPasswordProtectionProxy a Register-AzureADPasswordProtectionForest nyní podporují službu Multi-Factor Authentication
* Register-AzureADPasswordProtectionProxy vyžaduje v doméně řadič domény WS2012 nebo novější, aby se předešlo chybám šifrování.
* Služba agenta DC je spolehlivější na vyžádání nových zásad hesel z Azure při spuštění.
* Služba agenta DC v případě potřeby vyžádá nové zásady pro hesla z Azure každou hodinu, ale teď to provede náhodně vybraným časem zahájení.
* Služba agenta řadiče domény již nezpůsobí neomezené zpoždění při instalaci na server před povýšením jako replika v novém oznámení řadiče domény.
* Služba agenta řadiče domény teď bude mít nastavení konfigurace povolit ochranu heslem na Windows serveru Active Directory.
* Agent řadiče domény i instalační programy proxy teď budou podporovat místní upgrade při upgradu na budoucí verze.

> [!WARNING]
> Místní upgrade z verze 1.1.10.3 se nepodporuje a bude mít za následek chybu instalace. Pokud chcete upgradovat na verzi 1.2.10 nebo novější, musíte nejdřív odinstalovat agenta řadiče domény a softwaru proxy serveru a pak nainstalovat novou verzi úplně od začátku. Vyžaduje se opakovaná registrace služby proxy ochrany heslem Azure AD.  Není nutné znovu registrovat doménovou strukturu.

> [!NOTE]
> Místní upgrady softwaru agenta DC budou vyžadovat restart.

* Agent řadiče domény a proxy služby teď podporují spouštění na serveru, který je nakonfigurovaný tak, aby používal jenom algoritmy kompatibilní se standardem FIPS.
* Drobné opravy výkonu a odolnosti
* Vylepšené protokolování

## <a name="11103"></a>1.1.10.3

Datum vydání: 15. června 2018

Úvodní verze Public Preview

## <a name="next-steps"></a>Další kroky

[Nasazení ochrany heslem Azure AD](howto-password-ban-bad-on-premises-deploy.md)
