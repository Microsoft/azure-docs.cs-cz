---
title: 'Azure AD Connect: požadavky na hybridní identitu pro Azure Government'
description: Zvláštní požadavky na nasazení Azure AD Connect s využitím cloudu pro státní správu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378922"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Aspekty hybridní identity pro Azure Government 
Následující dokument popisuje požadavky pro implementaci hybridního prostředí s Azure Governmentm cloudem.  Tyto informace jsou k dispozici jako reference pro správce a architekty, kteří pracují s Azure Governmentm cloudem.  
> [!NOTE] 
> Aby bylo možné integrovat místní prostředí služby AD s cloudem Azure Governemnt, musíte upgradovat na nejnovější verzi [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Úplný seznam koncových bodů DoD pro státní správu USA najdete v [dokumentaci](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) . 

## <a name="pass-through-authentication"></a>Předávací ověřování 
Následující informace jsou k dispozici pro implementaci předávacího ověřování (PTA) a cloudu Azure Government.

### <a name="allow-access-to-urls"></a>Povolení přístupu k adresám URL  
Před nasazením předávacího agenta pro ověřování ověřte, jestli mezi vašimi servery a službou Azure AD existuje brána firewall. Pokud vaše brána firewall nebo proxy server povoluje přidávání do seznamu povolených serverů DNS, přidejte následující připojení: 
> [!NOTE]
> Následující pokyny platí i pro instalaci [konektoru proxy aplikací](https://aka.ms/whyappproxy) pro Azure Government prostředí.

|zprostředkovatele identity |Jak se používá|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Komunikace mezi agentem a cloudovou službou Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Agent používá tyto adresy URL k ověření certifikátů.| 
|Login.Windows.us Secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.NET </br> *. msauthimages.NET </br>*. msecnd.NET</br>*. msftauth.NET </br>*. msftauthimages.NET</br>*. PhoneFactor.NET </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Agent tyto adresy URL používá během procesu registrace.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalace agenta pro Azure Government Cloud 
Chcete-li nainstalovat agenta pro Azure Government Cloud, je nutné postupovat podle následujících kroků: v terminálu příkazového řádku přejděte do složky, kde je umístěn spustitelný soubor pro instalaci agenta. Spusťte následující příkaz, který určuje instalaci Azure Government. 

Pro průchozí ověřování: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Pro proxy aplikace:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Jednotné přihlašování 
Nastavení serveru Azure AD Connect: Pokud jako metodu přihlašování použijete předávací ověřování, nevyžaduje se žádná další Kontrola požadavků. Pokud jako metodu přihlašování použijete synchronizaci hodnot hash hesel, a pokud je mezi Azure AD Connect a službou Azure AD brána firewall, zajistěte, aby:
- Použijete 1.1.644.0 nebo novější verzi Azure AD Connect. 
- Pokud brána firewall nebo proxy umožňuje přidávání do seznamu povolených serverů DNS, přidejte připojení k adresám URL *. msapproxy.us přes port 443. V takovém případě povolte přístup k rozsahům IP adres datacentra Azure, které se aktualizují týdně. Tato požadovaná součást platí pouze v případě, že funkci povolíte. Nepožaduje se pro vlastní přihlášení uživatelů. 

### <a name="rolling-out-seamless-sso"></a>Zajištění bezproblémového jednotného přihlašování 
Bezproblémovou přihlašování pro uživatele můžete postupně zavádět pomocí níže uvedených pokynů. Začnete přidáním následující adresy URL služby Azure AD do nastavení zóny intranetu pro všechny nebo vybrané uživatele pomocí Zásady skupiny ve službě Active Directory:https://autologon.microsoft.us 

Kromě toho musíte povolit nastavení zásad zóny intranetu s názvem povolit aktualizace stavového řádku prostřednictvím skriptu prostřednictvím Zásady skupiny. Pokyny pro prohlížeč Mozilla Firefox (všechny platformy) Mozilla Firefox nepoužívá automatické ověřování pomocí protokolu Kerberos. Každý uživatel musí do svého nastavení Firefox ručně přidat adresu URL služby Azure AD pomocí následujících kroků: 
1. Spusťte Firefox a do adresního řádku zadejte about: config. Zavřete všechna oznámení, která vidíte. 
2. Vyhledejte síť. vyjednat-auth. Trusted-URI preference. Tato předvolby obsahuje seznam důvěryhodných lokalit prohlížeče Firefox pro ověřování protokolem Kerberos. 
3. Klikněte pravým tlačítkem a vyberte Upravit. 
4. Do https://autologon.microsoft.us pole zadejte.
5. Vyberte OK a pak znovu otevřete prohlížeč. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge na základě chromu (všechny platformy) 
Pokud jste přepsali `AuthNegotiateDelegateAllowlist` nastavení nebo `AuthServerAllowlist` zásadu ve vašem prostředí, ujistěte se, že jste přidali adresu URL služby Azurehttps://autologon.microsoft.us) AD (taky do nich). 

### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy) 
Pokud jste přepsali `AuthNegotiateDelegateWhitelist` nastavení nebo `AuthServerWhitelist` zásadu ve vašem prostředí, ujistěte se, že jste přidali adresu URL služby Azurehttps://autologon.microsoft.us) AD (taky do nich). 

## <a name="next-steps"></a>Další kroky
[Předávací ověřování](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
–[jednotné přihlašování](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
