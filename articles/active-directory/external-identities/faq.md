---
title: Nejčastější dotazy týkající se spolupráce B2B – Azure Active Directory | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Active Directory spolupráce B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3308c2263f80a0772a389900e08c81cfe8da32a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952622"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Nejčastější dotazy k Azure Active Directory spolupráci B2B

Tyto nejčastější dotazy týkající se spolupráce B2B (Business-to-Business) Azure Active Directory (Azure AD) se pravidelně aktualizují, aby obsahovaly nová témata.

> [!IMPORTANT]
> - **Od 4. ledna 2021** je Google [zastaralá podpora přihlašování v nástroji WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Pokud používáte Google Federation nebo samoobslužnou registraci pomocí služby Gmail, měli byste [testovat kompatibilitu vašich obchodních nativních aplikací](google-federation.md#deprecation-of-webview-sign-in-support).
> - **Od října 2021** přestane společnost Microsoft podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří nespravované účty a klienty Azure AD. V přípravě doporučujeme zákazníkům, aby se přihlásili k [e-mailu ověřování jednorázovým heslem](one-time-passcode.md). Uvítáme vaše názory na tuto funkci Public Preview a zajímáme si vytváření ještě více způsobů, jak spolupracovat.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Můžeme přizpůsobit naši přihlašovací stránku, aby byla intuitivnější pro naše uživatele typu Host pro spolupráci B2B?
Samozřejmě. Další informace najdete v našem [blogovém příspěvku o této funkci](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Další informace o tom, jak přizpůsobit přihlašovací stránku vaší organizace, najdete v tématu [Přidání firemního brandingu pro přihlášení a přístup ke stránkám na panelu](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Můžou uživatelé spolupráce B2B přistupovat k SharePointu Online a OneDrivu?
Ano. Možnost hledání stávajících uživatelů typu Host v SharePointu Online pomocí výběru osob je ale ve výchozím nastavení **vypnutá** . Pokud chcete zapnout možnost hledání stávajících uživatelů typu Host, nastavte **ShowPeoplePickerSuggestionsForGuestUsers** na **zapnuto**. Toto nastavení můžete zapnout buď na úrovni tenanta, nebo na úrovni kolekce webů. Toto nastavení můžete změnit pomocí rutin Set-SPOTenant a Set-SPOSite. Pomocí těchto rutin můžou členové vyhledat všechny existující uživatele typu Host v adresáři. Změny v oboru tenanta neovlivňují online weby SharePointu, které již byly zřízeny.

### <a name="is-the-csv-upload-feature-still-supported"></a>Je funkce nahrávání CSV pořád podporovaná?
Ano. Další informace o použití funkce nahrání souboru. CSV najdete v [této ukázce PowerShellu](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak můžu přizpůsobit své e-maily pozvánky?
Pomocí [rozhraní API pro pozvánky B2B](customize-invitation-api.md)můžete přizpůsobit skoro všechno, co se týká procesu pozvánky.

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Můžou uživatelé typu Host resetovat svoji službu Multi-Factor Authentication?
Ano. Uživatelé typu Host můžou svou metodu vícefaktorového ověřování resetovat stejným způsobem jako běžné uživatele.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Která organizace zodpovídá za licence Multi-Factor Authentication?
Organizace pro pozvání provádí službu Multi-Factor Authentication. Organizace pro pozvání musí zajistit, že má organizace dostatek licencí pro své uživatele B2B, kteří používají službu Multi-Factor Authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co když už partnerská organizace používá službu Multi-Factor Authentication, je nastavená? Může se nám vymezit ověřování Multi-Factor Authentication a nepoužívat naše vlastní vícefaktorové ověřování?
Tato funkce se v tuto chvíli nepodporuje. Pokud přístup k prostředkům vaší organizace vyžaduje vícefaktorové ověřování, partnerská organizace bude muset zaregistrovat službu Multi-Factor Authentication ve vaší organizaci (pozvání).

### <a name="how-can-i-use-delayed-invitations"></a>Jak můžu použít zpožděné pozvánky?
Organizace může chtít přidat uživatele spolupráce B2B, podle potřeby je zřídit s aplikacemi a pak odesílat pozvánky. K přizpůsobení pracovního postupu připojování můžete použít rozhraní API pozvánky B2B pro spolupráci.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Je možné uživatele typu Host zobrazit v globálním seznamu adres systému Exchange?
Ano. Objekty hosta nejsou ve výchozím nastavení v globálním seznamu adres vaší organizace viditelné, ale můžete je zobrazit pomocí Azure Active Directory PowerShellu. Viz [, jak se můžou objekty hosta zobrazit v globálním seznamu adres?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Můžu uživatele typu Host nastavit jako omezeného správce?
Jistě. Další informace najdete v tématu [Přidání uživatelů typu Host do role](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Umožňuje spolupráce Azure AD B2B uživatelům přístup k Azure Portal?
Pokud uživatel nemá přiřazenou roli omezeného správce, nebudou uživatelé spolupráce B2B vyžadovat přístup k Azure Portal. Uživatelé spolupráce B2B, kteří mají přiřazenou roli omezeného správce, ale mají přístup k portálu. Také pokud uživatel typu Host, který nemá přiřazenou jednu z těchto rolí správce, přistupuje k portálu, může mít uživatel přístup k určitým částem prostředí. Uživatelská role hosta má některá oprávnění v adresáři.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Můžu přístup k Azure Portal zablokovat pro uživatele typu Host?

Ano. Můžete vytvořit zásadu podmíněného přístupu, která zablokuje všem uživatelům typu Host a externím uživatelům přístup k Azure Portal. Při konfiguraci této zásady buďte opatrní, abyste se vyhnuli nechtěnému blokování přístupu ke členům a správcům.

1. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com/) jako správce zabezpečení nebo správce podmíněného přístupu.
2. Na portálu Azure Portal vyberte **Azure Active Directory**. 
3. V části **Spravovat** vyberte **zabezpečení**.
4. V části **chránit** vyberte **podmíněný přístup**. Vyberte **nové zásady**.
5. Na **nové** stránce zadejte do textového pole **název** název zásady (například zablokovat hostům přístup k portálu).
6. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
7. Na kartě **Zahrnout** zvolte **Vybrat uživatele a skupiny** a pak vyberte **Všichni uživatelé typu Host a externí uživatelé (Preview)**.
9. Vyberte **Hotovo**.
10. Na **nové** stránce v části **přiřazení** vyberte **cloudové aplikace nebo akce**.
11. Na stránce **cloudové aplikace nebo akce** zvolte **vybrat aplikace** a pak zvolte **Vybrat**.
12. Na stránce **Vybrat** zvolte **Microsoft Azure Management** a potom zvolte **Vybrat**.
13. Na stránce **cloudové aplikace nebo akce** vyberte **Hotovo**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Podporuje spolupráce Azure AD B2B službu Multi-Factor Authentication a e-mailové účty uživatelů?
Ano. Služba Multi-Factor Authentication a e-mailové e-mailové účty se podporují pro spolupráci Azure AD B2B.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Podporujete resetování hesla pro uživatele spolupráce Azure AD B2B?
Pokud je váš tenant služby Azure AD domovským adresářem pro uživatele, můžete [resetovat heslo uživatele](../fundamentals/active-directory-users-reset-password-azure-portal.md) z Azure Portal. Nemůžete ale přímo obnovit heslo pro uživatele typu Host, který se přihlásí pomocí účtu spravovaného jiným adresářem služby Azure AD nebo externím zprostředkovatelem identity. Heslo může resetovat jenom uživatel typu Host nebo správce v domovském adresáři uživatele. Tady je několik příkladů, jak funguje resetování hesla pro uživatele typu Host:
 
* Uživatelé typu Host v tenantovi Azure AD s označením Host (UserType = = Host) se nemůžou registrovat pro SSPR prostřednictvím [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Tento typ uživatele typu Host může provádět pouze SSPR prostřednictvím [https://aka.ms/sspr](https://aka.ms/sspr) . 
* Uživatelé typu Host, kteří se přihlásí pomocí účet Microsoft (například guestuser@live.com ), mohou resetovat vlastní hesla pomocí účet Microsoft samoobslužného resetování hesla (SSPR). Přečtěte si informace [o resetování hesla účet Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Uživatelé typu Host, kteří se přihlásí pomocí účtu Google nebo jiného externího zprostředkovatele identity, můžou resetovat svoje vlastní hesla pomocí metody SSPR zprostředkovatele identity. Uživatel typu host s účtem Google guestuser@gmail.com může například resetovat heslo podle pokynů v tématu [Změna nebo resetování hesla](https://support.google.com/accounts/answer/41078).
* Pokud je tenant identity tenanta JIT (just-in-time) nebo "virová" tenant (což znamená, že se jedná o samostatného nespravovaného tenanta Azure), může heslo resetovat jenom uživatel typu Host. Někdy organizace [převezme správu virového tenanta](../enterprise-users/domains-admin-takeover.md) , které se vytvoří, když zaměstnanci použijí své pracovní e-mailové adresy k registraci služeb. Až organizace převezme klienta virového, může resetování hesla uživatele nebo povolení SSPR jenom správce v této organizaci. V případě potřeby můžete jako pozvánku do organizace odebrat uživatelský účet hosta z adresáře a znovu odeslat pozvánku.

* Pokud je domovský adresář uživatele typu Host vaším klientem služby Azure AD, můžete resetovat heslo uživatele. Mohli jste například vytvořit uživatele nebo synchronizovat uživatele z místní služby Active Directory a nastavit jejich UserType na hodnotu Host. Vzhledem k tomu, že se tento uživatel nachází v adresáři, můžete resetovat heslo z Azure Portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Poskytuje Microsoft Dynamics 365 podporu spolupráce B2B Azure AD online?
Ano, Dynamics 365 (online) podporuje spolupráci Azure AD B2B. Další informace najdete v článku o řešení Dynamics 365, který [pozvaní uživatelů s spoluprací B2B v Azure AD](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Jaká je doba života počátečního hesla nově vytvořeného uživatele spolupráce B2B?
Azure AD má pevně danou sadu znaků, sílu hesla a požadavky na uzamčení účtu, které platí stejně pro všechny uživatelské účty cloudu Azure AD. Uživatelské účty cloudu jsou účty, které nejsou federované s jiným poskytovatelem identity, jako je například 
* Účet Microsoft
* Facebook
* Active Directory Federation Services
* Jiný tenant cloudu (pro spolupráci B2B)

U federovaných účtů Zásady hesel závisí na zásadách, které se uplatní v místní architektuře, a v nastaveních účet Microsoft uživatele.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizace může chtít mít ve svých aplikacích různá prostředí pro uživatele klientů a uživatele typu Host. Máte pro vás standardní pokyny? Je přítomnost zprostředkovatele identity správné použití modelu?
Uživatel typu Host může k ověření použít libovolného poskytovatele identity. Další informace najdete v tématu [Vlastnosti uživatele spolupráce B2B](user-properties.md). Pomocí vlastnosti **usertype** Určete činnost koncového uživatele. Deklarace **usertype** není aktuálně zahrnutá v tokenu. Aplikace by měly používat rozhraní Microsoft Graph API k dotazování adresáře pro uživatele a získání UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Kde můžu najít komunitu spolupráce B2B ke sdílení řešení a odesílání nápadů?
Nepřetržitě posloucháme vaše názory a vylepšujeme spolupráci B2B. Sdílejte prosím své uživatelské scénáře, osvědčené postupy a co byste chtěli o spolupráci Azure AD B2B. Připojte se k diskusi v [technické komunitě Microsoftu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Pomůžeme vám také podávat nápady a hlasovat pro budoucí funkce v [nápadech spolupráce B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Můžeme poslat pozvánku, která se automaticky provedla, aby byl uživatel jenom "připravený k přechodu"? Nebo se musí pokaždé proklikat přes adresu URL pro uplatnění žádosti?
Další uživatele v partnerské organizaci můžete pozvat pomocí uživatelského rozhraní, skriptů PowerShellu nebo rozhraní API. Pak můžete uživateli typu Host poslat přímý odkaz na sdílenou aplikaci. Ve většině případů už není nutné otevírat e-mailovou pozvánku a kliknout na adresu URL pro uplatnění. Viz [Azure Active Directory uplatnění pozvánky B2B pro spolupráci](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Jak funguje spolupráce B2B, pokud pozvaný partner k přidání vlastního místního ověřování používá federování?
Pokud má partner tenanta Azure AD federovaného do místní ověřovací infrastruktury, automaticky bude dosaženo místního jednotného přihlašování (SSO). Pokud partner nemá tenanta Azure AD, vytvoří se pro nové uživatele účet služby Azure AD. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Myslelo se, že služba Azure AD B2B nepřijímala e-mailové adresy gmail.com a outlook.com a tato B2C se použila pro tyto typy účtů?
Odstraňujeme rozdíly mezi spoluprací B2B a B2C (Business-to-Consumer), z nichž jsou identity podporované. Použitá identita není dobrým důvodem pro výběr mezi použitím B2B nebo pomocí B2C. Informace o volbě možnosti spolupráce najdete v tématu [porovnání spolupráce B2B a B2C v Azure Active Directory](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Může se místní účet Azure AD B2C pozvat do tenanta Azure AD pro spolupráci B2B?
No. Místní účet Azure AD B2C lze použít pouze pro přihlášení k tenantovi Azure AD B2C. Účet se nedá použít k přihlášení do tenanta Azure AD. Pozvání Azure AD B2C místního účtu do tenanta Azure AD pro spolupráci B2B není podporované.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jaké aplikace a služby podporují uživatele typu Host Azure B2B?
Všechny aplikace integrované s Azure AD můžou podporovat uživatele typu Host Azure B2B, ale musí používat koncový bod nastavený jako tenant k ověřování uživatelů typu Host. Je také možné, že budete muset [přizpůsobit deklarace identity](claims-mapping.md) v tokenu SAML, který je vydaný, když se uživatel typu Host ověří do aplikace. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Můžeme vynutit vícefaktorové ověřování pro uživatele typu hosta B2B, pokud naši partneři nepoužívají službu Multi-Factor Authentication?
Ano. Další informace najdete v tématu [podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>V SharePointu můžete pro externí uživatele definovat seznam "Povolit" nebo "Odepřít". Můžete to udělat v Azure?
Ano. Spolupráce B2B Azure AD podporuje seznamy povolených a zakázaných seznamů. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jaké licence potřebujeme k používání Azure AD B2B?
Informace o tom, jaké licence vaše organizace potřebuje k používání Azure AD B2B, najdete v tématu [ceny externích identit](external-identities-pricing.md).

### <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)