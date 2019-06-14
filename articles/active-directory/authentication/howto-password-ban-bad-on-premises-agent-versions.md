---
title: Místní historie vydaných verzí agenta ochrany hesel služby Azure AD – Azure Active Directory
description: Historii změn pro vydání verze dokumentů a chování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60ce66b079942944176540826c7f3e7a91b070d2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60358204"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Historie verze agenta Azure AD ochrana heslem

## <a name="121250"></a>1.2.125.0

Datum vydání: 3/22/2019

* Opravte chyby menší překlep v protokolu událostí zprávy
* Smlouvu EULA aktualizace na finální verzi všeobecné dostupnosti

> [!NOTE]
> Sestavení 1.2.125.0 je obecně dostupná sestavení. Děkujeme, že jste znovu pro všechny uživatele v produktu poskytuje zpětnou vazbu!

## <a name="121160"></a>1.2.116.0

Datum vydání: 3/13/2019

* Get-AzureADPasswordProtectionProxy a Get-AzureADPasswordProtectionDCAgent rutiny teď verze softwaru sestavy a aktuální Azure tenanta s těmito omezeními:
  * Verze softwaru a data Azure tenanta se pouze k dispozici pro řadič domény agenty a servery proxy verzí 1.2.116.0 nebo novější.
  * Data tenanta Azure nemusí být hlášena až do opětovné registraci (nebo prodloužení platnosti) proxy server nebo doménové struktury došlo k chybě.
* Proxy služba teď vyžaduje, aby nainstalované rozhraní .NET 4.7.
  * .NET 4.7 musí již nainstalován na serveru Windows Server kompletně aktualizovaný. Pokud to není tento případ, stáhněte a spusťte instalační program na [offline instalační program .NET Framework 4.7 pro Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * V systémech jádra serveru může být potřeba předat příznakem /q do instalačního programu .NET 4.7 její úspěšné.
* Služba proxy server nyní podporuje automatický upgrade. Automatický upgrade používá Microsoft Azure AD Connect agenta aktualizační služby, která je nainstalovaná – souběžně se službou Proxy. Ve výchozím nastavení zapnutý automatický upgrade.
* Automatický upgrade může být povoleno nebo zakázáno, pomocí rutiny Set-AzureADPasswordProtectionProxyConfiguration. Aktuální nastavení může být dotázán pomocí rutiny Get-AzureADPasswordProtectionProxyConfiguration.
* Binární služby pro službu agent řadič domény byl přejmenován na AzureADPasswordProtectionDCAgent.exe.
* Binární služby pro službu Proxy byl přejmenován na AzureADPasswordProtectionProxy.exe. Pravidla brány firewall možná muset odpovídajícím způsobem upravit, pokud je brána firewall třetích stran používané.
  * Poznámka: Pokud soubor konfigurace proxy serveru http se používal v předchozí proxy server nainstalovat, ji budou muset přejmenovat (z *proxyservice.exe.config* k *AzureADPasswordProtectionProxy.exe.config*) po této upgrade.
* Všechny kontroly časově omezené funkce byly odebrány z agenta řadiče domény.
* Protokolování vylepšení a oprav drobných chyb.

## <a name="12650"></a>1.2.65.0

Datum vydání: 2/1/2019

Změny:

* Agent a server proxy služby řadiče domény jsou teď podporovány na jádro serveru. Požadavky na minimální operační systém se neliší od před: Windows Server 2012 pro agenty řadiče domény a Windows Server 2012 R2 pro proxy servery.
* Rutiny AzureADPasswordProtectionProxy rejstříku a registrace AzureADPasswordProtectionForest nyní podporují režimy ověřování Azure na základě zařízení kódu.
* Rutina Get-AzureADPasswordProtectionDCAgent bude ignorovat spojovací body služby pozměnění a/nebo neplatný. To řeší chyby, kdy řadiče domény se někdy zobrazují více než jednou ve výstupu.
* Rutina Get-AzureADPasswordProtectionSummaryReport bude ignorovat spojovací body služby pozměnění a/nebo neplatný. To řeší chyby, kdy řadiče domény se někdy zobrazují více než jednou ve výstupu.
* Modul proxy serveru prostředí powershell je teď zaregistrovaný z % ProgramFiles%\WindowsPowerShell\Modules. Proměnná prostředí PSModulePath počítače je již změnit.
* Nová Rutina Get-AzureADPasswordProtectionProxy je přidaný do pomoci při zjišťování registrované servery proxy v doménové struktuře nebo doméně.
* Agent řadič domény používá nové složky ve sdílené složce sysvol pro replikaci zásady pro hesla a další soubory.

   Původní umístění složky:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nové umístění složky:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Tato změna byla provedena, aby upozornění falešně pozitivní "osamocený objekt zásad skupiny").

   > [!NOTE]
   > Žádná migrace nebo sdílení dat bude provedeno mezi složkou staré a nové složky. Starší verzí agenta pro řadič domény bude nadále používat staré umístění, dokud nebudou upgradovány na tuto verzi nebo novější. Jakmile všichni agenti řadiče domény běží verze 1.2.65.0 nebo později, může ručně odstranit starý složku sysvol.

* Službu řadiče domény a agent proxy nyní rozpozná a odstranit pozměnění kopie spojovací body jejich příslušné služby.
* Každý agent řadiče domény se pravidelně odstraní spojovací body pozměnění a zastaralých služby ve své doméně pro oba řadiče domény a agent proxy spojovací body služby. Oba řadiče domény a agent proxy spojovací body služby se považují za zastaralé, pokud její časové razítko prezenčního signálu je starší než 7 dní.
* Řadič domény agenta bude nyní obnovit certifikát doménové struktury, podle potřeby.
* Služba Proxy bude nyní obnovit certifikát proxy serveru, podle potřeby.
* Aktualizace hesla algoritmus pro ověření: seznam zakázaných hesel globální a seznam zakázaných hesel zákaznického (je-li konfigurováno) zkombinují před ověření hesla. Nyní mohou být odmítnuty dané heslo (selhat nebo jenom pro auditování) Pokud obsahuje tokeny ze seznamu globální a specifické pro zákazníka. Dokumentace ke službě protokol událostí byl aktualizován tak, aby odrážely to; Podrobnosti najdete na [ochrana hesel Azure AD monitorování](howto-password-ban-bad-on-premises-monitor.md).
* Výkon a odolnost opravy
* Zdokonalené funkce protokolování

> [!WARNING]
> Časově omezené funkce: Služba agenta řadiče domény v této verzi (1.2.65.0) se zastaví zpracování žádostí o ověření hesla od 1. září 2019.  Řadič domény služby agenta v předchozích verzích (viz následující seznam) se zastaví zpracování od 1. července 2019. Služba agenta řadiče domény ve všech verzích bude zaznamenávat 10021 události do protokolu událostí správce do dvou měsíců dovedou až tyto termíny. Odebere všechny časový limit omezení v nadcházející verzi všeobecné dostupnosti. Službu agent Proxy není časově omezené v žádné verzi, ale stále nutné upgradovat na nejnovější verzi. abyste mohli využívat všechny další opravy chyb a další vylepšení.

## <a name="12250"></a>1.2.25.0

Datum vydání: 11/01/2018

Opravy:

* Řadič domény proxy serveru a agent služby by měl už neselže kvůli chybám certifikátu vztahu důvěryhodnosti.
* Služba agenta a proxy řadič domény nemá další opravy pro počítače kompatibilní se standardem FIPS.
* Služba proxy bude nyní fungovat správně v protokolu TLS 1.2 – jen síťovém prostředí.
* Menší výkon a odolnost opravy
* Zdokonalené funkce protokolování

Změny:

* Minimální požadovaná úroveň operačního systému pro službu proxy serveru je nyní systému Windows Server 2012 R2. Minimální požadovanou úroveň operačního systému pro službu agenta DC zůstal ve Windows serveru 2012.
* Služba Proxy nyní vyžaduje rozhraní .NET verze 4.6.2.
* Algoritmus pro ověření hesla používá tabulku normalizace rozšířené znaky. Výsledkem může být odmítnuta, hesla, která byla přijata v předchozích verzích.

## <a name="12100"></a>1.2.10.0

Datum vydání: 8/17/2018

Opravy:

* Register-AzureADPasswordProtectionProxy a zaregistrujte AzureADPasswordProtectionForest teď podporují ověřování službou Multi-Factor Authentication
* Register-AzureADPasswordProtectionProxy vyžaduje WS2012 nebo novější řadič domény v doméně, aby zabránil chybám při šifrování.
* Služba agenta pro řadič domény je spolehlivější o vyžádání nové zásady hesla z Azure při spuštění.
* Služba agenta řadiče domény z Azure každou hodinu v případě potřeby bude požadovat nové zásady hesel, ale bude teď udělat na náhodně vybrané počáteční čas.
* Služba agenta řadiče domény už způsobí neomezenou zpoždění v nový řadič domény oznámení o inzerovaném programu při instalaci na serveru před jeho povýšení jako replika.
* Služba agenta pro řadič domény bude nyní případném dalším sdílení dodržovat nastavení "Povolit ochranu hesla na Windows Server Active Directory"
* Oba řadiče domény a agent proxy instalační programy teď podporují místní upgrade, při upgradu na budoucí verze.

> [!WARNING]
> Místní upgrade z verze 1.1.10.3 nepodporuje a způsobí chybu instalace. Na upgrade na verzi 1.2.10 nebo novější, je nutné nejprve úplně odinstalovat softwaru řadič domény proxy serveru a agent služby, potom nainstalujte novou verzi úplně od začátku. Vyžaduje se opětovná registrace ochrany hesla Azure AD službu proxy serveru.  Není potřeba znovu zaregistrovat doménové struktury.

> [!NOTE]
> Místní upgrady softwaru agenta pro řadič domény bude vyžadovat restartování.

* Řadič domény proxy serveru a agent služby teď podporují běží na serveru nakonfigurovaný tak, aby pouze používat algoritmy odpovídající standardu FIPS.
* Menší výkon a odolnost opravy
* Zdokonalené funkce protokolování

## <a name="11103"></a>1.1.10.3

Datum vydání: 6/15/2018

Počáteční veřejné verze preview

## <a name="next-steps"></a>Další postup

[Nasazení ochrany hesel Azure AD](howto-password-ban-bad-on-premises-deploy.md)
