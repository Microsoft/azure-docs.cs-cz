---
title: Povolení výpis Microsoft AppSource a webu Azure Marketplace pomocí služby Azure Active Directory | Azure
description: Povolte typ výpisu pomocí Azure Active Directory v Azure Marketplace a AppSource pro vydavatele aplikace a služby.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 3ad9308f3bc714ee2877627da8fdb328459b9fe4
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351945"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Povolení AppSource a Marketplace seznam pomocí služby Azure Active Directory

 Azure Active Directory (Azure AD) je Cloudová služba identit, která umožňuje ověření pomocí účtu Microsoft. Azure AD používá standardní rozhraní. [Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Výhody Azure AD

Microsoft AppSource a webu Azure Marketplace zákazníci slouží k vyhledání katalogy výpis prostředí v rámci produktu. Tyto akce, aby zákazníci pro přihlášení k produktu. Integrace se službou Azure AD poskytuje následující výhody:

- Rychlejší zapojení a optimalizované uživatelské prostředí
- Jednotné přihlašování (SSO) pro miliony podnikových uživatelů
- Jednotné přihlašování napříč aplikacemi publikoval(a) různými partnery
- Škálovatelná a multiplatformní ověřování pro mobilní zařízení a cloudové aplikace

## <a name="offers-that-require-azure-ad"></a>Nabídky, které vyžadují služby Azure AD

Různé [výpis možností a nabízí typy](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) AppSource a webu Azure Marketplace budou mít jiné požadavky pro implementaci služby Azure AD. Podrobnosti najdete v následující tabulce:

| **Typ nabídky**    | **Vyžaduje se jednotné přihlašování služby Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontaktujte mě | Zkušební verze | Testovací verze | Transakce |
| Virtuální počítač | neuvedeno | Ne | Ne | Ne |
| Aplikace Azure (Šablona řešení)  | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Spravované aplikace  | neuvedeno | neuvedeno | neuvedeno | Ne |
| SaaS  | Ne | Ano | Ano | Ano |
| Containers  | neuvedeno | neuvedeno | neuvedeno | Ne |
| Konzultační služby  | Ne | neuvedeno | neuvedeno | neuvedeno |

Další informace o SaaS technických požadavků najdete v tématu [aplikací SaaS nabízí průvodce publikováním](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integrace se službou Azure AD

- Informace o tom, jak povolit jednotné přihlašování prostřednictvím integrace Azure AD do vašeho seznamu najdete v tématu [Azure Active Directory pro vývojáře]( https://aka.ms/aaddev).
- Chcete-li získat podrobné informace o službě Azure AD jednotného přihlašování, naleznete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Povolit zkušební výpis

Nastavení automatizovaného odběratele zvýšit pravděpodobnost, že převod. Když zákazník vybere zkušební výpis a je přesměrován na zkušební prostředí, můžete nastavit zákazníka přímo bez nutnosti dalších kroků přihlášení.

Při ověřování Azure AD odešle token do své aplikace nebo nabídky. Informace o uživateli poskytnuté token, který umožňuje vytvořit uživatelský účet v aplikaci nebo nabídky. Další informace najdete v tématu [ukázkový tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Kdy použít Azure AD, aby ověřování jedním kliknutím v aplikaci nebo zkušební výpisu, můžete:

- Zjednodušte zkušenosti z webu Marketplace na zkušební verzi výpis.
- Udržujte chování prostředí v rámci produktu dokonce i když se uživatel přesměruje z webu Marketplace k doméně nebo zkušebním prostředí.
- Když uživatelé jsou přesměrováni, protože nejsou žádné další kroky, přihlášení, snížit pravděpodobnost, že další.
- Snižte nasazení překážek pro velký počet obyvatel uživatelů Azure AD.

## <a name="verify-azure-ad-integration"></a>Ověření integrace Azure AD

### <a name="multitenant-solutions"></a>Víceklientské řešení

Používání Azure AD pro podporu následujících akcí:

- Zaregistrujte aplikaci v jednom z prodejních míst Marketplace. Zobrazení [registrace aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) nebo [certifikace AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) Další informace.
- Povolte funkci podporu víceklientské architektury v Azure AD a získat zkušební prostředí jedním kliknutím.

Pokud začínáte používat Azure AD federované jednotné přihlašování, proveďte tyto kroky:

1. Svou aplikaci zaregistrujte na webu Marketplace.
1. Vývoj s využitím jednotného přihlašování s Azure AD [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) nebo [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Povolte funkci podporu víceklientské architektury v Azure AD, která nabízí zkušební prostředí jedním kliknutím.

### <a name="single-tenant-solutions"></a>Řešení pro jednoho tenanta

Pomocí Azure AD za účelem podpory, jeden z následujících akcí:

- Přidat uživatele typu Host do adresáře s použitím [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Ručně nastavit zkušební verze pro zákazníky s použitím **Kontaktujte mě** publikování možnost.
- Vývoj testovací verze na zákazníka.
- Vytváření víceklientských ukázkovou aplikaci ukázku, která používá jednotné přihlašování.

## <a name="next-steps"></a>Další postup

- Ujistěte se, že jste [zaregistrovaný na webu Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/) k vytvoření nebo dokončení vaší nabídky.
