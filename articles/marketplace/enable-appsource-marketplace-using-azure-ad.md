---
title: Integrace nabídky Microsoft Commercial Marketplace s Azure Active Directory
description: K ověření Microsoft AppSource a Azure Marketplace nabídek použijte Azure Active Directory.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: a6e304e5ffeab8f0a44cbdfe1566465f2b9bf34a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88607421"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integrujte svůj výpis na komerčním trhu s Azure Active Directory

 Tento článek vysvětluje požadavky na integraci výpisu nebo nabídky komerčního tržiště pomocí Azure Active Directory (Azure AD). Azure AD je cloudová služba identit, která pomocí standardních platforem umožňuje ověřování pomocí účet Microsoft. [Přečtěte si další informace o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Výhody Azure AD

Zákazníci Microsoft AppSource a Azure Marketplace používají prostředí v produktu k hledání katalogů seznamů Online Storu. Tyto akce vyžadují, aby se zákazníci přihlásili k produktu. Integrace Azure AD přináší následující výhody:

- Rychlejší zapojení a optimalizované prostředí pro zákazníky
- Jednotné přihlašování (SSO) pro miliony podnikových uživatelů
- Konzistentní prostředí pro přihlašování napříč aplikacemi, které publikovali různí partneři
- Škálovatelné ověřování více platforem pro mobilní a cloudové aplikace

## <a name="offers-that-require-azure-ad"></a>Nabídky vyžadující Azure AD

Různé možnosti výpisu komerčního tržiště [a typy nabídek](determine-your-listing-type.md) mají různé požadavky na implementaci Azure AD. Podrobnosti najdete v následující tabulce.

| Typ nabídky    | Služba Azure AD SSO vyžaduje, aby mě kontaktovala?  | Vyžaduje se jednotné přihlašování Azure AD pro zkušební verzi? | Vyžaduje se jednotné přihlašování Azure AD pro testovací jednotku?  | Pro Transact se vyžaduje jednotné přihlašování Azure AD. |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Virtuální počítač | Není k dispozici | No | No | No |
| Aplikace Azure (šablona řešení)  | N/A | N/A | N/A | N/A |
| Spravované aplikace  | N/A | N/A | N/A | No |
| SaaS  | No | Yes | Yes | Yes |
| Kontejnery  | N/A | N/A | N/A | No |
| Konzultační služby  | No | N/A | N/A | N/A |

Další informace o technických požadavcích SaaS najdete v tématu [Azure AD a SaaS nabídky v obchodě na komerčním webu Marketplace](./azure-ad-saas.md).

## <a name="azure-ad-integration"></a>Integrace Azure AD

- Podrobnosti o tom, jak integrovat Azure AD pro nabídky SaaS (software jako služba), najdete [v tématu Azure AD a SaaS nabídky na komerčním webu Marketplace](./azure-ad-saas.md).
- Informace o tom, jak povolit jednotné přihlašování integrací služby Azure AD do svého seznamu, najdete v tématu [Azure Active Directory pro vývojáře](../active-directory/develop/index.yml).
- Pokud chcete získat podrobné informace o jednotném přihlašování Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md).

## <a name="enable-a-trial-listing"></a>Povolit výpis zkušební verze

Automatické nastavení zákazníků může zvýšit pravděpodobnost převodu. Když zákazník vybere váš výpis vaší zkušební verze a bude přesměrován do vašeho zkušebního prostředí, můžete zákazníka nastavit přímo bez nutnosti dalších kroků pro přihlášení.

Při ověřování Azure AD pošle token vaší aplikaci nebo nabídce. Informace o uživateli poskytované tokenem umožňují vytvořit uživatelský účet v aplikaci nebo nabídce. Další informace najdete v tématu [ukázkové tokeny](../active-directory/develop/id-tokens.md).

Když použijete Azure AD k povolení ověřování jedním kliknutím ve vaší aplikaci nebo v seznamu zkušebních verzí, můžete:

- Zjednodušte si prostředí pro zákazníky z komerčního tržiště až po výpis vaší zkušební verze.
- Udržujte si dojem o prostředí v produktu i v případě, že je uživatel přesměrován z komerčního tržiště do vaší domény nebo zkušebního prostředí.
- Snižte pravděpodobnost zrušení při přesměrování uživatelů, protože nejsou k dispozici žádné další kroky pro přihlášení.
- Snižte překážky nasazení pro velké populace uživatelů Azure AD.

## <a name="verify-azure-ad-integration"></a>Ověření integrace služby Azure AD

### <a name="multitenant-solutions"></a>Víceklientské řešení

Pomocí Azure AD můžete podporovat tyto akce:

- Zaregistrujte svou aplikaci v jednom z komerčních obchodů online pro obchod Marketplace. Další informace najdete v zobrazení [Registrace aplikace](../active-directory/develop/quickstart-register-app.md) nebo [certifikace AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md) .
- Povolení funkce podpory víceklientské architektury ve službě Azure AD pro získání zkušebního prostředí jedním kliknutím.

Pokud s použitím federovaného jednotného přihlašování Azure AD začínáte, proveďte tyto kroky:

1. Zaregistrujte svou aplikaci na komerčním webu Marketplace.
1. K vývoji jednotného přihlašování pomocí Azure AD použijte [OAuth 2,0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) nebo [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Povolení funkce podpory víceklientské architektury ve službě Azure AD za účelem poskytování zkušebního prostředí jedním kliknutím.

### <a name="single-tenant-solutions"></a>Řešení pro jednoho tenanta

Pomocí Azure AD můžete podporovat jednu z následujících akcí:

- Přidejte uživatele typu Host do adresáře pomocí [Azure AD B2B](../active-directory/b2b/what-is-b2b.md).
- Ručně nastavte zkušební verze pro zákazníky pomocí možnosti publikování v **kontaktu** .
- Vývoj testovacích jednotek pro zákazníka
- Vytvořte ukázkovou ukázkovou aplikaci pro více tenantů, která používá jednotné přihlašování.

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, 

- [Přečtěte si](https://azuremarketplace.microsoft.com/sell) o komerčním tržišti.

Pokud se chcete zaregistrovat v partnerském centru, začněte vytvářet novou nabídku nebo pracovat na stávajícím:

- [Přihlaste se do partnerského centra](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) a vytvořte nebo dokončete vaši nabídku.
