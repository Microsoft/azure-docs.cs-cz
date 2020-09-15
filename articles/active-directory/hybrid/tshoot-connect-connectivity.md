---
title: 'Azure AD Connect: řešení potíží s připojením služby Azure AD | Microsoft Docs'
description: Vysvětluje, jak řešit potíže s připojením pomocí Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 897c0f3c51d6d9bea1f90a66ccf50aa51e22f118
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088302"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Řešení potíží s připojením služby Azure AD
Tento článek vysvětluje, jak funguje konektivita mezi Azure AD Connect a Azure AD a jak řešit problémy s připojením. Tyto problémy se pravděpodobně zobrazují v prostředí s proxy server.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Poradce při potížích s připojením v Průvodci instalací
Azure AD Connect používá moderní ověřování (pomocí knihovny ADAL) pro ověřování. Průvodce instalací a modul pro synchronizaci musí správně nakonfigurovat machine.config, protože tyto dva jsou aplikace .NET.

V tomto článku se dozvíte, jak se Fabrikam připojuje k Azure AD prostřednictvím jeho proxy serveru. Proxy server má název fabrikamproxy a používá port 8080.

Nejdřív je potřeba zajistit, aby byla správně nakonfigurovaná [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) a **Microsoft Azure AD Synchronization Service** se po aktualizaci souboru machine.config znovu restartovala.
![Snímek obrazovky se zobrazí jako součást konfiguračního souboru pro Machine dot.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> V některých blogůch od jiných společností než Microsoft je uvedeno, že se místo toho mají udělat změny miiserver.exe.config. Tento soubor se ale při každém upgradu přepíše, takže když během počáteční instalace funguje, systém přestane při prvním upgradu fungovat. Z tohoto důvodu doporučujeme místo toho aktualizovat machine.config.
>
>

Proxy server musí mít také otevřené požadované adresy URL. Oficiální seznam je popsaný v části [adresy URL a rozsahy IP adres Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Z těchto adres URL je v následující tabulce absolutní minimum, které se může připojit ke službě Azure AD. Tento seznam neobsahuje žádné volitelné funkce, jako je třeba zpětný zápis hesla nebo Azure AD Connect Health. Najdete tady informace, které vám pomůžou při řešení potíží s počáteční konfigurací.

| URL | Port | Popis |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Slouží ke stažení seznamů CRL. |
| \*. verisign.com |HTTP/80 |Slouží ke stažení seznamů CRL. |
| \*. entrust.net |HTTP/80 |Slouží ke stažení seznamů CRL pro MFA. |
| \*.windows.net |HTTPS/443 |Používá se pro přihlášení ke službě Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Používá se pro MFA. |
| \*.microsoftonline.com |HTTPS/443 |Slouží ke konfiguraci adresáře služby Azure AD a importu/exportu dat. |

## <a name="errors-in-the-wizard"></a>Chyby v Průvodci
Průvodce instalací používá dva různé kontexty zabezpečení. Na stránce **připojit k Azure AD**se používá aktuálně přihlášený uživatel. Na stránce **Konfigurace**se změní na účet, na [kterém je spuštěna služba pro synchronizační modul](reference-connect-accounts-permissions.md#adsync-service-account). Pokud dojde k nějakému problému, zdá se, že už je na stránce **připojit ke službě Azure AD** v průvodci, protože konfigurace proxy serveru je globální.

Následující problémy jsou nejběžnější chyby, se kterými se setkáte v Průvodci instalací nástroje.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Průvodce instalací nebyl správně nakonfigurován.
Tato chyba se zobrazí, pokud se samotný průvodce nemůže připojit k proxy serveru.
![Snímek obrazovky znázorňující chybu: Nepodařilo se ověřit přihlašovací údaje.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Pokud se zobrazí tato chyba, ověřte, že je [machine.config](how-to-connect-install-prerequisites.md#connectivity) správně nakonfigurovaný.
* Pokud to vypadá správně, postupujte podle kroků v tématu [ověření připojení proxy serveru](#verify-proxy-connectivity) a zjistěte, jestli se problém vyskytuje i mimo průvodce.

### <a name="a-microsoft-account-is-used"></a>Používá se účet Microsoft.
Pokud místo **školy nebo účtu organizace** použijete **účet Microsoft** , zobrazí se obecná chyba.
![Používá se účet Microsoft.](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Koncový bod MFA není dostupný.
Tato chyba se zobrazí, pokud koncový bod **https://secure.aadcdn.microsoftonline-p.com** není dostupný a váš globální správce má povolený MFA.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Pokud se zobrazí tato chyba, ověřte, že se do proxy serveru přidal koncový bod **Secure.aadcdn.microsoftonline-p.com** .

### <a name="the-password-cannot-be-verified"></a>Heslo nelze ověřit.
Pokud se Průvodce instalací úspěšně připojí k Azure AD, ale samotné heslo se nedá ověřit, zobrazí se toto: ![ chybné heslo.](./media/tshoot-connect-connectivity/badpassword.png)

* Je heslo dočasné heslo a musí se změnit? Je vlastně správné heslo? Zkuste se přihlásit k `https://login.microsoftonline.com` (na jiném počítači, než je server Azure AD Connect), a ověřte, že je účet použitelný.

### <a name="verify-proxy-connectivity"></a>Ověření připojení proxy
Pokud chcete ověřit, jestli má server Azure AD Connect skutečné připojení k proxy serveru a Internetu, použijte k tomu, abyste viděli, jestli proxy povoluje webové požadavky, nebo ne. V příkazovém řádku PowerShellu spusťte příkaz `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` . (Technicky první volání je `https://login.microsoftonline.com` a tento identifikátor URI funguje stejně, ale druhý identifikátor URI je rychlejší, aby odpovídal.)

PowerShell ke kontaktování proxy serveru používá konfiguraci v souboru machine.config. Nastavení v příkazu winhttp/netsh by na tyto rutiny nemělo mít vliv.

Pokud je proxy server správně nakonfigurovaný, měli byste získat stav úspěch: ![ proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Pokud se vám **nedaří připojit ke vzdálenému serveru**, PowerShell se pokusí provést přímé volání bez použití proxy serveru nebo DNS není správně nakonfigurovaný. Ujistěte se, že je soubor **machine.config** správně nakonfigurovaný.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Pokud proxy server není správně nakonfigurovaný, zobrazí se chyba: ![ proxy200 ](./media/tshoot-connect-connectivity/invokewebrequest403.png)
 ![ proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Chyba | Text chyby | Komentář |
| --- | --- | --- |
| 403 |Forbidden |Proxy server nebyl otevřen pro požadovanou adresu URL. Znovu navštivte konfiguraci proxy serveru a ujistěte se, že jsou [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) otevřené. |
| 407 |Vyžaduje se ověřování proxy. |Proxy server vyžadoval přihlášení a žádné se nezadaly. Pokud vaše proxy server vyžaduje ověření, ujistěte se, že je toto nastavení nakonfigurované v machine.config. Také se ujistěte, že používáte doménové účty pro uživatele, který spouští Průvodce, a pro účet služby. |

### <a name="proxy-idle-timeout-setting"></a>Nastavení časového limitu nečinnosti proxy serveru
Když Azure AD Connect odešle požadavek na export do Azure AD, může Azure AD trvat až 5 minut, než se žádost zpracuje, než se vygeneruje odpověď. K tomu může dojít, pokud je v jedné žádosti o export mnoho objektů skupiny s velkými členství ve skupině. Zajistěte, aby byl časový limit nečinnosti proxy serveru delší než 5 minut. V opačném případě se může na Azure AD Connect serveru pozorovat přerušované problémy s připojením k Azure AD.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Způsob komunikace mezi Azure AD Connect a Azure AD
Pokud jste postupovali podle všech předchozích kroků a stále se nebudete moct připojit, můžete v tomto okamžiku začít hledat v protokolech sítě. Tato část dokumentuje normální a úspěšný vzor připojení. Také je uveden seznam běžných červených sleďů, které lze ignorovat při čtení síťových protokolů.

* Existují volání `https://dc.services.visualstudio.com` . Není nutné, aby byla tato adresa URL otevřená v proxy serveru, aby instalace proběhla úspěšně, a tato volání je možné ignorovat.
* Vidíte, že překlad DNS uvádí seznam aktuálních hostitelů, které mají být v oboru názvů DNS nsatc.net a jiné obory názvů, které nejsou v microsoftonline.com. Neexistují však žádné žádosti o webovou službu na skutečné názvy serverů a nemusíte tyto adresy URL přidávat do proxy serveru.
* Koncové body adminwebservice a provisioningapi jsou koncové body zjišťování a slouží k vyhledání skutečného koncového bodu, který má být použit. Tyto koncové body se liší v závislosti na vaší oblasti.

### <a name="reference-proxy-logs"></a>Referenční protokoly proxy
Tady je výpis z vlastního protokolu proxy serveru a stránky Průvodce instalací z místa, kde byla provedena (duplicitní položky byly odstraněny). Tato část se dá použít jako reference pro vlastní protokoly proxy a sítě. Skutečné koncové body se můžou ve vašem prostředí lišit (zejména tyto adresy URL jsou *kurzívou*).

**Připojení k Azure AD**

| Čas | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800 – kotva*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02 – Relay*. microsoftonline.com:443 |

**Konfigurace**

| Čas | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800 – kotva*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900 – kotva*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800 – kotva*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02 – Relay*. microsoftonline.com:443 |

**Počáteční synchronizace**

| Čas | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900 – kotva*. microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800 – kotva*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Chyby ověřování
V této části jsou popsány chyby, které lze vrátit z knihovny ADAL (knihovna ověřování používaná Azure AD Connect) a prostředí PowerShell. Vysvětlení chyby by vám měly porozumět dalším krokům.

### <a name="invalid-grant"></a>Neplatný grant
Neplatné uživatelské jméno nebo heslo. Další informace naleznete v tématu [nelze ověřit heslo](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Neznámý typ uživatele
Váš adresář služby Azure AD nejde najít nebo vyřešit. Možná se pokusíte přihlásit pomocí uživatelského jména v neověřené doméně?

### <a name="user-realm-discovery-failed"></a>Zjišťování sféry uživatele selhalo.
Problémy s konfigurací sítě nebo proxy serveru. Síť není dostupná. Informace najdete [v tématu Poradce při potížích s připojením v Průvodci instalací](#troubleshoot-connectivity-issues-in-the-installation-wizard)nástroje.

### <a name="user-password-expired"></a>Platnost hesla uživatele vypršela.
Vypršela platnost vašich přihlašovacích údajů. Změňte heslo.

### <a name="authorization-failure"></a>Chyba autorizace
Nepovedlo se autorizovat uživatele k provedení akce ve službě Azure AD.

### <a name="authentication-canceled"></a>Ověřování zrušeno
Výzva k ověření MFA (Multi-Factor Authentication) byla zrušena.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Nepovedlo se připojit k MS online
Ověřování bylo úspěšné, ale v Azure AD PowerShellu je problém s ověřením.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Je potřeba role globálního správce služby Azure AD.
Uživatel byl úspěšně ověřen. Uživatel ale nemá přiřazenou roli globálního správce. Tímto [způsobem můžete uživateli přiřadit roli globálního správce](../users-groups-roles/directory-assign-admin-roles.md) .

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management povoleno
Ověřování bylo úspěšné. Byla povolena Privileged Identity Management a momentálně nejste globální správce. Další informace najdete v tématu [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informace o společnosti nejsou k dispozici
Ověřování bylo úspěšné. Z Azure AD se nepovedlo načíst informace o společnosti.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informace o doméně nejsou k dispozici.
Ověřování bylo úspěšné. Z Azure AD se nepovedlo načíst informace o doméně.

### <a name="unspecified-authentication-failure"></a>Neurčená chyba ověřování
Zobrazuje se v Průvodci instalací jako Neočekávaná chyba. K tomu může dojít, pokud se pokusíte použít **účet Microsoft** místo **školy nebo účtu organizace**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Postup řešení potíží pro předchozí verze.
V případě verzí začínajících číslem buildu 1.1.105.0 (vydáno 2016) bylo vyřazení Pomocník pro přihlášení. Tato část a tato konfigurace by se už neměla vyžadovat, ale je zachovaná jako referenční.

Aby pomocník jednotného přihlašování fungoval, musí být nakonfigurováno WinHTTP. Tuto konfiguraci můžete provést pomocí [**příkazu netsh**](how-to-connect-install-prerequisites.md#connectivity).
![Snímek obrazovky se zobrazením okna příkazového řádku, ve kterém je spuštěný Nástroj Netsh pro nastavení proxy serveru.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Pomocník pro přihlášení není správně nakonfigurovaný.
Tato chyba se zobrazí, když se Pomocník pro přihlášení nemůže připojit k proxy serveru nebo proxy server nepovoluje požadavek.
![Snímek obrazovky s chybou: nelze ověřit přihlašovací údaje, ověřit připojení k síti a bránu firewall nebo nastavení proxy serveru.](./media/tshoot-connect-connectivity/nonetsh.png)

* Pokud se zobrazí tato chyba, podívejte se na konfiguraci proxy serveru v [netsh](how-to-connect-install-prerequisites.md#connectivity) a ověřte, jestli je správná.
  ![Snímek obrazovky s oknem příkazového řádku, na kterém je spuštěný Nástroj Netsh, který zobrazuje konfiguraci proxy serveru.](./media/tshoot-connect-connectivity/netshshow.png)
* Pokud to vypadá správně, postupujte podle kroků v tématu [ověření připojení proxy serveru](#verify-proxy-connectivity) a zjistěte, jestli se problém vyskytuje i mimo průvodce.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
