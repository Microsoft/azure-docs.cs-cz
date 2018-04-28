---
title: Osvědčené postupy zabezpečení pro vícefaktorového ověřování | Microsoft Docs
description: Tento dokument obsahuje osvědčené postupy kolem účty Azure pomocí Azure MFA
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 0fd90c4e59fa64c24ecfa6d7d8f23e025210e078
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Osvědčené postupy zabezpečení pro používání ověřování Azure Multi-Factor Authentication s účty Azure AD

Dvoustupňové ověření je upřednostňovanou volbou pro většinu organizací, které chcete zvýšit jejich procesu ověřování. Azure Multi-Factor Authentication (MFA) pomáhá společností splňují požadavky na jejich zabezpečení a dodržování předpisů při současném poskytování jednoduché prostředí přihlašování pro své uživatele. Tento článek se zabývá některé tipy, které byste měli zvážit při plánování pro přijetí Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Nasazení Azure MFA v cloudu

Existují dva způsoby, jak povolit Azure MFA pro všechny uživatele.

* Zakoupit licence pro každého uživatele (buď Azure MFA, Azure AD Premium nebo Enterprise Mobility + Security)
* Vytvoření poskytovatele Multi-Factor Auth a platím na uživatele nebo podle ověření

### <a name="licenses"></a>Licence
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Pokud máte Azure AD Premium nebo Enterprise Mobility + Security licence, už máte Azure MFA. Vaše organizace nevyžaduje žádné další rozšířit možnosti dvoustupňové ověření pro všechny uživatele. Potřebujete přiřadit licence pro uživatele a pak můžete zapnout vícefaktorové ověřování.

Při nastavování služby Multi-Factor Authentication, vezměte v úvahu následující tipy:

* Nevytvářejte zprostředkovatel vícefaktorového ověřování na ověřování. V takovém případě může skončili platícího pro žádosti o ověření od uživatelů, které už máte licence.
* Pokud nemáte dost licencí pro všechny uživatele, můžete vytvořit zprostředkovatel vícefaktorového ověřování na uživatele tak, aby pokrývalo zbytek vaší organizace. 
* Azure AD Connect je jenom potřeba, pokud synchronizujete prostředí služby Active Directory místní adresář služby Azure AD. Pokud používáte adresář služby Azure AD, který není synchronizován s místní instancí Active Directory, není nutné Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Zprostředkovatel vícefaktorového ověřování
![Zprostředkovatel vícefaktorového ověřování](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Pokud nemáte licence, které zahrnují Azure MFA, můžete vytvořit poskytovatele ověřování MFA. 

Při vytváření zprostředkovatele ověřování, potřebujete vyberte adresář a zvažte následující podrobnosti:

* Adresář služby Azure AD k vytvoření poskytovatele Multi-Factor Auth nepotřebujete, ale můžete získat další funkce s jednou. Když přiřadíte zprostředkovatel ověřování adresář služby Azure AD, jsou povoleny tyto funkce:  
  * Rozšíření dvoustupňové ověřování pro všechny uživatele  
  * Globální správci nabízí další funkce, jako je například portál pro správu, vlastní přivítání a sestav.
* Pokud synchronizujete prostředí služby Active Directory v místě s adresářem služby Azure AD, je třeba DirSync nebo AAD Sync. Pokud používáte adresář služby Azure AD, který není synchronizován s místní instancí Active Directory, není nutné DirSync nebo AAD Sync.
* Vyberte model spotřeby, který nejlépe vyhovuje vašim obchodním. Jakmile vyberete model využití, už nemůžete provést změnu. Dva modely jsou:
  * Za ověření: vám poplatky za každé ověření. Pokud chcete dvoustupňové ověřování pro každý uživatel, který přistupuje k určité aplikaci, ne pro konkrétní uživatele, použijte tento model.
  * Za povoleného uživatele: poplatky můžete pro každého uživatele, můžete povolit pro Azure MFA. Pokud máte někteří uživatelé s Azure AD Premium nebo Enterprise Mobility Suite licencí a některé bez použití tohoto modelu.

### <a name="supportability"></a>Možnosti podpory
Vzhledem k tomu, že většina uživatelů jsou zvykli na použití pouze hesel k ověření, je důležité, aby vaše společnost informuje pro všechny uživatele týkající se tohoto procesu. Toto sledování může snížit pravděpodobnost, že uživatelé volání vaše oddělení technické podpory pro menší problémy související s MFA. Existují však některých scénářích, kde je nutné dočasně zakázat MFA. Pomocí následujících pokynů pochopit, jak ke zpracování těchto scénářů:

* Cvičení pracovníci technické podpory pro zpracování scénáře, kde uživatel nemůže přihlásit, protože na mobilní aplikaci nebo telefon nepřijímá oznámení nebo telefonní hovor. Můžete se na technickou podporu [povolit jednorázové přihlášení](howto-mfa-mfasettings.md#one-time-bypass) chcete umožnit uživatelům jednorázově ověřit pomocí "obcházení" dvoustupňové ověřování. Toto jednorázové přihlášení je dočasné a vyprší po zadaném počtu sekund.
* Vezměte v úvahu [důvěryhodné IP adresy schopností](howto-mfa-mfasettings.md#trusted-ips) v Azure MFA jako způsob, jak minimalizovat dvoustupňové ověřování. Pomocí této funkce mohou správci klienta spravované nebo federované obejít dvoustupňové ověřování pro uživatele, kteří se přihlašují ze společnosti místní intranet. Funkce jsou dostupné pro klienty Azure AD, kteří mají licence Azure AD Premium, Enterprise Mobility Suite nebo Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Osvědčené postupy pro místní nasazení
Pokud vaše společnost se rozhodla využít svou vlastní infrastrukturu pro vícefaktorové ověřování zapnout, budete muset nasadit Azure Multi-Factor Authentication Server místní. Součásti serveru MFA se zobrazují v následujícím diagramu:

![Výchozí součásti serveru MFA: konzoly, synchronizační modul, portálu pro správu, Cloudová služba](./media/multi-factor-authentication-security-best-practices/server.png) \*není ve výchozím nastavení nainstalovaná \** nainstalován, ale není povoleno ve výchozím nastavení

Azure Multi-Factor Authentication Server můžete zabezpečit cloudové prostředky a místní prostředky pomocí federace. Musí mít služby AD FS a mějte ho sdružených se službou klientovi Azure AD.
Při nastavování aplikace Multi-Factor Authentication Server, vezměte v úvahu následující podrobnosti:

* Pokud jsou zabezpečení prostředků Azure AD pomocí služby Active Directory Federation Services (AD FS), pak prvním krokem ověření se provádí místně pomocí služby AD FS. Druhý krok se provádí místně dodržením deklarace identity.
* Nemáte k instalaci serveru Azure Multi-Factor Authentication federační server služby AD FS. Ale musí být nainstalován Multi-Factor Authentication Adapter pro AD FS na Windows Server 2012 R2 službou AD FS. Můžete nainstalovat na server do jiného počítače, dokud se jedná o podporovanou verzi a instalovat adaptér AD FS samostatně na federační server služby AD FS. 
* Průvodce instalací služby Multi-Factor Authentication AD FS adapteru vytvoří skupinu zabezpečení s názvem PhoneFactor Admins ve službě Active Directory a pak do této skupiny přidá účet služby AD FS. Ověřte, že se v řadiči domény vytvořila skupina PhoneFactor Admins a mezi jejími členy je účet služby AD FS. Pokud to bude potřeba, přidejte účet služby AD FS do skupiny PhoneFactor Admins ve vašem řadiči domény ručně.

### <a name="user-portal"></a>Uživatelský portál
Portál user portal umožňuje schopnosti samoobslužné služby a poskytuje úplnou sadu funkcí správy uživatelů. Spustí se v webovému serveru Internet Information Server (IIS). Pro tuto komponentu nakonfigurovat použijte následující pokyny:

* Pomocí služby IIS 6 nebo vyšší.
* Nainstalujte a zaregistrujte ASP.NET v2.0.507207
* Ujistěte se, že tento server se dá nasadit v hraniční síti

### <a name="app-passwords"></a>Hesla aplikací
Pokud je vaše organizace Federovaná pro jednotné přihlašování s Azure AD a chcete používat Azure MFA, pak mějte na paměti následující podrobnosti:

* Heslo aplikace je ověřováno Azure AD a proto obchází federace. Federace se používá pouze při nastavování hesla aplikací.
* Hesla pro federované uživatele (SSO), jsou uložena v id organizace. Pokud uživatel odejde ze společnosti, že informace o musí přejít do id organizace pomocí nástroje DirSync. Zakázání/odstranění účtu může trvat až tři hodiny pro synchronizaci, který zpoždění zakázání/odstranění hesla aplikací ve službě Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Žádné místní ověřování protokolování nebo auditování funkce je dostupná pro hesla aplikací.
* Některé pokročilé architektury návrhů může vyžadovat při použití dvoustupňové ověřování s klienty, v závislosti na tom, kde se ověřují pomocí kombinace organizační uživatelského jména a hesla aplikace. Pro klienty, kteří se ověřují se na místní infrastrukturu byste použili organizační uživatelské jméno a heslo. Pro klienty, kteří se ověřují se Azure AD použijete heslo aplikace.
* Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Pokud potřebujete povolit uživatelům vytvářet hesla aplikací, vyberte **povolit uživatelům vytvářet hesla aplikací pro přihlášení do neprohlížečových aplikací** možnost.

## <a name="additional-considerations"></a>Další informace
Pomocí tohoto seznamu Další informace a pokyny pro jednotlivé komponenty, který je nasazen na místě:

- Nastavení služby Azure Multi-Factor Authentication se službou [Active Directory Federation Services (AD FS)](multi-factor-authentication-get-started-adfs.md).
- Nastavení a konfigurace Azure MFA Serveru s [ověřováním pomocí protokolu RADIUS](howto-mfaserver-dir-radius.md).
- Nastavení a konfigurace Azure MFA serveru s [ověřování služby IIS](howto-mfaserver-iis.md).
- Nastavení a konfigurace Azure MFA serveru s [ověřování systému Windows](howto-mfaserver-windows.md).
- Nastavení a konfigurace Azure MFA serveru s [ověřování pomocí protokolu LDAP](howto-mfaserver-dir-ldap.md).
- Nastavení a konfigurace Azure MFA serveru s [Brána vzdálené plochy a pomocí protokolu RADIUS serveru Azure Multi-Factor Authentication Server](howto-mfaserver-nps-rdg.md).
- Nastavení a konfiguraci synchronizace mezi Azure MFA serveru a [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
- [Nasazení webové služby mobilní aplikace Azure Multi-Factor Authentication Serveru](howto-mfaserver-deploy-mobileapp.md).
- [Rozšířené konfigurace sítě VPN s ověřováním Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) pro zařízení Cisco ASA, Citrix Netscaler a Juniper/Pulse Secure VPN pomocí protokolu LDAP nebo RADIUS.

## <a name="next-steps"></a>Další postup
Při tomto článku klade důraz některé osvědčené postupy pro Azure MFA, existují další prostředky, které můžete použít také při plánování nasazení vícefaktorového ověřování. Tento seznam obsahuje některé klíče články, které mohou pomoci během tohoto procesu:

* [Sestavy v Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Prostředí dvoustupňové ověření registrace](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)
* [Nejčastější dotazy k Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

