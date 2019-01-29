---
title: Povolit vzdálený přístup k Sharepointu pomocí Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Popisuje základní informace o tom, jak integrovat Azure AD Application Proxy místnímu serveru SharePoint.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: c2725b80c897ad07a2ae4245d9ef49c62fbfdc42
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203223"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy

Tento článek popisuje, jak integrovat Proxy aplikací Azure Active Directory (Azure AD) místnímu serveru SharePoint.

Pokud chcete povolit vzdálený přístup k Sharepointu pomocí Proxy aplikací Azure AD, postupujte podle části v tomto článku krok za krokem.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již SharePoint 2013 nebo novější ve vašem prostředí. Kromě toho zvažte následující požadavky:

* SharePoint obsahuje nativní podpora protokolu Kerberos. Uživatelé, kteří přistupují k interním lokalitám vzdáleně prostřednictvím Proxy aplikací Azure AD, proto můžete předpokládat mít možnosti jednotného přihlašování (SSO).

* Tento scénář obsahuje změny konfigurace serveru SharePoint. Doporučujeme používat pracovní prostředí. Tímto způsobem můžete nejdřív proveďte aktualizace na testovacím serveru a potom usnadnění testovacího cyklu před přechodem do produkčního prostředí.

* Požadujeme, aby protokolu SSL na publikovanou adresu URL. Je požadován protokol SSL také na interní adresa URL k zajištění, že odkazy odeslaných/namapované správně.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurace protokolu Kerberos omezeného delegování (KCD)

Pro místní aplikace, které používají ověřování Windows můžete dosáhnout jednotného přihlašování (SSO) s ověřovací protokol Kerberos a funkci s názvem omezené delegování protokolu Kerberos (KCD). KCD, při konfiguraci, umožňuje konektor Proxy aplikací k získání tokenu Windows pro uživatele, i v případě, že uživatel není přihlášený k Windows přímo. Další informace o KCD najdete v tématu [přehled omezeného delegování protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Chcete-li nastavovat KCD pro SharePoint server, použijte postupy v následujících částech sekvenční:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Ujistěte se, že webová aplikace SharePoint běží pod účtem domény

Nejprve ujistěte se, že webová aplikace SharePoint běží pod účtem domény – není místní systém, místní služba nebo účet network service. To proveďte tak, aby hlavní názvy služby (SPN) můžete připojit k tomuto účtu. Hlavní názvy služby jsou způsobu, jakým protokol Kerberos identifikuje různé služby. A budete potřebovat účet později ke konfiguraci KCD.

> [!NOTE]
Musíte mít dříve vytvořený účet služby Azure AD pro službu. Doporučujeme vám umožňují automatickou změnu hesel. Další informace o úplnou sadu kroků a řešení problémů najdete v tématu [konfigurovat automatickou změnu hesel ve službě SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Aby bylo zajištěno, že vaše weby běží pod účtem služby definované, postupujte následovně:

1. Otevřít **Centrální správa SharePoint** lokality.
2. Přejděte na **zabezpečení** a vyberte **konfigurovat účty služby**.
3. Vyberte **fond webových aplikací – SharePoint - 80**. Možnosti můžou mírně lišit podle názvu fondu web, nebo pokud fondu web ve výchozím nastavení používá protokol SSL.

  ![Možnosti pro konfiguraci účtu služby](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Pokud **vyberte účet pro tuto součást** je nastaveno na **místní služba** nebo **síťová služba**, budete muset vytvořit účet. Pokud ne, dokončení a můžete přejít k další části.
5. Vyberte **zaregistrovat nový účet spravovaný**. Po vytvoření účtu je nutné nastavit **fond webových aplikací** než použijete tento účet.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Nastavte hlavní název služby pro účet služby SharePoint

Než začnete konfigurovat KCD, budete muset:

* Identifikujte účet domény s webovou aplikaci SharePoint, která se zveřejňují Proxy služby Azure AD.
* Zvolte interní adresa URL, kterou nakonfigurujete v Proxy služby Azure AD a služby SharePoint. Toto interní adresa URL nesmí být již používá ve webové aplikaci a nikdy se zobrazí ve webovém prohlížeči.

Pokud interní adresa URL zvolené je <https://sharepoint>, pak je hlavní název služby:

```
HTTP/SharePoint
```

> [!NOTE]
> Prosíme vás o respektování následující doporučení pro interní adresa URL:
> * Použití protokolu HTTPS
> * Nepoužívejte vlastní porty
> * Ve službě DNS vytvořte k bodu a službě SharePoint WFE (nebo nástroj pro vyrovnávání zatížení) hostitele (A) a ne na Alias (CName)

Pokud chcete zaregistrovat tento hlavní název služby, spusťte následující příkaz z příkazového řádku jako správce domény:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Tento příkaz nastaví hlavní název služby _HTTP/SharePoint_ pro účet fondu aplikací služby SharePoint _demo\spAppPoolAccount_.

Nahraďte _HTTP/SharePoint_ pomocí hlavního názvu služby pro vaše interní adresa URL a _demo\spAppPoolAccount_ s účet fondu aplikací ve vašem prostředí. Příkaz Setspn hledá hlavního názvu služby předtím, než ho přidá. V již existuje, zobrazí se **duplicitní hodnota hlavního názvu služby** chyby. V takovém případě zvažte odebrání existující hlavní název služby, pokud není nastavená v rámci účtu fondu aplikací.

Můžete ověřit, že hlavní název služby byl přidán spuštěním příkazu Setspn -L možnost. Další informace o tomto příkazu najdete v tématu [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Ujistěte se, že konektor je důvěryhodný pro delegování SPN přidán účet fondu aplikací služby SharePoint

Nakonfigurujte KCD tak, aby služba Azure AD Application Proxy můžete delegovat identit uživatelů do účet fondu aplikací služby SharePoint. Nakonfigurujte KCD povolením konektoru Proxy aplikace načíst lístky protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Potom tento server v tomto případě předá kontextu do cílové aplikace nebo služby SharePoint.

Pokud chcete nakonfigurovat KCD, opakujte následující kroky pro každý počítač konektoru:

1. Přihlaste se jako správce domény na řadič domény a pak otevřete **Active Directory Users and Computers**.
2. Najděte, na kterém běží konektor na počítač. V tomto příkladu je stejný server SharePoint.
3. Klikněte dvakrát na počítač a potom klikněte na tlačítko **delegování** kartu.
4. Ujistěte se, že jsou nastavení delegování **důvěřovat tomuto počítači pro delegování pouze určeným službám**. Vyberte **použití libovolného protokolu pro ověřování**.
5. Klikněte na tlačítko **přidat** tlačítko, klikněte na tlačítko **uživatele nebo počítače**a vyhledejte účet fondu aplikací služby SharePoint, například _demo\spAppPoolAccount_.
6. V seznamu hlavních názvů služby vyberte ten, který jste dříve vytvořili pro účet služby.
7. Klikněte na **OK**. Klikněte na tlačítko **OK** znovu a uložte změny.
  
  ![Nastavení delegace](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Krok 2: Konfigurace Proxy služby Azure AD

Teď, když jste nakonfigurovali KCD, budete připraveni ke konfiguraci Azure AD Application Proxy.

1. Publikování webu služby SharePoint s následujícím nastavením. Podrobné pokyny najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-publish-azure-portal.md).
   * **Interní adresa URL**: Interní adresa URL Sharepointu, který jste vybrali dříve, jako například **<https://SharePoint/>**.
   * **Metoda předběžného ověřování služby**: Azure Active Directory
   * **Přeložit adresy URL v hlavičkách**: NO

   >[!TIP]
   >SharePoint používá _hlavičku hostitele_ hodnotu pro vyhledávání webu. Generuje také odkazy na základě této hodnoty. Výsledkem je, že je odkaz, který SharePoint vygeneruje publikované adresy URL, která je správně nastavený na použití externí adresu URL. Nastavením této hodnoty na **Ano** také povolí konektor k předání požadavku do back endové aplikace. Nicméně, nastavením této hodnoty na **ne** znamená, že konektor nebude odesílat název interního hostitele. Místo toho konektor odešle hlavičku hostitele jako adresu URL publikované na back endové aplikace.

   ![Publikovat jako aplikaci služby SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Po publikování aplikace konfigurujte nastavení jednotného přihlašování pomocí následujících kroků:

   1. Na stránce aplikace na portálu vyberte **jednotného přihlašování**.
   2. Režim jednotného přihlašování, vyberte **integrované ověřování Windows**.
   3. Vnitřní hlavní název služby aplikace nastavte na hodnotu, která jste nastavili dříve. V tomto příkladu, který by **HTTP/SharePoint**.
   4. V "Delegovaná identita přihlášení", vyberte **název účtu SAM On-premises**.

   ![Konfigurace ověření integrované Windows pro jednotné přihlašování](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Abychom mohli dokončit nastavování vaší aplikace, přejděte na **uživatelů a skupin** části a přiřadit uživatelům přístup k této aplikaci. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Krok 3: Konfigurace služby SharePoint k použití protokolu Kerberos a adresy URL proxy serveru Azure AD

Dalším krokem je rozšíření webové aplikace služby SharePoint do nové zóny nakonfigurované protokolu Kerberos a mapování odpovídající alternativní přístup povolit služby SharePoint pro zpracování příchozí požadavky odeslané na interní adresa URL, a reagovat s odkazy, které jsou vytvořené pro externí adresu URL.

1. Spustit **prostředí SharePoint Management Shell**.
2. Spusťte následující skript pro rozšíření webové aplikace do sítě Extranet zóny a zapněte ověřování protokolem Kerberos:

  ```powershell
  # Replace "http://spsites/" with the URL of your web application
  # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
  $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
  Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
  ```

3. Otevřít **Centrální správa SharePoint** lokality.
4. V části **nastavení systému**vyberte **konfigurace mapování alternativních adres URL**. Otevře se pole mapování alternativních adres URL.
5. Vyberte svou lokalitu, například **SharePoint - 80**. Pro tuto chvíli nemá extranetu zóny interní adresa URL správně nastavit ještě:

  ![Alternativní mapování pole](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Klikněte na tlačítko **přidejte interní adresy URL**.
7. V **protokol adresy URL, hostitele a port** textové pole, typ **interní adresa URL** nakonfigurované v proxy služby Azure AD, například <https://SharePoint/>.
8. Vyberte zónu **Extranet** v rozevíracím seznamu.
9. Klikněte na **Uložit**.
10. Mapování alternativních adres URL by teď měl vypadat takto:

  ![Opravte mapování alternativních adres URL](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Krok 4: Ujistěte se, že je nakonfigurovaný certifikát HTTPS pro web služby IIS extranetu zóny

Konfigurace služby SharePoint je nyní dokončena, ale protože je interní adresa URL extranetu zóny <https://SharePoint/>, certifikát musí být nastavena pro tuto lokalitu.

1. Otevřete konzolu prostředí Windows PowerShell.
2. Spusťte následující skript, který vygenerovat certifikát podepsaný svým držitelem a přidejte ho do počítače úložišti:

  ```powershell
  # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
  New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
  ```

  > [!NOTE]
  Certifikáty podepsané svým držitelem jsou vhodné pouze pro účely testování. V produkčním prostředí doporučujeme používat certifikáty vydané certifikační autoritou místo.

3. Otevřete konzolu "Správce Internetové informační služby".
4. Rozbalte položku serveru ve stromovém zobrazení rozbalte "Lokalit", vyberte lokalitu, "Proxy AAD – SharePoint" a klikněte na **vazby**.
5. Vyberte vazbu https a klikněte na tlačítko **upravit...** .
6. V poli certifikátu protokolu SSL, zvolte **SharePoint** certifikátu a klikněte na tlačítko OK.

Můžete teď přístup k webu služby SharePoint externě prostřednictvím Proxy aplikací Azure AD.

## <a name="next-steps"></a>Další postup

* [Práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md)
* [Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
