---
title: Webová služba mobilní aplikace Azure MFA Serveru | Dokumentace Microsoftu
description: Aplikace Microsoft Authenticator nabízí možnost dalšího ověření mimo pásmo.  To umožňuje serveru MFA používat nabízená oznámení pro uživatele.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 7ca5c7bcc82f0a77276f4f39a02d8abf2f47bc10
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Povolení ověření přes mobilní aplikaci se serverem Azure Multi-Factor Authentication

Aplikace Microsoft Authenticator nabízí možnost dalšího ověření mimo pásmo. Namísto zahájení automatizovaného telefonního hovoru nebo odeslání SMS zprávy uživateli při přihlášení, doručí služba Azure Multi-Factor Authentication oznámení do aplikace Microsoft Authenticator na tablet nebo smartphone uživatele. Uživatel v aplikaci jednoduše dokončí přihlášení klepnutím na **Ověřit** (nebo zadá kód PIN a klepne na Ověřit).

Použití mobilní aplikace pro dvojstupňové ověřování se upřednostňuje v případě, že je telefonní příjem nespolehlivý. Pokud aplikaci používáte jako generátor tokenu OAUTH, nevyžaduje připojení k síti ani k internetu.

V závislosti na vašem prostředí můžete chtít webovou službu mobilní aplikace nasadit na stejný server jako Azure Multi-Factor Authentication Server, nebo na jiný server s přístupem k internetu.

Pokud jste nainstalovali MFA Server verze 8.0 nebo novější, většinu níže uvedených kroků provádět nemusíte. Ověřování přes mobilní aplikaci můžete nastavit podle pokynů v části [Konfigurace mobilní aplikace](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Požadavky

Pokud chcete použít aplikaci Microsoft Authenticator, je třeba splnit následující podmínky, aby aplikace mohla úspěšně komunikovat s webovou službou mobilní aplikace:

* Azure Multi-Factor Authentication Server verze 6.x nebo novější
* Instalace webové služby mobilní aplikace na internetovém webovém serveru s [Internetovou informační službou Microsoft® (IIS) IIS 7.x nebo vyšší](http://www.iis.net/)
* Nainstalované, registrované a povolené rozhraní ASP.NET v4.0.30319
* Požadované role služby zahrnují technologie ASP.NET a kompatibilitu metabáze služby IIS 6
* Webová služba mobilní aplikace je přístupná přes veřejnou adresu URL.
* Webová služba mobilní aplikace je zabezpečena certifikátem SSL.
* Instalujte sadu SDK webové služby Azure Multi-Factor Authentication ve službě IIS 7.x nebo vyšší na **stejném serveru jako Azure Multi-Factor Authentication Server**.
* Sada SDK webové služby Azure Multi-Factor Authentication je zabezpečená certifikátem SSL.
* Webová služba mobilní aplikace se může připojit k sadě SDK webové služby Azure Multi-Factor Authentication přes protokol SSL.
* Webová služba mobilní aplikace se může ověřit v sadě SDK webové služby Azure MFA pomocí přihlašovacích údajů účtu služby, který je členem skupiny zabezpečení „PhoneFactor Admins“. Tento účet služby a skupina existují ve službě Active Directory, pokud Azure Multi-Factor Authentication Server běží na serveru připojeném k doméně. Tento účet služby a skupina existují místně na Azure Multi-Factor Authentication Serveru, pokud není připojený k doméně.

## <a name="install-the-mobile-app-web-service"></a>Instalaci webové služby mobilní aplikace

Před instalací webové služby mobilní aplikace mějte na paměti následující:

* Instalace webové služby mobilní aplikace není nutná ve verzi 8.0 nebo novější. Proveďte pouze kroky v části [Konfigurace mobilní aplikace](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).
* Potřebujete účet služby, který je součástí skupiny PhoneFactor Admins. Může to být stejný účet, jako jste použili k instalaci portálu User Portal.
* Je užitečné otevřít webový prohlížeč na webovém serveru s přístupem k internetu a přejít na adresu URL sady Web Service SDK, která byla zadán do souboru web.config. Pokud se prohlížeč úspěšně dostane k webové službě, měla by se zobrazit výzva k zadání přihlašovacích údajů. Zadejte uživatelské jméno a heslo, které jste zadali do souboru web.config, v naprosto stejném tvaru. Ujistěte se, že se nezobrazí žádná varování nebo chyby týkající se certifikátu.
* Pokud se před webovým serverem webové služby mobilní aplikace nachází reverzní proxy server nebo brána firewall a provádí snižování zátěže protokolu SSL, můžete upravit soubor web.config webové služby mobilní aplikace tak, aby webová služba mobilní aplikace mohla používat protokol http místo protokolu https. SSL je však stále zapotřebí ve směru z mobilní aplikace k reverznímu proxy serveru / bráně firewall. Do části \<appSettings\> přidejte následující klíč:

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Instalaci sady SDK webové služby

V obou scénářích, pokud sada SDK webové služby Azure Multi-Factor Authentication **není** na Azure Multi-Factor Authentication (MFA) Serveru nainstalovaná, proveďte následující kroky.

1. Otevřete konzolu Multi-Factor Authentication Serveru.
2. Přejděte do části **Sada SDK webové služby** a vyberte **Instalovat sadu SDK webové služby**.
3. Dokončete instalaci s použitím výchozích hodnot, pokud je z nějakého důvodu nepotřebujete změnit.
4. Vytvořte vazbu certifikátu SSL k webu ve službě IIS.

Pokud máte dotazy ke konfiguraci certifikátu SSL na serveru služby IIS, přečtěte si článek [Nastavení protokolu SSL ve službě IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis).

Sada SDK webové služby musí být zabezpečená certifikátem SSL. Pro tento účel stačí certifikát podepsaný svým držitelem. Importujte certifikát do úložiště „Důvěryhodné kořenové certifikační autority“ účtu místního počítače na webovém serveru portálu User Portal, aby byl certifikát důvěryhodný při navazování připojení SSL.

![Nastavení konfigurace MFA Serveru – sada SDK webové služby](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>Instalace služby

1. **Na MFA Serveru** přejděte do instalační cesty.
2. Přejděte do složky, kde je nainstalovaný Azure MFA Server – výchozí umístění je **C:\Program Files\Azure Multi-Factor Authentication**.
3. Vyhledejte instalační soubor **MultiFactorAuthenticationMobileAppWebServiceSetup64**. Pokud server **nemá** přístup k internetu, zkopírujte instalační soubor na server s přístupem k internetu.
4. Pokud MFA Server **nemá** přístup k internetu, přepněte na **server s přístupem k internetu**.
5. Spusťte instalační soubor **MultiFactorAuthenticationMobileAppWebServiceSetup64** jako správce, změňte v případě potřeby web, a pokud chcete, změňte virtuální adresář na krátký název.
6. Po dokončení instalace přejděte do umístění **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (nebo odpovídajícího adresáře podle názvu virtuálního adresáře) a upravte soubor Web.Config.

   * Vyhledejte klíč **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** a změňte **value=""** na **value="DOMÉNA\Uživatel"**, kde DOMÉNA\Uživatel je účet služby, který je součástí skupiny PhoneFactor Admins.
   * Vyhledejte klíč **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** a změňte **value=""** na **value="Heslo"**, kde Heslo je heslo pro účet úložiště zadaný na předchozím řádku.
   * Vyhledejte nastavení **pfMobile App Web Service_pfwssdk_PfWsSdk** a změňte jeho hodnotu z **http://localhost:4898/PfWsSdk.asmx** na adresu URL sady SDK webové služby (příklad: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx).
   * Uložte soubor Web.Config a zavřete Poznámkový blok.

   > [!NOTE]
   > Vzhledem k tomu, že se pro toto připojení používá protokol SSL, je třeba na sadu SDK webové služby odkazovat pomocí **plně kvalifikovaného názvu domény**, a **ne pomocí IP adresy**. Certifikát SSL by byl vydán pro plně kvalifikovaný název domény a použitá adresa URL se musí shodovat s názvem na certifikátu.

7. Pokud web, na který byla nainstalována webová služba mobilní aplikace, nebyl dosud navázaný na veřejně podepsaný certifikát, nainstalujte certifikát na server, otevřete Správce služby IIS a vytvořte vazbu certifikátu k webu.
8. Na libovolném počítači otevřete webový prohlížeč a přejděte na adresu URL, kam se nainstalovala webová služba mobilní aplikace (příklad: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService). Ujistěte se, že se nezobrazí žádná varování nebo chyby týkající se certifikátu.
9. Další informace o metodách dostupných v sadě SDK pro webové služby najdete v souboru nápovědy pro MFA Server.
10. Teď, když je instalována webová služba mobilní aplikace, musíte nakonfigurovat server Azure Multi-Factor Authentication pro práci s portálem.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurace serveru Azure Multi-Factor Authentication

1. V konzole Multi-Factor Authentication Serveru klikněte na ikonu User Portal. Pokud mají uživatelé povolení řídit své metody ověřování, zaškrtněte možnost **Mobilní aplikace** na kartě Nastavení v části **Povolit uživatelům výběr metody**. Bez povolení této funkce musí koncoví uživatelé pro dokončení aktivace mobilní aplikace kontaktovat podporu.
2. Zaškrtněte políčko **Povolit uživatelům aktivaci mobilní aplikace**.
3. Zaškrtněte políčko **Povolit zápis uživatele**.
4. Klikněte na ikonu **Mobilní aplikace**.
5. Pokud používáte verzi 8.0 nebo novější, následující krok přeskočte: Do pole **Adresa URL webové služby mobilní aplikace** zadejte adresu URL používanou s virtuálním adresářem vytvořeným při instalaci MultiFactorAuthenticationMobileAppWebServiceSetup64 (příklad: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/).
6. Do pole **Název účtu** vyplňte název společnosti nebo organizace, který se pro tento účet zobrazí v mobilní aplikaci.
   ![Konfigurace MFA Serveru – nastavení mobilní aplikace](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>Další kroky

- [Pokročilé scénáře se službou Azure Multi-Factor Authentication a sítěmi VPN třetích stran](multi-factor-authentication-advanced-vpn-configurations.md).
