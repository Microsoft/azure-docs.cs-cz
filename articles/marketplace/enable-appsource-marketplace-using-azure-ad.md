---
title: Povolení Microsoft AppSource a Azure Marketplace výpisu pomocí Azure Active Directory | Azure
description: Povolit typ výpisu pomocí Azure Active Directory v Azure Marketplace a AppSource pro vydavatele aplikací a služeb.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 0b68687a2bbaa817f2776757ccab9571638c0fd5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876004"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Povolení uvedení na AppSource a Marketplace s využitím Azure Active Directory

 Azure Active Directory (Azure AD) je cloudová služba identit, která umožňuje ověřování pomocí účet Microsoft. Azure AD používá Oborová standardní rozhraní. [Přečtěte si další informace o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Výhody Azure AD

Zákazníci Microsoft AppSource a Azure Marketplace používají prostředí v rámci produktu k prohledávání katalogů výpisů. Tyto akce vyžadují, aby se zákazníci přihlásili k produktu. Integrace Azure AD přináší následující výhody:

- Rychlejší zapojení a optimalizované prostředí pro zákazníky
- Jednotné přihlašování (SSO) pro miliony podnikových uživatelů
- Konzistentní prostředí pro přihlašování napříč aplikacemi, které publikovali různí partneři
- Škálovatelné ověřování více platforem pro mobilní a cloudové aplikace

## <a name="offers-that-require-azure-ad"></a>Nabídky vyžadující Azure AD

Různé [Možnosti výpisu a typy nabídek](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) pro AppSource a Azure Marketplace mají různé požadavky na implementaci Azure AD. Podrobnosti najdete v následující tabulce:

| **Typ nabídky**    | **Vyžaduje se jednotné přihlašování Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontaktujte mě | Zkušební verze | Testovací verze | Transakce |
| Virtuální počítač | Není k dispozici | Ne | Ne | Ne |
| Aplikace Azure (šablona řešení)  | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| Spravované aplikace  | Není k dispozici | Není k dispozici | Není k dispozici | Ne |
| SaaS  | Ne | Ano | Ano | Ano |
| Containers  | Není k dispozici | Není k dispozici | Není k dispozici | Ne |
| Konzultační služby  | Ne | Není k dispozici | Není k dispozici | Není k dispozici |

Další informace o technických požadavcích na SaaS najdete v tématu [SaaS Applications nabízí průvodce publikováním](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integrace Azure AD

- Informace o tom, jak povolit jednotné přihlašování integrací služby Azure AD do svého seznamu, najdete v tématu [Azure Active Directory pro vývojáře]( https://aka.ms/aaddev).
- Pokud chcete získat podrobné informace o jednotném přihlašování Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Povolit výpis zkušební verze

Automatické nastavení zákazníků může zvýšit pravděpodobnost převodu. Když zákazník vybere váš výpis vaší zkušební verze a bude přesměrován do vašeho zkušebního prostředí, můžete zákazníka nastavit přímo bez nutnosti dalších kroků pro přihlášení.

Při ověřování Azure AD pošle token vaší aplikaci nebo nabídce. Informace o uživateli poskytované tokenem umožňují vytvořit uživatelský účet v aplikaci nebo nabídce. Další informace najdete v tématu [ukázkové tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Když použijete Azure AD k povolení ověřování jedním kliknutím ve vaší aplikaci nebo v seznamu zkušebních verzí, můžete:

- Zjednodušte si prostředí pro zákazníky z webu Marketplace až po výpis vaší zkušební verze.
- Udržujte si dojem prostředí v produktu i v případě, že je uživatel přesměrován z webu Marketplace do vaší domény nebo zkušebního prostředí.
- Snižte pravděpodobnost zrušení při přesměrování uživatelů, protože nejsou k dispozici žádné další kroky pro přihlášení.
- Snižte překážky nasazení pro velké populace uživatelů Azure AD.

## <a name="verify-azure-ad-integration"></a>Ověření integrace služby Azure AD

### <a name="multitenant-solutions"></a>Víceklientské řešení

Pomocí Azure AD můžete podporovat tyto akce:

- Zaregistrujte svou aplikaci v některém z prodejní místa na webu Marketplace. Další informace najdete v zobrazení [Registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) nebo [certifikace AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) .
- Povolení funkce podpory víceklientské architektury ve službě Azure AD pro získání zkušebního prostředí jedním kliknutím.

Pokud s použitím federovaného jednotného přihlašování Azure AD začínáte, proveďte tyto kroky:

1. Zaregistrujte svou aplikaci na webu Marketplace.
1. K vývoji jednotného přihlašování pomocí Azure AD použijte [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) nebo [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Povolení funkce podpory víceklientské architektury ve službě Azure AD za účelem poskytování zkušebního prostředí jedním kliknutím.

### <a name="single-tenant-solutions"></a>Řešení pro jednoho tenanta

Pomocí Azure AD můžete podporovat jednu z následujících akcí:

- Přidejte uživatele typu Host do adresáře pomocí [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ručně nastavte zkušební verze pro zákazníky pomocí možnosti publikování v **kontaktu** .
- Vývoj testovacích jednotek pro zákazníka
- Vytvořte ukázkovou ukázkovou aplikaci pro více tenantů, která používá jednotné přihlašování.

## <a name="next-steps"></a>Další postup

- Ujistěte se, že jste zaregistrováni [v Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Další informace o tom, jak vytvořit nebo dokončit vaši nabídku, najdete v tématu [Postup vytvoření účtu partnerského centra](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) .
