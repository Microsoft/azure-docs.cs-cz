---
title: Nejčastější dotazy serveru Proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Naučte se odpovědi na nejčastější dotazy týkající se používání proxy aplikací Azure AD k publikování interních místních aplikací vzdáleným uživatelům.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ec9eeb0c35d96ee777771260686178faa536e909
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877299"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Nejčastější dotazy serveru Proxy aplikace služby Active Directory (Azure AD)

Tato stránka odpovídá na nejčastější dotazy týkající se proxy aplikací služby Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Povolení proxy aplikace Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Jakou licenci je potřeba k použití proxy aplikace Azure AD?

Chcete-li použít proxy aplikace Azure AD, musíte mít licenci Azure AD Premium P1 nebo P2. Další informace o licencování najdete v [tématu Azure Active Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Proč je tlačítko "Povolit proxy aplikace zobrazeno šedě"

Ujistěte se, že máte nainstalovanou alespoň licenci Azure AD Premium P1 nebo P2 a konektor proxy aplikací Azure AD. Po úspěšné instalaci prvního konektoru se služba Proxy aplikací Azure AD povolit automaticky.

## <a name="connector-configuration"></a>Konfigurace konektoru

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Může služba Application Proxy Connector běžet v jiném uživatelském kontextu než výchozí?

Ne, tento scénář není podporován. Výchozí nastavení jsou:

- Konektor proxy aplikací Microsoft AAD – WAPCSvc - Síťová služba
- Aktualizace konektoru proxy aplikace Microsoft AAD - WAPCUpdaterSvc - NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Moje back-endová aplikace je hostována na více webových serverech a vyžaduje trvalost uživatelské relace (lepivost). Jak lze dosáhnout trvalosti relace? 

Doporučení naleznete v tématu [Vysoká dostupnost a vyrovnávání zatížení konektorů a aplikací proxy aplikace](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Je podporováno ukončení TLS (kontrola nebo zrychlení TLS/HTTPS) na provozech ze serverů konektorů do Azure?

Konektor proxy aplikace provádí ověřování na základě certifikátu do Azure. Ukončení TLS (kontrola TLS/HTTPS nebo zrychlení) přeruší tuto metodu ověřování a není podporováno. Provoz z konektoru do Azure musí obejít všechna zařízení, která provádějí ukončení TLS.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Mám vytvořit vyhrazený účet pro registraci konektoru pomocí proxy aplikace Azure AD?

Není k tomu žádný důvod. Bude fungovat jakýkoli globální účet správce nebo správce aplikace. Pověření zadaná během instalace se po procesu registrace nepoužijí. Místo toho je certifikát vydán konektoru, který se používá pro ověřování od tohoto okamžiku.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Jak můžu sledovat výkon konektoru proxy aplikací Azure AD?

Existují čítače sledování výkonu, které jsou nainstalovány spolu s konektorem. Chcete-li je zobrazit:  

1. Vyberte **tlačítko Start**, zadejte "Perfmon" a stiskněte klávesu ENTER.
2. Vyberte **Sledování výkonu** **+** a klepněte na zelenou ikonu.
3. Přidejte čítače **konektorů proxy aplikací Microsoft AAD,** které chcete sledovat.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Má konektor proxy aplikace Azure AD musí být ve stejné podsíti jako prostředek?

Spojnice nemusí být ve stejné podsíti. Potřebuje však překlad názvů (DNS, soubor hosts) k prostředku a potřebné připojení k síti (směrování k prostředku, porty otevřené na prostředku atd.). Doporučení najdete [v tématu Důležité informace o topologii sítě při používání proxy aplikace služby Azure Active Directory](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Jaká je délka výchozího a "dlouhého" časového limitu back-endu? Může být časový čas prodloužen?

Výchozí délka je 85 sekund. Nastavení "long" je 180 sekund. Časový limit nelze prodloužit.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Jak změním vstupní stránku, kterou moje aplikace načte?

Na stránce Registrace aplikací můžete změnit adresu URL domovské stránky na požadovanou externí adresu URL vstupní stránky. Zadaná stránka se načte, když se aplikace spustí z moje aplikace nebo portálu Office 365. Kroky konfigurace najdete [v tématu Nastavení vlastní domovské stránky pro publikované aplikace pomocí proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Lze publikovat pouze aplikace založené na službách IIS? A co webové aplikace spuštěné na webových serverech, které nejsou součástí systému Windows? Musí být konektor nainstalován na serveru s nainstalovanou službou IIS?

Ne, neexistuje žádný požadavek na iis pro aplikace, které jsou publikovány. Webové aplikace spuštěné na jiných serverech než windows server můžete publikovat. Je však možné, že nebudete moci používat předběžné ověřování s nesystémem Windows Server, v závislosti na tom, zda webový server podporuje negotiate (kerberos authentication). Služba IIS není vyžadována na serveru, na kterém je konektor nainstalován.

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kdy mám použít metodu PrincipalsAllowedToDelegateToAccount při nastavování delegování omezeného protokolu Kerberos (KCD)?

PrincipalsAllowedToDelegateToAccount Metoda se používá, pokud konektor servery jsou v jiné doméně než účet webové aplikační služby. Vyžaduje použití omezeného delegování založeného na prostředku.
Pokud jsou servery konektorů a účet webové aplikační služby ve stejné doméně, můžete pomocí nástroje Uživatelé a počítače služby Active Directory nakonfigurovat nastavení delegování na každém z účtů počítače konektoru, což jim umožní delegovat na cílový název SPN.

Pokud jsou servery konektorů a účet webové aplikační služby v různých doménách, použije se delegování založené na prostředcích. Oprávnění delegování jsou konfigurována na cílovém webovém serveru a účtu webové aplikační služby. Tato metoda omezené delegování je relativně nová. Metoda byla zavedena v systému Windows Server 2012, který podporuje delegování mezi doménami tím, že umožňuje vlastníkovi prostředků (webové služby) řídit, které účty počítačů a služeb na něj mohou delegovat. Neexistuje žádné ui, které by vám pomohlo s touto konfigurací, takže budete muset použít Prostředí PowerShell.
Další informace naleznete v dokumentu white paper [Understanding Kerberos Constrained Delegation with Application Proxy](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Předávací ověřování

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Je možné používat zásady podmíněného přístupu pro aplikace publikované s předávacím ověřováním?

Zásady podmíněného přístupu se vynucují jenom pro úspěšně předem ověřené uživatele ve službě Azure AD. Předávací ověřování neaktivuje ověřování Azure AD, takže zásady podmíněného přístupu nelze vynutit. S předávacím ověřováním musí být zásady MFA implementovány na místním serveru, pokud je to možné, nebo povolením předběžného ověřování pomocí proxy aplikace Azure AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Mohu publikovat webovou aplikaci s požadavkem na ověření klientského certifikátu?

Ne, tento scénář není podporován, protože proxy aplikace ukončí přenosy TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publikování služby Brána vzdálené plochy

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Jak můžu publikovat bránu vzdálené plochy přes proxy aplikace Azure AD?

Odkazovat na [publikovat vzdálenou plochu pomocí proxy aplikace Azure AD](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Je možné použít delegování omezeného protokolu Kerberos (jednotné přihlašování – integrované ověřování systému Windows) ve scénáři publikování služby Brána vzdálené plochy?

Ne, tento scénář není podporován.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Moji uživatelé nepoužívají internet explorer 11 a scénář předběžného ověřování pro ně nefunguje. Očekává se to?

Ano, očekává se to. Scénář předběžného ověření vyžaduje ovládací prvek ActiveX, který není podporován v prohlížečích třetích stran.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Je webový klient vzdálené plochy (HTML5) podporován?

Ne, tento scénář není aktuálně podporován. Sledujte naše fórum pro zpětnou vazbu [UserVoice,](https://aka.ms/aadapuservoice) kde najdete aktuální informace o této funkci.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Po konfiguraci scénáře předběžného ověřování jsem si uvědomil, že uživatel musí ověřit dvakrát: nejprve ve formuláři pro přihlášení služby Azure AD a potom ve formuláři pro přihlášení rdweb. Očekává se to? Jak to můžu zredukovat na jedno přihlášení?

Ano, očekává se to. Pokud je počítač uživatele Azure AD připojen, uživatel se přihlásí k Azure AD automaticky. Uživatel musí zadat svá pověření pouze ve formuláři pro přihlášení k webu RDWeb.

## <a name="sharepoint-publishing"></a>Publikování na SharePointu

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Jak můžu publikovat SharePoint přes proxy aplikace Azure AD?

Odkazovat na [Povolení vzdáleného přístupu k SharePointu pomocí proxy aplikací Azure AD](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Můžu použít mobilní aplikaci SharePoint (iOS/ Android) pro přístup k publikovanému sharepointovému serveru?

[Mobilní aplikace SharePoint](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) momentálně nepodporuje předběžné ověřování služby Azure Active Directory.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publikování služby AD FS (Active Directory Federation Services) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Můžu použít proxy aplikace Azure AD jako proxy server služby AD FS (například proxy webové aplikace)?

Ne. Azure AD Proxy aplikace je navržen pro práci s Azure AD a nesplňuje požadavky působit jako proxy služby AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Podporuje websocket práci pro jiné aplikace než QlikSense?

V současné době je podpora protokolu WebSocket stále ve verzi Public Preview a nemusí fungovat pro jiné aplikace. Někteří zákazníci měli smíšený úspěch pomocí protokolu WebSocket s jinými aplikacemi. Pokud otestujete takové scénáře, rádi bychom slyšeli vaše výsledky. Prosím, pošlete aadapfeedback@microsoft.comnám svůj názor na .

Funkce (Eventlogs, PowerShell a Vzdálená plocha) v Centru pro správu Windows (WAC) nebo webovém klientovi vzdálené plochy (HTML5) v současné době nefungují prostřednictvím proxy aplikací Azure AD.

## <a name="link-translation"></a>Překlad odkazů

### <a name="does-using-link-translation-affect-performance"></a>Má použití překladu odkazů vliv na výkon?

Ano. Překlad propojení ovlivňuje výkon. Služba Application Proxy prohledá aplikaci pro pevně zakódované odkazy a nahradí je příslušnými publikovanými externími adresami URL před jejich předložením uživateli. 

Pro dosažení nejlepšího výkonu doporučujeme pomocí identických interních a externích adres URL konfigurací [vlastních domén](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Pokud použití vlastních domén není možné, můžete zvýšit výkon překladu odkazů pomocí rozšíření Moje aplikace zabezpečenépřihlášení nebo prohlížeče Microsoft Edge na mobilních zařízeních. Viz [Přesměrovat pevně zakódované odkazy pro aplikace publikované pomocí proxy aplikace Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Zástupné znaky

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Jak lze pomocí zástupných znaků publikovat dvě aplikace se stejným názvem vlastní domény, ale s různými protokoly, jeden pro HTTP a jeden pro protokol HTTPS?

Tento scénář není podporován přímo. Možnosti pro tento scénář jsou:

1. Publikujte adresy URL PROTOKOLU HTTP i HTTPS jako samostatné aplikace se zástupným znakem, ale každé z nich pořte jinou vlastní doménu. Tato konfigurace bude fungovat, protože mají různé externí adresy URL.

2. Publikujte adresu URL https prostřednictvím aplikace se zástupnými kódy. Publikujte aplikace HTTP samostatně pomocí těchto rutin application proxy powershellu:
   - [Správa aplikací proxy aplikace](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Správa konektoru proxy aplikace](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
