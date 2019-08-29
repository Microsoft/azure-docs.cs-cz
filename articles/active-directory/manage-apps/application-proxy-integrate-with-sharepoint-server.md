---
title: Povolit vzdálený přístup k Sharepointu pomocí Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Popisuje základní informace o tom, jak integrovat Azure AD Application Proxy místnímu serveru SharePoint.
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
ms.date: 08/28/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1265341ecfdb7f418ea89bb0ec848a20c6b430cd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127688"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy

Tento článek popisuje, jak integrovat Proxy aplikací Azure Active Directory (Azure AD) místnímu serveru SharePoint.

Pokud chcete povolit vzdálený přístup k Sharepointu pomocí Proxy aplikací Azure AD, postupujte podle části v tomto článku krok za krokem.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již SharePoint 2013 nebo novější ve vašem prostředí. Kromě toho zvažte následující požadavky:

* SharePoint obsahuje nativní podpora protokolu Kerberos. Uživatelé, kteří přistupují k interním lokalitám vzdáleně prostřednictvím Proxy aplikací Azure AD, proto můžete předpokládat mít možnosti jednotného přihlašování (SSO).
* Tento scénář obsahuje změny konfigurace serveru SharePoint. Doporučujeme používat pracovní prostředí. Tímto způsobem můžete nejdřív proveďte aktualizace na testovacím serveru a potom usnadnění testovacího cyklu před přechodem do produkčního prostředí.
* Požadujeme, aby protokolu SSL na publikovanou adresu URL. Na interní adrese URL je také vyžadován protokol SSL, aby bylo zajištěno, že jsou odkazy odesílány nebo správně mapovány.

> [!NOTE]
> Osvědčeným postupem je použití vlastních domén, kdykoli je to možné. S vlastní doménou můžete nakonfigurovat stejnou adresu URL pro interní adresu URL i externí adresu URL. Pak můžete stejný odkaz použít pro přístup k aplikaci z vaší sítě nebo mimo ni. Tato konfigurace optimalizuje prostředí pro uživatele a další aplikace, které potřebují přístup k vaší aplikaci. Přečtěte si další informace o [práci s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurace omezeného delegování protokolu Kerberos (KCD)

Pro místní aplikace, které používají ověřování Windows můžete dosáhnout jednotného přihlašování (SSO) s ověřovací protokol Kerberos a funkci s názvem omezené delegování protokolu Kerberos (KCD). KCD, při konfiguraci, umožňuje konektor Proxy aplikací k získání tokenu Windows pro uživatele, i v případě, že uživatel není přihlášený k Windows přímo. Další informace o KCD najdete v tématu [přehled omezeného delegování protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Chcete-li nastavovat KCD pro SharePoint server, použijte postupy v následujících částech sekvenční:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Zajistěte, aby webová aplikace SharePoint běžela pod účtem domény.

Nejprve se ujistěte, že je webová aplikace SharePoint spuštěna pod účtem domény – nejedná se o místní systém, místní službu nebo síťovou službu. Provedete to tak, že k tomuto účtu budete moct připojovat hlavní názvy služby (SPN). Hlavní názvy služby jsou způsobu, jakým protokol Kerberos identifikuje různé služby. A budete potřebovat účet později ke konfiguraci KCD.

> [!NOTE]
> Musíte mít dříve vytvořený účet služby Azure AD pro službu. Doporučujeme vám umožňují automatickou změnu hesel. Další informace o kompletní sadě kroků a odstraňování potíží najdete v tématu [Konfigurace automatické změny hesla ve službě SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Aby bylo zajištěno, že vaše weby běží pod účtem služby definované, postupujte následovně:

1. Otevřete web **centrální správy služby SharePoint** .
1. Přejděte na **zabezpečení** a vyberte **konfigurovat účty služby**.
1. Vyberte **fond webových aplikací – SharePoint - 80**. Možnosti můžou mírně lišit podle názvu fondu web, nebo pokud fondu web ve výchozím nastavení používá protokol SSL.

   ![Možnosti pro konfiguraci účtu služby](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Pokud **vyberte účet pro tuto součást** je nastaveno na **místní služba** nebo **síťová služba**, budete muset vytvořit účet. Pokud ne, dokončení a můžete přejít k další části.
1. Vyberte **zaregistrovat nový účet spravovaný**. Po vytvoření účtu je nutné nastavit **fond webových aplikací** než použijete tento účet.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Nastavte hlavní název služby pro účet služby SharePoint

Před konfigurací KCD je potřeba provést tyto kroky:

* Identifikujte účet domény, na kterém běží webová aplikace SharePoint, kterou bude služba Azure AD proxy vystavovat.
* Vyberte interní adresu URL, která se nakonfiguruje na proxy serveru Azure AD i na SharePointu. Tato interní adresa URL se nesmí ve webové aplikaci používat a nikdy se nezobrazí ve webovém prohlížeči.

Za předpokladu <https://sharepoint>, že je zvolená interní adresa URL, hlavní název služby (SPN):

```
HTTP/SharePoint
```

> [!NOTE]
> Použijte tato doporučení pro interní adresu URL:
> * Použít protokol HTTPS
> * Nepoužívat vlastní porty
> * V DNS Vytvořte hostitele (A), který odkazuje na SharePoint WFE (nebo nástroj pro vyrovnávání zatížení), a ne na alias (CName).

K registraci tohoto hlavního názvu služby (SPN) spusťte z příkazového řádku následující příkaz jako správce domény:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Tento příkaz nastaví _http/SharePoint_ hlavního názvu služby (SPN) pro účet fondu aplikací SharePoint _demo\spAppPoolAccount_.

Nahraďte _http/SharePoint_ pomocí hlavního názvu služby (SPN) pro vaši interní adresu URL a _demo\spAppPoolAccount_ účet fondu aplikací ve vašem prostředí. Příkaz Setspn hledá hlavního názvu služby předtím, než ho přidá. V takovém případě se zobrazí chyba s **duplicitní hodnotou hlavního názvu** služby (SPN). V takovém případě zvažte odebrání existujícího hlavního názvu služby (SPN), pokud není nastaven v rámci správného účtu fondu aplikací.

, Zda byl hlavní název služby (SPN) přidán, můžete ověřit spuštěním příkazu setspn s parametrem-L. Další informace o tomto příkazu najdete v tématu [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Zajistěte, aby byl konektor důvěryhodný pro delegování do hlavního názvu služby (SPN) přidaný do účtu fondu aplikací SharePoint.

Nakonfigurujte KCD, aby služba Azure Proxy aplikací služby AD mohla delegovat identity uživatelů na účet fondu aplikací SharePoint. Nakonfigurujte KCD povolením konektoru Proxy aplikace načíst lístky protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Potom tento server v tomto případě předá kontextu do cílové aplikace nebo služby SharePoint.

Pokud chcete nakonfigurovat KCD, opakujte následující kroky pro každý počítač konektoru:

1. Přihlaste se jako správce domény na řadič domény a pak otevřete **Active Directory Users and Computers**.
1. Najděte, na kterém běží konektor na počítač. V tomto příkladu je stejný server SharePoint.
1. Klikněte dvakrát na počítač a potom klikněte na tlačítko **delegování** kartu.
1. Ujistěte se, že jsou nastavení delegování **důvěřovat tomuto počítači pro delegování pouze určeným službám**. Vyberte **použití libovolného protokolu pro ověřování**.
1. Klikněte na tlačítko **Přidat** , klikněte na **Uživatelé nebo počítače**a vyhledejte účet fondu aplikací SharePoint, například _demo\spAppPoolAccount_.
1. V seznamu hlavních názvů služby vyberte ten, který jste dříve vytvořili pro účet služby.
1. Klikněte na **OK**. Klikněte na tlačítko **OK** znovu a uložte změny.
  
   ![Nastavení delegace](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Krok 2: Konfigurace proxy serveru Azure AD

Teď, když jste nakonfigurovali KCD, jste připraveni nakonfigurovat Azure Proxy aplikací služby AD.

1. Publikování webu služby SharePoint s následujícím nastavením. Podrobné pokyny najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Interní adresa URL**: Interní adresa URL služby SharePoint, kterou jste zvolili **<https://SharePoint/>** dříve, například.
   * **Metoda**předběžného ověřování: Azure Active Directory
   * **Přeložit adresu URL v hlavičkách**: NO

   > [!TIP]
   > SharePoint používá _hlavičku hostitele_ hodnotu pro vyhledávání webu. Generuje také odkazy na základě této hodnoty. Výsledkem je, že je odkaz, který SharePoint vygeneruje publikované adresy URL, která je správně nastavený na použití externí adresu URL. Nastavením této hodnoty na **Ano** také povolí konektor k předání požadavku do back endové aplikace. Nicméně, nastavením této hodnoty na **ne** znamená, že konektor nebude odesílat název interního hostitele. Místo toho konektor odešle hlavičku hostitele jako adresu URL publikované na back endové aplikace.

   ![Publikovat jako aplikaci služby SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Po publikování aplikace konfigurujte nastavení jednotného přihlašování pomocí následujících kroků:

   1. Na stránce aplikace na portálu vyberte **jednotného přihlašování**.
   1. Režim jednotného přihlašování, vyberte **integrované ověřování Windows**.
   1. Vnitřní hlavní název služby aplikace nastavte na hodnotu, která jste nastavili dříve. V tomto příkladu to bude **http/SharePoint**.
   1. V části delegovaná identita přihlášení vyberte nejvhodnější možnost pro konfiguraci doménové struktury služby Active Directory. Pokud máte například v doménové struktuře jednu doménu služby Active Directory, vyberte možnost místní **název účtu SAM** (jak je vidět níže), ale pokud uživatelé nejsou ve stejné doméně jako SharePoint a servery proxy konektoru aplikace, pak vyberte **místní hlavní název uživatele.** (nezobrazuje se).

   ![Konfigurace ověření integrované Windows pro jednotné přihlašování](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Abychom mohli dokončit nastavování vaší aplikace, přejděte na **uživatelů a skupin** části a přiřadit uživatelům přístup k této aplikaci. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Krok 3: Konfigurace služby SharePoint pro používání protokolu Kerberos a adres URL proxy serveru Azure AD

Dalším krokem je rozšiřování webové aplikace SharePoint do nové zóny, nakonfigurovanou pomocí protokolu Kerberos a odpovídajícího mapování alternativního přístupu, aby služba SharePoint mohla zpracovávat příchozí požadavky odeslané na interní adresu URL a reagovat s odkazy vytvořenými pro externí adresu URL.

1. Spusťte **prostředí SharePoint Management Shell**.
1. Spuštěním následujícího skriptu rozšíříte webovou aplikaci do zóny extranetu a povolíte ověřování pomocí protokolu Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

1. Otevřete web **centrální správy služby SharePoint** .
1. V části **nastavení systému**vyberte **konfigurace mapování alternativních adres URL**. Otevře se pole mapování alternativních adres URL.
1. Vyberte svou lokalitu, například **SharePoint-80**. V současnosti nemá zóna extranetu ještě správně nastavenou interní adresu URL:

   ![Zobrazuje pole mapování alternativního přístupu.](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

1. Klikněte na **Přidat interní adresy URL**.
1. Do textového pole **Protokol adresy URL, pole hostitel a port** zadejte **interní adresu URL** nakonfigurovanou v proxy serveru Azure <https://SharePoint/>ad, například.
1. V rozevíracím seznamu vyberte zóna **extranet** .
1. Klikněte na **Uložit**.
1. Mapování alternativního přístupu by teď mělo vypadat takto:

    ![Správné mapování alternativního přístupu](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Krok 4: Zajistěte, aby byl certifikát HTTPS nakonfigurovaný pro web IIS zóny extranetu.

Konfigurace SharePointu je teď dokončená, ale vzhledem k tomu, že interní adresa <https://SharePoint/>URL zóny extranetu je, musí být pro tento web nastaven certifikát.

1. Otevřete konzolu prostředí Windows PowerShell.
1. Spusťte následující skript, který vygeneruje certifikát podepsaný svým držitelem a přidá ho do počítače moje úložiště:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Certifikáty podepsané svým držitelem jsou vhodné pouze pro testovací účely. V produkčních prostředích doporučujeme místo toho použít certifikáty vydané certifikační autoritou.

1. Otevřete konzolu "Internetová informační služba Manager".
1. Rozbalte server ve stromovém zobrazení, rozbalte položku lokality, vyberte lokalitu SharePoint-AAD proxy a klikněte na **vazby**.
1. Vyberte vazba https a klikněte na **Upravit...** .
1. V poli certifikát SSL vyberte certifikát **SharePoint** a klikněte na OK.

Můžete teď přístup k webu služby SharePoint externě prostřednictvím Proxy aplikací Azure AD.

## <a name="next-steps"></a>Další postup

* [Práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md)
* [Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
