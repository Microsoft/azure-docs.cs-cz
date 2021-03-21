---
title: Povolení vzdáleného přístupu pro Power BI s využitím Azure Proxy aplikací služby AD
description: Obsahuje základní informace o tom, jak integrovat místní Power BI s využitím Azure Proxy aplikací služby AD.
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
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f0959ff51714e8a7e69f58a3e3abeab71e8359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259368"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Povolení vzdáleného přístupu k Power BI Mobile s využitím proxy aplikací služby Azure AD

Tento článek popisuje, jak pomocí služby Azure Proxy aplikací služby AD povolit, aby se mobilní aplikace Power BI připojovala Server sestav Power BI (PBIRS) a SQL Server Reporting Services (SSRS) 2016 a novější. Prostřednictvím této integrace můžou uživatelé mimo podnikovou síť přistupovat ke svým Power BIm sestavám z Power BI mobilní aplikace a chránit pomocí ověřování Azure AD. Tato ochrana zahrnuje [výhody zabezpečení](application-proxy-security.md#security-benefits) , jako je podmíněný přístup a Multi-Factor Authentication.

## <a name="prerequisites"></a>Předpoklady

Tento článek předpokládá, že jste již nasadili služby sestav a [povolili proxy aplikace](application-proxy-add-on-premises-application.md).

- Povolení proxy aplikací vyžaduje, abyste nainstalovali konektor na Windows Server a dokončili [požadavky](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) , aby konektor mohl komunikovat se službami Azure AD.
- Při publikování Power BI doporučujeme použít stejné interní a externí domény. Další informace o vlastních doménách najdete v tématu [práce s vlastními doménami v proxy aplikace](./application-proxy-configure-custom-domain.md).
- Tato integrace je k dispozici pro Power BI Mobile aplikace pro **iOS a Android** .

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurace omezeného delegování protokolu Kerberos (KCD)

U místních aplikací, které používají ověřování systému Windows, můžete dosáhnout jednotného přihlašování (SSO) pomocí ověřovacího protokolu Kerberos a funkce označované jako omezené delegování protokolu Kerberos (KCD). Když se nakonfiguruje, KCD umožňuje konektoru proxy aplikací získat pro uživatele token Windows, a to i v případě, že uživatel není přihlášený přímo k Windows. Další informace o KCD najdete v tématu [Přehled omezeného delegování Kerberos](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) a [vynucené delegování protokolu Kerberos pro jednotné přihlašování k vašim aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

Na straně služby Reporting Services není potřeba prakticky žádná konfigurace. Stačí, abyste měli platný hlavní název služby (SPN), aby mohlo probíhat správné ověřování protokolem Kerberos. Také se ujistěte, že je server služby Reporting Services povolen pro ověřování Negotiate.

Pokud chcete nastavit KCD pro službu Reporting Services, pokračujte podle následujících pokynů.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurace hlavního názvu služby (SPN)

Hlavní název služby je jedinečný identifikátor pro službu, která používá ověřování protokolem Kerberos. Budete se muset ujistit, že máte k dispozici správný hlavní název služby HTTP pro váš server sestav. Informace o tom, jak nakonfigurovat správný hlavní název služby (SPN) pro server sestav, najdete v tématu o tom, [jak zaregistrovat hlavní název služby (SPN) pro server sestav](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
, Zda byl hlavní název služby (SPN) přidán, můžete ověřit spuštěním příkazu setspn s parametrem-L. Další informace o tomto příkazu najdete v tématu [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Povolit ověřování Negotiate

Pokud chcete povolit, aby server sestav používal ověřování pomocí protokolu Kerberos, nakonfigurujte typ ověřování serveru sestav tak, aby byl RSWindowsNegotiate. Nakonfigurujte toto nastavení pomocí souboru rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Další informace najdete v tématu o [úpravách konfiguračního souboru služby Reporting Services](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) a o [konfiguraci ověřování systému Windows na serveru sestav](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Zajistěte, aby byl konektor důvěryhodný pro delegování do hlavního názvu služby (SPN) přidaný do účtu fondu aplikací služby Reporting Services.
Nakonfigurujte KCD, aby služba Azure Proxy aplikací služby AD mohla delegovat identity uživatelů na účet fondu aplikací služby Reporting Services. Nakonfigurujte KCD tak, že povolíte, aby konektor proxy aplikací mohl načíst lístky Kerberos pro uživatele, kteří byli ověřeni ve službě Azure Active Directory. Pak tento server předá v tomto případě kontext cílové aplikaci nebo služby Reporting Services.

Pokud chcete nakonfigurovat KCD, opakujte následující kroky pro každý počítač konektoru:

1. Přihlaste se k řadiči domény jako správce domény a pak otevřete položku **Uživatelé a počítače služby Active Directory**.
2. Vyhledejte počítač, na kterém konektor běží.
3. Poklikejte na počítač a vyberte kartu **Delegování**.
4. Nastavte delegování na **Důvěřovat tomuto počítači pro delegování pouze určeným službám**. Pak vyberte **použít libovolný protokol pro ověřování**.
5. Vyberte **Přidat** a pak vyberte **Uživatelé nebo počítače**.
6. Zadejte účet služby, který používáte pro službu Reporting Services. Jedná se o účet, ke kterému jste při konfiguraci služby Reporting Service přidali SPN.
7. Klikněte na **OK**. Klikněte znovu na tlačítko **OK** a uložte změny.

Další informace najdete v tématu [omezené delegování protokolu Kerberos pro jednotné přihlašování k vašim aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Krok 2: publikování služeb sestav prostřednictvím Azure Proxy aplikací služby AD

Teď jste připraveni nakonfigurovat proxy aplikací služby Azure Active Directory.

1. Publikujte služby sestav prostřednictvím proxy aplikací s následujícím nastavením. Podrobné pokyny k postupu publikování aplikace prostřednictvím proxy aplikací najdete v tématu [Publikování aplikací pomocí proxy aplikací služby Azure Active Directory](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Interní adresa URL**: zadejte adresu URL serveru sestav, ke kterému konektor může získat přístup v podnikové síti. Ujistěte se, že je tato adresa URL dosažitelná ze serveru, na kterém je konektor nainstalovaný. Osvědčeným postupem je použití domény nejvyšší úrovně, jako je například `https://servername/`, aby se předešlo problémům s dílčími cestami publikovanými prostřednictvím proxy aplikací. Použijte například `https://servername/` a ne `https://servername/reports/` nebo `https://servername/reportserver/`.
     > [!NOTE]
     > Doporučujeme použít zabezpečené připojení HTTPS k serveru sestav. Informace o postupu najdete v tématu [Konfigurace připojení protokolem SSL na server sestav v nativním režimu](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017).
   - **Externí adresa URL**: zadejte veřejnou adresu URL, ke které se Power BI mobilní aplikace připojí. Pokud se například používá vlastní doména, může vypadat jako `https://reports.contoso.com`. Pokud chcete použít vlastní doménu, nahrajte certifikát pro doménu a nasměrujte záznam DNS na výchozí doménu msappproxy.net pro vaši aplikaci. Podrobný postup najdete v tématu [Práce s vlastními doménami v proxy aplikací služby Azure Active Directory](application-proxy-configure-custom-domain.md).

   - **Metoda** předběžného ověřování: Azure Active Directory

2. Po publikování aplikace nakonfigurujte nastavení jednotného přihlašování podle následujících kroků:

   a. Na stránce aplikace na portálu vyberte **Jednotné přihlašování**.

   b. Pro **Režim jednotného přihlašování** vyberte **Integrované ověřování systému Windows**.

   c. Nastavte **Vnitřní hlavní název služby (SPN) aplikace** na hodnotu, kterou jste nastavili dříve.

   d. Vyberte **Delegovanou identitu přihlášení**, kterou má konektor používat jménem uživatelů. Další informace najdete v tématu [Práce s různými místními a cloudovými identitami](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Kliknutím na **Uložit** změny uložte.

Pokud chcete dokončit nastavování aplikace, přejděte do části **Uživatelé a skupiny** a přiřaďte uživatele k přístupu k této aplikaci.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Krok 3: Úprava identifikátoru URI odpovědi pro aplikaci

Předtím, než se může mobilní aplikace Power BI připojit a získat přístup ke službám sestav, musíte nakonfigurovat registraci aplikace, která byla vytvořena automaticky v kroku 2.

1. Na stránce **Přehled** v Azure Active Directory vyberte **Registrace aplikací**.
2. Na kartě **všechny aplikace** vyhledejte aplikaci, kterou jste vytvořili v kroku 2.
3. Vyberte aplikaci a pak vyberte **Ověřování**.
4. Přidejte následující identifikátory URI přesměrování podle toho, jakou používáte platformu.

   Při konfiguraci aplikace pro Power BI Mobile **iOS** přidejte následující identifikátory URI pro přesměrování typu Public Client (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Při konfiguraci aplikace pro Power BI Mobile **Android** přidejte následující identifikátory URI pro přesměrování typu Public Client (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Aby aplikace fungovala správně, musí být přidány identifikátory URI pro přesměrování. Pokud konfigurujete aplikaci pro Power BI Mobile iOS i Android, přidejte následující identifikátor URI přesměrování typu Public Client (mobilní & Desktop) do seznamu identifikátorů URI pro přesměrování nakonfigurovaných pro iOS: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Krok 4: připojení z aplikace Power BI Mobile

1. V mobilní aplikaci Power BI se připojte ke své instanci služby Reporting Services. Provedete to tak, že zadáte **externí adresu URL** pro aplikaci, kterou jste publikovali prostřednictvím proxy aplikace.

   ![Mobilní aplikace Power BI s externí adresou URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Vyberte **Connect** (Připojit). Budete přesměrováni na přihlašovací stránku Azure Active Directory.

3. Zadejte platné přihlašovací údaje pro daného uživatele a vyberte **Přihlásit se**. Uvidíte prvky ze serveru služby Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Krok 5: Konfigurace zásad Intune pro spravovaná zařízení (volitelné)

Microsoft Intune můžete použít ke správě klientských aplikací, které používají zaměstnanci vaší společnosti. Intune umožňuje používat funkce, jako je šifrování dat a další požadavky na přístup. Další informace o správě aplikací přes Intune najdete v tématu Správa aplikací Intune. Pokud chcete povolit spolupráci Power BI mobilní aplikace se zásadami Intune, použijte následující postup.

1. Přejít na **Azure Active Directory** a pak na **Registrace aplikací**.
2. Vyberte aplikaci nakonfigurovanou v kroku 3 při registraci nativní klientské aplikace.
3. Na stránce aplikace vyberte **oprávnění rozhraní API**.
4. Klikněte na **Přidat oprávnění**.
5. V části **rozhraní API, které používá moje organizace**, vyhledejte "Správa mobilních aplikací Microsoftu" a vyberte ji.
6. Přidání oprávnění **DeviceManagementManagedApps.** zápisu do aplikace
7. Kliknutím na **udělit souhlas správce** udělte přístup k aplikaci.
8. Nakonfigurujte požadované zásady Intune tak, že se rozhodnete, [jak vytvořit a přiřadit zásady ochrany aplikací](/intune/app-protection-policies).

## <a name="troubleshooting"></a>Poradce při potížích

Pokud aplikace vrátí chybovou stránku po pokusu o načtení sestavy déle než několik minut, může být nutné změnit nastavení časového limitu. Ve výchozím nastavení podporuje proxy aplikací aplikace, které na žádost zabírají až 85 sekund. **Pokud** chcete toto nastavení prodloužit na 180 sekund, vyberte časový limit back-endu na stránce nastavení proxy aplikace pro danou aplikaci. Tipy k vytváření rychlých a spolehlivých sestav najdete v tématu [Power BI osvědčené postupy](/power-bi/power-bi-reports-performance).

Pomocí služby Azure Proxy aplikací služby AD povolíte, aby se mobilní aplikace Power BI připojovala k místním Server sestav Power BI není podporovaná pro zásady podmíněného přístupu, které vyžadují aplikaci Microsoft Power BI jako schválenou klientskou aplikaci.

## <a name="next-steps"></a>Další kroky

- [Povolit nativním klientským aplikacím pracovat s proxy aplikacemi](application-proxy-configure-native-client-application.md)
- [Zobrazení místních sestav serveru sestav a klíčových ukazatelů výkonu v mobilních aplikacích Power BI](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
