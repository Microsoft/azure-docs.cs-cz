---
title: Nejčastější dotazy ke spolupráci b2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: Získejte odpovědi na nejčastější dotazy týkající se spolupráce služby Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92c3e0d77a26db406e24d6d2fa07e96349613634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050824"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Časté otázky pro spolupráci služby Azure Active Directory B2B

Tyto často kladené otázky (FAQ) o spolupráci Azure Active Directory (Azure AD) business-to-business (B2B) jsou pravidelně aktualizovány tak, aby zahrnovaly nová témata.

   > [!IMPORTANT]
   > **března 2021**, Microsoft již nebude podporovat uplatnění pozvánek vytvořením nespravované účty Azure AD a tenanty pro scénáře spolupráce B2B. V rámci přípravy doporučujeme zákazníkům, aby se přihlásili k [jednorázovému ověření hesla e-mailem](one-time-passcode.md). Vítáme vaši zpětnou vazbu k této funkci veřejného náhledu a jsme nadšeni, že můžeme vytvořit ještě více způsobů spolupráce.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Můžeme přizpůsobit naši přihlašovací stránku tak, aby byla intuitivnější pro naše uživatele s hosty pro spolupráci B2B?
Samozřejmě. Podívejte se na náš [blog post o této funkci](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Další informace o přizpůsobení přihlašovací stránky organizace naleznete v tématu [Přidání firemní značky pro přihlášení a stránky přístupového panelu](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Můžou uživatelé b2B spolupráce přistupovat k SharePointu Online a OneDrivu?
Ano. Možnost vyhledávat existující uživatele typu Host v SharePointu Online pomocí výběru osob je však ve výchozím nastavení **vypnutá.** Chcete-li zapnout možnost vyhledávání existujících uživatelů typu Host, nastavte **možnost ShowPeoplePickerSuggestionsForGuestUsers** na **Zapnuto**. Toto nastavení můžete zapnout buď na úrovni klienta, nebo na úrovni kolekce webů. Toto nastavení můžete změnit pomocí rutin Set-SPOTenant a Set-SPOSite. Pomocí těchto rutin mohou členové prohledávat všechny existující uživatele typu Host v adresáři. Změny v oboru klienta nemají vliv na weby SharePointu Online, které už byly zřízeny.

### <a name="is-the-csv-upload-feature-still-supported"></a>Je funkce nahrávání csv stále podporována?
Ano. Další informace o použití funkce nahrávání souboru .csv naleznete v [této ukázce prostředí PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak si můžu přizpůsobit e-maily s pozvánkami?
Můžete přizpůsobit téměř vše o procesu pozvat pomocí [b2B pozvání API](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Mohou uživatelé typu Host obnovit svou vícefaktorovou metodu ověřování?
Ano. Uživatelé typu Host mohou resetovat svou vícefaktorovou metodu ověřování stejným způsobem jako běžní uživatelé.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Která organizace je zodpovědná za vícefaktorové ověřování licencí?
Zvoucí organizace provádí vícefaktorové ověřování. Zvoucí organizace musí zajistit, aby organizace má dostatek licencí pro své uživatele B2B, kteří používají vícefaktorové ověřování.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co když partnerská organizace již má vícefaktorové ověřování nastaveno? Můžeme důvěřovat jejich vícefaktorovéau ověřování a nepoužívat vlastní vícefaktorové ověřování?
Tato funkce není v současné době podporována. Pokud přístup k prostředkům vaší organizace vyžaduje vícefaktorové ověřování, partnerská organizace se bude muset zaregistrovat pro vícefaktorové ověřování ve vaší (zvoucí) organizaci.

### <a name="how-can-i-use-delayed-invitations"></a>Jak mohu použít zpožděné pozvánky?
Organizace může chtít přidat uživatele spolupráce B2B, zřídit je aplikacím podle potřeby a pak odeslat pozvánky. Pomocí rozhraní API pro pozvání ke spolupráci B2B můžete přizpůsobit pracovní postup registrace.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Mohu uživatele typu Host zviditelnit v globálním seznamu adres serveru Exchange?
Ano. Objekty hosta nejsou ve výchozím nastavení viditelné v globálním seznamu adres vaší organizace, ale můžete je zviditelnit pomocí prostředí Azure Active Directory PowerShell. Viz [Lze objekty hosta zviditelnit v globálním seznamu adres?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Mohu z uživatele typu Host udělat omezeného správce?
Jistě. Další informace naleznete [v tématu Přidání uživatelů typu Host do role](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Umožňuje spolupráce Azure AD B2B uživatelům B2B přístup k portálu Azure?
Pokud uživateli není přiřazena role omezeného správce, uživatelé spolupráce B2B nebudou vyžadovat přístup k portálu Azure. Uživatelé spolupráce B2B, kterým je přiřazena role omezeného správce, však mají přístup k portálu. Také pokud uživatel hosta, kterému není přiřazena jedna z těchto rolí správce, přistupuje k portálu, může mít uživatel přístup k určitým částem prostředí. Role uživatele typu Host má v adresáři určitá oprávnění.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Můžu blokovat přístup k portálu Azure pro uživatele typu Host?

Ano! Můžete vytvořit zásadu podmíněného přístupu, která všem uživatelům hosta a externím uživatelům zablokuje přístup k portálu Azure. Při konfiguraci této zásady dávejte pozor, abyste neúmyslně neblokovali přístup členům a správcům.

1. Přihlaste se na [svůj portál Azure](https://portal.azure.com/) jako správce zabezpečení nebo správce podmíněného přístupu.
2. Na portálu Azure Portal vyberte **Azure Active Directory**. 
3. V části **Manage**vyberte **Zabezpečení**.
4. V části **Chránit**vyberte **možnost Podmíněný přístup**. Vyberte **nové zásady**.
5. Na stránce **Nový** zadejte do textového pole **Název** název zásady (například "Blokovat hostům přístup k portálu").
6. V části **Přiřazení** vyberte **Uživatelé a skupiny**.
7. Na kartě **Zahrnout** zvolte **Vybrat uživatele a skupiny**a pak vyberte **Všichni hosta a externí uživatele (náhled).**
9. Vyberte **Done** (Hotovo).
10. Na stránce **Nový** vyberte v části **Úlohy** **cloudové aplikace nebo akce**.
11. Na stránce **Cloudové aplikace nebo akce** zvolte **Vybrat aplikace**a pak **zvolte Vybrat**.
12. Na stránce **Vybrat** zvolte **Microsoft Azure Management** a potom zvolte **Vybrat**.
13. Na stránce **Cloudové aplikace nebo akce** vyberte **Hotovo**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Podporuje spolupráce azure ad B2B vícefaktorové ověřování a spotřebitelské e-mailové účty?
Ano. Vícefaktorové ověřování a spotřebitelské e-mailové účty jsou podporovány pro spolupráci Azure AD B2B.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Podporujete resetování hesla pro uživatele spolupráce Azure AD B2B?
Pokud je váš klient Azure AD domovský adresář pro uživatele, můžete [obnovit heslo uživatele](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) z webu Azure Portal. Ale nemůžete přímo obnovit heslo pro uživatele typu Host, který se přihlásí pomocí účtu, který je spravovaný jiným adresářem Azure AD nebo externím poskytovatelem identity. Heslo může resetovat pouze uživatel typu Host nebo správce v domovském adresáři uživatele. Zde je několik příkladů, jak funguje resetování hesla pro uživatele typu Host:
 
* Uživatelé typu Host, kteří se guestuser@live.compřihlásí pomocí účtu Microsoft (například), mohou resetovat svá vlastní hesla pomocí samoobslužného resetování hesla účtu Microsoft (SSPR). Přečtěte si [článek Jak obnovit heslo k účtu Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Uživatelé typu Host, kteří se přihlásí pomocí účtu Google nebo jiného externího poskytovatele identity, mohou resetovat vlastní hesla pomocí metody samoresetování poskytovatele identity. Uživatel typu Host s účtem guestuser@gmail.com Google může například obnovit své heslo podle pokynů v části [Změna nebo resetování hesla](https://support.google.com/accounts/answer/41078).
* Pokud je klient identity klienta just-in-time (JIT) nebo "virové" tenanta (což znamená, že je samostatný, nespravované azure tenanta), pouze uživatel typu Host můžete resetovat své heslo. Někdy organizace [převezme správu virových tenantů,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) které jsou vytvořeny, když zaměstnanci používají své pracovní e-mailové adresy k registraci služeb. Poté, co organizace převezme virové tenanta, pouze správce v této organizaci můžete obnovit heslo uživatele nebo povolit sspr. V případě potřeby můžete jako zvoucí organizace odebrat uživatelský účet hosta z adresáře a znovu odeslat pozvánku.

* Pokud je domovský adresář uživatele typu Host vaším klientem Azure AD, můžete resetovat heslo uživatele. Můžete například vytvořit uživatele nebo synchronizovat uživatele z místní služby Active Directory a nastavit jeho usertype na Guest. Vzhledem k tomu, že tento uživatel je doma ve vašem adresáři, můžete obnovit své heslo z webu Azure Portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Poskytuje Microsoft Dynamics 365 online podporu pro spolupráci Azure AD B2B?
Ano, Dynamics 365 (online) podporuje spolupráci Azure AD B2B. Další informace najdete v článku Dynamics 365 [Pozvat uživatele se spoluprací azure ad b2b](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Jaká je životnost počátečního hesla pro nově vytvořeného uživatele spolupráce B2B?
Azure AD má pevnou sadu znaků, sílu hesla a požadavky na uzamčení účtu, které platí stejně pro všechny uživatelské účty cloudu Azure AD. Uživatelské účty cloudu jsou účty, které nejsou federovány s jiným poskytovatelem identity, například 
* Účet Microsoft
* Facebook
* Active Directory Federation Services
* Další tenant cloudu (pro spolupráci B2B)

U federovaných účtů zásady hesel závisí na zásadách, které se používají v místním nájmu, a na nastavení účtu Microsoft uživatele.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizace může chtít mít různé možnosti ve svých aplikacích pro uživatele klienta a uživatele typu Host. Existuje pro to standardní pokyny? Je přítomnost zprostředkovatele identity nárokovat správný model použít?
Uživatel typu Host může k ověření použít libovolného poskytovatele identity. Další informace naleznete [v tématu Vlastnosti uživatele spolupráce B2B](user-properties.md). Pomocí vlastnosti **UserType** určete uživatelské prostředí. Deklarace **Typu UserType** není aktuálně zahrnuta v tokenu. Aplikace by měly používat rozhraní Microsoft Graph API k dotazování adresáře pro uživatele a získat UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Kde najdu komunitu pro spolupráci b2B, která bude sdílet řešení a předkládat nápady?
Neustále nasloucháme vaší zpětné vazbě, abychom zlepšili spolupráci B2B. Prosím, podělte se o své uživatelské scénáře, osvědčené postupy a co se vám líbí o spolupráci Azure AD B2B. Zapojte se do diskuse v technické [komunitě Microsoftu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Zveme vás také k odeslání vašich nápadů a hlasovat pro budoucí funkce na [B2B Spolupráce Myšlenky](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Můžeme poslat pozvánku, která je automaticky uplatněna, aby byl uživatel připraven k odeslání? Nebo se musí pokaždé proklikat přes adresu URL pro uplatnění žádosti?
Můžete pozvat další uživatele v partnerské organizaci pomocí uživatelského prostředí, skriptů prostředí PowerShell nebo api. Potom můžete uživateli typu Host poslat přímý odkaz na sdílenou aplikaci. Ve většině případů již není nutné otevírat e-mailovou pozvánku a kliknout na adresu URL pro využití. Podívejte se [na uplatnění pozvánky na spolupráci služby Azure Active Directory B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Jak funguje spolupráce B2B, pokud pozvaný partner k přidání vlastního místního ověřování používá federování?
Pokud má partner tenanta Azure AD federovaného do místní ověřovací infrastruktury, automaticky bude dosaženo místního jednotného přihlašování (SSO). Pokud partner nemá klienta Azure AD, vytvoří se účet Azure AD pro nové uživatele. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Myslel jsem, že Azure AD B2B nepřijal gmail.com a outlook.com e-mailové adresy a že B2C byl použit pro tyto typy účtů?
Odstraňujeme rozdíly mezi b2b a business-to-consumer (B2C) spolupráce, pokud jde o identity, které jsou podporovány. Použitá identita není dobrým důvodem k výběru mezi použitím B2B nebo b2c. Informace o výběru možnosti spolupráce najdete [v tématu Porovnání spolupráce B2B a B2C ve službě Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jaké aplikace a služby podporují uživatele typu Host Azure B2B?
Všechny aplikace integrované s Azure AD můžou podporovat uživatele typu Host Azure B2B, ale musí k ověřování uživatelů typu Host použít koncový bod nastavený jako tenant. Možná také budete muset [přizpůsobit deklarace identity](claims-mapping.md) v tokenu SAML, který je vydán při ověření uživatele typu Host do aplikace. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Můžeme vynutit vícefaktorové ověřování pro uživatele typu Host B2B, pokud naši partneři nemají vícefaktorové ověřování?
Ano. Další informace naleznete [v tématu Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>V SharePointu můžete definovat seznam "povolit" nebo "odepřít" pro externí uživatele. Můžeme to udělat v Azure?
Ano. Spolupráce Azure AD B2B podporuje povolit seznamy a odepřít seznamy. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jaké licence potřebujeme k použití Azure AD B2B?
Informace o tom, jaké licence vaše organizace potřebuje k použití Azure AD B2B, najdete v [tématu Pokyny k licencování pro spolupráci služby Azure Active Directory B2B](licensing-guidance.md).

### <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)

