---
title: Povolení vzdáleného přístupu k SharePointu – proxy aplikací Azure AD
description: Popisuje základní informace o tom, jak integrovat místní SharePoint server s proxy aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481292"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolení vzdáleného přístupu k SharePointu s využitím Proxy aplikací služby Azure AD

Tento podrobný průvodce vysvětluje, jak integrovat místní sharepointovou farmu s proxy aplikacemi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést konfiguraci, potřebujete následující prostředky:
- Farma SharePointu 2013 nebo novější.
- Tenant Azure AD s plánem, který zahrnuje proxy aplikace. Další informace o [plánech Azure AD a cenách](https://azure.microsoft.com/pricing/details/active-directory/).
- [Vlastní ověřená doména](../fundamentals/add-custom-domain.md) v tenantovi Azure AD.
- Místní služba Active Directory synchronizovaná s Azure AD Connect, přes kterou se můžou uživatelé [přihlásit k Azure](../hybrid/plan-connect-user-signin.md).
- Konektor proxy aplikace nainstalovaný a spuštěný v počítači v rámci podnikové domény.

Konfigurace SharePointu s proxy aplikacemi vyžaduje dvě adresy URL:
- Externí adresa URL, viditelná pro koncové uživatele a určená ve službě Azure AD. Tato adresa URL může používat vlastní doménu. Další informace o [práci s vlastními doménami v proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).
- Interní adresa URL, známá pouze v rámci podnikové domény a nikdy použitá přímo.

> [!IMPORTANT]
> Chcete-li se ujistit, že jsou odkazy správně namapovány, postupujte podle následujících doporučení pro interní adresu URL:
> - Použijte protokol HTTPS.
> - Nepoužívejte vlastní porty.
> - V podnikovém systému DNS (DOMAIN Name System) vytvořte hostitele (A), který přejde na sharepointwfe (nebo nástroj pro vyrovnávání zatížení), a nikoli na alias (CName).

Tento článek používá následující hodnoty:
- Interní adresa URL:`https://sharepoint`
- Externí adresa URL:`https://spsites-demo1984.msappproxy.net/`
- Účet fondu aplikací pro webovou aplikaci SharePoint:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Krok 1: Konfigurace aplikace ve službě Azure AD, která používá proxy aplikace

V tomto kroku vytvoříte aplikaci v tenantovi služby Azure Active Directory, která používá proxy aplikace. Nastavíte externí adresu URL a určíte interní adresu URL, která se použije později v SharePointu.

1. Vytvořte aplikaci, jak je popsáno v následujícím nastavení. Podrobné pokyny najdete v tématu [Publikování aplikací pomocí proxy aplikací Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Interní adresa URL**: Interní adresa URL SharePointu, `https://sharepoint`která bude nastavena později v SharePointu, například .
   * **Předběžné ověření:** Služba Azure Active Directory
   * **Přeložit adresy URL v záhlaví**: Ne
   * **Přeložit adresy URL v těle aplikace**: Ne

   ![Publikování SharePointu jako aplikace](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po publikování aplikace nakonfigurujte nastavení jednotného přihlašování takto:

   1. Na stránce aplikace na portálu vyberte **Možnost Jednotné přihlašování**.
   1. V **režimu jednotného přihlašování**vyberte **možnost Integrované ověřování systému Windows**.
   1. Nastavte **hlavní název služby interní aplikace** na hodnotu, kterou jste nastavili dříve. V tomto příkladu `HTTP/sharepoint`je hodnota .
   1. V části **Delegovaná identita přihlášení**vyberte nejvhodnější možnost pro konfiguraci doménové struktury služby Active Directory. Pokud máte například v doménové struktuře jednu doménu služby Active Directory, vyberte **název účtu Místní SAM** (jak je znázorněno na následujícím snímku obrazovky). Ale pokud vaši uživatelé nejsou ve stejné doméně jako SharePoint a servery Konektor proxy aplikací, vyberte **místní hlavní jméno uživatele** (není zobrazeno na snímku obrazovky).

   ![Konfigurace integrovaného ověřování systému Windows pro službu SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Chcete-li dokončit nastavení aplikace, přejděte do části **Uživatelé a skupiny** a přiřaďte uživatelům přístup k této aplikaci. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Krok 2: Konfigurace webové aplikace SharePointu

Webová aplikace SharePoint musí být nakonfigurovaná pomocí protokolu Kerberos a odpovídající mapování alternativního přístupu, aby správně fungovala s proxy aplikacemi Azure AD. Existují dvě možnosti:

- Vytvořte novou webovou aplikaci a používejte pouze výchozí zónu. Toto je upřednostňovaná možnost, protože nabízí nejlepší prostředí se SharePointem (například odkazy v e-mailových upozorněních generovaných SharePointem vždy odkazují na výchozí zónu).
- Rozšiřte existující webovou aplikaci a nakonfigurujte protokol Kerberos v nevýchozí zóně.

> [!IMPORTANT]
> Bez ohledu na používanou zónu musí být účet fondu aplikací webové aplikace SharePoint, aby protokol Kerberos fungoval správně.

### <a name="provision-the-sharepoint-web-application"></a>Zřízení webové aplikace SharePoint

- Pokud vytvoříte novou webovou aplikaci a použijete pouze výchozí zónu (upřednostňovaná možnost):

    1. Spusťte **prostředí SharePoint Management Shell** a spusťte následující skript:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Otevřete web **Centrální správy SharePointu.**
    1. V části **Nastavení systému**vyberte **Konfigurovat mapování alternativního přístupu**. Otevře se pole **Kolekce mapování alternativního přístupu.**
    1. Filtrujte displej pomocí nové webové aplikace a potvrďte, že vidíte něco takového:

       ![Alternativní mapování přístupu webové aplikace](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Pokud rozšíříte existující webovou aplikaci do nové zóny (v případě, že nemůžete použít výchozí zónu):

    1. Spusťte prostředí SharePoint Management Shell a spusťte následující skript:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Otevřete web **Centrální správy SharePointu.**
    1. V části **Nastavení systému**vyberte **Konfigurovat mapování alternativního přístupu**. Otevře se pole **Kolekce mapování alternativního přístupu.**
    1. Filtrujte displej pomocí rozšířené webové aplikace a potvrďte, že vidíte něco takového:

        ![Alternativní mapování přístupu rozšířené aplikace](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Zkontrolujte, jestli je sharepointová webová aplikace spuštěná pod účtem domény.

Chcete-li identifikovat účet se spuštěným fondem aplikací webové aplikace SharePoint a ujistěte se, že se jedná o účet domény, postupujte takto:

1. Otevřete web **Centrální správy SharePointu.**
1. Přejděte na **Zabezpečení** a vyberte **Konfigurovat účty služeb**.
1. Vyberte **fond webových aplikací - Název aplikace Web .**

   ![Volby pro konfiguraci účtu služby](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Potvrďte, že **funkce Vybrat účet pro tuto komponentu** vrátí účet domény a zapamatuje si ho, protože bude potřeba v dalším kroku.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Zkontrolujte, zda je pro lokalitu služby IIS zóny Extranet nakonfigurován certifikát HTTPS.

Vzhledem k tomu,`https://SharePoint/`že interní adresa URL používá protokol HTTPS ( ), musí být na webu Internetové informační služby (IIS) nastaven certifikát.

1. Otevřete konzolu Prostředí Windows PowerShell.
1. Spusťte následující skript, který vygeneruje certifikát podepsaný svým držitelem a přidá jej do obchodu MY počítače:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Certifikáty podepsané svým držitelem jsou vhodné pouze pro testovací účely. V produkčních prostředích důrazně doporučujeme použít certifikáty vydané certifikační autoritou.

1. Otevřete konzolu Správce Internetové informační služby.
1. Rozbalte server ve stromovém zobrazení, rozbalte **možnost Weby**, vyberte web **Proxy služby SharePoint - AAD** a **vyberte Vazby**.
1. Vyberte **vazbu https** a pak vyberte **Upravit**.
1. V poli Certifikát TLS/SSL zvolte **SharePoint certificate** a pak vyberte **OK**.

Teď můžete přistupovat k webu SharePoint externě prostřednictvím proxy aplikace Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Krok 3: Konfigurace delegování s omezením protokolu Kerberos

Uživatelé se nejprve ověří ve službě Azure AD a poté na SharePoint pomocí protokolu Kerberos prostřednictvím konektoru proxy Azure AD. Chcete-li povolit konektoru získat token Protokolu Kerberos jménem uživatele Azure AD, je nutné nakonfigurovat kerberos omezené delegování (KCD) s přechodem protokolu. Další informace o kcd, naleznete [v tématu Kerberos omezené delegování přehled](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Nastavení hlavního nového nebo nového účtu služby SharePoint

V tomto článku je `https://sharepoint`interní adresa URL , a proto `HTTP/sharepoint`je hlavní název služby (SPN). Tyto hodnoty je nutné nahradit hodnotami, které odpovídají vašemu prostředí.
Chcete-li `HTTP/sharepoint` zaregistrovat název SPN `Contoso\spapppool`pro účet fondu aplikací služby SharePoint , spusťte následující příkaz z příkazového řádku jako správce domény:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Příkaz `Setspn` vyhledá hlavní línečku před přidáním. Pokud hlavní aktualizace služby SPN již existuje, zobrazí se chyba **duplicitní hodnoty spn.** V takovém případě zvažte odebrání existujícího hlavního název služby, pokud není nastaven pod správným účtem fondu aplikací. Můžete ověřit, že hlavní aktualizace služeb spn byla úspěšně přidána spuštěním příkazu `Setspn` s -L možnost. Další informace o tomto příkazu naleznete v [tématu Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Ujistěte se, že konektor je důvěryhodný pro delegování do hlavního okruhu spn, který byl přidán do účtu fondu aplikací služby SharePoint

Nakonfigurujte disk KCD tak, aby služba Proxy aplikací Azure AD mohla delegovat identity uživatelů na účet fondu aplikací SharePointu. Nakonfigurujte KCD povolením konektoru proxy aplikace k načtení lístků protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Potom tento server předá kontext cílové aplikaci (SharePoint v tomto případě).

Chcete-li konfigurovat disk KCD, postupujte takto pro každý počítač s konektory:

1. Přihlaste se k řadiči domény jako správce domény a spusťte službu Uživatelé a počítače služby Active Directory.
1. Najděte počítač s konektorem Proxy Azure AD. V tomto příkladu je to samotný sharepointový server.
1. Poklepejte na počítač a vyberte kartu **Delegování.**
1. Zkontrolujte, zda jsou možnosti delegování nastaveny na **možnost Důvěřovat tomuto počítači pouze pro delegování na určené služby**. Potom vyberte **Použít libovolný ověřovací protokol**.
1. Vyberte tlačítko **Přidat,** vyberte **Uživatelé nebo Počítače**a vyhledejte účet fondu aplikací Služby SharePoint. Například: `Contoso\spapppool`.
1. V seznamu hlavní seznamy SPN vyberte ten, který jste vytvořili dříve pro účet služby.
1. Vyberte **OK** a pak znovu vyberte **OK,** chcete-li změny uložit.
  
   ![Nastavení delegování](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Teď jste připraveni přihlásit se k SharePointu pomocí externí adresy URL a k ověření pomocí Azure.

## <a name="troubleshoot-sign-in-errors"></a>Poradce při potížích s přihlášením

Pokud přihlášení k webu nefunguje, můžete získat další informace o problému v protokolech konektoru: Ze zařízení se spuštěným konektorem otevřete prohlížeč událostí, přejděte na **aplikace a služby** > **Microsoft** > Microsoft**AadApplicationProxy** > **Connector**a zkontrolujte protokol **správce.**

## <a name="next-steps"></a>Další kroky

* [Práce s vlastními doménami v proxy aplikacíazure AD](application-proxy-configure-custom-domain.md)
* [Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)
