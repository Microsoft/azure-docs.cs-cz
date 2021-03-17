---
title: 'Azure AD Connect: požadavky na hybridní identitu pro cloud Azure Government'
description: Zvláštní požadavky na nasazení Azure AD Connect v cloudu Azure Government.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e163ea34948906060996ee952f45ec0cdb467557
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504351"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Požadavky na hybridní identitu pro cloud Azure Government

Tento článek popisuje požadavky na integraci hybridního prostředí s Microsoft Azure Governmentm cloudem. Tyto informace jsou k dispozici jako reference pro správce a architekty, kteří pracují s Azure Governmentm cloudem.

> [!NOTE]
> Pokud chcete integrovat prostředí služby Microsoft Active Directory (místně nebo hostované v IaaS, které je součástí stejné instance cloudu) s Azure Governmentm cloudem, musíte upgradovat na nejnovější verzi [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

> [!NOTE]
> Tento článek obsahuje odkazy na seznam *povolených* termínů, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

Úplný seznam USAch koncových bodů obrany najdete v [dokumentaci](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## <a name="azure-ad-pass-through-authentication"></a>Předávací ověřování Azure AD

Následující informace popisují implementaci předávacího ověřování a cloudu Azure Government.

### <a name="allow-access-to-urls"></a>Povolení přístupu k adresám URL

Před nasazením předávacího agenta pro ověřování ověřte, zda existuje brána firewall mezi servery a službou Azure AD. Pokud brána firewall nebo proxy povolí blokované nebo bezpečné programy DNS (Domain Name System), přidejte následující připojení.

> [!NOTE]
> Následující pokyny platí také pro instalaci [konektoru služby Azure proxy aplikací služby AD](../manage-apps/what-is-application-proxy.md) pro Azure Government prostředí.

|URL |Jak se používá|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Agent používá tyto adresy URL ke komunikaci s cloudovou službou Azure AD. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| Agent používá tyto adresy URL k ověření certifikátů.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| Agent tyto adresy URL používá během procesu registrace.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalace agenta pro Azure Government Cloud

Pomocí těchto kroků nainstalujte agenta pro cloud Azure Government:

1. V terminálu příkazového řádku přejděte do složky, která obsahuje spustitelný soubor, který nainstaluje agenta.
1. Spusťte následující příkazy, které určují, že instalace Azure Government.

   Pro předávací ověřování:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Pro proxy aplikace:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Jednotné přihlašování

### <a name="set-up-your-azure-ad-connect-server"></a>Nastavení serveru Azure AD Connect

Pokud jako metodu přihlašování použijete předávací ověřování, nevyžaduje se žádná další Kontrola požadovaných součástí. Pokud jako metodu přihlašování použijete synchronizaci hodnot hash hesel a mezi Azure AD Connect a Azure AD je brána firewall, zajistěte, aby:

- Použijete Azure AD Connect verze 1.1.644.0 nebo novější.
- Pokud brána firewall nebo proxy umožňuje blokované nebo bezpečné programy DNS, přidejte připojení k &#42;. adresy URL msappproxy.us přes port 443.

  V takovém případě povolte přístup k rozsahům IP adres datacentra Azure, které se aktualizují týdně. Tato podmínka platí pouze v případě, že funkci povolíte. Nevyžaduje se pro vlastní přihlášení uživatelů.

### <a name="roll-out-seamless-single-sign-on"></a>Zavedení bezproblémového jednoduchého Sign-On

K Sign-On bezproblémovému nasazení Azure AD pro uživatele můžete použít následující pokyny. Začnete přidáním adresy URL Azure AD `https://autologon.microsoft.us` do nastavení zóny intranetu pro všechny nebo vybrané uživatele pomocí Zásady skupiny ve službě Active Directory.

Musíte taky povolit nastavení zásad zóny intranetu **Povolit aktualizace stavového řádku prostřednictvím skriptu prostřednictvím zásady skupiny**.

## <a name="browser-considerations"></a>Prohlížeč – požadavky

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (všechny platformy)

Mozilla Firefox nepoužívá automatické ověřování pomocí protokolu Kerberos. Každý uživatel musí do svého nastavení Firefox ručně přidat adresu URL služby Azure AD pomocí následujících kroků:

1. Spusťte Firefox a do adresního řádku zadejte **About: config**   . Zavřete všechna oznámení, která se mohou zobrazit.
1. Vyhledejte **síť. vyjednat-auth. Trusted-URI**   preference. Tato předvolba obsahuje seznam webů důvěryhodných aplikací Firefox pro ověřování protokolem Kerberos.
1. Klikněte pravým tlačítkem na název předvolby a pak vyberte **Upravit**.
1. `https://autologon.microsoft.us`Do pole zadejte.
1. Vyberte **OK**   a pak znovu otevřete prohlížeč.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge na základě chromu (všechny platformy)

Pokud jste v prostředí přepsali  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   nastavení nebo, ujistěte se, že jste do nich přidali adresu URL služby Azure AD `https://autologon.microsoft.us` .

### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy)

Pokud jste v prostředí přepsali  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   nastavení nebo, ujistěte se, že jste do nich přidali adresu URL služby Azure AD `https://autologon.microsoft.us` .

## <a name="next-steps"></a>Další kroky

- [Předávací ověřování](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Jednotné přihlašování](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
