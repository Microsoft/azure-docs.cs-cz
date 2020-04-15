---
title: 'Azure AD Connect: Aspekty hybridní identity pro Azure Government'
description: Zvláštní důležité informace o nasazení Azure AD Connect s vládním cloudem.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378922"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Důležité informace o hybridní identitě pro Azure Government 
Následující dokument popisuje důležité informace o implementaci hybridního prostředí s cloudem Azure Government.  Tyto informace se poskytují jako odkaz pro správce a architekty, kteří pracují s cloudem Azure Government.  
> [!NOTE] 
> Chcete-li integrovat místní prostředí služby AD s cloudem Azure Governemnt, je třeba upgradovat na nejnovější verzi [služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594). 

> [!NOTE] 
> Úplný seznam koncových bodů ministerstva dod vlády USA naleznete v [dokumentaci](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Předávací ověřování 
Následující informace jsou k dispozici pro implementaci předávacího ověřování (PTA) a cloudazure governmentu.

### <a name="allow-access-to-urls"></a>Povolit přístup k adresám URL  
Před nasazením předávacího agenta ověřování ověřte, jestli mezi vašimi servery a službou Azure AD existuje brána firewall. Pokud brána firewall nebo proxy server umožňuje seznam povolených dnsů, přidejte následující připojení: 
> [!NOTE]
> Následující pokyny platí také pro instalaci [konektoru proxy aplikací](https://aka.ms/whyappproxy) pro prostředí Azure Government.

|zprostředkovatele identity |Jak se používá|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Komunikace mezi agentem a cloudovou službou Azure AD |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Agent používá tyto adresy URL k ověření certifikátů.| 
|login.windows.us secure.aadcdn.microsoftonline-p.com microsoftonline.us * </br>.microsoftonline.us .microsoftonline-p.us *microsoftonline.us </br>*.msauth.net </br> *.msauthimages.net </br>*Msftauth.net</br>.msecnd.net*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Agent používá tyto adresy URL během procesu registrace.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Instalace agenta pro cloud Azure Government 
Chcete-li nainstalovat agenta pro cloud Azure Government, musíte postupovat podle následujících konkrétních kroků: V terminálu příkazového řádku přejděte do složky, kde se nachází spustitelný soubor pro instalaci agenta. Spusťte následující příkaz, který určuje, že instalace je pro Azure Government. 

Pro předávací ověřování: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Pro proxy aplikace:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Jednotné přihlašování 
Nastavení serveru Azure AD Connect: Pokud jako metodu přihlášení použijete předávací ověřování, není vyžadována žádná další kontrola předpokladů. Pokud jako metodu přihlášení používáte synchronizaci hodnot hash hesla a mezi Službou Azure AD Connect a Službou Azure AD existuje brána firewall, ujistěte se, že:
- Používáte verzi 1.1.644.0 nebo novější služby Azure AD Connect. 
- Pokud brána firewall nebo proxy server umožňuje seznam povolených adres DNS, přidejte připojení k adresám URL *.msapproxy.us přes port 443. Pokud ne, povolte přístup k rozsahům IP adres datového centra Azure, které se aktualizují každý týden. Tento předpoklad je použitelný pouze v případě, že povolíte funkci. Není vyžadována pro skutečné přihlášení uživatele. 

### <a name="rolling-out-seamless-sso"></a>Zavedení bezproblémového jednotného přihlašování 
Pomocí níže uvedených pokynů můžete uživatelům postupně zavést bezproblémové jednotné přihlašování. Začnete přidáním následující adresy URL Azure AD do nastavení zóny intranetu všech nebo vybraných uživatelů pomocí zásad skupiny ve službě Active Directory:https://autologon.microsoft.us 

Kromě toho je třeba povolit nastavení zásad zóny intranetu s názvem Povolit aktualizace stavového řádku prostřednictvím skriptu prostřednictvím zásad skupiny. Aspekty prohlížeče Mozilla Firefox (všechny platformy) Mozilla Firefox nepoužívá automaticky ověřování Kerberos. Každý uživatel musí ručně přidat adresu URL Azure AD do nastavení Firefoxu pomocí následujících kroků: 
1. Spusťte Firefox a zadejte about:config do adresního řádku. Zavřete všechna oznámení, která se zobrazí. 
2. Vyhledejte předvolbu network.negotiate-auth.trusted-uris. Tato předvolba uvádí důvěryhodné weby Firefoxu pro ověřování protokolem Kerberos. 
3. Klepněte pravým tlačítkem myši a vyberte Změnit. 
4. Zadejte https://autologon.microsoft.us do pole.
5. Vyberte OK a pak znovu otevřete prohlížeč. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge založený na chromu (všechny platformy) 
Pokud `AuthNegotiateDelegateAllowlist` jste přepsali `AuthServerAllowlist` nastavení zásad nebo nastavení zásad ve vašem prostředí,https://autologon.microsoft.us) ujistěte se, že přidáte adresu URL Azure AD (k nim také. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy) 
Pokud `AuthNegotiateDelegateWhitelist` jste přepsali `AuthServerWhitelist` nastavení zásad nebo nastavení zásad ve vašem prostředí,https://autologon.microsoft.us) ujistěte se, že přidáte adresu URL Azure AD (k nim také. 

## <a name="next-steps"></a>Další kroky
[Předávací ověřování](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[jednotné přihlašování](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
