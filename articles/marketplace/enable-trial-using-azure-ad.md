---
title: Povolit zkušební verze na webu Azure Marketplace pomocí Azure Active Directory | Azure
description: Povolte zkušební typ seznamu pomocí Azure Active Directory v Azure Marketplace a AppSource pro vydavatele aplikace a služby.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: c5b7b4967c1acef733d366e651d50706db42aace
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160463"
---
# <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>Povolit zkušební výpis pomocí Azure Active Directory

Azure Active Directory (Azure AD) je Cloudová služba identit umožňující ověřování pomocí služby Microsoft pracovního nebo školního účtu pomocí standardních rozhraní. Azure AD podporuje ověřování OAuth a OpenID Connect. [Azure Marketplace](https://azuremarketplace.microsoft.com) používá k ověření vy i vaši zákazníci služby Azure AD.

Další informace o službě Azure AD najdete v tématu [Azure Active Directory](https://azure.microsoft.com/services/active-directory).

Poté, co zákazník vybere vaší zkušební verze uvedení na webu Marketplace, vaše zákazníky je přesměrován na zkušební prostředí. Ve vašem zkušební prostředí můžete nastavit zákazníka přímo bez nutnosti dalších kroků přihlášení. Vaše aplikace nebo nabídka přijímá token z Azure AD během ověřování. Token, který obsahuje cenné informace o uživateli mají se používá k vytvoření uživatelského účtu v aplikaci nebo nabídky. Můžete automatizovat instalaci zákazníků a zvýšit pravděpodobnost, že převod.

Další informace o tokenu, který se odesílá z Azure AD během ověřování najdete v tématu [ukázkový tokeny](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens).

Pomocí služby Azure AD povolit ověřování jedním kliknutím v aplikaci nebo zkušební verze. Azure AD nabízí následující výhody: 
*   Zjednodušte zkušenosti z webu Marketplace na zkušební verzi.
*   Udržujte chování prostředí v rámci produktu, i když se uživatel přesměruje z webu Marketplace k doméně nebo zkušebním prostředí.
*   Snížit pravděpodobnost, že další při přesměrování, protože nejsou žádné další kroky, přihlášení.
*   Snižte nasazení překážek pro velký počet obyvatel uživatelů Azure AD.

## <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>Ověřte svoji integraci Azure AD na webu Marketplace: Víceklientských aplikací
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

## <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>Ověřte svoji integraci Azure AD na webu Marketplace: aplikace pro jednoho tenanta
Používání Azure AD pro jednu z následujících možností podpory pro řešení jednoho tenanta: 
*   Přidání uživatelů do vašeho adresáře jako uživatele typu Host pomocí Azure Active Directory B2B (Azure AD B2B).
    
    Další informace o Azure AD B2B najdete v tématu [co je spolupráce B2B ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
*   Ručně nastavte zkušební verze pro zákazníky s použitím kontaktu mi publikování možnost.
*   Vývoj testovací verze na zákazníka.
*   Vytváření víceklientských ukázkové ukázkovou aplikaci, která používá jednotné přihlašování.

## <a name="next-steps"></a>Další postup
*   Zkontrolujte [Průvodce publikováním webu Azure Marketplace a AppSource](./marketplace-publishers-guide.md).
