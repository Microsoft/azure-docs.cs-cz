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
ms.openlocfilehash: bd9b07f1f7aed479e94e77a5641130cb784dd69e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741962"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historie verzí agenta ochrany hesel Azure AD

## <a name="121250"></a>1.2.125.0

Datum vydání: 3/22/2019

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
  * Rozhraní .NET 4,7 by již mělo být nainstalováno na plně aktualizovaný systém Windows Server. V takovém případě si stáhněte a spusťte instalační program, který najdete v [instalačním programu .NET Framework 4,7 offline pro systém Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * V systémech jádra serveru může být nutné předat příznak/q instalačnímu programu .NET 4,7 a získat tak jeho úspěšnost.
* Služba proxy teď podporuje automatický upgrade. Automatický upgrade používá službu aktualizace agenta Microsoft Azure AD Connect, která je nainstalovaná souběžně s proxy službou. Automatický upgrade je ve výchozím nastavení zapnutý.
* Automatický upgrade může být povolený nebo zakázaný pomocí rutiny Set-AzureADPasswordProtectionProxyConfiguration. Pomocí rutiny Get-AzureADPasswordProtectionProxyConfiguration se dá zadat dotaz na aktuální nastavení.
* Binární soubor služby agenta řadiče domény byl přejmenován na AzureADPasswordProtectionDCAgent.exe.
* Binární soubor služby pro proxy službu byl přejmenován na AzureADPasswordProtectionProxy.exe. Pokud používáte bránu firewall jiného výrobce, může být nutné změnit pravidla brány firewall.
  * Poznámka: Pokud se konfigurační soubor proxy serveru HTTP používá v předchozí instalaci proxy serveru, bude nutné po tomto upgradu přejmenovat (od *proxyservice.exe.config* do *AzureADPasswordProtectionProxy.exe.config*).
* Všechny kontroly funkcí s časovým omezením byly odebrány z agenta řadiče domény.
* Drobné opravy chyb a vylepšení protokolování.

## <a name="12650"></a>1.2.65.0

Datum vydání: 2/1/2019

Provedeny

* Agent serveru a proxy služby se teď v jádru serveru podporují. Požadavky na operační systém Mininimum se nezměnily před: Windows Server 2012 pro agenty DC a Windows Server 2012 R2 pro proxy servery.
* Rutiny Register-AzureADPasswordProtectionProxy a Register-AzureADPasswordProtectionForest nyní podporují režimy ověřování Azure založené na kódu zařízení.
* Rutina Get-AzureADPasswordProtectionDCAgent bude ignorovat pozměněný nebo neplatný spojovací body služby. Tím se vyřeší chyba, při které by se ve výstupu někdy zobrazovaly řadiče domény vícekrát.
* Rutina Get-AzureADPasswordProtectionSummaryReport bude ignorovat pozměněný nebo neplatný spojovací body služby. Tím se vyřeší chyba, při které by se ve výstupu někdy zobrazovaly řadiče domény vícekrát.
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
* Aktualizace algoritmu ověřování hesla: globální seznam zakázaných hesel a seznam zakázaných hesel specifických pro zákazníka (Pokud je nakonfigurovaný) se sloučí před ověřením hesla. Toto heslo může být teď odmítnuté (pokud obsahuje tokeny ze seznamu globálních i specifických pro zákazníka) (pouze v případě selhání nebo auditu). Dokumentace protokolu událostí byla aktualizována tak, aby odrážela tuto skutečnost; Viz téma [monitorování ochrany heslem služby Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Opravy výkonu a odolnosti
* Vylepšené protokolování

> [!WARNING]
> Časově omezená funkce: Služba agenta DC v této verzi (1.2.65.0) zastaví zpracování požadavků na ověření hesla od 1. září 2019.  Služby agenta DC v předchozích verzích (viz seznam níže) zastaví zpracování od 1. července 2019. Služba agenta DC ve všech verzích bude protokolovat události 10021 do protokolu událostí správce během dvou měsíců, které provedou tyto termíny. Všechna omezení s časovým limitem budou v nadcházející verzi GA odebrána. Služba agenta proxy serveru není časově omezená v jakékoli verzi, ale je třeba ji upgradovat na nejnovější verzi, aby bylo možné využívat všechny následné opravy chyb a další vylepšení.

## <a name="12250"></a>1.2.25.0

Datum vydání: 11/01/2018

Řeší

* Agent řadiče domény a proxy služba by už neměly selhat kvůli chybám důvěryhodnosti certifikátů.
* Agent řadiče domény a proxy služby mají další opravy pro počítače kompatibilní se standardem FIPS.
* Služba proxy bude nyní fungovat správně v síťovém prostředí pouze TLS 1,2.
* Drobné opravy výkonu a odolnosti
* Vylepšené protokolování

Provedeny

* Minimální požadovaná úroveň operačního systému pro službu proxy serveru je teď Windows Server 2012 R2. Minimální požadovaná úroveň operačního systému pro službu agenta řadiče domény zůstává v systému Windows Server 2012.
* Služba proxy teď vyžaduje rozhraní .NET verze 4.6.2.
* Algoritmus ověřování hesla používá tabulku normalizace rozšířené znakové sady. To může mít za následek odmítnutí hesla, která byla přijata v předchozích verzích.

## <a name="12100"></a>1.2.10.0

Datum vydání: 8/17/2018

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

Datum vydání: 6/15/2018

Úvodní verze Public Preview

## <a name="next-steps"></a>Další kroky

[Nasazení ochrany heslem Azure AD](howto-password-ban-bad-on-premises-deploy.md)
