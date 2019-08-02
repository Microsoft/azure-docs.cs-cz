---
title: Řešení potíží v ochraně heslem Azure AD – Azure Active Directory
description: Vysvětlení běžných potíží s ochranou heslem Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d96f5bb189dfd20c65fc6fc6ddcb8fff66d52ff
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666241"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Řešení potíží s ochranou hesel Azure AD

Po nasazení ochrany heslem služby Azure AD může být potřeba řešení potíží. Tento článek podrobně popisuje některé běžné kroky při řešení potíží.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Agent řadiče domény nemůže najít proxy v adresáři.

Hlavním příznakem tohoto problému jsou 30017 události v protokolu událostí správce agenta řadiče domény.

Obvyklou příčinou tohoto problému je to, že proxy server ještě není zaregistrovaný. Pokud je proxy server zaregistrován, může dojít k prodlevě z důvodu latence replikace služby AD, dokud konkrétnímu agentovi řadiče domény nebude moci daný proxy server zobrazit.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Agent řadiče domény nemůže komunikovat s proxy serverem.

Hlavním příznakem tohoto problému jsou 30018 události v protokolu událostí správce agenta řadiče domény. Tento problém může mít několik možných příčin:

1. Agent řadiče domény se nachází v izolované části sítě, která neumožňuje síťové připojení k registrovaným proxy serverem. Tento problém může být proto neškodný, pokud ostatní agenti řadiče domény mohou komunikovat s proxy serverem, aby mohli stahovat zásady hesel z Azure, které se pak získají izolovaným řadičem domény prostřednictvím replikace souborů zásad ve sdílené složce SYSVOL.

1. Hostitelský počítač proxy blokuje přístup k koncovému bodu mapovače koncových bodů RPC (port 135).

   Instalační program proxy ochrany heslem služby Azure AD automaticky vytvoří příchozí pravidlo brány Windows Firewall, které umožňuje přístup k portu 135. Pokud je toto pravidlo později odstraněno nebo zakázáno, agenti řadiče domény nebudou moci komunikovat se službou proxy. Pokud byla předdefinovaná brána Windows Firewall zakázána místo jiného produktu brány firewall, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup k portu 135.

1. Hostitelský počítač proxy blokuje přístup ke koncovému bodu RPC (dynamický nebo statický), na kterém naslouchá služba proxy.

   Instalační program proxy ochrany heslem služby Azure AD automaticky vytvoří příchozí pravidlo brány Windows Firewall, které umožňuje přístup k jakýmkoli vstupním portům, na které naslouchá služba Azure AD Password Protection proxy. Pokud je toto pravidlo později odstraněno nebo zakázáno, agenti řadiče domény nebudou moci komunikovat se službou proxy. Pokud byla předdefinovaná brána Windows Firewall zakázaná místo jiného produktu firewallu, musíte bránu firewall nakonfigurovat tak, aby povolovala přístup k jakýmkoli vstupním portům, na které naslouchá služba Azure AD Password Protection proxy. Tato konfigurace může být konkrétnější, pokud byla proxy služba nakonfigurovaná tak, aby naslouchala konkrétnímu statickému portu RPC (pomocí `Set-AzureADPasswordProtectionProxyConfiguration` rutiny).

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy služba nemůže komunikovat s Azure

1. Zajistěte, aby měl proxy počítač připojení ke koncovým bodům uvedeným v [požadavcích na nasazení](howto-password-ban-bad-on-premises-deploy.md).

1. Ujistěte se, že je doménová struktura a všechny proxy servery zaregistrované u stejného tenanta Azure.

   Tento požadavek můžete ověřit spuštěním `Get-AzureADPasswordProtectionProxy` rutin prostředí PowerShell a `Get-AzureADPasswordProtectionDCAgent` potom porovnejte `AzureTenant` vlastnost jednotlivých vrácených položek. Pro správnou operaci musí být oznámený název tenanta stejný ve všech agentech DC a proxy serverech.

   Pokud neshoda s registrací tenanta Azure existuje, můžete tento problém vyřešit spuštěním `Register-AzureADPasswordProtectionProxy` rutin a/nebo `Register-AzureADPasswordProtectionForest` PowerShellu podle potřeby a tím, že použijete přihlašovací údaje ze stejného tenanta Azure pro všechny registrace.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Agent řadiče domény nemůže šifrovat nebo dešifrovat soubory zásad hesel.

Tento problém se může vyskytnout v důsledku nejrůznějších příznaků, ale obvykle má běžnou hlavní příčinu.

Ochrana heslem Azure AD má kritickou závislost na funkcích šifrování a dešifrování poskytovaných službou Microsoft Key Distribution Service, která je dostupná na řadičích domény se systémem Windows Server 2012 a novějším. Služba KDS musí být povolená a funkční na všech řadičích domény se systémem Windows Server 2012 a novějším v doméně.

Ve výchozím nastavení je režim spuštění služby KDS nakonfigurovaný jako ruční (spuštění triggeru). Tato konfigurace znamená, že při prvním pokusu o použití služby se klient spustí na vyžádání. Tento výchozí režim spuštění služby je přijatelný pro fungování ochrany hesel služby Azure AD.

Pokud je režim spuštění služby KDS nakonfigurovaný tak, aby byl zakázaný, musí se tato konfigurace opravit předtím, než bude správně fungovat ochrana heslem Azure AD.

Jednoduchý test tohoto problému je ruční spuštění služby KDS, a to buď prostřednictvím konzoly MMC pro správu služby, nebo pomocí jiných nástrojů pro správu (například spuštěním příkazu "net start kdssvc" z konzoly příkazového řádku). Očekává se, že se služba KDS úspěšně spustí a zůstane spuštěná.

Nejběžnější hlavní příčinou nefunkčnosti služby KDS je, že objekt řadiče domény služby Active Directory je umístěný mimo výchozí organizační jednotku řadiče domény. Tato konfigurace není službou KDS podporována a nejedná se o omezení vyplývající z ochrany hesel služby Azure AD. Opravou této podmínky je přesunutí objektu řadiče domény do umístění v rámci výchozí organizační jednotky řadičů domény.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Jsou přijímána slabá hesla, ale neměla by být

Tento problém může mít několik příčin.

1. Agenti řadičů domény se nedají stáhnout zásady nebo nemůžou dešifrovat existující zásady. Vyhledejte možné příčiny ve výše uvedených tématech.

1. Režim prosazování zásad hesla je stále nastaven na audit. Pokud tato konfigurace vstoupí v platnost, překonfigurujte ji tak, aby se vynutila pomocí portálu ochrany hesel Azure AD. Viz [Povolení ochrany heslem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Zásady hesel jsou zakázané. Pokud tato konfigurace vstoupí v platnost, překonfigurujte ji na povolenou pomocí portálu ochrany hesel Azure AD. Viz [Povolení ochrany heslem](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Nenainstalovali jste software agenta DC na všechny řadiče domény v doméně. V takové situaci je obtížné zajistit, aby vzdálení klienti Windows během operace změny hesla nacíleny na konkrétní řadič domény. Pokud jste si myslíte, že jste se úspěšně zaměřili na konkrétní řadič domény, na kterém je nainstalovaný software agenta DC, můžete ověřit tak, že dvakrát zkontrolujete protokol událostí správce agenta řadiče domény: bez ohledu na výsledek bude obsahovat alespoň jednu událost, která bude dokumentovat výsledek hesla. Export. Pokud pro uživatele, jehož heslo je změněno, není k dispozici žádná událost, změna hesla byla zřejmě zpracována jiným řadičem domény.

   Jako alternativní test zkuste setting\changing hesla při přihlášení přímo na řadič domény, na kterém je nainstalovaný software agenta DC. Tato technika se nedoporučuje pro produkční domény služby Active Directory.

   I když je v souladu s těmito omezeními podporováno přírůstkové nasazení softwaru agenta DC, společnost Microsoft důrazně doporučuje, aby byl software agenta DC nainstalován na všech řadičích domény v doméně co nejdříve.

1. Algoritmus ověřování hesla může ve skutečnosti fungovat podle očekávání. Podívejte [se, jak jsou hesla vyhodnocována](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Nástroje Ntdsutil. exe se nepodařilo nastavit slabé heslo pro režim obnovení adresářových služeb.

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

Když služba Azure AD Password Protection zaznamená události protokolu událostí ověření hesla pro heslo služby Active Directory DSRM, předpokládá se, že zprávy protokolu událostí nebudou obsahovat uživatelské jméno. K tomu dochází, protože účet DSRM je místní účet, který není součástí skutečné domény služby Active Directory.  

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

Pokud je řadič domény spuštěný v režimu opravy adresářových služeb, služba agenta řadiče domény tuto podmínku detekuje a způsobí zakázání všech ověření a aktivit vynucení hesla bez ohledu na aktuálně aktivní konfiguraci zásad.

## <a name="emergency-remediation"></a>Nouzová náprava

Pokud dojde k situaci, kdy služba agenta DC způsobuje problémy, služba agenta řadiče domény se může okamžitě vypnout. Knihovna DLL filtru hesel agenta řadiče domény se stále pokouší zavolat nespuštěnou službu a bude protokolovat události upozornění (10012, 10013), ale během této doby budou přijata všechna příchozí hesla. Služba agenta DC se pak dá nakonfigurovat taky prostřednictvím Správce řízení služeb systému Windows s typem spuštění zakázáno, jak je potřeba.

Další mírou nápravy by bylo nastavit režim povolení na ne na portálu ochrany hesel Azure AD. Po stažení aktualizovaných zásad přejde každá služba agenta řadiče domény do režimu quiescent, ve kterém jsou všechna hesla přijímána tak, jak je. Další informace najdete v tématu [režim](howto-password-ban-bad-on-premises-operations.md#enforce-mode)vynutilení.

## <a name="removal"></a>Odebrání

Pokud se rozhodnete odinstalovat software ochrany heslem služby Azure AD a vyčistit všechny související stavy z domén a doménových struktur, můžete tuto úlohu provést pomocí následujících kroků:

> [!IMPORTANT]
> Tyto kroky je důležité provést v uvedeném pořadí. Pokud bude kterákoli instance služby proxy spuštěna, bude se pravidelně znovu vytvářet Objekt serviceConnectionPoint. Pokud je spuštěná kterákoli instance služby agenta řadiče domény, bude se pravidelně znovu vytvářet Objekt serviceConnectionPoint a stav adresáře SYSVOL.

1. Odinstalujte software proxy ze všech počítačů. Tento krok nevyžaduje restartování.
2. Odinstalujte software agenta DC ze všech řadičů domény. Tento krok **vyžaduje** restart.
3. Ručně odeberte všechny spojovací body služby proxy v každém názvovém kontextu domény. Umístění těchto objektů může být zjištěno s následujícím příkazem PowerShellu služby Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Vynechejte hvězdičku ("*") na konci hodnoty proměnné $keywords.

   Výsledné objekty, které byly nalezeny prostřednictvím `Get-ADObject` příkazu, lze následně přesměrovat do `Remove-ADObject`kanálu nebo odstranit ručně.

4. Ručně odeberte všechny spojovací body agenta DC v každém názvovém kontextu domény. V závislosti na tom, jak rozsáhlá verze softwaru byla nasazena, může být jeden z těchto objektů na řadič domény v doménové struktuře. Umístění tohoto objektu může být zjištěno pomocí následujícího příkazu prostředí PowerShell služby Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Výsledné objekty, které byly nalezeny prostřednictvím `Get-ADObject` příkazu, lze následně přesměrovat do `Remove-ADObject`kanálu nebo odstranit ručně.

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

## <a name="next-steps"></a>Další postup

[Nejčastější dotazy k ochraně hesel Azure AD](howto-password-ban-bad-on-premises-faq.md)

Další informace o globálním a vlastním seznamu zakázaných hesel najdete v článku [zákaz chybných hesel](concept-password-ban-bad.md) .
