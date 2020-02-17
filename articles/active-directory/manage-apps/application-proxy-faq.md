---
title: Nejčastější dotazy k Azure Proxy aplikací služby AD | Microsoft Docs
description: Přečtěte si odpovědi na nejčastější dotazy k používání služby Azure Proxy aplikací služby AD k publikování interních místních aplikací pro vzdálené uživatele.
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
ms.openlocfilehash: de2b40ea0339b564b97d17601415d1071bdc6a6e
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367909"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Nejčastější dotazy k proxy aplikací služby Active Directory (Azure AD)

Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se proxy aplikace Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Povolení služby Azure Proxy aplikací služby AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Jaká licence je nutná k používání Azure Proxy aplikací služby AD?

Pokud chcete používat Proxy aplikací služby AD Azure, musíte mít licenci Azure AD Premium P1 nebo P2. Další informace o licencování najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Proč je tlačítko "Povolit proxy aplikací zobrazeno šedě?"

Ujistěte se, že máte alespoň Azure AD Premium licenci P1 nebo P2 a že je nainstalovaný konektor Azure Proxy aplikací služby AD. Po úspěšné instalaci prvního konektoru se služba Azure Proxy aplikací služby AD povolí automaticky.

## <a name="connector-configuration"></a>Konfigurace konektoru

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Můžou služby Application proxy Connector běžet v jiném uživatelském kontextu než výchozí?

Ne, tento scénář se nepodporuje. Výchozí nastavení:

- Microsoft AAD Application proxy Connector – WAPCSvc – síťová služba
- Aktualizátor konektorů proxy aplikace služby Microsoft AAD-WAPCUpdaterSvc-NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Moje back-endové aplikace je hostována na více webových serverech a vyžaduje trvalost uživatelských relací (vytrvalost). Jak můžu dosáhnout trvalosti relace? 

Doporučení najdete v tématu [Vysoká dostupnost a vyrovnávání zatížení konektorů a aplikací proxy aplikací](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Můžu umístit dopředné proxy zařízení mezi servery konektoru a back-end aplikační server?

Ne, tento scénář se nepodporuje. Jenom konektory a služby aktualizací se dají nakonfigurovat tak, aby pro odchozí přenosy do Azure používaly předaný proxy server. Zobrazit [práci se stávajícími místními proxy servery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-ssl-termination-sslhttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Je u provozu z konektorových serverů do Azure podporováno ukončení protokolu SSL (kontrola nebo akcelerace SSL/HTTPS)?

Konektor proxy aplikací provádí ověřování pomocí certifikátů v Azure. Ukončení protokolu SSL (kontrola SSL/HTTPS) ruší tuto metodu ověřování a nepodporuje se. Přenos z konektoru do Azure musí obejít všechna zařízení, která provádí ukončení protokolu SSL.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Mám vytvořit vyhrazený účet pro registraci konektoru s využitím Azure Proxy aplikací služby AD?

Neexistuje žádný důvod. Bude fungovat jakýkoli účet správce globálních správců nebo aplikací. Přihlašovací údaje zadané během instalace se nepoužívají po procesu registrace. Místo toho se certifikát vydá pro konektor, který se používá pro ověření od tohoto okamžiku.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Jak můžu monitorovat výkon konektoru služby Azure Proxy aplikací služby AD?

K dispozici jsou čítače sledování výkonu, které jsou nainstalovány spolu s konektorem. Postup při jejich zobrazení:  

1. Vyberte **Start**, zadejte "perfmon" a stiskněte klávesu ENTER.
2. Vyberte **sledování výkonu** a klikněte na ikonu zeleného **+** .
3. Přidejte čítače **Microsoft AAD Application proxy Connector** , které chcete monitorovat.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Musí být konektor Azure Proxy aplikací služby AD ve stejné podsíti jako prostředek?

Konektor se nemusí nacházet ve stejné podsíti. Nicméně potřebuje překlad názvů (DNS, soubor hostitelů) k prostředku a nezbytné síťové připojení (směrování do prostředku, porty otevřené v prostředku atd.). Doporučení najdete v tématu [požadavky na topologii sítě při použití proxy aplikací služby Azure Active Directory](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Konfigurace aplikací

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Jaká je délka výchozího a "dlouhého" back-endu časového limitu? Může být časový limit prodloužen?

Výchozí délka je 85 sekund. Nastavení "Long" je 180 sekund. Časový limit nelze rozšířit.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Návody změnit cílovou stránku Moje aplikace se načte?

Na stránce registrace aplikací můžete změnit adresu URL domovské stránky na požadovanou externí adresu URL cílové stránky. Zadaná stránka se načte při spuštění aplikace z mých aplikací nebo z portálu Office 365. Postup konfigurace najdete v tématu [nastavení vlastní domovské stránky pro publikované aplikace pomocí Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Můžou se publikovat jenom aplikace založené na službě IIS? Jaké webové aplikace běží na webových serverech s jiným systémem než Windows? Musí být konektor nainstalovaný na serveru s nainstalovanou službou IIS?

Ne, pro publikované aplikace není k dispozici žádný požadavek služby IIS. Můžete publikovat webové aplikace běžící na jiných serverech než Windows Server. V závislosti na tom, jestli webový server podporuje Negotiate (ověřování protokolem Kerberos), ale možná nebudete moct používat předběžné ověřování s jiným systémem než Windows serverem. Na serveru, na kterém je konektor nainstalovaný, není služba IIS nutná.

## <a name="integrated-windows-authentication"></a>Integrované ověřování systému Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kdy mám použít metodu PrincipalsAllowedToDelegateToAccount při nastavování omezeného delegování protokolu Kerberos (KCD)?

Metoda PrincipalsAllowedToDelegateToAccount se používá v případě, že servery konektorů jsou v jiné doméně než účet služby webové aplikace. Vyžaduje použití omezeného delegování založeného na prostředku.
Pokud jsou servery konektorů a účet služby webové aplikace ve stejné doméně, můžete použít modul Uživatelé a počítače služby Active Directory ke konfiguraci nastavení delegování na každém účtu počítače konektoru, který jim umožní delegovat na cílový hlavní název služby.

Pokud jsou servery konektorů a účet služby webové aplikace v různých doménách, používá se delegování na základě prostředků. Oprávnění k delegování jsou nakonfigurovaná na cílovém webovém serveru a účtu služby webové aplikace. Tato metoda omezeného delegování je relativně nová. Tato metoda byla představena v systému Windows Server 2012, který podporuje delegování mezi doménami tím, že umožňuje vlastníkovi prostředků (webové služby) určit, které počítače a účty služeb se k němu mohou delegovat. Pro pomoc s touto konfigurací není k dispozici žádné uživatelské rozhraní, takže budete muset použít PowerShell.
Další informace najdete v dokumentu White Paper [Principy omezeného delegování protokolu Kerberos pomocí proxy aplikace](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Předávací ověřování

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Můžu pro aplikace publikované s předávacím ověřováním použít zásady podmíněného přístupu?

Zásady podmíněného přístupu se vynutily jenom pro úspěšné předběžně ověřené uživatele v Azure AD. Předávací ověřování neaktivuje ověřování Azure AD, takže nejde vyhovět zásadám podmíněného přístupu. V případě předávacího ověřování musí být zásady MFA implementované na místním serveru, pokud je to možné, nebo povolením předběžného ověřování pomocí Azure Proxy aplikací služby AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Můžu publikovat webovou aplikaci s požadavkem na ověření certifikátu klienta?

Ne, tento scénář se nepodporuje, protože proxy aplikace ukončí provoz TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Brána vzdálené plochy publikování

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Jak můžu publikovat Brána vzdálené plochy přes Azure Proxy aplikací služby AD?

Informace najdete [v tématu publikování vzdálené plochy pomocí Azure proxy aplikací služby AD](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>Můžu ve scénáři publikování Brána vzdálené plochy použít omezené delegování Kerberos?

Ne, tento scénář se nepodporuje.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Uživatelé nepoužívají Internet Explorer 11 a scénář předběžného ověřování pro ně nefunguje. Je to očekávané?

Ano, očekává se. Scénář předběžného ověření vyžaduje ovládací prvek ActiveX, který není podporován v prohlížečích třetích stran.

### <a name="is-the-remote-desktop-web-client-supported"></a>Je webový klient vzdálené plochy podporován?

Ne, tento scénář se momentálně nepodporuje. Podle našeho fóra pro názory na [UserVoice](https://aka.ms/aadapuservoice) si Projděte aktualizace této funkce.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Po nakonfigurovaném scénáři předběžného ověření jsem dřív, že se uživatel musí ověřit dvakrát: nejdřív ve formuláři přihlášení ke službě Azure AD a pak na formuláři pro přihlášení k RDWeb. Je to očekávané? Jak se dá snížit na jedno přihlášení?

Ano, očekává se. Pokud je počítač uživatele připojený k Azure AD, uživatel se přihlásí automaticky do Azure AD. Uživatel musí zadat své přihlašovací údaje pouze v přihlašovacím formuláři RDWeb.

## <a name="sharepoint-publishing"></a>Publikování SharePointu

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Jak můžu publikovat SharePoint přes Azure Proxy aplikací služby AD?

Další informace najdete [v tématu Povolení vzdáleného přístupu ke službě SharePoint pomocí Azure proxy aplikací služby AD](application-proxy-integrate-with-sharepoint-server.md).

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publikování Active Directory Federation Services (AD FS) (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Můžu použít Azure Proxy aplikací služby AD jako proxy server AD FS (jako je proxy webových aplikací)?

Ne. Služba Azure Proxy aplikací služby AD je navržená tak, aby spolupracovala s Azure AD a nesplňovala požadavky, které by fungovaly jako proxy AD FS.

## <a name="websocket"></a>Protokolu WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Funguje podpora protokolu WebSocket pro jiné aplikace než QlikSense?

V současné době je podpora protokolu WebSocket stále ve verzi Public Preview a nemusí fungovat pro ostatní aplikace. Někteří zákazníci mají při použití protokolu WebSocket v jiných aplikacích smíšenou úspěšnost. Při testování takových scénářů bychom rádi slyšeli vaše výsledky. Pošlete nám prosím svůj názor na aadapfeedback@microsoft.com.

Funkce (Eventlogs, PowerShell a Vzdálená plocha) v centru pro správu systému Windows (WAC) nebo webový klient vzdálené plochy nefungují prostřednictvím služby Azure Proxy aplikací služby AD v předsoučasné době.

## <a name="link-translation"></a>Překlad propojení

### <a name="does-using-link-translation-affect-performance"></a>Má použití překladu odkazů vliv na výkon?

Ano. Překlad propojení má vliv na výkon. Služba proxy aplikací vyhledá v aplikaci odkazy na pevně zakódované a nahradí je jejich příslušnými, publikovanými externími adresami URL. teprve potom je prezentuje uživateli. 

Pro nejlepší výkon doporučujeme použít stejné interní a externí adresy URL konfigurací [vlastních domén](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Pokud není možné používat vlastní domény, můžete zlepšit výkon překladu propojení pomocí rozšíření zabezpečeného přihlašování k aplikacím nebo prohlížeče Microsoft Edge na mobilních zařízeních. Přečtěte si téma [přesměrování pevně zakódované odkazů pro aplikace publikované s Azure proxy aplikací služby AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Zástupné znaky

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Návody pomocí zástupných znaků publikovat dvě aplikace se stejným vlastním názvem domény, ale s různými protokoly, jednu pro HTTP a jednu pro protokol HTTPS?

Tento scénář se nepodporuje přímo. Pro tento scénář máte tyto možnosti:

1. Publikujte adresy URL HTTP i HTTPS jako samostatné aplikace se zástupnými znaky, ale poskytněte každé z nich jinou vlastní doménu. Tato konfigurace bude fungovat, protože má různé externí adresy URL.

2. Publikujte adresu URL HTTPS prostřednictvím aplikace se zástupnými znaky. Publikujte aplikace HTTP samostatně pomocí těchto rutin PowerShellu pro proxy aplikace:
   - [Správa aplikací proxy aplikací](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Správa konektoru proxy aplikací](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
