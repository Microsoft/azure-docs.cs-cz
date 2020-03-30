---
title: Povolení vzdáleného přístupu k Power BI pomocí proxy aplikací Azure AD
description: Popisuje základní informace o tom, jak integrovat místní Power BI s proxy aplikací Azure AD.
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111588"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Povolení vzdáleného přístupu k Power BI Mobile s využitím proxy aplikací služby Azure AD

Tento článek popisuje, jak pomocí proxy aplikace Azure AD povolit mobilní aplikaci Power BI pro připojení k Serveru sestav Power BI (PBIRS) a SQL Server Reporting Services (SSRS) 2016 a novější. Díky této integraci mají uživatelé, kteří jsou mimo podnikovou síť, přístup ke svým sestavnám Power BI z mobilní aplikace Power BI a budou chráněni ověřováním Azure AD. Tato ochrana zahrnuje [výhody zabezpečení,](application-proxy-security.md#security-benefits) jako je podmíněný přístup a vícefaktorové ověřování.  

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nasadili službu Report Services a [povolili proxy aplikace](application-proxy-add-on-premises-application.md).

- Povolení proxy aplikace vyžaduje instalaci konektoru na serveru windows a dokončení [předpokladů](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) tak, aby konektor může komunikovat se službami Azure AD.  
- Při publikování Power BI doporučujeme používat stejné interní a externí domény. Další informace o vlastních doménách najdete [v tématu Práce s vlastními doménami v proxy aplikaci](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Tato integrace je dostupná pro aplikaci **Power BI Mobile pro iOS a Android.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Krok 1: Konfigurace delegování omezeného protokolu Kerberos (KCD)

U místních aplikací, které používají ověřování systému Windows, můžete dosáhnout jednotného přihlašování (SSO) pomocí ověřovacího protokolu Kerberos a funkce s názvem Kerberos constrained delegation (KCD). Při konfiguraci KCD umožňuje konektoru proxy aplikace získat token systému Windows pro uživatele, i když uživatel nebyl přihlášen přímo do systému Windows. Další informace o kcd, najdete [v tématu Kerberos omezené delegování přehled](https://technet.microsoft.com/library/jj553400.aspx) a [Kerberos omezené delegování pro jednotné přihlašování k aplikacím s proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

Na straně služby Reporting Services není potřeba prakticky žádná konfigurace. Ujistěte se, že máte platný hlavní název služby (SPN), abyste povolili správné ověřování protokolem Kerberos. Také se ujistěte, že server Reporting Services je povolen pro vyjednávání ověřování.

Chcete-li nastavit KCD pro služby reporting, pokračujte následujícími kroky.

### <a name="configure-the-service-principal-name-spn"></a>Konfigurace hlavního názvu služby (SPN)

Hlavní název služby je jedinečný identifikátor pro službu, která používá ověřování protokolem Kerberos. Budete se muset ujistit, že máte pro server sestav k dispozici správný název SLUŽBY HTTP. Informace o tom, jak nakonfigurovat správný hlavní název služby (SPN) pro server sestav, najdete v tématu o tom, [jak zaregistrovat hlavní název služby (SPN) pro server sestav](https://msdn.microsoft.com/library/cc281382.aspx).
Můžete ověřit, že hlavní aktualizace služeb pro zvýšení stavu služeb byly přidány spuštěním příkazu Setspn s volbou -L. Další informace o tomto příkazu naleznete v [tématu Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Povolit vyjednávání ověřování

Chcete-li povolit serveru sestav použití ověřování protokolem Kerberos, nakonfigurujte typ ověřování serveru sestav na RSWindowsNegotiate. Toto nastavení nakonfigurujte pomocí souboru rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Další informace najdete v tématu o [úpravách konfiguračního souboru služby Reporting Services](https://msdn.microsoft.com/library/bb630448.aspx) a o [konfiguraci ověřování systému Windows na serveru sestav](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Ujistěte se, že konektor je důvěryhodný pro delegování do hlavního okruhu spn přidaného k účtu fondu aplikací služby Reporting Services
Nakonfigurujte kcd tak, aby služba Proxy aplikací Azure AD mohla delegovat identity uživatelů na účet fondu aplikací služby Reporting Services. Nakonfigurujte KCD povolením konektoru proxy aplikace k načtení lístků protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Potom tento server předá kontext cílové aplikace nebo Reporting Services v tomto případě.

Chcete-li konfigurovat disk KCD, opakujte následující kroky pro každý konektorový počítač:

1. Přihlaste se k řadiči domény jako správce domény a spusťte **službu Uživatelé a počítače služby Active Directory**.
2. Najděte počítač, ve který je konektor spuštěn.  
3. Poklepejte na počítač a vyberte kartu **Delegování.**
4. Nastavte nastavení delegování **na: Důvěřovat tomuto počítači pouze pro delegování na určené služby**. Potom vyberte **Použít libovolný ověřovací protokol**.
5. Vyberte **Přidat**a pak vyberte **Uživatelé nebo Počítače**.
6. Zadejte účet služby, který používáte pro službu Reporting Services. Jedná se o účet, ke kterému jste při konfiguraci služby Reporting Service přidali SPN.
7. Klikněte na tlačítko **OK**. Chcete-li změny uložit, klepněte znovu na **tlačítko OK.**

Další informace naleznete v [tématu Kerberos Constrained Delegation for single sign-on your apps with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Krok 2: Publikování služby sestav prostřednictvím proxy aplikace Azure AD

Teď jste připraveni nakonfigurovat proxy aplikace Azure AD.

1. Publikovat služby Sestav prostřednictvím proxy aplikace s následujícím nastavením. Podrobné pokyny k publikování aplikace prostřednictvím proxy aplikace najdete v tématu [Publikování aplikací pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Interní adresa URL**: Zadejte adresu URL serveru sestav, ke které může konektor dosáhnout v podnikové síti. Ujistěte se, že tato adresa URL je dostupná ze serveru, na který je konektor nainstalován. Osvědčeným postupem je použití domény `https://servername/` nejvyšší úrovně, například k zabránění problémům s podcestami publikovanými prostřednictvím proxy aplikace. Například použití `https://servername/` a `https://servername/reports/` `https://servername/reportserver/`ne nebo .
     > [!NOTE]
     > Doporučujeme používat zabezpečené připojení HTTPS k serveru sestav. Informace o tom, jak [naserveru sestav nativního režimu naleznete v tématu Konfigurace připojení SSL na serveru sestav nativním režimu.](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)
   - **Externí adresa URL**: Zadejte veřejnou adresu URL, ke které se připojí mobilní aplikace Power BI. Může například vypadat, `https://reports.contoso.com` jako by se používala vlastní doména. Chcete-li použít vlastní doménu, nahrajte certifikát pro doménu a namiřte záznam DNS na výchozí doménu msappproxy.net pro vaši aplikaci. Podrobné kroky najdete [v tématu Práce s vlastními doménami v proxy aplikací Azure AD](application-proxy-configure-custom-domain.md).

   - **Metoda předběžného ověření:** Služba Azure Active Directory

2. Po publikování aplikace nakonfigurujte nastavení jednotného přihlašování pomocí následujících kroků:

   a. Na stránce aplikace na portálu vyberte **Možnost Jednotné přihlašování**.

   b. V **režimu jednotného přihlašování**vyberte **možnost Integrované ověřování systému Windows**.

   c. Nastavte **hlavní název služby interní aplikace** na hodnotu, kterou jste nastavili dříve.  

   d. Zvolte **delegované přihlašovací identity** pro konektor použít jménem vašich uživatelů. Další informace najdete [v tématu Práce s různými místními a cloudovými identitami](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Kliknutím na **Uložit** uložte změny.

Chcete-li dokončit nastavení aplikace, přejděte do části **Uživatelé a skupiny** a přiřaďte uživatelům přístup k této aplikaci.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Krok 3: Úprava identifikátorů URI pro odpověď pro aplikaci

Než se mobilní aplikace Power BI bude moct připojit a získat přístup ke Službám sestav, musíte nakonfigurovat registraci aplikace, která byla automaticky vytvořena v kroku 2. 

1. Na stránce **Přehled** služby Azure Active Directory vyberte **Registrace aplikací**.
2. Na kartě **Všechny aplikace** vyhledejte aplikaci, kterou jste vytvořili v kroku 2.
3. Vyberte aplikaci a pak vyberte **Ověřování**.
4. Přidejte následující identifikátory URI přesměrování na základě platformy, kterou používáte.

   Při konfiguraci aplikace pro Power BI Mobile **iOS**přidejte následující identifikátory URI přesměrování typu Veřejný klient (mobilní & desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Při konfiguraci aplikace pro Power BI Mobile **Android**přidejte následující identifikátory URI přesměrování typu Veřejný klient (Mobilní & desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Aby aplikace fungovala správně, je nutné přidat identifikátory URI přesměrování. Pokud konfigurujete aplikaci pro Power BI Mobile iOS i Android, přidejte do seznamu identifikátorů URI přesměrování nakonfigurovaných pro iOS následující identifikátor URI přesměrování typu Veřejný klient (Mobilní & desktop): `urn:ietf:wg:oauth:2.0:oob`.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Krok 4: Připojení z mobilní aplikace Power BI

1. V mobilní aplikaci Power BI se připojte k instanci Reporting Services. Chcete-li to provést, zadejte **externí adresu URL** aplikace, kterou jste publikovali prostřednictvím proxy aplikace.

   ![Mobilní aplikace Power BI s externí adresou URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Vyberte **Connect** (Připojit). Budete přesměrováni na přihlašovací stránku služby Azure Active Directory.

3. Zadejte platná pověření uživatele a **vyberte Přihlásit se**. Zobrazí se prvky ze serveru Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Krok 5: Konfigurace zásad Intune pro spravovaná zařízení (volitelně)

Microsoft Intune můžete použít ke správě klientských aplikací, které zaměstnanci vaší společnosti používají. Intune umožňuje používat funkce, jako je šifrování dat a další požadavky na přístup. Další informace o správě aplikací prostřednictvím Intune najdete v tématu Správa aplikací intune. Pokud chcete mobilní aplikaci Power BI povolit práci se zásadami Intune, postupujte takto.

1. Přejděte na **Azure Active Directory** a potom na Registrace **aplikací**.
2. Při registraci nativní klientské aplikace vyberte aplikaci nakonfigurovanou v kroku 3.
3. Na stránce aplikace vyberte **oprávnění rozhraní API**.
4. Klepněte **na tlačítko Přidat oprávnění**. 
5. V části **Rozhraní API, která moje organizace používá**, vyhledejte výraz "Microsoft Mobile Application Management" a vyberte ji.
6. Přidání oprávnění **DeviceManagementManagedApps.ReadWrite** do aplikace
7. Chcete-li aplikaci udělit oprávnění k udělení oprávnění, klepněte na **tlačítko Udělit souhlas správce.**
8. Nakonfigurujte požadované zásady Intune odkazem na [Jak vytvořit a přiřadit zásady ochrany aplikací](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Řešení potíží

Pokud aplikace vrátí chybovou stránku po pokusu o načtení sestavy po dobu delší než několik minut, bude pravděpodobně nutné změnit nastavení časového času. Ve výchozím nastavení podporuje proxy aplikace aplikace, které trvá až 85 sekund reagovat na požadavek. Chcete-li toto nastavení prodloužit na 180 sekund, vyberte časový rozsah back-end na **dlouhý** na stránce Nastavení proxy aplikace pro aplikaci. Tipy, jak vytvářet rychlé a spolehlivé sestavy, najdete v [tématu Doporučené postupy pro sestavy Power BI](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Další kroky

- [Povolení interakce s aplikacemi proxy nativními klientskými aplikacemi](application-proxy-configure-native-client-application.md)
- [Zobrazení místních sestav serveru sestav a klíčových ukazatelů výkonu v mobilních aplikacích Power BI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
