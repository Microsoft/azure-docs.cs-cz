---
title: Doprovodné materiály k zabezpečení pro Azure Multi-Factor Authentication – Azure Active Directory
description: Tento dokument poskytuje pokyny k používání Azure MFA s účty Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e42234e9fcdcfe3ee5ce975babbe03b64a750e36
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846823"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Pokyny k zabezpečení při používání Azure Multi-Factor Authentication s účty Azure AD

Dvoustupňové ověřování je upřednostňovanou volbou pro většinu organizací, které chtějí zlepšit proces ověřování. Azure Multi-Factor Authentication (MFA) pomáhá společnostem splnit požadavky na zabezpečení a dodržování předpisů a přitom zajistit jednoduché přihlášení pro své uživatele. Tento článek popisuje několik tipů, které byste měli vzít v úvahu při plánování přijetí Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Nasazení Azure MFA v cloudu

Existují dva způsoby, jak [Povolit Azure MFA pro všechny uživatele](howto-mfa-getstarted.md).

* Koupit licence pro každého uživatele (Azure MFA, Azure AD Premium nebo Enterprise Mobility + Security)
* Vytvoření poskytovatele vícefaktorového ověřování a platby za jednotlivé uživatele nebo ověřování

### <a name="licenses"></a>Licence

![Použití licencí pro uživatele, povolení, oznamování](./media/multi-factor-authentication-security-best-practices/ems.png)

Pokud máte licence Azure AD Premium nebo Enterprise Mobility + Security, již máte Azure MFA. Vaše organizace nepotřebuje žádné další, aby rozšířila možnosti dvoustupňové ověřování pro všechny uživatele. K uživateli stačí přiřadit licenci a pak můžete zapnout vícefaktorové ověřování.

Při nastavování Multi-Factor Authentication Vezměte v úvahu následující tipy:

* Nevytvářejte poskytovatele Multi-Factor auth pro ověřování. Pokud tak učiníte, můžete platit za žádosti o ověření od uživatelů, kteří už mají licence.
* Pokud nemáte dostatečná licence pro všechny uživatele, můžete vytvořit poskytovatele vícefaktorového ověřování pro každého uživatele, který bude pokrývat zbytek vaší organizace. 
* Azure AD Connect se vyžaduje jenom v případě, že synchronizujete místní prostředí Active Directory s adresářem Azure AD. Pokud použijete adresář služby Azure AD, který není synchronizovaný s místní instancí služby Active Directory, nepotřebujete Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Poskytovatel Multi-Factor auth

![Poskytovatel Multi-Factor Authentication](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Pokud nemáte licence, které zahrnují Azure MFA, můžete [vytvořit poskytovatele vícefaktorového ověřování](concept-mfa-authprovider.md).

Při vytváření poskytovatele ověřování je nutné vybrat adresář a vzít v úvahu následující podrobnosti:

* K vytvoření poskytovatele vícefaktorového ověřování nepotřebujete adresář Azure AD, ale získáte více funkcí s jedním. Při přidružení poskytovatele ověřování k adresáři služby Azure AD jsou povoleny následující funkce:
  * Rozšířené ověřování dvou kroků pro všechny uživatele
  * Poskytněte globálním správcům další funkce, jako je portál pro správu, vlastní pozdravy a sestavy.
* Pokud synchronizujete místní prostředí Active Directory s adresářem služby Azure AD, budete potřebovat DirSync nebo Azure AD Sync. Pokud použijete adresář služby Azure AD, který není synchronizovaný s místní instancí Active Directory, nepotřebujete DirSync nebo Azure AD Sync.
* Vyberte model spotřeby, který nejlépe vyhovuje vašemu podniku. Jakmile vyberete model využití, nemůžete ho změnit. Dva modely:
  * Za ověřování: účtuje se za každé ověření. Tento model použijte, pokud chcete, aby dvoustupňové ověřování pro kohokoli, kdo přistupuje k určité aplikaci, ne pro konkrétní uživatele.
  * Na povolené uživatele: účtuje se za každého uživatele, který povolíte pro Azure MFA. Tento model použijte, pokud máte nějaké uživatele s Azure AD Premium nebo licencemi Enterprise Mobility Suite a bez.

### <a name="supportability"></a>Možnosti podpory

Vzhledem k tomu, že většina uživatelů je zvyklá používat k ověřování jenom hesla, je důležité, aby vaše společnost pocházela s vědomím pro všechny uživatele týkající se tohoto procesu. Toto povědomí může snížit pravděpodobnost, že uživatelé volají vaši technickou podporu s menšími problémy souvisejícími s MFA. Existují však situace, kdy je nutné dočasně zakázat MFA. Následující pokyny vám pomohou pochopit, jak tyto scénáře zvládnout:

* Školením pracovníků technické podpory můžete zvládnout scénáře, kdy se uživatel nemůže přihlásit, protože mobilní aplikace nebo telefon nepřijímá oznámení nebo telefonní hovor. Technická podpora může [umožnit jednorázové](howto-mfa-mfasettings.md#one-time-bypass) přihlášení, které umožňuje uživateli ověřování jednorázovým ověřováním pomocí dvou kroků. Vynechání je dočasné a po zadaném počtu sekund vyprší platnost.
* Vezměte v úvahu [schopnost důvěryhodných IP adres](howto-mfa-mfasettings.md#trusted-ips) v Azure MFA jako způsob minimalizace dvoustupňového ověřování. Díky této funkci můžou správci spravovaného nebo federovaného tenanta obejít dvoustupňové ověřování pro uživatele, kteří se přihlásí z místního intranetu společnosti. Tyto funkce jsou k dispozici pro klienty Azure AD, kteří mají Azure AD Premium, Enterprise Mobility Suite nebo Azure Multi-Factor Authentication licence.

## <a name="best-practices-for-an-on-premises-deployment"></a>Osvědčené postupy pro místní nasazení

Pokud se vaše společnost rozhodla využít svoji vlastní infrastrukturu pro povolení vícefaktorového ověřování, musíte [nasadit Azure Multi-Factor Authentication Server místně](howto-mfaserver-deploy.md). Komponenty MFA serveru jsou uvedené v následujícím diagramu:

![výchozí součásti MFA serveru](./media/multi-factor-authentication-security-best-practices/server.png) \*nejsou ve výchozím nastavení nainstalované \** nainstalované, ale nejsou ve výchozím nastavení povolené.

Azure Multi-Factor Authentication Server může zabezpečit cloudové prostředky a místní prostředky pomocí federace. Musíte mít AD FS a musí být federované s vaším klientem služby Azure AD.
Při nastavování Multi-Factor Authentication Server Vezměte v úvahu následující podrobnosti:

* Pokud zabezpečujete prostředky Azure AD pomocí Active Directory Federation Services (AD FS) (AD FS), pak se první krok ověření provádí místně pomocí AD FS. Druhý krok se provádí místně dodržením deklarace identity.
* Nemusíte instalovat Azure Multi-Factor Authentication Server AD FS federačního serveru. Multi-Factor Authentication adaptér pro AD FS ale musí být nainstalovaný na Windows Serveru 2012 R2 se spuštěným AD FS. Server můžete nainstalovat na jiný počítač, pokud se jedná o podporovanou verzi, a nainstalovat adaptér AD FS samostatně na AD FS federačního serveru. 
* Průvodce instalací adaptéru Multi-Factor Authentication AD FS vytvoří ve službě Active Directory skupinu zabezpečení nazvanou PhoneFactor Admins a potom do této skupiny přidá účet služby AD FS. Ověřte, že se v řadiči domény vytvořila skupina PhoneFactor Admins a mezi jejími členy je účet služby AD FS. Pokud to bude potřeba, přidejte účet služby AD FS do skupiny PhoneFactor Admins ve vašem řadiči domény ručně.

### <a name="user-portal"></a>Uživatelský portál

User Portal umožňuje samoobslužné funkce a poskytuje úplnou sadu funkcí pro správu uživatelů. Spouští se na webu Internet Information Server (IIS). Ke konfiguraci této součásti použijte následující pokyny:

* Použití IIS 6 nebo vyšší
* Instalace a registrace ASP.NET v 2.0.507207
* Zajistěte, aby tento server bylo možné nasadit v hraniční síti.

### <a name="app-passwords"></a>Hesla aplikací

Pokud je vaše organizace federované pro jednotné přihlašování pomocí Azure AD a budete používat Azure MFA, pamatujte na tyto podrobnosti:

* Heslo aplikace ověřuje služba Azure AD, takže se federace vynechá. Federace se používá jenom při nastavování hesel aplikací.
* Pro uživatele federovaného (SSO) se hesla ukládají do ID organizace. Pokud uživatel odejde ze společnosti, musí tyto informace nasměrovat do ID organizace pomocí DirSync. Vypnutí nebo odstranění účtu může trvat až tři hodiny, než se synchronizuje, což zpozdí zakázání a odstranění hesel aplikací v Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Pro hesla aplikací nejsou k dispozici žádné místní možnosti protokolování a auditování ověřování.
* Některé pokročilé návrhy architektury mohou vyžadovat použití kombinace uživatelského jména a hesla organizace a hesla aplikací při použití dvoustupňového ověřování u klientů v závislosti na tom, kde jsou ověřovány. U klientů, kteří se ověřují s místní infrastrukturou, byste měli použít uživatelské jméno a heslo organizace. Pro klienty, kteří se ověřují v Azure AD, byste použili heslo aplikace.
* Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Pokud potřebujete, aby uživatelé mohli vytvářet hesla aplikací, vyberte možnost **dovolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** .

## <a name="additional-considerations"></a>Další oblasti, které je třeba vzít v úvahu

V tomto seznamu najdete další informace a pokyny pro jednotlivé komponenty, které jsou nasazené místně:

* Nastavení služby Azure Multi-Factor Authentication se službou [Active Directory Federation Services (AD FS)](multi-factor-authentication-get-started-adfs.md).
* Nastavení a konfigurace Azure MFA Serveru s [ověřováním pomocí protokolu RADIUS](howto-mfaserver-dir-radius.md).
* Nastavte a nakonfigurujte Azure MFA Server s [ověřováním IIS](howto-mfaserver-iis.md).
* Nastavení a konfigurace Azure MFA serveru s [ověřováním systému Windows](howto-mfaserver-windows.md).
* Nastavení a konfigurace Azure MFA serveru s [ověřováním pomocí protokolu LDAP](howto-mfaserver-dir-ldap.md).
* Nastavte a nakonfigurujte Azure MFA Server pomocí [Brána vzdálené plochy a azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md).
* Nastavte a nakonfigurujte synchronizaci mezi Azure MFA serverem a [službou Windows Server Active Directory](howto-mfaserver-dir-ad.md).
* [Nasazení webové služby mobilní aplikace Azure Multi-Factor Authentication Serveru](howto-mfaserver-deploy-mobileapp.md).
* [Pokročilá konfigurace sítě VPN s využitím Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) pro zařízení VPN Cisco ASA, Citrix NetScaler a Juniper/Pulse Secure VPN pomocí protokolu LDAP nebo RADIUS.

## <a name="next-steps"></a>Další kroky

I když tento článek popisuje některé osvědčené postupy pro Azure MFA, existují další prostředky, které můžete využít i při plánování nasazení MFA. Následující seznam obsahuje některé klíčové články, které vám můžou pomoct během tohoto procesu:

* [Sestavy v Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Prostředí pro registraci dvou kroků při ověřování](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Nejčastější dotazy k Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
