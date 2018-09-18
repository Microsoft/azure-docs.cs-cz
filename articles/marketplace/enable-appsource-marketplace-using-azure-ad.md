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
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986422"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>Povolení výpis Microsoft AppSource a webu Azure Marketplace pomocí služby Azure Active Directory

Microsoft Azure Active Directory (Azure AD) je Cloudová služba identit, která umožňuje provádět ověření pomocí účtu Microsoft pomocí standardních rozhraní.  Další informace o službě Azure AD najdete v tématu [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="benefits-of-using-azure-active-directory"></a>Výhody používání služby Azure Active Directory

Microsoft AppSource a webu Azure Marketplace zákazníky slouží k vyhledání seznam katalogy, které bude nutné, aby přihlášení k produktu prostředí v rámci produktu.  Díky integraci vaší aplikace s Azure AD, můžete zvýšit zapojení a optimalizovat uživatelské prostředí. Azure AD:

- Umožňuje jednotné přihlašování (SSO) pro miliony podnikových uživatelů.
- Umožňuje konzistentní uživatelské přihlašování napříč aplikací publikovaných serverem různými partnery.
- Poskytuje škálovatelné, multiplatformní ověřování pro mobilní a cloudové aplikace.

Jak je uvedeno v následující části některé nabídky jsou nutná pro implementaci služby Azure AD pro publikování na webu Marketplace.

## <a name="azure-active-directory-requirements"></a>Požadavky služby Azure Active Directory

Existují různé [výpis možností a nabízí typy](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) Microsoft AppSource a webu Azure Marketplace.  Níže jsou uvedeny požadavky služby Azure AD pro tyto typy nabídky a možnosti zobrazení:

| **Typ nabídky**    | **Vyžaduje se jednotné přihlašování AAD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontaktujte mě | Zkušební verze | Test Drive | Transakce |
| Virtuální počítač | neuvedeno | Ne | Ne | Ne |
| Aplikace Azure (Šablona řešení)  | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Spravované aplikace  | neuvedeno | neuvedeno | neuvedeno | Ne |
| SaaS  | Ne | Ano | Ano | Ano |
| Containers  | neuvedeno | neuvedeno | neuvedeno | Ne |
| Konzultační služby  | Ne | neuvedeno | neuvedeno | neuvedeno |

Další informace o SaaS technických požadavků najdete v tématu [aplikací SaaS nabízí průvodce publikováním](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="integration-with-azure-active-directory"></a>Integrace s Azure Active Directory

Informace o tom, jak integrovat s Azure AD umožňuje jednotné přihlašování, najdete v článku https://aka.ms/aaddev.

Další informace o jednotné přihlašování služby Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)?

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Povolit zkušební výpis pomocí Azure Active Directory

Poté, co zákazník vybere vaší zkušební verze uvedení na webu Marketplace, vaše zákazníky je přesměrován na zkušební prostředí. Ve vašem zkušební prostředí můžete nastavit zákazníka přímo bez nutnosti dalších kroků přihlášení. Vaše aplikace nebo nabídka přijímá token z Azure AD během ověřování. Token, který obsahuje cenné informace o uživateli mají se používá k vytvoření uživatelského účtu v aplikaci nebo nabídky. Můžete automatizovat instalaci zákazníků a zvýšit pravděpodobnost, že převod.

Další informace o tokenu, který se odesílá z Azure AD během ověřování najdete v tématu [ukázkový tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Pomocí služby Azure AD povolit ověřování jedním kliknutím v aplikaci nebo zkušební verze. Azure AD nabízí následující výhody: 
*   Zjednodušte zkušenosti z webu Marketplace na zkušební verzi.
*   Udržujte chování prostředí v rámci produktu, i když se uživatel přesměruje z webu Marketplace k doméně nebo zkušebním prostředí.
*   Snížit pravděpodobnost, že další při přesměrování, protože nejsou žádné další kroky, přihlášení.
*   Snižte nasazení překážek pro velký počet obyvatel uživatelů Azure AD.

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Ověřte svoji integraci Azure AD na webu Marketplace: Víceklientských aplikací
Následující možnosti podpory pro vaše řešení pomocí Azure AD:
*   Zaregistrujte aplikaci v prodejní místa na webu Marketplace.
*   Povolte funkci podporu víceklientské architektury v Azure AD a získat zkušební prostředí jedním kliknutím.

Další informace o registraci aplikace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

Pokud jste ještě na používání Azure AD federované jedním přihlašování (SSO), proveďte následující kroky:
1.  Svou aplikaci zaregistrujte na webu Marketplace. 
2.  Vývoj jednotné přihlašování s Azure AD pomocí OAuth 2.0 nebo OpenID Connect.
    *   Další informace o OAuth 2.0, naleznete v tématu [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
    *   Další informace o Open ID Connect najdete v tématu [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
3.  Povolte funkci podporu víceklientské architektury v Azure AD, která nabízí zkušební prostředí jedním kliknutím.
    
    Další informace o certifikace AppSource najdete v tématu [certifikace AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified). 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Ověřte svoji integraci Azure AD na webu Marketplace: aplikace pro jednoho tenanta
Používání Azure AD pro jednu z následujících možností podpory pro řešení jednoho tenanta: 
*   Přidání uživatelů do vašeho adresáře jako uživatele typu Host pomocí Azure Active Directory B2B (Azure AD B2B). Další informace o Azure AD B2B najdete v tématu [co je spolupráce B2B ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Ručně nastavte zkušební verze pro zákazníky s použitím kontaktu mi publikování možnost.
*   Vývoj testovací verze na zákazníka.
*   Vytváření víceklientských ukázkovou aplikaci ukázku, která používá jednotné přihlašování.

## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili, 
- [Zaregistrujte](https://azuremarketplace.microsoft.com/sell) na webu Marketplace.

Pokud budete zaregistrováni a vytvoření nové nabídky nebo pracující na existující,
- [Přihlaste se na portál Cloud Partner](https://cloudpartner.azure.com/) k vytvoření nebo dokončení vaší nabídky.

