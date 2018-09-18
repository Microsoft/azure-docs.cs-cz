---
title: Spolupráce Azure Active Directory s B2B nejčastější dotazy | Dokumentace Microsoftu
description: Získejte odpovědi na nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: reference
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b4407ac6b7a0d9fdbf52b84fb94223c32868f0c5
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983848"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Spolupráce Azure Active Directory s B2B nejčastější dotazy

Tyto nejčastější dotazy ohledně spolupráce Azure Active Directory (Azure AD) business-to-business (B2B) jsou pravidelně aktualizovány zahrnout nová témata.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Jsme naše přihlašovací stránka přizpůsobit, tak, aby byl intuitivnější našich uživatelů typu Host pro spolupráci B2B?
Absolutně! Najdete v našich [blogový příspěvek o této funkci](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Další informace o tom, jak přizpůsobit přihlašovací stránku vaší organizace, najdete v části [přidat vlastní firemní branding pro přihlášení a stránky přístupového panelu](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Uživatele spolupráce B2B přístup k Sharepointu Online a Onedrivu?
Ano. Je ale možnost vyhledat existující uživatele typu Host do Sharepointu Online pomocí nástroje pro výběr osoby **vypnout** ve výchozím nastavení. Chcete-li zapnout možnost vyhledat existující uživatele typu Host, nastavte **ShowPeoplePickerSuggestionsForGuestUsers** k **na**. Toto nastavení můžete zapnout buď na úrovni tenanta nebo na úrovni kolekce webů. Toto nastavení můžete změnit pomocí rutiny Set-SPOTenant a Set-SPOSite. Pomocí těchto rutin můžete členy hledat všechny existující uživatele typu Host v adresáři. Změny v rámci tenanta neovlivní weby SharePoint Online, které již byly vytvořeny.

### <a name="is-the-csv-upload-feature-still-supported"></a>Je funkce sdíleného svazku clusteru nahrávání stále podporovány?
Ano. Další informace o funkci odesílání souborů CSV pomocí najdete v tématu [této ukázce Powershellu](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak můžete přizpůsobit e-mailových pozvánek?
Téměř vše, co o odesílatel pozvánky procesu můžete přizpůsobit pomocí [B2B pozvánku rozhraní API](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Uživatelé typu Host můžete resetovat své metody ověřování službou Multi-Factor Authentication?
Ano. Uživatelé typu Host můžete resetovat své metody ověřování službou Multi-Factor Authentication stejným způsobem jako běžní uživatelé.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Organizaci, které je zodpovědný za licence služby Multi-Factor authentication?
Zvoucí organizaci provádí ověřování službou Multi-Factor Authentication. Zvoucí organizaci Ujistěte se, že má organizace dostatek licencí pro uživatele B2B, kteří používají ověřování službou Multi-Factor Authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co když partnerské organizace už má nastavení služby Multi-Factor authentication? Můžete jsme důvěřovat ověřování službou Multi-Factor Authentication a nepoužívat naše vlastní služby Multi-Factor authentication?
Tato funkce je plánovaná pro budoucí verze, tak, která pak můžete vybrat konkrétní partnerů, které chcete vyloučit z vaší služby Multi-Factor authentication (zvoucí organizaci.).

### <a name="how-can-i-use-delayed-invitations"></a>Použití zpožděného pozvánky
Organizace chtít přidat uživatele spolupráce B2B, zřizování aplikací podle potřeby a potom odeslat pozvánky. Pozvání spolupráce B2B rozhraní API můžete použít k přizpůsobení pracovního postupu registrace.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Můžete mi zviditelnit uživatele typu Host do globálního seznamu adres Exchange?
Ano. Ve výchozím nastavení objekty typu Host, se nezobrazí v seznamu globálního adresáře vaší organizace, ale můžete použít Azure Active Directory PowerShell je Pokud chcete zviditelnit. Podrobnosti najdete v tématu **můžete udělám hostů objekty viditelné v globálním?** v [přístup hosta v Office 365 Groups](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ).

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Můžete vytvořit uživatele typu Host správce s omezeními?
Jistě. Další informace najdete v tématu [přidání uživatele typu Host do role](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Umožňuje spolupráci B2B ve službě Azure AD uživatelům B2B přístup k webu Azure portal?
Pokud uživatel má přiřazenou roli globálního správce nebo správce s omezením, nevyžadují uživatele spolupráce B2B přístup k webu Azure portal. Uživatele spolupráce B2B, kteří mají přiřazenou roli globálního správce nebo správce s omezením můžete přístup k portálu. Také pokud uživatel typu Host, který není přiřazen jeden z těchto rolí správce přistupuje k portálu, uživatel může být mít přístup k určitým částem prostředí. Role uživatele typu Host má některá oprávnění v adresáři.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Může blokovat přístup k webu Azure portal pro uživatele typu Host?
Ano! Když nakonfigurujete tuto zásadu, pečlivě nedošlo omylem blokují přístup k členům a správci.
Blokovat přístup uživatelů typu Host [webu Azure portal](https://portal.azure.com), použijte zásady podmíněného přístupu v rozhraní API Windows Azure pro model nasazení classic:
1. Upravit **všichni uživatelé** tak, aby obsahoval pouze členové skupiny.
  ![Upravit skupiny – snímek obrazovky](media/faq/modify-all-users-group.png)
2. Vytvoření dynamické skupiny obsahující uživatele typu Host.
  ![Vytvoření skupiny – snímek obrazovky](media/faq/group-with-guest-users.png)
3. Nastavení zásad podmíněného přístupu k blokování uživatelů typu Host z přístup k portálu, jak je znázorněno v následujícím videu:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Spolupráce B2B ve službě Azure AD podporuje vícefaktorové ověřování a příjemce e-mailové účty?
Ano. Vícefaktorové ověřování a příjemce e-mailové účty jsou podporované pro spolupráci B2B ve službě Azure AD.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Plánování pro podporu resetování hesla pro uživatele spolupráce B2B ve službě Azure AD?
Ano. Tady jsou důležité podrobnosti pro samoobslužné resetování hesla (SSPR) pro uživatele B2B, kdo je pozvaný z partnerské organizace:
 
* Samoobslužné resetování HESLA se vyskytuje pouze v tenant identity uživatele B2B.
* Pokud je identity tenant účtu Microsoft, se používá mechanismus samoobslužné resetování HESLA účtu Microsoft.
* Pokud je identity tenant just-in-time (JIT) nebo "virálního" tenanta, se odešle e-mail s resetování hesla.
* U jiných klientů následuje standardního procesu samoobslužné resetování HESLA pro uživatele B2B. Jako člen samoobslužné resetování HESLA pro uživatele B2B, v rámci prostředku tenantů blokovaný. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Je resetování hesel k dispozici pro uživatele typu Host v just-in-time (JIT) nebo "virálního" klientů, kteří přijetí pozvánky s pracovním nebo školním e-mailovou adresu, ale který neměli předem existující účet služby Azure AD?
Ano. E-mailu resetování hesla mohou být odesílány, která umožňuje uživatelům resetovat své heslo v tenantů JIT.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Poskytuje Microsoft Dynamics 365 online podpory pro spolupráci B2B ve službě Azure AD?
Ano, Dynamics 365 (online) poskytuje podporu pro spolupráci B2B ve službě Azure AD. Další informace najdete v článku Dynamics 365 [pozvat uživatele s Azure AD B2B collaboration](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Co je doba života počáteční heslo nově vytvořeného uživatele spolupráce B2B?
Azure AD má pevnou sadu znaků, síly hesla a účtu, že požadavky na uzamčení, které se vztahují na všechny Azure AD i cloudové uživatelské účty. Cloudové uživatelské účty jsou účty, které nejsou Federovaná pomocí jiného zprostředkovatele identity, jako například 
* Účet Microsoft
* Facebook
* Služba AD FS (Active Directory Federation Services)
* Jiného tenanta cloudu (pro spolupráci B2B)

Pro federované účty zásady pro hesla závisí na zásady, které jsou použity v místních tenantů a nastavení účtu Microsoft uživatele.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizace může být vhodné mít ve svých aplikacích pro tenanta uživatele a uživatele typu Host pracovat v různých prostředích. Existuje standardní pokyny k tomu? Je přítomnost zprostředkovatele identity deklarací správný model se má použít?
 Uživatel typu Host mohou využívat kteréhokoli zprostředkovatele identity k ověření. Další informace najdete v tématu [vlastnosti uživatele spolupráce B2B](user-properties.md). Použití **UserType** a určí činnost koncového uživatele. **UserType** deklarace identity není aktuálně součástí tokenu. Aplikace by měly používat rozhraní Graph API k dotazování adresáře pro uživatele a získat UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Kde najdu komunita B2B spolupráce a sdílení řešení Zanechte své nápady?
Posloucháme vás neustále na vaši zpětnou vazbu, ke zlepšení spolupráce B2B ve službě. Zveme vás k sdílet vaše uživatelské scénáře, osvědčené postupy a co se vám líbí o spolupráci B2B ve službě Azure AD. Připojte se k diskusi v [technické komunitě Microsoftu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Také neváhejte předkládat své nápady a Hlasujte pro budoucí funkce na [nápady spolupráce B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Můžeme tak, aby uživatel je právě "připravená" odeslání pozvánky, který je automaticky uplatnit? Nebo se uživatel vždy lze procházet k adrese URL uplatnění?
Odesílatele pozvánky může pozvat ostatní uživatele v organizaci partnera poskytujícího pomocí uživatelského rozhraní, skriptů Powershellu nebo rozhraní API. Potom odesílatel pozvánky můžete odeslat uživatele typu Host přímý odkaz do sdílené aplikace. Ve většině případů již není potřeba otevřít e-mailovou pozvánku a klikněte na adresu URL pro uplatnění. Další informace najdete v tématu [uplatnění pozvání spolupráce Azure Active Directory s B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Jak spolupráce B2B ve službě fungují při pozvaný partnera je použití federace přidat vlastní místní ověřování?
Pokud partner tenanta Azure AD, která je Federovaná do místní infrastruktury ověřování, v místním jednotné přihlašování (SSO) je automaticky dosáhnout. Pokud partner nemá tenanta služby Azure AD, vytvoří se účet služby Azure AD pro nové uživatele. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Můžu si mysleli, že B2B ve službě Azure AD nepřijal e-mailové adresy gmail.com a outlook.com. proto, že B2C se použil pro tyto druhy účtů?
Rozdíly mezi B2B a business-to-consumer spolupráce (B2C) z hlediska, které jsou podporovány identity odebíráme. Identita používaná není dobrý důvod, proč výběr mezi použitím B2B a B2C pomocí. Informace o volbě vaše možnosti spolupráce naleznete v tématu [spolupráce porovnání B2B a B2C v Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jaké aplikace a služby podporují uživatelů typu Host Azure B2B?
Všechny integrované s Active Directory aplikace na platformě Azure podporují hosty B2B ve službě Azure. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Jsme v případě našich partnerů nemají ověřování službou Multi-Factor Authentication vynutit ověřování Multi-Factor Authentication pro uživatele typu Host B2B?
Ano. Další informace najdete v tématu [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>Ve službě SharePoint můžete definovat přehledu "Povolit" nebo "Zakázat" pro externí uživatele. Můžeme to lze provést v Azure?
Ano. Azure AD B2B collaboration podporuje seznamy seznamů povolených a zakázaných. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jaké licence je potřeba použít Azure AD B2B?
Informace o typech licencí, vaše organizace musí používat Azure AD B2B najdete v tématu [doprovodné materiály k licencování spolupráce Azure Active Directory B2B](licensing-guidance.md).

### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)

