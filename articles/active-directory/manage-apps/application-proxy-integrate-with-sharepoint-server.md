---
title: Povolení vzdáleného přístupu ke službě SharePoint – Azure Proxy aplikací služby AD
description: Obsahuje základní informace o tom, jak integrovat místní SharePointový Server s využitím Azure Proxy aplikací služby AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b5895d016e2d9d9b471218bc083ea7585254b45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258677"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolení vzdáleného přístupu k SharePointu s využitím Proxy aplikací služby Azure AD

Tento podrobný průvodce vysvětluje, jak integrovat místní farmu služby SharePoint s proxy aplikací Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Předpoklady

K provedení konfigurace potřebujete tyto prostředky:
- Farma SharePoint 2013 nebo novější.
- Tenant Azure AD s plánem, který zahrnuje proxy aplikace. Přečtěte si další informace o [plánech a cenách Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
- [Vlastní ověřená doména](../fundamentals/add-custom-domain.md) v TENANTOVI Azure AD.
- Místní služba Active Directory synchronizovaná s Azure AD Connect, přes které se uživatelé můžou [přihlásit k Azure](../hybrid/plan-connect-user-signin.md).
- Konektor proxy aplikací nainstalovaný a spuštěný v počítači v podnikové doméně.

Konfigurace SharePointu s proxy aplikací vyžaduje dvě adresy URL:
- Externí adresa URL, která se zobrazí koncovým uživatelům a určená v Azure AD. Tato adresa URL může používat vlastní doménu. Přečtěte si další informace o [práci s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).
- Interní adresa URL, která je známá jenom v podnikové doméně a nikdy se nepoužila přímo

> [!IMPORTANT]
> Chcete-li se ujistit, že jsou odkazy správně mapovány, postupujte podle těchto doporučení pro interní adresu URL:
> - Použijte protokol HTTPS.
> - Nepoužívejte vlastní porty.
> - V podnikové službě DNS (Domain Name System) vytvořte hostitele (A), který odkazuje na SharePoint WFE (nebo nástroj pro vyrovnávání zatížení), a ne na alias (CName).

Tento článek používá následující hodnoty:
- Interní adresa URL: `https://sharepoint`
- Externí adresa URL: `https://spsites-demo1984.msappproxy.net/`
- Účet fondu aplikací pro webovou aplikaci SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Krok 1: Konfigurace aplikace v Azure AD, která používá proxy aplikace

V tomto kroku vytvoříte v tenantovi Azure Active Directory aplikaci, která používá proxy aplikace. Nastavíte externí adresu URL a zadáte interní adresu URL, kterou použijete později v SharePointu.

1. Vytvořte aplikaci, jak je popsáno v následujících nastaveních. Podrobné pokyny najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Interní adresa URL**: interní adresa URL služby SharePoint, která bude nastavena později v SharePointu, například `https://sharepoint` .
   * **Předběžné ověření**: Azure Active Directory
   * **Přeložit adresy URL v hlavičkách**: ne
   * **Přeložit adresy URL v těle aplikace**: ne

   ![Publikování SharePointu jako aplikace](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po publikování aplikace postupujte podle těchto kroků a nakonfigurujte nastavení jednotného přihlašování:

   1. Na stránce aplikace na portálu vyberte **Jednotné přihlašování**.
   1. Pro **Režim jednotného přihlašování** vyberte **Integrované ověřování systému Windows**.
   1. Nastavte **vnitřní hlavní název aplikace** na hodnotu, kterou jste nastavili dříve. V tomto příkladu je hodnota `HTTP/sharepoint` .
   1. V části **delegovaná identita přihlášení** vyberte nejvhodnější možnost pro konfiguraci doménové struktury služby Active Directory. Pokud máte například v doménové struktuře jednu doménu Active Directory, vyberte možnost místní **název účtu SAM** (jak je znázorněno na následujícím snímku obrazovky). Pokud ale uživatelé nejsou ve stejné doméně jako SharePoint a na serverech konektoru proxy aplikací, vyberte **místní hlavní název uživatele** (nezobrazuje se na snímku obrazovky).

   ![Konfigurace integrovaného ověřování systému Windows pro jednotné přihlašování](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Pokud chcete dokončit nastavení aplikace, přejděte do oddílu **Uživatelé a skupiny** a udělte uživatelům přístup k této aplikaci. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Krok 2: Konfigurace webové aplikace SharePoint

Aby bylo možné správně pracovat s Azure Proxy aplikací služby AD, musí být webová aplikace SharePoint nakonfigurována pomocí protokolu Kerberos a příslušná mapování alternativního přístupu. Existují dvě možné možnosti:

- Vytvořte novou webovou aplikaci a použijte pouze výchozí zónu. Tato možnost je upřednostňovaná, protože nabízí nejlepší prostředí se službou SharePoint (například odkazy v e-mailových výstrahách generovaných službou SharePoint vždy ukazují na výchozí zónu).
- Rozšiřování existující webové aplikace pro konfiguraci protokolu Kerberos v jiné než výchozí zóně.

> [!IMPORTANT]
> Bez ohledu na použitou zónu musí být účet fondu aplikací webové aplikace SharePoint účtem domény pro správné fungování protokolu Kerberos.

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

    2. Otevřete web **centrální správy služby SharePoint** .
    1. V části **nastavení systému** vyberte **Konfigurovat mapování alternativního přístupu**. Otevře se pole **kolekce mapování alternativního přístupu** .
    1. Vyfiltrujte zobrazení pomocí nové webové aplikace a potvrďte, že se zobrazí něco podobného:

       ![Mapování alternativních přístupů webové aplikace](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Pokud rozšíříte existující webovou aplikaci do nové zóny (pro případ, že nemůžete použít výchozí zónu):

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

    2. Otevřete web **centrální správy služby SharePoint** .
    1. V části **nastavení systému** vyberte **Konfigurovat mapování alternativního přístupu**. Otevře se pole **kolekce mapování alternativního přístupu** .
    1. Vyfiltrujte zobrazení pomocí rozšířené webové aplikace a potvrďte, že se zobrazí něco podobného:

        ![Mapování alternativních přístupů k rozšířené aplikaci](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Ujistěte se, že webová aplikace SharePoint běží pod účtem domény.

Chcete-li identifikovat účet, ve kterém je spuštěn fond aplikací webové aplikace služby SharePoint a ověřit, zda se jedná o doménový účet, postupujte takto:

1. Otevřete web **centrální správy služby SharePoint** .
1. Přejít na **zabezpečení** a vyberte **Konfigurovat účty služeb**.
1. Vyberte **fond webových aplikací – YourWebApplicationName**.

   ![Možnosti konfigurace účtu služby](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Potvrďte, že **možnost vybrat účet pro tuto součást** vrátí doménový účet a zapamatuje si ji, protože bude potřeba v dalším kroku.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Ujistěte se, že je certifikát HTTPS nakonfigurovaný pro web IIS zóny extranetu.

Vzhledem k tomu, že interní adresa URL používá protokol HTTPS ( `https://SharePoint/` ), musí být na webu Internetová informační služba (IIS) nastaven certifikát.

1. Otevřete konzolu prostředí Windows PowerShell.
1. Spusťte následující skript, který vygeneruje certifikát podepsaný svým držitelem a přidá ho do úložiště MY v počítači:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Certifikáty podepsané svým držitelem jsou vhodné pouze pro testovací účely. V produkčních prostředích doporučujeme místo toho použít certifikáty vydané certifikační autoritou.

1. Otevřete konzolu nástroje Internetová informační služba Manager.
1. Rozbalte server ve stromovém zobrazení, rozbalte položku **lokality**, vyberte **proxy server služby SharePoint-AAD** a vyberte možnost **vazby**.
1. Vyberte **vazba https** a pak vyberte **Upravit**.
1. V poli certifikát TLS/SSL zvolte certifikát **SharePoint** a pak vyberte **OK**.

Teď můžete k webu služby SharePoint přistupovat externě prostřednictvím Azure Proxy aplikací služby AD.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Krok 3: Konfigurace omezeného delegování protokolu Kerberos

Uživatelé se zpočátku ověřují ve službě Azure AD a pak na SharePoint pomocí protokolu Kerberos prostřednictvím konektoru proxy Azure AD. Pokud chcete, aby konektor mohl získat token protokolu Kerberos jménem uživatele Azure AD, musíte nakonfigurovat omezené delegování protokolu Kerberos (KCD) s přechodem protokolu. Další informace o KCD najdete v tématu [Přehled omezeného delegování protokolu Kerberos](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Nastavení hlavního názvu služby (SPN) pro účet služby SharePoint

V tomto článku je interní adresa URL `https://sharepoint` , a proto je hlavní název služby (SPN) `HTTP/sharepoint` . Tyto hodnoty je nutné nahradit hodnotami, které odpovídají vašemu prostředí.
Chcete-li zaregistrovat hlavní název `HTTP/sharepoint` služby (SPN) pro účet fondu aplikací SharePoint `Contoso\spapppool` , spusťte následující příkaz z příkazového řádku jako správce domény:

`setspn -S HTTP/sharepoint Contoso\spapppool`

`Setspn`Příkaz vyhledá hlavní název služby (SPN) před tím, než ho přidá. Pokud hlavní název služby (SPN) už existuje, zobrazí se chyba s **duplicitní hodnotou hlavního názvu** služby. V takovém případě zvažte odebrání existujícího hlavního názvu služby (SPN), pokud není nastaven v rámci správného účtu fondu aplikací. , Zda byl hlavní název služby (SPN) úspěšně přidán, můžete ověřit spuštěním `Setspn` příkazu s parametrem-L. Další informace o tomto příkazu najdete v tématu [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Zajistěte, aby byl konektor důvěryhodný pro delegování do hlavního názvu služby (SPN), který byl přidán do účtu fondu aplikací služby SharePoint.

Nakonfigurujte KCD, aby služba Azure Proxy aplikací služby AD mohla delegovat identity uživatelů na účet fondu aplikací SharePoint. Nakonfigurujte KCD tak, že povolíte, aby konektor proxy aplikací mohl načíst lístky Kerberos pro uživatele, kteří byli ověřeni ve službě Azure Active Directory. Pak tento server předá kontext cílové aplikaci (v tomto případě SharePoint).

Ke konfiguraci KCD použijte následující postup pro každý počítač konektoru:

1. Přihlaste se k řadiči domény jako správce domény a pak otevřete položku Uživatelé a počítače služby Active Directory.
1. Najděte počítač, na kterém běží konektor proxy služby Azure AD. V tomto příkladu je to samotný server SharePoint.
1. Poklikejte na počítač a vyberte kartu **Delegování**.
1. Ujistěte se, že možnosti delegování jsou nastavené tak, aby **důvěřovaly tomuto počítači pro delegování pouze určeným službám**. Pak vyberte **použít libovolný protokol pro ověřování**.
1. Vyberte tlačítko **Přidat** , vyberte možnost **Uživatelé nebo počítače** a vyhledejte účet fondu aplikací služby SharePoint. Příklad: `Contoso\spapppool`.
1. V seznamu SPN vyberte ten, který jste předtím vytvořili pro účet služby.
1. Vyberte **OK** a pak znovu vyberte **OK** , aby se změny uložily.
  
   ![Nastavení delegování](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Nyní jste připraveni se přihlásit ke službě SharePoint pomocí externí adresy URL a ověřit ji pomocí Azure.

## <a name="troubleshoot-sign-in-errors"></a>Řešení chyb při přihlašování

Pokud přihlášení k webu nefunguje, můžete získat další informace o problému v protokolech konektoru: z počítače, na kterém konektor spouštíte, otevřete Prohlížeč událostí, přejdete do části **protokoly aplikací a služeb**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector** a zkontrolujte protokol **správce** .

## <a name="next-steps"></a>Další kroky

* [Práce s vlastními doménami v Azure Proxy aplikací služby AD](application-proxy-configure-custom-domain.md)
* [Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)