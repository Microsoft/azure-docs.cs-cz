---
title: Doprovodné materiály zabezpečení pro ověřování Azure Multi-Factor Authentication
description: Tento dokument obsahuje pokyny k jeho použití Azure MFA s účty Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: f1df6544ea7ca5a0f84bfb164982d816633d7fbe
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054399"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Doprovodné materiály zabezpečení pro používání ověřování Azure Multi-Factor Authentication s účty služby Azure AD

Dvoustupňové ověření je upřednostňovanou volbou pro většinu organizací, které chcete vylepšit jejich procesu ověřování. Azure Multi-Factor Authentication (MFA) pomáhá společnostem vyhovuje jejich požadavkům na zabezpečení a dodržování předpisů poskytuje jednoduchou funkci přihlašování svým uživatelům. Tento článek obsahuje některé tipy, které byste měli zvážit při plánování pro přijetí Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Nasazení Azure MFA v cloudu

Existují dva způsoby, jak [povolit Azure MFA pro všechny uživatele](howto-mfa-getstarted.md).

* Kupte si licence pro jednotlivé uživatele (buď Azure MFA, Azure AD Premium nebo Enterprise Mobility + Security)
* Vytvoření poskytovatele Multi-Factor Auth a platit za uživatele nebo na ověřování

### <a name="licenses"></a>Licence
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Pokud máte Azure AD Premium nebo Enterprise Mobility + Security koupím, už máte Azure MFA. Vaše organizace není nutné žádné další rozšířit funkci dvoustupňové ověření pro všechny uživatele. Potřebujete pouze přiřadit licenci uživateli, a pak můžete zapnout vícefaktorové ověřování.

Při nastavování služby Multi-Factor Authentication, vezměte v úvahu následující tipy:

* Nevytvářejte poskytovatele služby Multi-Factor Auth na ověřování. Pokud tak učiníte, můžou být nakonec platíte za ověření požadavků od uživatelů, které už máte licence.
* Pokud nemáte k dispozici dostatek licencí pro vaše uživatele, můžete vytvořit poskytovatele Multi-Factor Auth jednotlivých uživatelů k pokrytí zbytku organizace. 
* Azure AD Connect je jenom nutné, pokud synchronizujete prostředí místní služby Active Directory s adresářem Azure AD. Pokud používáte adresář Azure AD, který není synchronizován s místní instancí Active Directory, není nutné Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Poskytovatel Multi-Factor Auth
![Poskytovatel Multi-Factor Auth](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Pokud nemáte licence, které zahrnují Azure MFA, pak můžete [vytvoření poskytovatele ověřování MFA](concept-mfa-authprovider.md).

Při vytváření poskytovatele vícefaktorového ověřování, budete muset vybrat adresář a zvážit následující podrobnosti:

* Nepotřebujete adresář Azure AD k vytvoření poskytovatele Multi-Factor Auth, ale získáte další funkce s jednou. Když přiřadíte zprostředkovatele ověřování s adresářem Azure AD jsou povoleny následující funkce:
  * Rozšíření dvoustupňové ověřování pro všechny uživatele
  * Nabízí globální správci dalších funkcí, jako jsou portál pro správu, vlastní přivítání a sestavy.
* Pokud synchronizujete prostředí místní služby Active Directory s adresářem Azure AD, musíte nástroj DirSync a AAD Sync. Pokud používáte adresář Azure AD, který není synchronizován s místní instancí Active Directory, není nutné DirSync a AAD Sync.
* Vyberte model spotřeby, která nejlépe vyhovuje vaší firmy. Jakmile vyberete model použití, nelze změnit. Jsou dva modely:
  * Podle ověřování: můžete poplatky za každé ověření. Tento model použijte, pokud chcete dvoustupňové ověření pro kohokoli, který přistupuje k určité aplikaci, ne pro konkrétní uživatele.
  * Za povoleného uživatele: vám poplatky za každý uživatel, který můžete povolit pro Azure MFA. Tento model použijte, pokud máte uživatelé s Azure AD Premium nebo Enterprise Mobility Suite licence a některé bez.

### <a name="supportability"></a>Možnosti podpory
Protože většina uživatelů jsou zvyklí používat jenom hesla pro ověření, je důležité, že vaše společnost informuje všem uživatelům týkající se tohoto procesu. Toto sledování může snížit pravděpodobnost, že uživatelé volat vaše oddělení technické podpory pro menší problémy související s MFA. Existují však některé scénáře, kde dočasným zákazem MFA je nezbytné. Chcete-li pochopit, jak zvládnout tyto scénáře pomocí následujících pokynů:

* Trénování pracovníky technické podpory pro zpracování scénářů, ve kterém uživatel nemůže přihlásit, protože na mobilní aplikace nebo telefon nepřijímá oznámení nebo telefonní hovor. Technická podpora můžete [povolit jednorázové přihlášení](howto-mfa-mfasettings.md#one-time-bypass) umožňuje uživatele jednorázově ověřit obejitím"" dvoustupňové ověřování. Jednorázové přihlášení je dočasné a vyprší po zadaném počtu sekund.
* Vezměte v úvahu [důvěryhodné IP adresy funkce](howto-mfa-mfasettings.md#trusted-ips) v Azure MFA jako způsob, jak minimalizovat dvoustupňové ověřování. Pomocí této funkce můžou správci tenanta spravované nebo federované obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují ze společnosti místní intranet. Funkce jsou dostupné pro klienty Azure AD, kteří mají licenci Azure AD Premium, Enterprise Mobility Suite nebo ověřování Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Osvědčené postupy pro místní nasazení
Pokud vaše společnost se rozhodla využít svou vlastní infrastrukturu na povolit vícefaktorové ověřování, pak budete muset [nasazení Azure Multi-Factor Authentication Server v místním](howto-mfaserver-deploy.md). Součásti serveru MFA můžete vidět v následujícím diagramu:

![Výchozí součásti serveru MFA: konzoly, synchronizační modul, portál pro správu, cloudovou službu](./media/multi-factor-authentication-security-best-practices/server.png) \*není nainstalovaný ve výchozím nastavení \** ale není povoleno ve výchozím nastavení nainstalovaná

Azure Multi-Factor Authentication Server můžete zabezpečit cloudové prostředky a místní prostředky pomocí federace. Musíte mít službu AD FS a jeho sdružených se službou vašeho tenanta Azure AD.
Při nastavování serveru Multi-Factor Authentication Server, vezměte v úvahu následující podrobnosti:

* Pokud jsou zabezpečení prostředků Azure AD pomocí služby Active Directory Federation Services (AD FS), pak první krok ověření se provádí místně pomocí služby AD FS. Druhý krok se provádí místně dodržením deklarace identity.
* Není nutné k instalaci Azure Multi-Factor Authentication Server federační server AD FS. Však musí být nainstalován adaptér ověřování službou Multi-Factor Authentication pro službu AD FS na Windows Server 2012 R2 běží služba AD FS. Můžete nainstalovat server na jiném počítači, dokud se jedná o podporovanou verzi a adaptér AD FS nainstalovat samostatně na federační server služby AD FS. 
* Průvodce instalací adaptéru Multi-Factor Authentication AD FS vytvoří skupinu zabezpečení s názvem PhoneFactor Admins ve službě Active Directory a potom do této skupiny přidá účet služby AD FS. Ověřte, že se v řadiči domény vytvořila skupina PhoneFactor Admins a mezi jejími členy je účet služby AD FS. Pokud to bude potřeba, přidejte účet služby AD FS do skupiny PhoneFactor Admins ve vašem řadiči domény ručně.

### <a name="user-portal"></a>Uživatelský portál
Portál user portal umožňuje samoobslužné funkce a poskytuje kompletní sadu funkcí správy uživatelů. Spuštění webu Internet Information Server (IIS). Pomocí následujících pokynů ke konfiguraci této součásti:

* Použití služby IIS 6 nebo vyšší.
* Instalace a registrace technologie ASP.NET v2.0.507207
* Ujistěte se, že tento server se dá nasadit v hraniční síti

### <a name="app-passwords"></a>Hesla aplikací
Pokud je vaše organizace Federovaná pro jednotné přihlašování s Azure AD a chcete používat Azure MFA, pak mějte na paměti následující:

* Heslo aplikace je ověřováno Azure AD a proto obchází federace. Federace se používá pouze při nastavování hesla aplikace.
* Hesla pro federované uživatele (SSO), jsou uložena v id organizace. Pokud uživatel odejde ze společnosti, musí tyto informace do id organizace pomocí nástroje DirSync. Zakázání/odstranění účtu může trvat až tři hodiny pro synchronizaci, což způsobuje zpoždění zakázání/odstranění hesla aplikace ve službě Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Žádné místní ověřování protokolování a auditování funkce je dostupná na hesla aplikací.
* Některé pokročilé architektury návrhy může vyžadovat při použití dvoustupňové ověření u klientů, v závislosti na tom, kde se ověřit pomocí kombinace organizační uživatelského jména a hesla aplikace. Pro klienty, kteří ověřování na základě místní infrastrukturu můžete využít organizační uživatelské jméno a heslo. Pro klienty, které se ověřují ve službě Azure AD by použít heslo aplikace.
* Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Pokud je potřeba povolit uživatelům vytvoření hesel aplikací, vyberte **povolit uživatelům vytvářet hesla aplikací pro přihlášení do neprohlížečových aplikací** možnost.

## <a name="additional-considerations"></a>Další informace
Pomocí tohoto seznamu další důležité informace a pokyny pro jednotlivé komponenty, která je nasazena v místním:

- Nastavení služby Azure Multi-Factor Authentication se službou [Active Directory Federation Services (AD FS)](multi-factor-authentication-get-started-adfs.md).
- Nastavení a konfigurace Azure MFA Serveru s [ověřováním pomocí protokolu RADIUS](howto-mfaserver-dir-radius.md).
- Nastavení a konfigurace Azure MFA serveru s [ověřování služby IIS](howto-mfaserver-iis.md).
- Nastavení a konfigurace Azure MFA serveru s [ověřování Windows](howto-mfaserver-windows.md).
- Nastavení a konfigurace Azure MFA serveru s [ověřování pomocí protokolu LDAP](howto-mfaserver-dir-ldap.md).
- Nastavení a konfigurace Azure MFA serveru s [Brána vzdálené plochy a Azure Multi-Factor Authentication Server pomocí protokolu RADIUS](howto-mfaserver-nps-rdg.md).
- Nastavení a konfiguraci synchronizace mezi Azure MFA serverem a [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
- [Nasazení webové služby mobilní aplikace Azure Multi-Factor Authentication Serveru](howto-mfaserver-deploy-mobileapp.md).
- [Pokročilé konfigurace sítě VPN s ověřováním Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) pro zařízení Cisco ASA, Citrix Netscaler a Juniper/Pulse Secure VPN pomocí protokolu LDAP nebo pomocí protokolu RADIUS.

## <a name="next-steps"></a>Další postup
Přestože tento článek popisuje některé osvědčené postupy pro Azure MFA, existují další prostředky, které můžete použít také při plánování nasazení MFA. Následující seznam obsahuje některé klíče články, které vám mohou pomoci během tohoto procesu:

* [Sestavy v Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Možnosti registrace dvoustupňové ověření](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Nejčastější dotazy k Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
