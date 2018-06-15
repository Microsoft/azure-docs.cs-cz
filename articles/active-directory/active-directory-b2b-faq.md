---
title: Spolupráce Azure Active Directory s B2B nejčastější dotazy | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy o spolupráci Azure Active Directory s B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 77dc239385261c9544b00cf2cf2e450136dce0ae
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076569"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Spolupráce Azure Active Directory s B2B nejčastější dotazy

Tyto nejčastější dotazy (FAQ) o spolupráci Azure Active Directory (Azure AD) business-to-business (B2B) se pravidelně aktualizují zahrnout nová témata.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Jsme naše přihlašovací stránka přizpůsobit, tak, aby byl pro naše uživatele typu Host spolupráce B2B intuitivnější?
Absolutně! V tématu naše [příspěvku na blogu o této funkci](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Další informace o tom, jak přizpůsobit přihlašovací stránku vaší organizace najdete v tématu [přidání firemního brandingu na přihlašovací a přístupového panelu](customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Mohou uživatelé spolupráce B2B, získat přístup k SharePoint Online a OneDrive?
Ano. Je však umožňuje vyhledat existující uživatele typu Host ve službě SharePoint Online pomocí nástroje pro výběr osob **vypnout** ve výchozím nastavení. Chcete-li zapnout možnost vyhledat existující uživatele typu Host, nastavte **ShowPeoplePickerSuggestionsForGuestUsers** k **na**. Toto nastavení můžete zapnout na buď na úrovni klienta nebo na úrovni kolekce webů. Toto nastavení můžete změnit pomocí rutiny Set-SPOTenant a Set-SPOSite. Pomocí těchto rutin můžete hledat členy v adresáři, všechny existující uživatele typu Host. Změny v oboru klienta nemají vliv na weby SharePoint Online, které již byly zřízeny.

### <a name="is-the-csv-upload-feature-still-supported"></a>Je funkce sdíleného svazku nahrávání stále podporovány?
Ano. Další informace týkající se použití funkce nahrávání souboru CSV najdete v tématu [této ukázce prostředí PowerShell](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak můžete přizpůsobit Moje pozvánku e-mailů?
Téměř všechny údaje o procesu pozvánky lze přizpůsobit pomocí [B2B pozvánku rozhraní API](active-directory-b2b-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Můžete resetovat uživatele typu Host jejich metoda služby Multi-Factor authentication?
Ano. Uživatele typu Host můžete resetovat jejich metoda služby Multi-Factor authentication stejným způsobem, že běžní uživatelé provádět.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Které organizace je zodpovědná za licence služby Multi-Factor authentication?
Pozváním organizace provádí ověřování Multi-Factor authentication. Pozváním organizace musí zajistit, že organizace má dostatek licencí pro své B2B uživatele, kteří používají službu Multi-Factor authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co když partnerské organizace už má nastavení služby Multi-Factor authentication? Můžete jsme důvěřovat jejich služby Multi-Factor authentication a nechcete použít vlastní vícefaktorové ověřování?
Tato funkce je plánovaná pro budoucí použití, která pak můžete vybrat konkrétní partnery, které chcete vyloučit z vaší služby Multi-Factor authentication (pozváním organizace).

### <a name="how-can-i-use-delayed-invitations"></a>Jak můžete použít zpožděné pozvánek?
Organizace chtít přidat uživatele spolupráce B2B, podle potřeby je zřizovat aplikace a pak odeslat pozvánky. Spolupráce B2B pozvánku rozhraní API můžete použít k přizpůsobení pracovního postupu registrace.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Můžete vytvořit uživatele guest správce s omezeními?
Jistě. Další informace najdete v tématu [přidání uživatele typu Host roli](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Umožňuje spolupráci Azure AD B2B s B2B uživatelům přístup k portálu Azure?
Pokud uživatel má přiřazenou roli Globální správce nebo správce s omezeními, uživatelé spolupráce B2B nebude vyžadovat přístup k portálu Azure. Uživatelé spolupráce B2B, kteří mají přiřazenou roli Globální správce nebo správce s omezeními získat přístup k portálu. Navíc pokud uživatel hosta, který není přiřazen jedna z těchto rolí správce má přístup k portálu, uživatel může být přístup k určité části prostředí. Některá oprávnění má role uživatele guest v adresáři.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Může blokovat přístup k portálu Azure pro uživatele typu Host?
Ano! Když nakonfigurujete tuto zásadu, je potřeba zabránit omylem blokování přístupu na členy a správci.
Blokování přístupu uživatel typu Host k [portál Azure](https://portal.azure.com), použijte zásady podmíněného přístupu v rozhraní API modelu nasazení classic systému Windows Azure:
1. Změnit **všichni uživatelé** tak, aby obsahoval pouze členové skupiny.
  ![Upravit skupinu snímek obrazovky](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Vytvoření dynamické skupiny, která obsahuje uživatele typu Host.
  ![Vytvoření skupiny – snímek obrazovky](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Nastavte zásady podmíněného přístupu uživatele typu Host blokovat přístup k portálu, jak je znázorněno v následujícím videu:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Podporuje spolupráci Azure AD B2B Multi-Factor authentication a příjemce e-mailové účty?
Ano. Vícefaktorové ověřování a příjemce e-mailové účty jsou podporované pro spolupráci Azure AD B2B.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Máte v úmyslu podporovat resetování hesla pro uživatele spolupráce Azure AD B2B?
Ano. Tady jsou důležité podrobnosti o samoobslužné resetování hesla (SSPR) pro uživatele B2B, který jste pozvánku, od organizace partnera:
 
* SSPR dojde pouze v klientovi identity uživatele B2B.
* Pokud klient identitu účtu Microsoft, se používá účet Microsoft SSPR mechanismus.
* Pokud je identita klienta v běhu (JIT) nebo "virální" klienta, se budou odesílat e-mailu s heslo resetovat.
* U jiných klientů je pro uživatele B2B následovaný standardního procesu SSPR. Jako člen SSPR B2B uživatelům, v rámci prostředků je blokována klientů. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Je resetování hesla k dispozici pro uživatele typu Host v v běhu (JIT) nebo "virální" klientů, kteří přijali pozvánek s pracovním nebo školním e-mailovou adresu, ale který neměly existující účet Azure AD?
Ano. A heslo resetovat poštu lze odeslat buď, která umožňuje uživatelům resetovat heslo v JIT klientů.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Poskytuje Microsoft Dynamics 365 online podpory společnosti Microsoft pro spolupráci Azure AD B2B?
Ano, Dynamics 365 (online) poskytuje podporu pro spolupráci Azure AD B2B. Další informace najdete v článku Dynamics 365 [pozvat uživatele s spolupráce Azure AD B2B](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Co je životnost počátečního hesla pro nově vytvořeného uživatele spolupráce B2B?
Azure AD má pevnou sadu znaků, síly hesla a účet, který uzamčení požadavky, které se rovněž vztahují na všechny Azure AD cloud uživatelské účty. Cloud uživatelské účty jsou účty, které nejsou Federovaná pomocí jiného poskytovatele identity, jako například 
* Účet Microsoft
* Facebook
* Služba AD FS (Active Directory Federation Services)
* Jiného klienta cloudu (pro spolupráci B2B)

Pro federované účty zásady hesel závisí na zásadu, která je použitá v místní klientů a nastavení účtu Microsoft uživatele.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizace může chtít, aby různých prostředích ve svých aplikacích pro uživatele klienta a uživatele typu Host. Je k dispozici standardní pokyny pro tento? Je přítomnost zprostředkovatele identity deklarace správné modelu, který má používat?
 Uživatel guest mohou využívat kteréhokoli zprostředkovatele identity k ověření. Další informace najdete v tématu [vlastnosti uživatele spolupráce B2B](active-directory-b2b-user-properties.md). Použití **UserType** vlastnosti k určení činnost koncového uživatele. **UserType** deklarace identity není aktuálně zahrnutý v tokenu. Aplikace by měly používat rozhraní Graph API k dotazování adresáře pro uživatele a získat UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Kde komunita spolupráce B2B sdílet řešení a k odeslání nápadů
Nemůžeme se neustále naslouchání vaše připomínky pro zlepšení spolupráce B2B. Doporučujeme vám sdílet vaše uživatelské scénáře, osvědčené postupy a co se vám líbí o spolupráci Azure AD B2B. Připojit k diskuse [Microsoft technická komunita](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Také doporučujeme odeslat nápady a hlasů pro budoucí funkce na [nápady spolupráce B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Můžete odeslat požadavek, který je automaticky uplatněny, tak, aby uživatel právě "jste připraveni..."? Nebo uživatel vždy muset kliknout na tlačítko prostřednictvím na adresu URL uplatnění?
Pozvánky můžete pozvat jiných uživatelů v organizaci partnera pomocí uživatelského rozhraní, skriptů prostředí PowerShell nebo rozhraní API. Potom pozvání odeslal odeslat uživatele guest přímý odkaz na sdílené aplikaci. Ve většině případů už není potřeba otevřít pozvánku e-mailu a klikněte na adresu URL uplatnění kódu. Další informace najdete v tématu [uplatnění pozvánku spolupráce Azure Active Directory s B2B](active-directory-b2b-redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Spolupráce B2B funkce když pozvané partnera používá federační přidat vlastní místního ověřování
Pokud partner má tenanta služby Azure AD, který je federovaného ověřování místní infrastruktury, místní jednotné přihlašování (SSO) automaticky dosáhnout. Pokud partner nemá klient služby Azure AD, vytvoří se účet služby Azure AD pro nové uživatele. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Ačkoli Azure AD B2B nepřijal gmail.com a outlook.com e-mailové adresy a že B2C byla použita pro tyto typy účtů?
Rozdíly mezi B2B a spolupráci obchodní k příjemce (B2C) z hlediska, které jsou podporovány identity jsme se odebrat. Identita použitá není funkční důvod, proč zvolit mezi použitím B2B a použitím B2C. Informace o výběru vaše možnosti spolupráce naleznete v tématu [spolupráce B2B porovnat a B2C v Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jaké aplikace a služby podporují uživatele typu Host Azure B2B?
Aplikace všechny integrované s Active Directory Azure podporují uživatele typu Host Azure B2B. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Jsme Pokud našich partnerů nemají služby Multi-Factor authentication vynutit službu Multi-Factor authentication pro uživatele typu Host B2B?
Ano. Další informace najdete v tématu [podmíněného přístupu pro uživatele spolupráce B2B](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>Ve službě SharePoint můžete definovat seznam aplikace "Povolit" nebo "deny" pro externí uživatele. Můžete jsme to udělat v Azure?
Ano. Podporuje spolupráce Azure AD s B2B povolit seznamy a seznamy zakázat. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jaké licence je potřeba použít Azure AD B2B?
Informace o co licence potřeb vaší organizace používat Azure AD B2B najdete v tématu [spolupráce Azure Active Directory s B2B licencování pokyny](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Další postup

- [Co je spolupráce B2B ve službě Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)

