---
title: Řešení potíží s místní ochranou hesel služby Azure AD
description: Přečtěte si, jak řešit potíže s ochranou hesel Azure AD pro místní Active Directory Domain Services prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508d3942626c319221f4b690aaf444e034195bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966623"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Řešení potíží: místní ochrana heslem Azure AD

Po nasazení ochrany heslem služby Azure AD může být potřeba řešení potíží. Tento článek podrobně popisuje některé běžné kroky při řešení potíží.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent řadiče domény nemůže najít proxy v adresáři.

Hlavním příznakem tohoto problému jsou 30017 události v protokolu událostí správce agenta řadiče domény.

Obvyklou příčinou tohoto problému je to, že proxy server ještě není zaregistrovaný. Pokud je proxy server zaregistrován, může dojít k prodlevě z důvodu latence replikace služby AD, dokud konkrétnímu agentovi řadiče domény nebude moci daný proxy server zobrazit.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent řadiče domény nemůže komunikovat s proxy serverem.

Hlavním příznakem tohoto problému jsou 30018 události v protokolu událostí správce agenta řadiče domény. Tento problém může mít několik možných příčin:

1. Agent řadiče domény se nachází v izolované části sítě, která neumožňuje síťové připojení k registrovaným proxy serverem. Tento problém může být neškodný, pokud ostatní agenti DC můžou komunikovat s proxy servery, aby mohli stahovat zásady hesel z Azure. Po stažení se tyto zásady pak získají izolovaným řadičem domény prostřednictvím replikace souborů zásad ve sdílené složce SYSVOL.

1. Hostitelský počítač proxy blokuje přístup k koncovému bodu mapovače koncových bodů RPC (port 135).

   Instalační program proxy ochrany heslem služby Azure AD automaticky vytvoří příchozí pravidlo brány Windows Firewall, které umožňuje přístup k portu 135. Pokud je toto pravidlo později odstraněno nebo zakázáno, agenti řadiče domény nebudou moci komunikovat se službou proxy. Pokud byla předdefinovaná brána Windows Firewall zakázána místo jiného produktu brány firewall, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup k portu 135.

1. Hostitelský počítač proxy blokuje přístup ke koncovému bodu RPC (dynamický nebo statický), na kterém naslouchá služba proxy.

   Instalační program proxy ochrany heslem služby Azure AD automaticky vytvoří příchozí pravidlo brány Windows Firewall, které umožňuje přístup k jakýmkoli vstupním portům, na které naslouchá služba Azure AD Password Protection proxy. Pokud je toto pravidlo později odstraněno nebo zakázáno, agenti řadiče domény nebudou moci komunikovat se službou proxy. Pokud byla předdefinovaná brána Windows Firewall zakázaná místo jiného produktu firewallu, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup k jakýmkoli vstupním portům, na které naslouchá služba Azure AD Password Protection proxy. Tato konfigurace může být konkrétnější, pokud byla proxy služba nakonfigurovaná tak, aby naslouchala konkrétnímu statickému portu RPC (pomocí `Set-AzureADPasswordProtectionProxyConfiguration` rutiny).

1. Hostitelský počítač proxy není nakonfigurovaný tak, aby umožňoval řadičům domény možnost přihlásit se k počítači. Toto chování se řídí pomocí přiřazování uživatelských oprávnění "přístup k tomuto počítači ze sítě". Toto oprávnění musí být uděleno všem řadičům domény ve všech doménách v doménové struktuře. Toto nastavení se často omezuje jako součást většího úsilí při posílení zabezpečení sítě.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy služba nemůže komunikovat s Azure

1. Zajistěte, aby měl proxy počítač připojení ke koncovým bodům uvedeným v [požadavcích na nasazení](howto-password-ban-bad-on-premises-deploy.md).

1. Ujistěte se, že je doménová struktura a všechny proxy servery zaregistrované u stejného tenanta Azure.

   Tento požadavek můžete ověřit spuštěním  `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` rutin prostředí PowerShell a potom porovnejte `AzureTenant` vlastnost jednotlivých vrácených položek. Pro správnou operaci musí být nahlášený název tenanta stejný ve všech agentech DC a proxy serverech.

   Pokud neshoda s registrací tenanta Azure existuje, můžete tento problém vyřešit spuštěním `Register-AzureADPasswordProtectionProxy` rutin a/nebo `Register-AzureADPasswordProtectionForest` PowerShellu podle potřeby a tím, že použijete přihlašovací údaje ze stejného tenanta Azure pro všechny registrace.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Agent řadiče domény nemůže šifrovat nebo dešifrovat soubory zásad hesel.

Ochrana heslem Azure AD má kritickou závislost na funkci šifrování a dešifrování, kterou poskytuje služba Microsoft Key Distribution Service. Selhání šifrování nebo dešifrování může být v manifestu s nejrůznějšími příznaky a má několik možných příčin.

1. Ujistěte se, že je služba KDS povolená a funkční na všech řadičích domény se systémem Windows Server 2012 a novějším v doméně.

   Ve výchozím nastavení je režim spuštění služby KDS nakonfigurovaný jako ruční (spuštění triggeru). Tato konfigurace znamená, že při prvním pokusu o použití služby se klient spustí na vyžádání. Tento výchozí režim spuštění služby je přijatelný pro fungování ochrany hesel služby Azure AD.

   Pokud je režim spuštění služby KDS nakonfigurovaný tak, aby byl zakázaný, musí se tato konfigurace opravit předtím, než bude správně fungovat ochrana heslem Azure AD.

   Jednoduchý test tohoto problému je ruční spuštění služby KDS, a to buď prostřednictvím konzoly MMC pro správu služby, nebo pomocí jiných nástrojů pro správu (například spuštěním příkazu "net start kdssvc" z konzoly příkazového řádku). Očekává se, že se služba KDS úspěšně spustí a zůstane spuštěná.

   Nejběžnější hlavní příčinou nefunkčnosti služby KDS je, že objekt řadiče domény služby Active Directory je umístěný mimo výchozí organizační jednotku řadiče domény. Tato konfigurace není službou KDS podporována a nejedná se o omezení vyplývající z ochrany hesel služby Azure AD. Opravou této podmínky je přesunutí objektu řadiče domény do umístění v rámci výchozí organizační jednotky řadičů domény.

1. Nekompatibilní Změna formátu KDS šifrované vyrovnávací paměti z Windows Serveru 2012 R2 na Windows Server 2016

   Oprava zabezpečení KDS byla představena ve Windows serveru 2016, která mění formát KDS šifrovaných vyrovnávacích pamětí; v některých případech se v systému Windows Server 2012 a Windows Server 2012 R2 nezdaří dešifrování těchto vyrovnávacích pamětí. Obrácený směr je v pořádku – vyrovnávací paměti, které jsou zašifrované KDS na Windows Serveru 2012 a Windows Server 2012 R2, se vždycky úspěšně dešifrují v systému Windows Server 2016 a novějším. Pokud na řadičích domény v doménách služby Active Directory běží kombinace těchto operačních systémů, můžou se nahlásit občasné chyby dešifrování ochrany heslem Azure AD. Není možné přesně předpovědět časování nebo příznaky těchto selhání s ohledem na povahu opravy zabezpečení a s tím, že je nedeterministické, ke kterému řadiči domény Azure AD s ochranou hesel se v daný okamžik zašifruje data.

   Pro tento problém není k dispozici žádné alternativní řešení, než aby se nespouštěla kombinace těchto nekompatibilních operačních systémů ve vašich doménách služby Active Directory. Jinými slovy, měli byste spustit pouze řadiče domény se systémem Windows Server 2012 a Windows Server 2012 R2 nebo byste měli spustit pouze systém Windows Server 2016 a vyšší řadiče domény.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>Agent DC se domnívá, že doménová struktura není zaregistrovaná.

Příznakem tohoto problému jsou 30016 události, které se zaznamenávají do Agent\Admin kanálu DC, který uvádí část:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Existují dva možné příčiny tohoto problému.

1. Doménová struktura není zaregistrovaná. Chcete-li tento problém vyřešit, spusťte prosím příkaz Register-AzureADPasswordProtectionForest, jak je popsáno v tématu [požadavky na nasazení](howto-password-ban-bad-on-premises-deploy.md).
1. Byla zaregistrována doménová struktura, ale agent řadiče domény nemůže dešifrovat registrační data doménové struktury. Tento případ má stejnou hlavní příčinu jako problém #2 uvedený výše v části [Agent DC není schopen šifrovat nebo dešifrovat soubory zásad hesel](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). Snadným způsobem, jak tuto teoretickou potvrdit, je to, že se tato chyba zobrazí jenom v agentech DC, které běží na řadičích domény s Windows Serverem 2012 nebo Windows server 2012R2, zatímco agenti DC spuštěné v systému Windows Server 2016 a novějších řadičích domény jsou přesné. Alternativní řešení je stejné: Upgradujte všechny řadiče domény na Windows Server 2016 nebo novější.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Jsou přijímána slabá hesla, ale neměla by být

Tento problém může mít několik příčin.

1. Na vašich agentech řadiče domény běží verze softwaru Public Preview, jejíž platnost vypršela. Viz [platnost softwaru agenta řadiče domény Public Preview](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Agenti řadičů domény se nedají stáhnout zásady nebo nemůžou dešifrovat existující zásady. Vyhledejte možné příčiny ve výše uvedených tématech.

1. Režim prosazování zásad hesla je stále nastaven na audit. Pokud tato konfigurace vstoupí v platnost, překonfigurujte ji tak, aby se vynutila pomocí portálu ochrany hesel Azure AD. Další informace najdete v tématu [režimy provozu](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Zásady hesel jsou zakázané. Pokud tato konfigurace vstoupí v platnost, překonfigurujte ji na povolenou pomocí portálu ochrany hesel Azure AD. Další informace najdete v tématu [režimy provozu](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Nenainstalovali jste software agenta DC na všechny řadiče domény v doméně. V takové situaci je obtížné zajistit, aby vzdálení klienti Windows během operace změny hesla nacíleny na konkrétní řadič domény. Pokud si myslíte, že jste se úspěšně zaměřili na konkrétní řadič domény, na kterém je nainstalovaný software agenta DC, můžete ověřit tak, že dvakrát zkontrolujete protokol událostí správce agenta řadiče domény: bez ohledu na výsledek bude k dispozici alespoň jedna událost, která bude dokumentovat výsledek ověření hesla. Pokud pro uživatele, jehož heslo je změněno, není k dispozici žádná událost, změna hesla byla zřejmě zpracována jiným řadičem domény.

   Jako alternativní test zkuste setting\changing hesla při přihlášení přímo na řadič domény, na kterém je nainstalovaný software agenta DC. Tato technika se nedoporučuje pro produkční domény služby Active Directory.

   I když je v souladu s těmito omezeními podporováno přírůstkové nasazení softwaru agenta DC, společnost Microsoft důrazně doporučuje, aby byl software agenta DC nainstalován na všech řadičích domény v doméně co nejdříve.

1. Algoritmus ověřování hesla může ve skutečnosti fungovat podle očekávání. Podívejte [se, jak jsou hesla vyhodnocována](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe se nepovedlo nastavit slabé heslo pro režim obnovení adresářových služeb.

Služba Active Directory vždy ověří nové heslo režimu opravy adresářových služeb, aby bylo zajištěno, že splňuje požadavky na složitost hesla domény. Toto ověření také volá knihovny DLL filtru hesel, jako je ochrana heslem Azure AD. Pokud se nové heslo DSRM odmítne, zobrazí se následující chybová zpráva:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Když služba Azure AD Password Protection zaznamená události protokolu událostí ověření hesla pro heslo služby Active Directory DSRM, předpokládá se, že zprávy protokolu událostí nebudou obsahovat uživatelské jméno. K tomuto chování dochází, protože účet DSRM je místní účet, který není součástí skutečné domény služby Active Directory.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Zvýšení úrovně repliky řadiče domény se nezdařilo kvůli slabému heslu DSRM

Během procesu povýšení řadiče domény se nové heslo režimu opravy adresářových služeb pošle do existujícího řadiče domény pro ověřování. Pokud se nové heslo DSRM odmítne, zobrazí se následující chybová zpráva:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Stejně jako u výše uvedeného problému všechna událost výsledku ověření hesla ochrany heslem služby Azure AD budou mít pro tento scénář prázdná uživatelská jména.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Degradování řadiče domény se nezdařilo kvůli slabému heslu místního správce.

Je podporováno snížení úrovně řadiče domény, na kterém je stále spuštěný software agenta DC. Správci by si měli být vědomi, že software agenta DC během procesu degradace stále vynutil současné zásady hesel. Nové heslo účtu místního správce (zadané jako součást operace degradování) se ověřuje jako jakékoli jiné heslo. Microsoft doporučuje, aby v rámci procesu snížení úrovně řadiče domény byly zvolené zabezpečené hesla pro účty místních správců.

Po úspěšném snížení úrovně a restartování řadiče domény a opětovném spuštění jako normálního členského serveru se software agenta řadiče domény vrátí do provozu v pasivním režimu. Později je můžete odinstalovat.

## <a name="booting-into-directory-services-repair-mode"></a>Spuštění do režimu opravy adresářových služeb

Pokud je řadič domény spuštěný v režimu opravy adresářových služeb, knihovna DLL filtru hesel agenta řadiče domény detekuje tuto podmínku a způsobí, že se všechny aktivity ověřování a vynucování hesla budou deaktivovat bez ohledu na aktuálně aktivní konfiguraci zásad. Knihovna DLL filtru hesla agenta řadiče domény zaznamená událost upozornění 10023 do protokolu událostí správce, například:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Platnost softwaru agenta řadiče domény ve verzi Public Preview vypršela.

Během období veřejné verze Preview ochrany heslem Azure AD byl software agenta DC pevně zakódovaný, aby zastavil zpracování žádostí o ověření hesla v následujících datech:

* Verze 1.2.65.0 zastaví zpracování požadavků na ověření hesla od září 1 2019.
* Verze 1.2.25.0 a předchozí zastavily zpracování žádostí o ověření hesla od července 1 2019.

Po uplynutí konečného termínu budou všechny časově omezené verze agenta řadiče domény generovat událost 10021 v protokolu událostí správce agenta řadiče domény při spuštění, který vypadá takto:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Po uplynutí konečného termínu budou všechny časově omezené verze agenta řadiče domény generovat událost 10022 v protokolu událostí správce agenta řadiče domény při spuštění, který vypadá takto:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Vzhledem k tomu, že konečný termín je kontrolován pouze při počátečním spuštění, tyto události se nemusí zobrazit, dokud neuplyne konečný termín kalendáře. Jakmile je konečný termín rozpoznán, nebudou se automaticky schvalovat žádné negativní účinky na řadič domény ani na větší prostředí.

> [!IMPORTANT]
> Microsoft doporučuje, aby agenti řadiče domény s vypršenou platností verze Public Preview byli hned upgradováni na nejnovější verzi.

Snadný způsob, jak zjistit agenty řadiče domény v prostředí, které je potřeba upgradovat, je spuštění `Get-AzureADPasswordProtectionDCAgent` rutiny, třeba:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Pro toto téma je pole SoftwareVersion zjevnou klíčovou vlastností, která se má podívat na. Filtrování prostředí PowerShell můžete také použít k odfiltrování agentů řadiče domény, kteří jsou již ve verzi požadovaná základní verze, například:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Software proxy ochrany heslem služby Azure AD není časově omezený v jakékoli verzi. Společnost Microsoft stále doporučuje, aby se řadiče DC i proxy upgradovali na nejnovější verze hned po jejich vydání. `Get-AzureADPasswordProtectionProxy`Rutina se dá použít k vyhledání agentů proxy, kteří vyžadují upgrady, podobně jako v příkladu výše pro agenty řadiče domény.

Další podrobnosti o konkrétních postupech upgradu najdete v tématu [Upgrade agenta řadiče domény](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) a [upgrade služby proxy serveru](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) .

## <a name="emergency-remediation"></a>Nouzová náprava

Pokud dojde k situaci, kdy služba agenta DC způsobuje problémy, služba agenta řadiče domény se může okamžitě vypnout. Knihovna DLL filtru hesel agenta řadiče domény se stále pokouší zavolat nespuštěnou službu a bude protokolovat události upozornění (10012, 10013), ale během této doby budou přijata všechna příchozí hesla. Služba agenta DC se pak dá nakonfigurovat taky prostřednictvím Správce řízení služeb systému Windows s typem spuštění zakázáno, jak je potřeba.

Další mírou nápravy by bylo nastavit režim povolení na ne na portálu ochrany hesel Azure AD. Po stažení aktualizovaných zásad přejde každá služba agenta řadiče domény do režimu quiescent, ve kterém jsou všechna hesla přijímána tak, jak je. Další informace najdete v tématu [režimy provozu](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Instalační

Pokud se rozhodnete odinstalovat software ochrany heslem služby Azure AD a vyčistit všechny související stavy z domén a doménových struktur, můžete tuto úlohu provést pomocí následujících kroků:

> [!IMPORTANT]
> Tyto kroky je důležité provést v uvedeném pořadí. Pokud bude kterákoli instance služby proxy spuštěna, bude se pravidelně znovu vytvářet Objekt serviceConnectionPoint. Pokud je spuštěná kterákoli instance služby agenta řadiče domény, bude se pravidelně znovu vytvářet Objekt serviceConnectionPoint a stav adresáře SYSVOL.

1. Odinstalujte software proxy ze všech počítačů. Tento **Krok nevyžaduje restartování** .
2. Odinstalujte software agenta DC ze všech řadičů domény. Tento krok **vyžaduje** restart.
3. Ručně odeberte všechny spojovací body služby proxy v každém názvovém kontextu domény. Umístění těchto objektů může být zjištěno s následujícím příkazem PowerShellu služby Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Vynechejte hvězdičku ("*") na konci hodnoty proměnné $keywords.

   Výsledné objekty, které byly nalezeny prostřednictvím `Get-ADObject` příkazu, lze následně přesměrovat do kanálu `Remove-ADObject` nebo odstranit ručně.

4. Ručně odeberte všechny spojovací body agenta DC v každém názvovém kontextu domény. V závislosti na tom, jak rozsáhlá verze softwaru byla nasazena, může být jeden z těchto objektů na řadič domény v doménové struktuře. Umístění tohoto objektu může být zjištěno pomocí následujícího příkazu prostředí PowerShell služby Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Výsledné objekty, které byly nalezeny prostřednictvím `Get-ADObject` příkazu, lze následně přesměrovat do kanálu `Remove-ADObject` nebo odstranit ručně.

   Vynechejte hvězdičku ("*") na konci hodnoty proměnné $keywords.

5. Ručně odeberte stav konfigurace na úrovni doménové struktury. Stav konfigurace doménové struktury je udržován v kontejneru v názvovém kontextu konfigurace služby Active Directory. Můžete ji zjistit a odstranit následujícím způsobem:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ručně odstraňte všechny stavy související se složkou SYSVOL ručním odstraněním následující složky a veškerého jejího obsahu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   V případě potřeby je k této cestě taky možné přistupovat místně na daném řadiči domény. výchozí umístění by mělo být podobné následující cestě:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Tato cesta se liší, pokud je sdílená složka SYSVOL nakonfigurovaná na jiném než výchozím umístění.

## <a name="next-steps"></a>Další kroky

[Nejčastější dotazy k ochraně hesel Azure AD](howto-password-ban-bad-on-premises-faq.md)

Další informace o globálním a vlastním seznamu zakázaných hesel najdete v článku [zákaz chybných hesel](concept-password-ban-bad.md) .
