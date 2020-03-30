---
title: Poradce při potížích s místní ochranou heslem Azure AD
description: Zjistěte, jak řešit potíže s ochranou heslem služby Azure AD pro místní prostředí služby Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263644"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Poradce při potížích: Místní ochrana heslem Azure AD

Po nasazení ochrany heslem Azure AD může být vyžadováno řešení potíží. Tento článek je podrobně popsán, abyste lépe porozuměli některým běžným krokům řešení potíží.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent řadiče domény nemůže najít proxy server v adresáři.

Hlavním příznakem tohoto problému je 30017 události v protokolu událostí agenta řadiče domény.

Obvyklou příčinou tohoto problému je, že proxy ještě nebyla zaregistrována. Pokud byl zaregistrován proxy server, může dojít k určitému zpoždění z důvodu latence replikace služby AD, dokud konkrétní agent řadiče domény nebude moci zobrazit tento proxy server.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent dc není schopen komunikovat s proxy serverem

Hlavním příznakem tohoto problému je 30018 události v protokolu událostí agenta řadiče domény. Tento problém může mít několik možných příčin:

1. Agent řadiče domény se nachází v izolované části sítě, která neumožňuje připojení k síti k registrovaným proxy serverům. Tento problém může být neškodný, pokud ostatní agenti řadiče domény mohou komunikovat s proxy servery za účelem stažení zásad hesel z Azure. Po stažení budou tyto zásady získány izolovaným řadičem domény prostřednictvím replikace souborů zásad ve sdílené složce sysvol.

1. Hostitelský počítač proxy blokuje přístup ke koncovému bodu mapovače koncových bodů Vzdáleného volání procedur (port 135)

   Instalační program proxy služby Azure AD Password Protection automaticky vytvoří příchozí pravidlo brány Windows Firewall, které umožňuje přístup k portu 135. Pokud je toto pravidlo později odstraněno nebo zakázáno, nebudou moci agenti řadiče domény komunikovat se službou Proxy. Pokud byla vestavěná brána Windows Firewall zakázána místo jiného produktu brány firewall, je nutné tuto bránu firewall nakonfigurovat tak, aby umožňovala přístup k portu 135.

1. Hostitelský počítač proxy blokuje přístup ke koncovému bodu Vzdáleného volání procedur (dynamickému nebo statickému) naslouchaném službou Proxy

   Instalační program proxy serveru ochrany hesel Azure AD automaticky vytvoří příchozí pravidlo brány Windows Firewall, které umožňuje přístup ke všem příchozím portům, které poslouchá služba Proxy ochrany hesel Azure AD. Pokud je toto pravidlo později odstraněno nebo zakázáno, nebudou moci agenti řadiče domény komunikovat se službou Proxy. Pokud byla integrovaná brána Windows Firewall zakázána místo jiného produktu brány firewall, je nutné tuto bránu firewall nakonfigurovat tak, aby umožňovala přístup k příchozím portům, které poslouchá služba Proxy ochrany hesel Azure AD. Tato konfigurace může být konkrétnější, pokud byla služba Proxy nakonfigurována tak, `Set-AzureADPasswordProtectionProxyConfiguration` aby naslouchala na konkrétním statickém portu Vzdáleného volání procedur (pomocí rutiny).

1. Hostitelský počítač proxy není nakonfigurován tak, aby umožňoval řadičům domény přihlášení k počítači. Toto chování je řízeno prostřednictvím přiřazení uživatelských oprávnění "Přístup k tomuto počítači ze sítě". Toto oprávnění musí být uděleno všem řadičům domény ve všech doménách v doménové struktuře. Toto nastavení je často omezeno jako součást většího úsilí o posílení zabezpečení sítě.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Služba proxy nemůže komunikovat s Azure

1. Ujistěte se, že počítač proxy má připojení ke koncovým bodům uvedeným v [požadavcích na nasazení](howto-password-ban-bad-on-premises-deploy.md).

1. Ujistěte se, že doménové struktury a všechny proxy servery jsou registrovány proti stejnému tenantovi Azure.

   Tento požadavek můžete zkontrolovat `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` spuštěním rutin a rutin prostředí `AzureTenant` PowerShell a poté porovnat vlastnost každé vrácené položky. Pro správnou funkci musí být ohlášený název klienta stejný ve všech agentech řadiče domény a serverech proxy.

   Pokud existuje podmínka neshody registrace klienta Azure, tento problém `Register-AzureADPasswordProtectionProxy` lze `Register-AzureADPasswordProtectionForest` vyřešit spuštěním rutiny a/nebo PowerShell podle potřeby, ujistěte se, že používat přihlašovací údaje od stejného klienta Azure pro všechny registrace.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Agent řadiče domény nemůže šifrovat nebo dešifrovat soubory zásad hesel.

Azure AD Password Protection má kritickou závislost na funkci šifrování a dešifrování poskytované službou Microsoft Key Distribution Service. Selhání šifrování nebo dešifrování se mohou projevit s různými příznaky a mají několik možných příčin.

1. Ujistěte se, že je služba KDS povolená a funkční ve všech řadičích domény systému Windows Server 2012 a novějších v doméně.

   Ve výchozím nastavení je režim spuštění služby Služby KDS nakonfigurován jako ruční (trigger Start). Tato konfigurace znamená, že při prvním pokusu klienta o použití služby je spuštěna na vyžádání. Tento výchozí režim spuštění služby je přijatelné pro Azure AD heslem.

   Pokud byl režim spuštění služby KDS nakonfigurován na Zakázáno, musí být tato konfigurace opravena, aby služba Azure AD Password Protection fungovala správně.

   Jednoduchý test tohoto problému je ruční spuštění služby KDS, a to buď prostřednictvím konzoly MMC pro správu služby, nebo pomocí jiných nástrojů pro správu (například spustit "net start kdssvc" z konzoly příkazového řádku). Očekává se, že služba KDS bude úspěšně spuštěna a zůstane spuštěná.

   Nejběžnější hlavní příčinou spuštění služby KDS je, že objekt řadiče domény služby Active Directory je umístěn mimo výchozí jednotku řadičů domény. Tato konfigurace není podporována službou KDS a není omezení matné uložené službou Azure AD Password Protection. Oprava této podmínky je přesunutí objektu řadiče domény do umístění pod výchozí jednotkou řadičů domény.

1. Nekompatibilní změna formátu šifrované vyrovnávací paměti KDS ze systému Windows Server 2012 R2 na Windows Server 2016

   V systému Windows Server 2016 byla zavedena oprava zabezpečení KDS, která upravuje formát šifrovaných vyrovnávacích pamětí KDS. Tyto vyrovnávací paměti se někdy nezdaří dešifrovat v systémech Windows Server 2012 a Windows Server 2012 R2. Opačný směr je v pořádku – vyrovnávací paměti, které jsou šifrované kds v systémech Windows Server 2012 a Windows Server 2012 R2, budou vždy úspěšně dešifrovat v systému Windows Server 2016 a novějším. Pokud řadiče domény ve vašich doménách služby Active Directory jsou spuštěny mix těchto operačních systémů, mohou být hlášeny občasné chyby dešifrování Azure AD Password Protection. Není možné přesně předpovědět načasování nebo příznaky těchto selhání vzhledem k povaze opravy zabezpečení a vzhledem k tomu, že je nedeterministický, který agent ochrany heslem Azure AD DC, na kterém řadiči domény bude šifrovat data v daném okamžiku.

   Společnost Microsoft zkoumá opravu tohoto problému, ale zatím není k dispozici žádný ETA. Do té doby neexistuje žádné řešení pro tento problém, než nespouštět kombinaci těchto nekompatibilních operačních systémů v doménách služby Active Directory. Jinými slovy, měli byste spouštět pouze řadiče domény Windows Server 2012 a Windows Server 2012 R2, nebo byste měli spouštět pouze windows server 2016 a vyšší řadiče domény.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Slabá hesla jsou přijímána, ale neměla by být

Tento problém může mít několik příčin.

1. Agenty řadiče domény jsou spuštěna verze softwaru Public Preview, jejíž platnost vypršela. Viz [Public preview DC agent software vypršela](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Agentdomény nemůže stáhnout zásadu nebo nemůže dešifrovat existující zásady. Zkontrolujte možné příčiny ve výše uvedených tématech.

1. Režim vynucení zásad hesla je stále nastaven na audit. Pokud je tato konfigurace v platnosti, překonfigurujte ji vynutit pomocí portálu Azure AD Password Protection. Další informace naleznete [v tématu Provozní režimy](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Zásady hesel byly zakázány. Pokud je tato konfigurace v platnosti, překonfigurujte ji na povolenou pomocí portálu Azure AD Password Protection. Další informace naleznete [v tématu Provozní režimy](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Nenainstalovali jste software agenta řadiče domény do všech řadičů domény v doméně. V takovém případě je obtížné zajistit, aby vzdálení klienti systému Windows cílit na konkrétní řadič domény během operace změny hesla. Pokud se domníváte, že jste úspěšně zacílili na konkrétní řadič domény, kde je nainstalován software agenta řadiče domény, můžete ověřit dvojitou kontrolou protokolu událostí správce agenta řadiče domény: bez ohledu na výsledek bude existovat alespoň jedna událost, která zdokumentuje výsledek hesla Ověření. Pokud pro uživatele, jehož heslo se změní, není k dispozici žádná událost, byla změna hesla pravděpodobně zpracována jiným řadičem domény.

   Jako alternativní test zkuste nastavit\změna hesla při přihlášení přímo do řadiče domény, kde je nainstalován software agenta řadiče domény. Tato technika se nedoporučuje pro produkční domény služby Active Directory.

   Zatímco přírůstkové nasazení softwaru agenta řadiče domény je podporováno s výhradou těchto omezení, společnost Microsoft důrazně doporučuje, aby byl software agenta řadiče domény nainstalován ve všech řadičích domény v doméně co nejdříve.

1. Algoritmus ověření hesla může ve skutečnosti fungovat podle očekávání. Podívejte se na informace o [tom, jak se vyhodnocují hesla](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Soubor Ntdsutil.exe nenastavil slabé heslo služby DSRM

Služba Active Directory vždy ověří nové heslo režimu opravy adresářových služeb, aby se ujistila, že splňuje požadavky na složitost hesla domény. toto ověření také volá do dll filtru hesel, jako je Ochrana heslem Azure AD. Pokud je nové heslo služby DSRM odmítnuto, zobrazí se následující chybová zpráva:

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

Když Azure AD Password Protection protokoluje události protokolu událostí ověření hesla pro heslo služby Active Directory DSRM, očekává se, že zprávy protokolu událostí nebude obsahovat uživatelské jméno. K tomuto chování dochází, protože účet DSRM je místní účet, který není součástí skutečné domény služby Active Directory.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Povýšení replikřadiče domény se nezdaří z důvodu slabého hesla služby DSRM

Během procesu propagace řadiče domény bude nové heslo režimu opravy adresářových služeb odesláno do existujícího řadiče domény v doméně k ověření. Pokud je nové heslo služby DSRM odmítnuto, zobrazí se následující chybová zpráva:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Stejně jako ve výše uvedeném problému, všechny Azure AD Password Protection výsledek události ověření hesla bude mít prázdné uživatelské názvy pro tento scénář.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Snížení úrovně řadiče domény se nezdaří z důvodu slabého hesla místního správce

Je podporována snížením úrovně řadiče domény, na který je stále spuštěn software agenta řadiče domény. Správci by si však měli být vědomi toho, že software agenta řadiče domény nadále vynucuje aktuální zásady hesel během postupu snížení úrovně. Nové heslo účtu místního správce (zadané jako součást operace snížení úrovně) je ověřeno stejně jako jakékoli jiné heslo. Společnost Microsoft doporučuje, aby byla v rámci postupu snížení úrovně řadiče domény vybrána zabezpečená hesla pro místní účty správce.

Jakmile se snížení úrovně podaří a řadič domény byl restartován a je opět spuštěn jako normální členský server, software agenta řadiče domény se vrátí ke spuštění v pasivním režimu. To pak může být odinstalován kdykoliv.

## <a name="booting-into-directory-services-repair-mode"></a>Spuštění do režimu opravy adresářových služeb

Pokud je řadič domény spuštěn do režimu opravy adresářových služeb, dll filtru zabezpečení pro zabezpečení agenta řadiče domény tuto podmínku rozpozná a způsobí zakázání všech aktivit ověření hesla nebo vynucení bez ohledu na aktuálně aktivní zásady Konfigurace. DLL filtru hesel agenta řadiče domény zaznamená do protokolu událostí správce událost 10023, například:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Vypršela platnost softwaru agenta řadiče domény ve verzi Public Preview.

Během období veřejné verze preview azure ad password protection byl software agenta řadiče domény pevně zakódován, aby se zastavilo zpracování požadavků na ověření hesla v následujících datech:

* Verze 1.2.65.0 přestane zpracovávat žádosti o ověření hesla v září 1 2019.
* Verze 1.2.25.0 a předchozí zastavení zpracování žádostí o ověření hesla v červenci 1 2019.

S blížícím se termínem budou všechny časově omezené verze agentů řadiče domény vyzařovat událost 10021 v protokolu událostí správce agenta řadiče domény při spuštění, který vypadá takto:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Po uplynutí lhůty budou všechny časově omezené verze agentů řadiče domény vyzařovat událost 10022 v protokolu událostí správce agenta řadiče domény při spuštění, který vypadá takto:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Vzhledem k tomu, že konečný termín je kontrolován pouze při počátečním spuštění, nemusí se tyto události zobrazit až dlouho po uplynutí kalendářního termínu. Po rozpoznání konečného termínu nebudou automaticky schváleny žádné negativní účinky na řadič domény ani na větší prostředí než na všechna hesla.

> [!IMPORTANT]
> Společnost Microsoft doporučuje, aby agenti řadiče domény pro ukončenou platnost public preview byli okamžitě upgradovány na nejnovější verzi.

Snadný způsob, jak zjistit agenty řadiče domény ve `Get-AzureADPasswordProtectionDCAgent` vašem prostředí, které je třeba upgradovat, je spuštěním rutiny, například:

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

Pro toto téma, SoftwareVersion pole je samozřejmě klíčovou vlastností podívat. Filtrování prostředí PowerShell můžete také použít k odfiltrování agentů řadiče domény, kteří jsou již na nebo nad požadovanou verzí směrného plánu, například:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Software proxy ochrany hesel Azure AD není časově omezený v žádné verzi. Společnost Microsoft stále doporučuje, aby agenti řadiče domény i proxy byli po vydání upgradovány na nejnovější verze. Rutina `Get-AzureADPasswordProtectionProxy` může být použita k nalezení proxy agentů, které vyžadují inovace, podobně jako v příkladu výše pro agenty řadiče domény.

Další podrobnosti o konkrétních postupech upgradu naleznete [v části Upgrade agenta řadiče domény](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) a Upgrade [služby Proxy.](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)

## <a name="emergency-remediation"></a>Nouzová náprava

Pokud nastane situace, kdy služba agenta řadiče domény způsobuje problémy, může být služba agenta řadiče domény okamžitě ukončena. DLL filtru hesel agenta řadiče domény se stále pokouší volat nespuštěnou službu a bude protokolovat události upozornění (10012, 10013), ale během této doby jsou přijata všechna příchozí hesla. Služba agenta řadiče domény pak může být také konfigurována prostřednictvím Správce řízení služeb systému Windows s typem spuštění "Zakázáno" podle potřeby.

Dalším nápravným opatřením by bylo nastavení režimu Povolit na ne na portálu Azure AD Password Protection. Po stažení aktualizované zásady, každá služba agenta řadiče domény přejde do klidový režim, kde jsou všechna hesla přijata jako-je. Další informace naleznete [v tématu Provozní režimy](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Odstranění

Pokud se rozhodne odinstalovat software ochrany heslem Azure AD a vyčistit veškerý související stav z domény a doménové struktury, lze tento úkol provést pomocí následujících kroků:

> [!IMPORTANT]
> Je důležité provést tyto kroky v pořadí. Pokud je spuštěna jakákoli instance služby Proxy, bude pravidelně znovu vytvářet svůj objekt serviceConnectionPoint. Pokud je spuštěna jakákoli instance služby agenta řadiče domény, bude pravidelně znovu vytvářet svůj objekt serviceConnectionPoint a stav sysvol.

1. Odinstalujte software Proxy ze všech počítačů. Tento krok **nevyžaduje** restartování počítače.
2. Odinstalujte software DC Agent ze všech řadičů domény. Tento krok **vyžaduje** restartování.
3. Ručně odeberte všechny body připojení služby Proxy v každém kontextu pojmenování domény. Umístění těchto objektů může být zjištěno pomocí následujícího příkazu prostředí Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nevynechejte hvězdičku ("*") na konci $keywords hodnoty proměnné.

   Výsledné objekty nalezené pomocí příkazu `Get-ADObject` lze poté piped do `Remove-ADObject`, nebo odstranit ručně.

4. Ručně odeberte všechny body připojení agenta řadiče domény v každém kontextu pojmenování domény. V závislosti na tom, jak široce byl software nasazen, může být jeden z těchto objektů na řadič domény v doménové struktuře. Umístění tohoto objektu může být zjištěno pomocí následujícího příkazu prostředí Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Výsledné objekty nalezené pomocí příkazu `Get-ADObject` lze poté piped do `Remove-ADObject`, nebo odstranit ručně.

   Nevynechejte hvězdičku ("*") na konci $keywords hodnoty proměnné.

5. Ručně odeberte stav konfigurace na úrovni doménové struktury. Stav konfigurace doménové struktury je udržován v kontejneru v názvovém kontextu konfigurace služby Active Directory. To může být objevena a odstraněna takto:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ručně odstraňte veškerý stav související se sysvolručním odstraněním následující složky a veškerého jejího obsahu:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   V případě potřeby lze k této cestě přistupovat také místně na daném řadiči domény. výchozí umístění by bylo něco jako následující cesta:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Tato cesta se liší, pokud byla sdílená sysvol nakonfigurována v jiném než výchozím umístění.

## <a name="next-steps"></a>Další kroky

[Nejčastější dotazy týkající se ochrany heslem Azure AD](howto-password-ban-bad-on-premises-faq.md)

Další informace o globálních a vlastních seznamech zakázaných hesel naleznete v článku [Zákaz chybná hesla](concept-password-ban-bad.md)
