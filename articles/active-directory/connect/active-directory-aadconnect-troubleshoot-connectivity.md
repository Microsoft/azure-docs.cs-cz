---
title: 'Azure AD Connect: Problémů s připojením | Microsoft Docs'
description: Vysvětluje, jak chcete-li vyřešit potíže se službou Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 1c8bbbde653ed8e927ab1550c32ae86a4dc2ffac
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Řešení potíží s připojením službou Azure AD Connect
Tento článek vysvětluje, jak funguje připojení mezi Azure AD Connect a službou Azure AD a jak vyřešit problémy s připojením. Tyto problémy budou pravděpodobně se zobrazí v prostředí s proxy serverem.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Řešení potíží s připojením v Průvodci instalací
Azure AD Connect používá moderní ověřování (pomocí knihovny ADAL) pro ověřování. Průvodce instalací a správný synchronizační modul vyžadují machine.config správně nakonfigurovat vzhledem k tomu, že jsou tyto dva aplikací .NET.

V tomto článku jsme ukazují, jak společnost Fabrikam připojuje ke službě Azure AD pomocí jeho proxy. Proxy server je s názvem fabrikamproxy a používá port 8080.

Je potřeba nejdřív zkontrolujte [ **machine.config** ](active-directory-aadconnect-prerequisites.md#connectivity) je správně nakonfigurován.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> V některých jiných společností než Microsoft blogy je popsané, že by měl být provedeny změny miiserver.exe.config místo. Tento soubor je však na každém upgradu, tak i že pokud funguje během počáteční instalace, systém přestane fungovat v prvním upgradu přepsána. Z tohoto důvodu doporučuje se místo toho aktualizace souboru machine.config.
>
>

Proxy server musí mít také otevřít požadované adresy URL. Oficiální seznamu jsou uvedené v [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Tyto adresy URL v následující tabulce je absolutní minimum, abyste mohli připojit ke službě Azure AD ve všech. Tento seznam neobsahuje žádné volitelné funkce, jako je například zpětný zápis hesla nebo Azure AD Connect Health. Je popsané v tomto poli při řešení potíží pro počáteční konfiguraci.

| zprostředkovatele identity | Port | Popis |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Používá ke stahování seznamů CRL. |
| \*.verisign.com |HTTP/80 |Používá ke stahování seznamů CRL. |
| \*.entrust.com |HTTP/80 |Používá ke stahování seznamů CRL pro MFA. |
| \*.windows.net |HTTPS/443 |Použít pro přihlášení k Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Použít pro MFA. |
| \*.microsoftonline.com |HTTPS/443 |Použít ke konfiguraci adresáře služby Azure AD a importu a exportu dat. |

## <a name="errors-in-the-wizard"></a>Chyby v Průvodci
Průvodce instalací je pomocí dvou různých kontextech zabezpečení. Na stránce **připojit k Azure AD**, používá aktuálně přihlášeného uživatele. Na stránce **konfigurace**, je změna na [účtu využívajícího službu pro synchronizační modul](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Pokud nastane problém, zobrazí se pravděpodobně už na **připojit k Azure AD** stránku průvodce, protože je globální konfiguraci proxy serveru.

Následující problémy jsou nejběžnějších chyb, které zaznamenáte v Průvodci instalací.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Průvodce instalací nebyla nakonfigurována správně
Tato chyba se zobrazí, pokud Průvodce nemůže připojit k proxy serveru.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Pokud se zobrazí tato chyba, zkontrolujte [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) byl správně nakonfigurován.
* Pokud toto vypadá správné, postupujte podle kroků v [ověření proxy serveru konektivity](#verify-proxy-connectivity) zobrazíte, když problém se nachází mimo Průvodce také.

### <a name="a-microsoft-account-is-used"></a>Účet Microsoft se používá.
Pokud používáte **účtu Microsoft** ne **školní nebo organizace** účet, najdete v části obecné chybě.  
![Account Microsoft se používá.](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Není dostupný koncový bod vícefaktorového ověřování
Tato chyba se zobrazí, pokud koncový bod **https://secure.aadcdn.microsoftonline-p.com** je nedostupné a globálního správce povolené ověřování MFA.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Pokud se zobrazí tato chyba, ověřte, že koncový bod **secure.aadcdn.microsoftonline p.com** byla přidána k proxy serveru.

### <a name="the-password-cannot-be-verified"></a>Heslo nelze ověřit.
Pokud Průvodce instalací byl úspěšný v připojení k Azure AD, ale heslo samotné nelze ověřit, že se zobrazí tato chyba:  
![BadPassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* Heslo je dočasné heslo a je třeba změnit? Je ve skutečnosti správné heslo? Zkuste se přihlásit k https://login.microsoftonline.com (na jiném počítači než server Azure AD Connect) a ověřte účet je možné použít.

### <a name="verify-proxy-connectivity"></a>Ověřte připojení k proxy serveru
Pokud chcete ověřit, zda má server Azure AD Connect skutečné připojení k proxy serveru a Internet, použijte některé PowerShell zobrazíte, pokud proxy server umožňuje webových požadavků nebo ne. V příkazovém řádku prostředí PowerShell, spusťte `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Technicky prvním volání je https://login.microsoftonline.com a tento identifikátor URI funguje stejně, ale jiné URI se rychleji reagovat.)

Prostředí PowerShell používá ke kontaktování proxy konfiguraci v souboru machine.config. Nastavení v winhttp nebo netsh by neměla mít vliv na tyto rutiny.

Pokud proxy server správně nakonfigurovaný, měli byste obdržet stav úspěšného dokončení: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Pokud se zobrazí **nebylo možné se připojit ke vzdálenému serveru**, pak se pokouší prostředí PowerShell přímé volání bez použití proxy serveru nebo DNS není správně nakonfigurována. Zajistěte, aby **machine.config** souboru je správně nakonfigurován.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Pokud proxy server není nakonfigurovaná správně, dojde k chybě: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Chyba | Text chyby | Poznámka |
| --- | --- | --- |
| 403 |Zakázáno |Proxy server nebyl otevřen pro požadovanou adresu URL. Pokroku konfiguraci proxy serveru a zajistěte, aby [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) jsou otevřené. |
| 407 |Vyžadováno ověření proxy serveru |Proxy server vyžaduje přihlášení a nebyl poskytnut žádný. Pokud proxy server vyžaduje ověřování, ujistěte se, že jste nemá toto nastavení nakonfigurované v souboru machine.config. Také zkontrolujte, zda že používáte doménové účty pro uživatele spuštěním průvodce a pro účet služby. |

### <a name="proxy-idle-timeout-setting"></a>Časový limit nečinnosti nastavení proxy serveru
Pokud Azure AD Connect odešle žádost o export do služby Azure AD, Azure AD může trvat až 5 minut pro zpracování požadavku před vygenerováním odpověď. To může dojít, zejména v případě, že existuje několik objektů skupiny s velké členství ve skupinách zahrnuté ve stejném požadavku export. Zajistěte, aby že časový limit nečinnosti proxy server nakonfigurovaný tak, aby byl větší než 5 minut. Problém s občasným připojením s Azure AD, jinak hodnota může být dodržen na server Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Vzor komunikace mezi Azure AD Connect a službou Azure AD
Pokud jste provedli všechny předchozí kroky a pořád nemůžete připojit, může v tuto chvíli spustit vyhledávání v síťových protokolech. Tato část je dokumentace se vzorem normálního a úspěšné připojení. Je také je výpis běžné red herrings, které můžete ignorovat při čtení protokoly sítě.

* Existují volání https://dc.services.visualstudio.com. Není nutné mít tuto adresu URL, otevřete v proxy server pro instalace proběhla úspěšně a těchto volání můžete ignorovat.
* Uvidíte, že překlad názvů dns uvádí skutečné hostitelů v nsatc.net místo názvu DNS a jiných oborech názvů není v části microsoftonline.com. Ale na serveru skutečné názvy nejsou k dispozici žádné žádosti webové služby a není třeba přidávat tyto adresy URL k proxy serveru.
* Koncové body adminwebservice a provisioningapi jsou koncové body pro zjišťování a použít k vyhledání skutečný koncový bod používat. Tyto koncové body se liší v závislosti na vaší oblasti.

### <a name="reference-proxy-logs"></a>Referenční dokumentace proxy protokoly
Tady je výpis z protokolu skutečné proxy serveru a na stránku průvodce instalace z kde nebyla provedena (duplicitní položky na stejné koncový bod jsme odebrali). V této části můžete použít jako odkaz pro svoje vlastní protokoly proxy serveru a sítě. Skutečné koncové body se mohou lišit ve vašem prostředí (zejména tyto adresy URL v *Kurzíva*).

**Připojení k Azure AD**

| Čas | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurace**

| Čas | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Počáteční synchronizace**

| Čas | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Chybám při ověřování
Tato část obsahuje chyby, které mohou být vráceny z ADAL (knihovnu ověřování používá Azure AD Connect) a prostředí PowerShell. Chyba vysvětlené by vám pomůže v pochopit další kroky.

### <a name="invalid-grant"></a>Neplatný Grant
Neplatné uživatelské jméno nebo heslo. Další informace najdete v tématu [nelze ověřit heslo](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Typ Neznámý uživatele
Adresář Azure AD nelze nalézt nebo přeložit. Možná zkuste se přihlásit s uživatelským jménem v neověřené domény?

### <a name="user-realm-discovery-failed"></a>Zjišťování sféry uživatele se nezdařilo
Problémy s konfigurací sítě nebo proxy server. Není dostupný v síti. V tématu [problémů s připojením v Průvodci instalací](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Platnost hesla uživatele
Vypršela platnost přihlašovacích údajů. Změňte heslo.

### <a name="authorizationfailure"></a>AuthorizationFailure
Neznámý problém.

### <a name="authentication-cancelled"></a>Ověřování zrušena
Výzva služby Multi-Factor authentication (MFA) byla zrušena.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Ověření bylo úspěšné, ale Azure AD PowerShell má problém s ověřování.

### <a name="azurerolemissing"></a>AzureRoleMissing
Ověření bylo úspěšné. Nejste globálním správcem.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Ověření bylo úspěšné. Správa privilegovaných identit byl povolen a aktuálně nejste globálním správcem. Další informace najdete v tématu [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Ověření bylo úspěšné. Nelze načíst informace o společnosti z Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Ověření bylo úspěšné. Nelze načíst informace o doméně z Azure AD.

### <a name="unexpected-exception"></a>Neočekávaná výjimka
Zobrazit jako Neočekávaná chyba v Průvodci instalací. Může dojít, pokud se pokusíte použít **Account Microsoft** ne **školní nebo organizace účet**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Řešení potíží pro předchozí verze.
S verzemi bylo vyřazeno počínaje číslo sestavení 1.1.105.0 (vydané. února 2016), Pomocníka pro přihlášení. V této části a konfiguraci už třeba vyžadovat, ale se ukládají jako odkaz.

Single – přihlašovací Pomocníka pro práci musí být nakonfigurované winhttp. Tato konfigurace se provádí pomocí [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Pomocník pro přihlášení není správně nakonfigurován.
Tato chyba se zobrazí, když se Pomocník pro přihlášení nemůže připojit k proxy serveru nebo proxy serveru není povolením požadavku.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Pokud se zobrazí tato chyba, podívejte se na konfiguraci proxy serveru v [netsh](active-directory-aadconnect-prerequisites.md#connectivity) a ověřte je správná.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Pokud toto vypadá správné, postupujte podle kroků v [ověření proxy serveru konektivity](#verify-proxy-connectivity) zobrazíte, když problém se nachází mimo Průvodce také.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
