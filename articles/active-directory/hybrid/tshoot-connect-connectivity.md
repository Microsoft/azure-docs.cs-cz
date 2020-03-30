---
title: 'Azure AD Connect: Řešení potíží s připojením Azure AD | Dokumenty společnosti Microsoft'
description: Vysvětluje, jak řešit problémy s připojením s Azure AD Connect.
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
ms.topic: article
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72dbb404d1b4d3618909e0233f332d2f98b51516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049731"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Poradce při potížích s připojením Azure AD
Tento článek vysvětluje, jak funguje připojení mezi Azure AD Connect a Azure AD a jak řešit problémy s připojením. Tyto problémy jsou s největší pravděpodobností vidět v prostředí s proxy serverem.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Poradce při potížích s připojením v Průvodci instalací
Azure AD Connect používá pro ověřování moderní ověřování (pomocí knihovny ADAL). Průvodce instalací a vlastní synchronizační modul vyžadují správnou konfiguraci souboru machine.config, protože tyto dvě aplikace jsou .NET.

V tomto článku ukážeme, jak se společnost Fabrikam připojuje k Azure AD prostřednictvím jeho proxy serveru. Proxy server se jmenuje fabrikamproxy a používá port 8080.

Nejprve se musíme [**ujistit, že machine.config**](how-to-connect-install-prerequisites.md#connectivity) je správně nakonfigurován.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> V některých blozích jiných společností než Microsoft je zdokumentováno, že by měly být provedeny změny na miiserver.exe.config. Tento soubor je však přepsán při každém upgradu, takže i když funguje během počáteční instalace, systém přestane pracovat při prvním upgradu. Z tohoto důvodu je doporučení aktualizovat machine.config místo.
>
>

Na proxy serveru musí být také otevřeny požadované adresy URL. Oficiální seznam je zdokumentován v [adresách URL Office 365 a v rozsahu IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Z těchto adres URL následující tabulka je absolutní minimum, aby bylo možné se připojit k Azure AD vůbec. Tento seznam neobsahuje žádné volitelné funkce, jako je například zpětný zápis hesla nebo Azure AD Connect Health. Je zde dokumentován, aby vám pomohl při řešení potíží s počáteční konfigurací.

| zprostředkovatele identity | Port | Popis |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Slouží ke stažení seznamů seznamu ODVol. |
| \*.verisign.com |HTTP/80 |Slouží ke stažení seznamů seznamu ODVol. |
| \*.entrust.net |HTTP/80 |Slouží ke stažení seznamů seznamu CRL pro vícefaktorové finanční seznamy. |
| \*.windows.net |HTTPS/443 |Slouží k přihlášení ke službě Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Používá se pro vícefaktorové finanční hod. |
| \*.microsoftonline.com |HTTPS/443 |Slouží ke konfiguraci adresáře Azure AD a importu a exportu dat. |

## <a name="errors-in-the-wizard"></a>Chyby v průvodci
Průvodce instalací používá dva různé kontexty zabezpečení. Na stránce **Připojit k Azure AD**, používá aktuálně přihlášeného uživatele. Na stránce **Konfigurovat**se změní na [účet, ve který je spuštěna služba synchronizačního modulu](reference-connect-accounts-permissions.md#adsync-service-account). Pokud se jedná o problém, zobrazí se s největší pravděpodobností již na stránce **Připojit k Azure AD** v průvodci, protože konfigurace proxy je globální.

Následující problémy jsou nejčastější chyby, se kterými se setkáte v průvodci instalací.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Průvodce instalací nebyl správně nakonfigurován.
Tato chyba se zobrazí, pokud samotný průvodce nemůže dosáhnout proxy serveru.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Pokud se zobrazí tato chyba, ověřte, zda byl [soubor machine.config](how-to-connect-install-prerequisites.md#connectivity) správně nakonfigurován.
* Pokud to vypadá správně, postupujte podle kroků v [ověření připojení proxy](#verify-proxy-connectivity) a zjistěte, zda je problém přítomen i mimo průvodce.

### <a name="a-microsoft-account-is-used"></a>Používá se účet Microsoft
Pokud používáte **účet Microsoft,** nikoli **účet školy nebo organizace,** zobrazí se obecná chyba.  
![Používá se účet Microsoft](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Koncový bod vícefaktorové dohody nelze dosáhnout.
Tato chyba se zobrazí, pokud koncový bod **https://secure.aadcdn.microsoftonline-p.com** nelze dosáhnout a váš globální správce má povolena vícefaktorové povolení.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Pokud se zobrazí tato chyba, ověřte, zda byl koncový bod **secure.aadcdn.microsoftonline-p.com** byl přidán do proxy serveru.

### <a name="the-password-cannot-be-verified"></a>Heslo nelze ověřit.
Pokud průvodce instalací je úspěšné připojení k Azure AD, ale samotné heslo nelze ověřit, zobrazí se tato chyba:  
![Špatné heslo.](./media/tshoot-connect-connectivity/badpassword.png)

* Je heslo dočasné heslo a musí být změněno? Je to vlastně správné heslo? Pokuste se `https://login.microsoftonline.com` přihlásit k (na jiném počítači než server Azure AD Connect) a ověřte, že účet je použitelný.

### <a name="verify-proxy-connectivity"></a>Ověření připojení k serveru proxy
Chcete-li ověřit, zda server Azure AD Connect má skutečné připojení k proxy a internetu, použijte některé PowerShell zjistit, jestli proxy umožňuje webové požadavky, nebo ne. V příkazovém řádku `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`prostředí PowerShell spusťte . (Technicky první volání `https://login.microsoftonline.com` je a tento identifikátor URI funguje také, ale druhý identifikátor URI je rychlejší reagovat.)

Prostředí PowerShell používá konfiguraci v souboru machine.config ke kontaktování proxy serveru. Nastavení ve winhttp/netsh by neměla mít vliv na tyto rutiny.

Pokud je proxy server správně nakonfigurován, ![měli byste získat stav úspěchu: proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Pokud se nedaří **připojit ke vzdálenému serveru**, pokusí se prostředí PowerShell provést přímé volání bez použití proxy serveru nebo není správně nakonfigurován server DNS. Zkontrolujte, zda je soubor **machine.config** správně nakonfigurován.
![nelze připojit](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Pokud proxy není správně nakonfigurován, zobrazí ![se chyba: proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Chyba | Text chyby | Poznámka |
| --- | --- | --- |
| 403 |Forbidden |Proxy server nebyl otevřen pro požadovanou adresu URL. Znovu zkontrolujte konfiguraci proxy serveru a ujistěte se, že adresy URL byly [otevřeny.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) |
| 407 |Je vyžadováno ověření serveru proxy. |Proxy server vyžadoval přihlášení a žádný nebyl k dispozici. Pokud proxy server vyžaduje ověření, ujistěte se, že toto nastavení je nakonfigurováno v souboru machine.config. Také se ujistěte, že používáte účty domény pro uživatele, který spouštěl průvodce, a pro účet služby. |

### <a name="proxy-idle-timeout-setting"></a>Nastavení časového limitu nečinnosti proxy serveru
Když Azure AD Connect odešle požadavek na export do Azure AD, Azure AD může trvat až 5 minut ke zpracování požadavku před generováním odpovědi. K tomu může dojít zejména v případě, že existuje několik objektů skupiny s velkým členstvím ve skupinách zahrnuty do stejné žádosti o export. Ujistěte se, že časový limit nečinnosti proxy serveru je nakonfigurován tak, aby byl větší než 5 minut. V opačném případě může být na serveru Azure AD Connect pozorován občasný problém s připojením k Azure AD.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Vzor komunikace mezi Azure AD Connect a Azure AD
Pokud jste postupovali podle všech předchozích kroků a stále se nemůžete připojit, můžete se v tomto okamžiku začít dívat na síťové protokoly. Tato část dokumentuje normální a úspěšný vzor připojení. Je také seznam běžných červených sledě, které mohou být ignorovány při čtení síťových protokolů.

* Existují volání `https://dc.services.visualstudio.com`do . Není nutné mít tuto adresu URL otevřenou v proxy serveru, aby instalace proběhla úspěšně, a tato volání mohou být ignorována.
* Uvidíte, že překlad DNS uvádí skutečné hostitele, které mají být v oboru názvů DNS nsatc.net a dalších oborech názvů, které nejsou pod microsoftonline.com. Neexistují však žádné požadavky webové služby na skutečné názvy serverů a není třeba přidat tyto adresy URL na proxy server.
* Koncové body adminwebservice a provisioningapi jsou zjišťování koncových bodů a slouží k nalezení skutečného koncového bodu, který chcete použít. Tyto koncové body se liší v závislosti na oblasti.

### <a name="reference-proxy-logs"></a>Referenční protokoly proxy
Zde je výpis ze skutečného protokolu proxy a stránka průvodce instalací, ze které byla odebrána (duplicitní položky do stejného koncového bodu byly odebrány). Tuto část lze použít jako referenci pro vlastní proxy server y a síťové protokoly. Skutečné koncové body se mohou lišit ve vašem prostředí (zejména adresy URL *kurzívou).*

**Připojení k Azure AD**

| Time | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-kotva*(microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relé*.microsoftonline.com:443 |

**Konfigurace**

| Time | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-kotva*(microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-kotva*(microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-kotva*(microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relé*.microsoftonline.com:443 |

**Počáteční synchronizace**

| Time | zprostředkovatele identity |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-kotva*(microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-kotva*(microsoftonline.com:443 |

## <a name="authentication-errors"></a>Chyby ověřování
Tato část popisuje chyby, které lze vrátit z ADAL (ověřovací knihovna používaná službou Azure AD Connect) a PowerShellu. Vysvětlená chyba by vám měla pomoci pochopit další kroky.

### <a name="invalid-grant"></a>Neplatný grant
Neplatné uživatelské jméno nebo heslo. Další informace naleznete [v tématu Heslo nelze ověřit](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Neznámý typ uživatele
Váš adresář Azure AD nelze najít nebo vyřešit. Možná se pokusíte přihlásit s uživatelským jménem v neověřené doméně?

### <a name="user-realm-discovery-failed"></a>Zjišťování sféry uživatele se nezdařilo.
Problémy s konfigurací sítě nebo serveru proxy. Síť není k zastižení. Viz [Poradce při potížích s připojením v Průvodci instalací](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Platnost uživatelského hesla vypršela
Platnost vašich přihlašovacích údajů vypršela. Změňte heslo.

### <a name="authorization-failure"></a>Selhání autorizace
Nepodařilo se autorizovat uživatele k provádění akcí ve službě Azure AD.

### <a name="authentication-canceled"></a>Ověřování bylo zrušeno.
Výzva vícefaktorového ověřování (MFA) byla zrušena.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Připojení k ms online se nezdařilo
Ověřování bylo úspěšné, ale Azure AD PowerShell má problém s ověřováním.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Potřeba role globálního správce Azure AD
Uživatel byl úspěšně ověřen. Uživateli však není přiřazena role globálního správce. Tímto způsobem můžete uživateli [přiřadit roli globálního správce.](../users-groups-roles/directory-assign-admin-roles.md) 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Povolena správa privilegovaných identit
Ověření bylo úspěšné. Správa privilegovaných identit byla povolena a vy v současné době nejste globálním správcem. Další informace naleznete [v tématu Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informace o společnosti nejsou k dispozici
Ověření bylo úspěšné. Nelze načíst informace o společnosti z Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informace o doméně nejsou k dispozici
Ověření bylo úspěšné. Nelze načíst informace o doméně z Azure AD.

### <a name="unspecified-authentication-failure"></a>Nespecifikované ověření se nepodařilo
Zobrazena jako neočekávaná chyba v průvodci instalací. Může k tomu dojít, pokud se pokusíte použít **účet Microsoft,** nikoli **účet školy nebo organizace**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Postup řešení potíží s předchozími verzemi.
S verzemi začínajícími číslem sestavení 1.1.105.0 (vydáno v únoru 2016) byl asistent přihlášení vyřazen. Tato část a konfigurace by již neměly být vyžadovány, ale jsou uchovávány jako reference.

Aby pomocník pro jednotné přihlášení fungoval, musí být nakonfigurováno winhttp. Tuto konfiguraci lze provést pomocí [**netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![Netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Pomocník pro přihlášení nebyl správně nakonfigurován.
Tato chyba se zobrazí, když pomocník pro přihlášení nemůže dosáhnout proxy serveru nebo proxy server neumožňuje požadavek.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Pokud se zobrazí tato chyba, podívejte se na konfiguraci proxy v [netsh](how-to-connect-install-prerequisites.md#connectivity) a ověřte, že je správná.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Pokud to vypadá správně, postupujte podle kroků v [ověření připojení proxy](#verify-proxy-connectivity) a zjistěte, zda je problém přítomen i mimo průvodce.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
