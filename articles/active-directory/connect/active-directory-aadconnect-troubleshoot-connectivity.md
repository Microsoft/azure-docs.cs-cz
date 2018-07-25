---
title: 'Azure AD Connect: Řešení problémů s připojením | Dokumentace Microsoftu'
description: Vysvětluje, jak řešit problémy s připojením u služby Azure AD Connect.
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
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d30006fae8a0d495909b9a53cf0bffb5cc824433
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295392"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Řešení potíží s připojením u služby Azure AD Connect
Tento článek vysvětluje, jak funguje připojení mezi Azure AD Connect a službou Azure AD a jak řešit problémy s připojením. Tyto problémy jsou pravděpodobně se zobrazí v prostředí s proxy serverem.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Řešení potíží s připojením v Průvodci instalací
Azure AD Connect používá moderní ověřování (používat knihovnu ADAL) pro ověřování. Průvodce instalací a správný synchronizační modul vyžaduje machine.config správně nakonfigurované, protože tyto dvě aplikace .NET.

V tomto článku vám ukážeme, jak společnost Fabrikam připojí k Azure AD prostřednictvím jeho proxy. Proxy server je s názvem fabrikamproxy a používá port 8080.

Nejprve je třeba Ujistěte se, že [ **machine.config** ](active-directory-aadconnect-prerequisites.md#connectivity) je správně nakonfigurovaný.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> V některých jiných společností než Microsoft blogy je uvedeno, že by měly být provedeny změny miiserver.exe.config místo toho. Tento soubor je však přepsány při každé upgradu, tak i že pokud funguje během počáteční instalace, systém přestane fungovat v prvním upgradu. Z tohoto důvodu doporučujeme místo toho aktualizovat soubor machine.config.
>
>

Proxy server musí mít také požadované adresy URL otevřít. Oficiálního seznamu jsou uvedené v [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Z těchto adres URL v následující tabulce je absolutní minimum, bude moct připojit ke službě Azure AD vůbec. Tento seznam neobsahuje žádné volitelné funkce, jako je například zpětný zápis hesla nebo Azure AD Connect Health. Je tady popsali, při řešení potíží pro počáteční konfiguraci.

| zprostředkovatele identity | Port | Popis |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Používá ke stahování seznamů CRL. |
| \*.verisign.com |HTTP/80 |Používá ke stahování seznamů CRL. |
| \*. entrust.com |HTTP/80 |Používá ke stahování seznamů CRL pro vícefaktorové ověřování. |
| \*.windows.net |HTTPS/443 |Používá k přihlášení do služby Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Používá se pro MFA. |
| \*.microsoftonline.com |HTTPS/443 |Slouží ke konfiguraci adresáře služby Azure AD a import/export dat. |

## <a name="errors-in-the-wizard"></a>Chyby v Průvodci
Průvodce instalací je pomocí dvou různých kontextech zabezpečení. Na stránce **připojit ke službě Azure AD**, používá aktuálně přihlášeného uživatele. Na stránce **konfigurovat**, se mění na [účet, který spouští službu pro synchronizační modul](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Pokud dochází k nějakému problému, zobrazí se pravděpodobně již **připojit ke službě Azure AD** stránku průvodce, protože je globální konfiguraci proxy serveru.

Následující problémy se nejběžnějších chyb, se kterými v Průvodci instalací.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Průvodce instalací nebyla nakonfigurována správně
Tato chyba se zobrazí, když Průvodce nemá přístup proxy serveru.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Pokud se zobrazí tato chyba, zkontrolujte, [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) není správně nakonfigurovaná.
* Pokud, která vypadá správně, postupujte podle kroků v [ověřte připojení k proxy serveru](#verify-proxy-connectivity) zobrazíte, pokud se problém nachází mimo Průvodce také.

### <a name="a-microsoft-account-is-used"></a>Použít účet Microsoft
Pokud používáte **účtu Microsoft** spíše než **školní nebo organizace** účtu, zobrazí obecná chyba.  
![Microsoft Account slouží](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Koncový bod MFA není dostupný.
Tato chyba se zobrazí, pokud koncový bod **https://secure.aadcdn.microsoftonline-p.com** není dostupný a globálního správce je povolená služba MFA.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Pokud se zobrazí tato chyba, ověřte, že koncový bod **secure.aadcdn.microsoftonline p.com** byla přidána k proxy serveru.

### <a name="the-password-cannot-be-verified"></a>Heslo nelze ověřit.
Pokud Průvodce instalací byl úspěšný při připojování ke službě Azure AD, ale heslo samotné nelze ověřit, že se zobrazí tato chyba:  
![BadPassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* Dočasné heslo je heslo a musí být změněno? Je ve skutečnosti správné heslo? Zkuste se přihlásit k https://login.microsoftonline.com (na jiném počítači než na serveru služby Azure AD Connect) a ověřte účet je použitelná.

### <a name="verify-proxy-connectivity"></a>Ověřte připojení k proxy serveru
Pokud chcete ověřit, zda má server Azure AD Connect skutečné připojení k proxy serveru a Internet, pomocí Powershellu zobrazíte, pokud proxy server povoluje webové požadavky nebo ne. V řádku prostředí PowerShell, spusťte `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Technicky první volání https://login.microsoftonline.com a tento identifikátor URI funguje stejně, ale jiný identifikátor URI je rychleji reagovat.)

Prostředí PowerShell používá ke kontaktování proxy konfiguraci v souboru machine.config. Nastavení služby winhttp/netsh by neměla mít vliv těchto rutin.

Pokud se server proxy je správně nakonfigurovaná, měli byste získat stav úspěšného dokončení: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Pokud se zobrazí **nelze se připojit ke vzdálenému serveru**, pak se pokouší PowerShell přímé volání bez použití proxy serveru nebo není správně nakonfigurovaný DNS. Ujistěte se, **machine.config** je správně nakonfigurován soubor.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Pokud proxy server není správně nakonfigurovaná, dojde k chybě: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Chyba | Text chyby | Poznámka |
| --- | --- | --- |
| 403 |Zakázáno |Proxy server nebyl otevřen pro požadovanou adresu URL. Návštěvě konfiguraci proxy serveru a ujistěte se, [adresy URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) nebyla otevřena. |
| 407 |Vyžadováno ověřování proxy serveru |U přihlášení potřeba proxy server, ale žádný není zadaný. Pokud váš proxy server vyžaduje ověření, ujistěte se, že nemá toto nastavení nakonfigurované v souboru machine.config. Ujistěte se také, že používáte doménové účty pro uživatele, který spustil průvodce a pro účet služby. |

### <a name="proxy-idle-timeout-setting"></a>Nastavení časového limitu nečinnosti proxy
Pokud Azure AD Connect odešle žádost o export do služby Azure AD, Azure AD může trvat až 5 minut, než na zpracování žádosti. před generováním odpověď. K tomu může dojít, zejména v případě, že existuje mnoho objektů skupiny s velké členství ve skupinách součástí stejného žádost o export. Ujistěte se, že je časový limit nečinnosti proxy server nakonfigurovaný tak, aby byl větší než 5 minut. V opačném případě může být dodržen přerušovaným připojením problém s Azure AD na serveru služby Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Vzor komunikace mezi službami Azure AD Connect a Azure AD
Pokud jste postupovali podle těchto předchozích kroků a pořád nemůžete připojit, v tomto okamžiku může zahájit, prohlížení síťové protokoly nástroje. Tato část je dokumentace vzoru normálního a úspěšné připojení. To je také výpis běžné herrings red, které můžete ignorovat při čtení síťové protokoly.

* Jsou volání https://dc.services.visualstudio.com. Není nutné mít tuto adresu URL, otevřete v proxy server pro instalace proběhla úspěšně a tato volání můžete ignorovat.
* Uvidíte, že překlad názvů dns obsahuje skutečné hostitele v nsatc.net místo názvu DNS a jiných oborech názvů není pod microsoftonline.com. Ale na server skutečné názvy nejsou žádné žádosti webové služby a není nutné přidat tyto adresy URL do proxy serveru.
* Adminwebservice koncových bodů a provisioningapi jsou koncové body pro zjišťování a použije k zjištění skutečný koncový bod, který chcete použít. Tyto koncové body se liší v závislosti na vaší oblasti.

### <a name="reference-proxy-logs"></a>Odkaz na protokoly proxy
Tady je výpis z skutečný proxy protokolu a stránce Průvodce instalací z kde byla získána (duplicitní položky na stejný koncový bod jsme odebrali). V této části slouží jako odkaz pro vlastní proxy server a síťových protokolů. Skutečné koncové body se může lišit ve vašem prostředí (zejména těchto adres URL v *Kurzíva*).

**Připojení k Azure AD**

| Čas | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |Připojte se: / /*bba800 ukotvení*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurace**

| Čas | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |Připojte se: / /*bba800 ukotvení*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |Připojte se: / /*bba800 ukotvení*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Počáteční synchronizace**

| Čas | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |Připojte se: / /*bba800 ukotvení*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Chyby ověřování
Tento oddíl řeší chyby, které mohou být vráceny z knihovny ADAL (knihovny pro ověřování použít Azure AD Connect) a prostředí PowerShell. Chyba je vysvětleno by mělo pomoci vám v dalších krocích.

### <a name="invalid-grant"></a>Neplatný udělení
Neplatné uživatelské jméno nebo heslo. Další informace najdete v tématu [nelze ověřit heslo](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Typ Neznámý uživatel
Adresáře služby Azure AD nemůže najít nebo vyřešení. Možná se pokusíte přihlásit s uživatelským jménem v neověřené domény?

### <a name="user-realm-discovery-failed"></a>Zjišťování sféry uživatele se nezdařilo
Problémy s konfigurací sítě nebo proxy serveru. V síti je nedostupné. Zobrazit [řešit problémy s připojením v Průvodci instalací](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Vypršela platnost hesla uživatele
Vypršela platnost vašich přihlašovacích údajů. Změňte si heslo.

### <a name="authorizationfailure"></a>AuthorizationFailure
Neznámý problém.

### <a name="authentication-cancelled"></a>Ověřování zrušeno
Výzva ověřování službou Multi-Factor Authentication (MFA) byla zrušena.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Ověření bylo úspěšné, ale má problém s ověřováním Azure AD PowerShell.

### <a name="azurerolemissing"></a>AzureRoleMissing
Ověření bylo úspěšné. Nejste globálním správcem.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Ověření bylo úspěšné. Privileged identity management. je povolen a momentálně nejste globální správce. Další informace najdete v tématu [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Ověření bylo úspěšné. Nelze načíst informace o společnosti ze služby Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Ověření bylo úspěšné. Nelze načíst informace o doméně z Azure AD.

### <a name="unexpected-exception"></a>Neočekávaná výjimka
Zobrazuje se jako Neočekávaná chyba v Průvodci instalací. Může dojít, pokud se pokusíte použít **Account Microsoft** spíše než **školní nebo organizaci účet**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Řešení potíží s kroky pro dřívější verze.
Spouští se s číslem sestavení 1.1.105.0 (všeobecně dostupné. února 2016), Pomocníka pro přihlášení skončil s verzemi. V této části a konfiguraci už nemá být povinné, ale se ukládají jako odkaz.

Jednotné přihlašování v Pomocníka s nastavením pro práci musí být nakonfigurována winhttp. Tato konfigurace se provádí pomocí [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Pomocník pro přihlášení není správně nakonfigurován.
Tato chyba se zobrazí, když Pomocníka pro přihlášení nemá přístup proxy serveru nebo proxy server nepovoluje žádná žádost.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Pokud se zobrazí tato chyba, podívejte se na konfiguraci proxy serveru v [netsh](active-directory-aadconnect-prerequisites.md#connectivity) a ověřte jeho správnost.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Pokud, která vypadá správně, postupujte podle kroků v [ověřte připojení k proxy serveru](#verify-proxy-connectivity) zobrazíte, pokud se problém nachází mimo Průvodce také.

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
