---
title: Povolení výpisu Microsoft AppSource a Azure Marketplace pomocí Služby Azure Active Directory | Azure
description: Povolte typ výpisu pomocí Azure Active Directory na Azure Marketplace a AppSource pro vydavatele aplikací a služeb.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dsindona
ms.openlocfilehash: 45855038e60dcdc3be4f98cfdceed69df5e8c946
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286315"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Povolení uvedení na AppSource a Marketplace s využitím Azure Active Directory

 Azure Active Directory (Azure AD) je cloudová identitová služba, která umožňuje ověřování pomocí účtu Microsoft. Azure AD používá standardní architektury. [Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Výhody Azure AD

Zákazníci Microsoft AppSource a Azure Marketplace používají k vyhledávání v katalogech výpisů prostředí v produktu. Tyto akce vyžadují, aby se zákazníci k produktu přihlásili. Integrace Azure AD poskytuje následující výhody:

- Rychlejší zapojení a optimalizované prostředí pro zákazníky
- Jednotné přihlašování (SSO) pro miliony podnikových uživatelů
- Konzistentní, přihlašovací prostředí napříč aplikacemi publikovanými různými partnery
- Škálovatelné ověřování napříč platformami pro mobilní a cloudové aplikace

## <a name="offers-that-require-azure-ad"></a>Nabídky, které vyžadují Azure AD

Různé [možnosti výpisu a typy nabídek](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) pro AppSource a Azure Marketplace mají různé požadavky na implementaci Azure AD. Podrobnosti naleznete v následující tabulce:

| **Typ nabídky**    | **Je vyžadováno přisychávací služby Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontaktujte mě | Zkušební verze | Testovací verze | Transact |
| Virtuální počítač | Není dostupné. | Ne | Ne | Ne |
| Aplikace Azure (šablona řešení)  | Není dostupné. | Není dostupné. | Není dostupné. | Není dostupné. |
| Spravované aplikace  | Není dostupné. | Není dostupné. | Není dostupné. | Ne |
| SaaS  | Ne | Ano | Ano | Ano |
| Kontejnery  | Není dostupné. | Není dostupné. | Není dostupné. | Ne |
| Konzultační služby  | Ne | Není dostupné. | Není dostupné. | Není dostupné. |

Další informace o technických požadavcích SaaS naleznete v [průvodci nabídkou publikování aplikací SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integrace Azure AD

- Informace o tom, jak povolit jednotné přihlašování integrací Azure AD do výpisu, najdete v [tématu Azure Active Directory pro vývojáře]( https://aka.ms/aaddev).
- Podrobnosti o jednotném přihlašování Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Povolení zkušebního zápisu

Automatické nastavení zákazníka může zvýšit pravděpodobnost převodu. Když zákazník vybere zkušební zápis a bude přesměrován do zkušebního prostředí, můžete ho nastavit přímo, aniž byste museli vyžadovat další kroky přihlášení.

Během ověřování Azure AD odešle token do vaší aplikace nebo nabídky. Informace o uživateli poskytnuté tokenem umožňují vytvoření uživatelského účtu ve vaší aplikaci nebo nabídce. Další informace naleznete v [tématu Ukázkové tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Když pomocí Azure AD povolíte ověřování jedním kliknutím ve vaší aplikaci nebo zkušebním zápisu, tak:

- Zjednodušte zákaznickou zkušenost od Marketplace až po zkušební zápis.
- Udržujte pocit prostředí v produktu i v případě, že je uživatel přesměrován z webu Marketplace do vaší domény nebo zkušebního prostředí.
- Snižte pravděpodobnost opuštění při přesměrování uživatelů, protože neexistují žádné další kroky přihlášení.
- Snižte bariéry nasazení pro velké množství uživatelů Azure AD.

## <a name="verify-azure-ad-integration"></a>Ověření integrace Azure AD

### <a name="multitenant-solutions"></a>Víceklientská řešení

Pomocí azure ad pro podporu následujícíakce:

- Zaregistrujte svou aplikaci v jedné z výlalech Marketplace. Zobrazit [registraci aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) nebo [appsource certifikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) pro další informace.
- Povolte funkci podpory multitenancy ve službě Azure AD, abyste získali zkušební prostředí s jedním kliknutím.

Pokud s používáním federovaného jednotného přihlašování Azure AD tečujete, postupujte takto:

1. Zaregistrujte svou aplikaci na Marketplace.
1. Vývoj přisávacího zabezpečení s Azure AD pomocí [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) nebo [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Povolte funkci podpory multitenancy ve službě Azure AD a poskytněte zkušební prostředí s jedním kliknutím.

### <a name="single-tenant-solutions"></a>Řešení pro jednoho klienta

Pomocí azure ad pro podporu jedné z následujících akcí:

- Přidejte uživatele typu Host do adresáře pomocí [azure ad b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ručně nastavte zkušební verze pro zákazníky pomocí možnosti **Publikování kontaktujte mě.**
- Vyvíjejte testovací jízdu pro zákazníka.
- Vytvořte ukázkovou aplikaci pro více klientů, která používá služby SSO.

## <a name="next-steps"></a>Další kroky

- Ujistěte se, že jste se [zaregistrovali na Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Další informace o tom, jak vytvořit nebo dokončit nabídku, najdete v tématu [Jak vytvořit účet Centra pro partnery.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)
