---
title: Historie vydání agenta ochrany heslem – Azure Active Directory
description: Historie verzí dokumentů a změny chování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847845"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historie verzí agenta ochrany heslem Azure AD

## <a name="121250"></a>1.2.125.0

Datum vydání: 3/22/2019

* Oprava menších překlepů ve zprávách protokolu událostí
* Aktualizace smlouvy EULA na konečnou verzi obecné dostupnosti

> [!NOTE]
> Sestavení 1.2.125.0 je sestavení obecné dostupnosti. Ještě jednou děkuji všem, kteří poskytli zpětnou vazbu k produktu!

## <a name="121160"></a>1.2.116.0

Datum vydání: 3/13/2019

* Rutiny Get-AzureADPasswordProtectionProxy a Get-AzureADPasswordProtectionDCAgent nyní hlásí verzi softwaru a aktuálního tenanta Azure s následujícími omezeními:
  * Verze softwaru a data tenanta Azure jsou k dispozici jenom pro agenty řadiče domény a proxy servery verze 1.2.116.0 nebo novější.
  * Data tenanta Azure se nemusí hlásit, dokud nenastala opětovná registrace (nebo obnovení) proxy serveru nebo doménové struktury.
* Služba Proxy nyní vyžaduje, aby byla nainstalována rozhraní .NET 4.7.
  * Síť .NET 4.7 by již měla být nainstalována v plně aktualizovaném systému Windows Server. Pokud tomu tak není, stáhněte a spusťte instalační program najdete v [rozhraní .NET Framework 4.7 offline instalační program pro Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * V systémech Jádra serveru může být nutné předat příznak /q instalačnímu programu .NET 4.7, aby byl úspěšný.
* Služba Proxy nyní podporuje automatický upgrade. Automatický upgrade používá službu Microsoft Azure AD Connect Agent Updater, která je nainstalována vedle sebe se službou Proxy. Automatický upgrade je ve výchozím nastavení zapnutý.
* Automatický upgrade lze povolit nebo zakázat pomocí rutiny Set-AzureADPasswordProtectionProtectionProxyConfiguration. Aktuální nastavení lze dotazovat pomocí rutiny Get-AzureADPasswordProtectionProtectionConfigurationConfiguration.
* Binární služba pro službu agenta řadiče domény byla přejmenována na AzureADPasswordProtectionDCAgent.exe.
* Binární služba pro službu Proxy byla přejmenována na AzureADPasswordProtectionProxy.exe. Pokud je brána firewall jiného výrobce v provozu, může být nutné odpovídajícím způsobem upravit pravidla brány firewall.
  * Poznámka: Pokud byl v předchozí instalaci proxy použit konfigurační soubor http proxy, bude nutné jej po tomto upgradu přejmenovat (z *proxyservice.exe.config* na *AzureADPasswordProtectionProxy.exe.config).*
* Všechny časově omezené kontroly funkčnosti byly odebrány z agenta řadiče domény.
* Drobné opravy chyb a vylepšení protokolování.

## <a name="12650"></a>1.2.65.0

Datum vydání: 2/1/2019

Změny:

* Na serveru Core jsou nyní podporováni agent řadičdomény a služba proxy. Požadavky na operační systém Mininimum se neměnily od dříve: Windows Server 2012 pro agenty řadiče domény a Windows Server 2012 R2 pro proxy servery.
* Rutiny Register-AzureADPasswordProtectionProxy a Register-AzureADPasswordProtectionForest nyní podporují režimy ověřování Azure založené na kódu zařízení.
* Rutina Get-AzureADPasswordProtectionDCAgent bude ignorovat pomačkané nebo neplatné spojovací body služby. To opravuje chybu, kdy řadiče domény by někdy zobrazit vícekrát ve výstupu.
* Rutina Get-AzureADPasswordProtectionReport bude ignorovat pomačkané nebo neplatné body připojení služby. To opravuje chybu, kdy řadiče domény by někdy zobrazit vícekrát ve výstupu.
* Modul proxy powershellu je nyní registrován z %ProgramFiles%\WindowsPowerShell\Modules. Proměnná prostředí PSModulePath počítače se již nemění.
* Nový Get-AzureADPasswordProtectionProxy rutina byla přidána na pomoc při zjišťování registrovaných proxy v doménové struktuře nebo doméně.
* Agent řadiče domény používá novou složku ve sdílené složce sysvol pro replikaci zásad hesel a dalších souborů.

   Staré umístění složky:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Umístění nové složky:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Tato změna byla provedena, aby se zabránilo falešně pozitivní "osamocené GPO" upozornění.)

   > [!NOTE]
   > Mezi starou a novou složkou nebude provedena žádná migrace ani sdílení dat. Starší verze agenta řadiče domény budou nadále používat staré umístění, dokud nebudou upgradovány na tuto verzi nebo novější. Jakmile jsou všichni agenti řadiče domény spuštěna verze 1.2.65.0 nebo novější, může být stará složka sysvol ručně odstraněna.

* Agent řadiče domény a služba proxy nyní zjišťuje a odstraňuje poškozené kopie příslušných bodů připojení služby.
* Každý agent řadiče domény bude pravidelně odstraňovat pomíchané a zastaralé body připojení služby ve své doméně pro body připojení agenta řadiče domény i proxy služby. Body připojení agenta řadiče domény a proxy služby jsou považovány za zastaralé, pokud je časové razítko prezenčního signálu starší než sedm dní.
* Agent řadiče domény nyní podle potřeby obnoví certifikát doménové struktury.
* Služba Proxy nyní podle potřeby obnoví certifikát proxy.
* Aktualizace algoritmu ověření hesla: globální seznam zakázaných hesel a seznam zakázaných hesel specifickýpro zákazníka (pokud je nakonfigurován) jsou kombinovány před ověřením hesla. Dané heslo může být nyní odmítnuto (pouze selhání nebo audit), pokud obsahuje tokeny z globálního seznamu i seznamu specifického pro zákazníka. Dokumentace protokolu událostí byla aktualizována tak, aby odrážela toto; viz [Monitorování ochrany heslem Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Opravy výkonu a robustnosti
* Vylepšené protokolování

> [!WARNING]
> Časově omezená funkce: služba agenta řadiče domény v této verzi (1.2.65.0) přestane zpracovávat požadavky na ověření hesla od září 1st 2019.  Služby agenta DC v předchozích verzích (viz seznam níže) se zastaví zpracování od července 1st 2019. Služba agenta řadiče domény ve všech verzích zaznamená události 10021 do protokolu událostí správce během dvou měsíců, které tyto termíny vedou. Všechna omezení časového limitu budou v nadcházející verzi GA odstraněna. Služba proxy agentnení časově omezena v žádné verzi, ale měla by být stále upgradována na nejnovější verzi, aby bylo možné využít všech následných oprav chyb a dalších vylepšení.

## <a name="12250"></a>1.2.25.0

Datum vydání: 11/01/2018

Opravy:

* Agent řadiče domény a služba proxy by již neměly selhat z důvodu selhání důvěryhodnosti certifikátu.
* Dc agent a proxy služba mají další opravy pro počítače kompatibilní s FIPS.
* Služba proxy bude nyní správně fungovat v síťovém prostředí tls 1.2.
* Drobné opravy výkonu a robustnosti
* Vylepšené protokolování

Změny:

* Minimální požadovaná úroveň operačního systému pro službu Proxy je nyní Windows Server 2012 R2. Minimální požadovaná úroveň operačního systému pro službu agenta řadiče domény zůstane na systému Windows Server 2012.
* Služba Proxy nyní vyžaduje rozhraní .NET verze 4.6.2.
* Algoritmus ověření hesla používá rozšířenou tabulku normalizace znaků. To může mít za následek odmítnutí hesel, která byla přijata v předchozích verzích.

## <a name="12100"></a>1.2.10.0

Datum vydání: 8/17/2018

Opravy:

* Register-AzureADPasswordProtectionProxy a Register-AzureADPasswordProtectionForest nyní podporují vícefaktorové ověřování
* Register-AzureADPasswordProtectionProxy vyžaduje řadič domény WS2012 nebo novější v doméně, aby se zabránilo chybám šifrování.
* Služba agenta řadiče domény je spolehlivější při vyžádání nových zásad hesel z Azure při spuštění.
* Služba agenta řadiče domény bude v případě potřeby požadovat nové zásady hesel z Azure každou hodinu, ale teď tak učiní v náhodně vybraném čase zahájení.
* Služba agenta řadiče domény již nebude způsobovat neurčité zpoždění v nové reklamě řadiče domény při instalaci na server před jeho povýšení jako replika.
* Služba agenta řadiče domény bude nyní respektovat nastavení konfigurace Povolit ochranu heslem ve službě Windows Server Active Directory
* Instalátory agentů řadiče domény i proxy servery budou nyní podporovat místní upgrade při upgradu na budoucí verze.

> [!WARNING]
> Aktuální upgrade z verze 1.1.10.3 není podporován a bude mít za následek chybu instalace. Chcete-li upgradovat na verzi 1.2.10 nebo novější, musíte nejprve zcela odinstalovat software pro agenta řadiče domény a proxy služby a poté nainstalovat novou verzi od začátku. Je vyžadována opětovná registrace služby proxy ochrany heslem Azure AD.  Není nutné znovu zaregistrovat doménovou strukturu.

> [!NOTE]
> Inovace softwaru agenta řadiče domény na místě budou vyžadovat restartování.

* Agent řadiče domény a služba proxy nyní podporují spuštění na serveru nakonfigurovaném tak, aby používal pouze algoritmy kompatibilní s FIPS.
* Drobné opravy výkonu a robustnosti
* Vylepšené protokolování

## <a name="11103"></a>1.1.10.3

Datum vydání: 6/15/2018

Počáteční verze public preview

## <a name="next-steps"></a>Další kroky

[Nasazení ochrany heslem Azure AD](howto-password-ban-bad-on-premises-deploy.md)
