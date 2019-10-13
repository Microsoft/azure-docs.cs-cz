---
title: Povolení vzdáleného přístupu ke službě SharePoint pomocí Azure Proxy aplikací služby AD | Microsoft Docs
description: Obsahuje základní informace o tom, jak integrovat místní SharePointový Server s využitím Azure Proxy aplikací služby AD.
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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286100"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolení vzdáleného přístupu k SharePointu s využitím Proxy aplikací služby Azure AD

Tento podrobný průvodce vysvětluje, jak integrovat místní farmu služby SharePoint s proxy aplikací Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Předpoklady

K provedení konfigurace potřebujete tyto prostředky:
- Farma SharePoint 2013 nebo novější.
- Tenant Azure Active Directory s plánem, který zahrnuje proxy aplikace. Přečtěte si další informace o [plánech a cenách Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- [Vlastní ověřená doména](../fundamentals/add-custom-domain.md) v TENANTOVI Azure AD.
- Místní služba Active Directory je synchronizovaná s Azure AD Connect s uživateli, kteří se můžou [přihlásit k Azure](../hybrid/plan-connect-user-signin.md).
- Konektor proxy aplikací nainstalovaný a spuštěný v počítači v podnikové doméně.

Konfigurace SharePointu s proxy aplikací vyžaduje dvě adresy URL:
- Externí adresa URL viditelná pro koncové uživatele a určená v Azure Active Directory. Tato adresa URL může používat vlastní doménu. Přečtěte si další informace o [práci s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).
- Interní adresa URL, která je známá jenom v podnikové doméně a nikdy se nepoužila přímo

> [!IMPORTANT]
> Chcete-li zajistit, aby odkazy byly správně mapovány, postupujte podle těchto doporučení pro interní adresu URL:
> - Použít protokol HTTPS
> - Nepoužívat vlastní porty
> - V podnikovém DNS Vytvořte hostitele (A), který odkazuje na SharePoint WFE (nebo nástroj pro vyrovnávání zatížení), a ne na alias (CName).

V tomto článku budou použity následující hodnoty:
- Interní adresa URL: `https://sharepoint`
- Externí adresa URL: `https://spsites-demo1984.msappproxy.net/`
- Účet fondu aplikací pro webovou aplikaci SharePoint: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Krok 1: Konfigurace aplikace v Azure AD, která používá proxy aplikace

V tomto kroku vytvoříte v tenantovi Azure Active Directory aplikaci, která bude používat proxy aplikace. Nastavíte externí adresu URL a zadáte interní adresu URL, kterou použijete později v SharePointu.

1. Vytvořte aplikaci, jak je popsáno v následujících nastaveních. Podrobné pokyny najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Interní adresa URL**: interní adresa URL služby SharePoint, která bude nastavena později v SharePointu, například `https://sharepoint`.
   * **Metoda**předběžného ověřování: Azure Active Directory
   * **Přeložit adresu URL v hlavičkách**: ne
   * **Přeložit adresu URL v těle aplikace**: ne

   ![Publikování SharePointu jako aplikace](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po publikování aplikace nakonfigurujte nastavení jednotného přihlašování pomocí následujících kroků:

   1. Na stránce aplikace na portálu vyberte **jednotné přihlašování**.
   1. V případě **režimu jednotného přihlašování**vyberte **integrované ověřování systému Windows**.
   1. Nastavte **vnitřní hlavní název aplikace** na hodnotu, kterou jste nastavili dříve. V tomto příkladu by byla hodnota `HTTP/sharepoint`.
   1. V části **delegovaná identita přihlášení**vyberte nejvhodnější možnost pro konfiguraci doménové struktury služby Active Directory. Pokud máte například v doménové struktuře jednu doménu služby Active Directory, vyberte možnost místní **název účtu SAM** (jak je vidět níže), ale pokud uživatelé nejsou ve stejné doméně jako SharePoint a servery konektoru proxy aplikací, vyberte **místní uživatelský objekt zabezpečení. název** (není zobrazený).

   ![Konfigurace integrovaného ověřování systému Windows pro jednotné přihlašování](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Pokud chcete dokončit nastavování aplikace, přejděte do části **Uživatelé a skupiny** a přiřaďte uživatele k přístupu k této aplikaci. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Krok 2: Konfigurace webové aplikace SharePoint

Aby bylo možné správně pracovat s Azure Proxy aplikací služby AD, musí být webová aplikace SharePoint nakonfigurována pomocí protokolu Kerberos a příslušná mapování alternativního přístupu. Existují dvě možné možnosti:

1. Vytvořte novou webovou aplikaci a použijte pouze výchozí zónu. Tato možnost je upřednostňovanou možností, jak se službou SharePoint (například odkazy v e-mailových výstrahách vygenerovaných serverem SharePoint vždy ukazují na výchozí zónu).
1. Rozšiřování existující webové aplikace pro konfiguraci protokolu Kerberos na jiné než výchozí zóně.

> [!IMPORTANT]
> Bez ohledu na to, kterou zónu použijete, musí být účet fondu aplikací webové aplikace SharePoint účtem domény, aby protokol Kerberos správně fungoval.

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

    1. Otevřete web **centrální správy služby SharePoint** .
    1. V části **nastavení systému**vyberte **Konfigurovat mapování alternativního přístupu**. Otevře se pole mapování alternativního přístupu.
    1. Vyfiltrujte zobrazení pomocí nové webové aplikace a potvrďte, že se zobrazí tento text:

       ![Mapování alternativních přístupů webové aplikace](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Pokud rozšíříte stávající webovou aplikaci do nové zóny (pro případ, že nemůžete použít výchozí zónu):

    1. Spusťte **prostředí SharePoint Management Shell** a spusťte následující skript:

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. Otevřete web **centrální správy služby SharePoint** .
    1. V části **nastavení systému**vyberte **Konfigurovat mapování alternativního přístupu**. Otevře se pole mapování alternativního přístupu.
    1. Vyfiltrujte zobrazení pomocí rozšířené webové aplikace a potvrďte, že se zobrazí tento text:

        ![Mapování alternativních přístupů k rozšířené aplikaci](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Ujistěte se, že webová aplikace SharePoint běží pod účtem domény.

Pomocí následujících kroků Identifikujte účet, ve kterém je spuštěn fond aplikací webové aplikace SharePoint, a ujistěte se, že se jedná o účet domény:

1. Otevřete web **centrální správy služby SharePoint** .
1. Přejít na **zabezpečení** a vyberte **Konfigurovat účty služeb**.
1. Vyberte **fond webových aplikací – YourWebApplicationName**.

   ![Možnosti konfigurace účtu služby](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Potvrďte, že **možnost vybrat účet pro tuto součást** vrátí účet domény a zapamatuje se, jak bude potřeba v dalším kroku.

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Zajistěte, aby byl certifikát HTTPS nakonfigurovaný pro web IIS zóny extranetu.

Vzhledem k tomu, že interní adresa URL používá protokol HTTPS (`https://SharePoint/`), musí být na webu IIS nastaven certifikát.

1. Otevřete konzolu prostředí Windows PowerShell.
1. Spusťte následující skript, který vygeneruje certifikát podepsaný svým držitelem a přidá ho do počítače moje úložiště:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Certifikáty podepsané svým držitelem jsou vhodné pouze pro testovací účely. V produkčních prostředích doporučujeme místo toho použít certifikáty vydané certifikační autoritou.

1. Otevřete konzolu "Internetová informační služba Manager".
1. Rozbalte server ve stromovém zobrazení, rozbalte položku lokality, vyberte lokalitu SharePoint-AAD proxy a klikněte na **vazby**.
1. Vyberte vazba https a klikněte na **Upravit...** .
1. V poli certifikát SSL vyberte certifikát **SharePoint** a klikněte na OK.

Nyní můžete k webu služby SharePoint přistupovat externě prostřednictvím Azure Proxy aplikací služby AD.

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>Krok 3: Konfigurace omezeného delegování protokolu Kerberos (KCD)

Uživatelé se zpočátku ověřují v Azure Active Directory a pak na SharePoint pomocí protokolu Kerberos prostřednictvím konektoru proxy služby Azure AD. Aby mohl konektor získat token protokolu Kerberos jménem Azure Active Directoryho uživatele, je nutné nakonfigurovat omezené delegování protokolu Kerberos pomocí přechodu protokolu. Další informace o KCD najdete v tématu [Přehled omezeného delegování protokolu Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>Nastavení hlavního názvu služby pro účet služby SharePoint

V tomto článku je interní adresa URL `https://sharepoint`, takže hlavní název služby je `HTTP/sharepoint`. Tyto hodnoty je potřeba nahradit těmi, které odpovídají vašemu prostředí.
Chcete-li zaregistrovat hlavní název služby (SPN `HTTP/sharepoint`) pro účet fondu aplikací SharePoint `Contoso\spapppool`, spusťte následující příkaz z příkazového řádku jako správce domény:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Příkaz Setspn vyhledá hlavní název služby (SPN) před tím, než ho přidá. Pokud už existuje, zobrazí se chyba s **duplicitní hodnotou SPN** . V takovém případě zvažte odebrání existujícího hlavního názvu služby (SPN), pokud není nastaven v rámci správného účtu fondu aplikací.  
Úspěšné přidání hlavního názvu služby (SPN) můžete ověřit spuštěním příkazu setspn s parametrem-L. Další informace o tomto příkazu najdete v tématu [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Zajistěte, aby byl konektor důvěryhodný pro delegování do hlavního názvu služby (SPN) přidaný do účtu fondu aplikací SharePoint.

Nakonfigurujte KCD, aby služba Azure Proxy aplikací služby AD mohla delegovat identity uživatelů na účet fondu aplikací SharePoint. Nakonfigurujte KCD tak, že povolíte konektor proxy aplikací k načtení lístků protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Pak tento server předá kontext cílové aplikaci nebo SharePoint v tomto případě.

Pokud chcete nakonfigurovat KCD, opakujte následující kroky pro každý počítač konektoru:

1. Přihlaste se k řadiči domény jako správce domény a pak otevřete položku **Uživatelé a počítače služby Active Directory**.
1. Najděte počítač, na kterém běží konektor proxy služby Azure AD. V tomto příkladu je to samotný server SharePoint.
1. Poklikejte na počítač a pak klikněte na kartu **delegování** .
1. Ujistěte se, že nastavení delegování je nastaveno na **Důvěřovat tomuto počítači pro delegování pouze určeným službám**. Pak vyberte **použít libovolný protokol pro ověřování**.
1. Klikněte na tlačítko **Přidat** , klikněte na položku **Uživatelé nebo počítače**a vyhledejte účet fondu aplikací služby SharePoint, například `Contoso\spapppool`.
1. V seznamu SPN vyberte ten, který jste předtím vytvořili pro účet služby.
1. Klikněte na **OK**. Opětovným kliknutím na tlačítko **OK** změny uložte.
  
   ![Nastavení delegování](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Nyní jste připraveni přihlásit se ke službě SharePoint pomocí externí adresy URL a ověřit ji pomocí Azure.

## <a name="troubleshoot-sign-in-errors"></a>Řešení chyb při přihlašování

Pokud přihlášení k webu nefunguje, můžete získat další informace o problému v protokolech konektoru: z počítače, na kterém konektor spouštíte, otevřete Prohlížeč událostí, přejdete do části **protokoly aplikací a služeb** > **Microsoft** >  **.** **Konektor**AadApplicationProxy  >  a zkontrolujte protokol **správce** .

## <a name="next-steps"></a>Další kroky

* [Práce s vlastními doménami v Azure Proxy aplikací služby AD](application-proxy-configure-custom-domain.md)
* [Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
