---
title: Pokyny pro zabezpečení azure vícefaktorového ověřování – Azure Active Directory
description: Tento dokument poskytuje pokyny k používání Azure MFA s účty Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846823"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Pokyny pro zabezpečení pro používání Azure Multi-Factor Authentication s účty Azure AD

Dvoustupňové ověření je upřednostňovanou volbou pro většinu organizací, které chtějí zlepšit proces ověřování. Azure Multi-Factor Authentication (MFA) pomáhá společnostem splnit jejich požadavky na zabezpečení a dodržování předpisů a zároveň poskytuje jednoduché přihlašovací prostředí pro své uživatele. Tento článek popisuje některé tipy, které byste měli zvážit při plánování přijetí Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Nasazení Azure MFA v cloudu

Azure MFA můžete [povolit dvěma způsoby pro všechny uživatele](howto-mfa-getstarted.md).

* Zakoupení licencí pro každého uživatele (Azure MFA, Azure AD Premium nebo Enterprise Mobility + Security)
* Vytvoření vícefaktorového zprostředkovatele ověření a platby na uživatele nebo ověřování

### <a name="licenses"></a>Licence

![Použití licencí pro uživatele, povolení, upozornění](./media/multi-factor-authentication-security-best-practices/ems.png)

Pokud máte licence Azure AD Premium nebo Enterprise Mobility + Security, už máte Azure MFA. Vaše organizace nepotřebuje nic dalšího k rozšíření dvoustupňového ověřování na všechny uživatele. Stačí přiřadit licenci uživateli a pak můžete zapnout vícefaktorové ověřování.

Při nastavování vícefaktorového ověřování zvažte následující tipy:

* Nevytvářejte zprostředkovatele vícefaktorového ověření pro ověřování podle ověřování. Pokud tak učiníte, můžete nakonec platit za žádosti o ověření od uživatelů, kteří již licence mají.
* Pokud nemáte dostatek licencí pro všechny uživatele, můžete vytvořit poskytovatele vícefaktorového ověření pro jednotlivé uživatele, který pokryje zbytek vaší organizace. 
* Azure AD Connect je potřeba jenom v případě, že synchronizujete místní prostředí služby Active Directory s adresářem Azure AD. Pokud používáte adresář Azure AD, který není synchronizován s místní instanci služby Active Directory, nepotřebujete Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Poskytovatel vícefaktorového authu

![Zprostředkovatel vícefaktorového ověřování](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Pokud nemáte licence, které obsahují Azure MFA, pak můžete [vytvořit zprostředkovatele auth Více faktoru](concept-mfa-authprovider.md)pro získání licence .

Při vytváření zprostředkovatele auth musíte vybrat adresář a zvážit následující podrobnosti:

* K vytvoření zprostředkovatele vícefaktorového ututla nepotřebujete adresář Azure AD, ale získáte další funkce s jedním. Následující funkce jsou povoleny, když přidružíte zprostředkovatele umítavého vztahu k adresáři Azure AD:
  * Rozšíření dvoustupňového ověření na všechny uživatele
  * Nabídněte globálním správcům další funkce, jako je portál pro správu, vlastní pozdravy a sestavy.
* Pokud synchronizujete místní prostředí Služby Active Directory s adresářem Azure AD, budete potřebovat DirSync nebo Azure AD Sync. Pokud používáte adresář Azure AD, který není synchronizován s místní instanci služby Active Directory, nepotřebujete DirSync nebo Azure AD Sync.
* Vyberte si model spotřeby, který nejlépe vyhovuje vašemu podnikání. Jakmile vyberete model použití, nemůžete jej změnit. Tyto dva modely jsou:
  * Na ověření: účtuje vám poplatky za každé ověření. Tento model použijte, pokud chcete dvoustupňové ověření pro každého, kdo přistupuje k určité aplikaci, ne pro konkrétní uživatele.
  * Na povoleného uživatele: účtuje poplatky za každého uživatele, kterého povolíte pro Azure MFA. Tento model použijte, pokud máte některé uživatele s licencemi Azure AD Premium nebo Enterprise Mobility Suite a někteří bez.

### <a name="supportability"></a>Možnosti podpory

Vzhledem k tomu, že většina uživatelů je zvyklá používat k ověření pouze hesla, je důležité, aby vaše společnost přinesla všem uživatelům povědomí o tomto procesu. Toto povědomí může snížit pravděpodobnost, že uživatelé volají na váš help desk pro drobné problémy související s vícefaktorové pomoci. Existují však některé scénáře, kde je nutné dočasně zakázat vícefaktorové zabezpečení. Pomocí následujících pokynů pochopit, jak zpracovat tyto scénáře:

* Vyškolte pracovníky technické podpory, aby zvládali scénáře, kdy se uživatel nemůže přihlásit, protože mobilní aplikace nebo telefon nedostává oznámení nebo telefonní hovor. Technická podpora může [povolit jednorázové obtok,](howto-mfa-mfasettings.md#one-time-bypass) který uživateli umožní ověření jednoho času "obcházením" dvoustupňového ověření. Obtok je dočasný a vyprší po zadaném počtu sekund.
* Zvažte [funkci důvěryhodných IP serverů](howto-mfa-mfasettings.md#trusted-ips) v Azure MFA jako způsob, jak minimalizovat dvoustupňové ověřování. Pomocí této funkce mohou správci spravovaného nebo federovaného klienta obejít dvoustupňové ověření pro uživatele, kteří se přihlašují z místního intranetu společnosti. Funkce jsou dostupné pro klienty Azure AD, kteří mají licence Azure AD Premium, Enterprise Mobility Suite nebo Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Doporučené postupy pro místní nasazení

Pokud se vaše společnost rozhodla využít vlastní infrastrukturu k povolení vícefaktorové ověřování, musíte [nasadit server Azure S vícefaktorovým ověřováním místně](howto-mfaserver-deploy.md). Součásti serveru MFA jsou zobrazeny v následujícím diagramu:

![Výchozí součásti serveru](./media/multi-factor-authentication-security-best-practices/server.png) \*MFA Server \*Nejsou ve výchozím nastavení nainstalovány *Nainstalováno, ale není ve výchozím nastavení povoleno

Azure Multi-Factor Authentication Server může zabezpečit cloudové prostředky a místní prostředky pomocí federace. Musíte mít službu AD FS a nechat ji federovat s vaším klientem Azure AD.
Při nastavování serveru s vícefaktorovým ověřováním zvažte následující podrobnosti:

* Pokud zabezpečujete prostředky Azure AD pomocí služby AD Federation Services (AD FS), první krok ověření se provede místně pomocí služby AD FS. Druhý krok se provádí místně dodržením deklarace identity.
* Není nutné instalovat server Azure multi-Factor Authentication Server federační server služby AD FS. Adaptér vícefaktorového ověřování pro službu AD FS však musí být nainstalován v systému Windows Server 2012 R2 se službou AD FS. Server můžete nainstalovat do jiného počítače, pokud se jedná o podporovanou verzi, a adaptér služby AD FS nainstalovat samostatně na federační server služby AD FS. 
* Průvodce instalací adaptéru služby AD FS pro vícefaktorové ověřování vytvoří ve službě Active Directory skupinu zabezpečení nazvanou Správci služby PhoneFactor a do této skupiny přidá účet služby AD FS. Ověřte, že se v řadiči domény vytvořila skupina PhoneFactor Admins a mezi jejími členy je účet služby AD FS. Pokud to bude potřeba, přidejte účet služby AD FS do skupiny PhoneFactor Admins ve vašem řadiči domény ručně.

### <a name="user-portal"></a>Uživatelský portál

Uživatelský portál umožňuje samoobslužné funkce a poskytuje úplnou sadu funkcí správy uživatelů. Běží na webu serveru Internet Information Server (IIS). Ke konfiguraci této součásti použijte následující pokyny:

* Použití iis 6 nebo vyšší
* Instalace a registrace ASP.NET 2.0.507207
* Ujistěte se, že tento server lze nasadit v hraniční síti

### <a name="app-passwords"></a>Hesla aplikací

Pokud vaše organizace je federována pro přihlašující služby s Azure AD a budete používat Azure MFA, pak si uvědomte následující podrobnosti:

* Heslo aplikace je ověřeno službou Azure AD a proto obchází federaci. Federace se používá pouze při nastavování hesel aplikací.
* Pro federované uživatele (SSO) jsou hesla uložena v id organizace. Pokud uživatel opustí společnost, tyto informace musí tok na ID organizace pomocí DirSync. Synchronizace účtu zazakázat nebo odstranit může trvat až tři hodiny, což zpozdí zakázání a odstranění hesel aplikací ve službě Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Pro hesla aplikací není k dispozici žádná funkce protokolování a auditování místního ověřování.
* Některé pokročilé architektonické návrhy mohou vyžadovat použití kombinace uživatelského jména organizace a hesel a hesel aplikací při použití dvoustupňového ověření s klienty v závislosti na tom, kde se ověřují. Pro klienty, kteří se ověřují podle místní infrastruktury, byste použili uživatelské jméno a heslo organizace. Pro klienty, kteří se ověřují proti Azure AD, byste použili heslo aplikace.
* Ve výchozím nastavení uživatelé nemohou vytvářet hesla aplikací. Pokud potřebujete uživatelům povolit vytváření hesel aplikací, vyberte možnost **Povolit uživatelům vytvářet hesla aplikací pro přihlášení k aplikacím, které nejsou v prohlížeči.**

## <a name="additional-considerations"></a>Další důležité informace

Tento seznam použijte pro další důležité informace a pokyny pro každou komponentu, která je nasazena místně:

* Nastavení služby Azure Multi-Factor Authentication se službou [Active Directory Federation Services (AD FS)](multi-factor-authentication-get-started-adfs.md).
* Nastavení a konfigurace Azure MFA Serveru s [ověřováním pomocí protokolu RADIUS](howto-mfaserver-dir-radius.md).
* Nastavte a nakonfigurujte server Azure MFA pomocí [ověřování služby IIS](howto-mfaserver-iis.md).
* Nastavte a nakonfigurujte server Azure MFA s [ověřováním systému Windows](howto-mfaserver-windows.md).
* Nastavte a nakonfigurujte server Azure MFA pomocí [ověřování LDAP](howto-mfaserver-dir-ldap.md).
* Nastavte a nakonfigurujte server Azure MFA pomocí [brány vzdálené plochy a serveru Azure Multi-Factor Authentication Server pomocí radius](howto-mfaserver-nps-rdg.md).
* Nastavte a nakonfigurujte synchronizaci mezi serverem Azure MFA Server a [službou Active Directory systému Windows Server](howto-mfaserver-dir-ad.md).
* [Nasazení webové služby serveru pro vícenásobné ověřování Azure](howto-mfaserver-deploy-mobileapp.md).
* [Pokročilá konfigurace VPN s vícefaktorovým ověřováním Azure](howto-mfaserver-nps-vpn.md) pro zařízení Cisco ASA, Citrix Netscaler a Juniper/Pulse Secure VPN pomocí protokolu LDAP nebo RADIUS.

## <a name="next-steps"></a>Další kroky

Zatímco tento článek zdůrazňuje některé osvědčené postupy pro Azure MFA, existují další prostředky, které můžete také použít při plánování nasazení MFA. Níže uvedený seznam obsahuje některé klíčové články, které vám mohou pomoci během tohoto procesu:

* [Sestavy v Azure vícefaktorové ověřování](howto-mfa-reporting.md)
* [Dvoustupňová zkušenost s registrací ověřování](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Nejčastější dotazy k vícefaktorovému ověřování azure](multi-factor-authentication-faq.md)
