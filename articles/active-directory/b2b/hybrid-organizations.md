---
title: Spolupráce B2B pro hybridní organizace – Azure AD
description: Díky spolupráci Azure AD B2B poskytujete partnerům přístup k místním i cloudovým prostředkům.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272470"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Spolupráce Azure Active Directory B2B pro hybridní organizace

Spolupráce b2B služby Azure Active Directory (Azure AD) usnadňuje přístup externím partnerům k aplikacím a prostředkům ve vaší organizaci. To platí i v hybridní konfiguraci, kde máte místní i cloudové prostředky. Nezáleží na tom, jestli aktuálně spravujete účty externích partnerů místně v místním systému identit, nebo pokud spravujete externí účty v cloudu jako uživatelé Azure AD B2B. Nyní můžete těmto uživatelům udělit přístup k prostředkům v obou umístěních pomocí stejných přihlašovacích údajů pro obě prostředí.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Udělte uživatelům B2B ve službě Azure AD přístup k místním aplikacím

Pokud vaše organizace používá možnosti spolupráce Azure AD B2B k pozvání uživatelů typu Host z partnerských organizací do služby Azure AD, můžete teď těmto uživatelům B2B poskytnout přístup k místním aplikacím.

U aplikací, které používají ověřování na základě SAML, můžete tyto aplikace zpřístupnit uživatelům B2B prostřednictvím portálu Azure, pomocí proxy aplikace Azure AD pro ověřování.

Pro aplikace, které používají integrované ověřování windows (IWA) s kerberos omezené delegování (KCD), můžete také použít Azure AD Proxy pro ověřování. Aby však autorizace fungovala, je v místní službě Active Directory systému Windows Server vyžadován objekt uživatele. Existují dvě metody, které můžete použít k vytvoření místníuživatelské objekty, které představují vaše uživatele typu Host B2B.

- Můžete použít Microsoft Identity Manager (MIM) 2016 SP1 a agent adiciace MIM pro Microsoft Graph.
- Můžete použít skript Prostředí PowerShell. (Toto řešení nevyžaduje MIM.)

Podrobnosti o tom, jak implementovat tato řešení, najdete [v tématu udělení B2B uživatelům ve službě Azure AD přístup k místním aplikacím](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udělení přístupu k místním spravovaným partnerským účtům ke cloudovým prostředkům

Před Azure AD organizace s místními systémy identit tradičně spravované partnerské účty v jejich místní adresář. Pokud jste taková organizace, měli byste se ujistit, že vaši partneři budou mít i nadále přístup při přesouvání aplikací a dalších prostředků do cloudu. V ideálním případě chcete, aby tito uživatelé používali stejnou sadu přihlašovacích údajů pro přístup ke cloudovým i místním prostředkům. 

Teď nabízíme metody, kde můžete pomocí Azure AD Connect synchronizovat tyto místní účty do cloudu jako "uživatelé typu Host", kde se účty chovají stejně jako uživatelé Azure AD B2B.

Chcete-li chránit firemní data, můžete řídit přístup ke správným prostředkům a nakonfigurovat zásady autorizace, které s těmito uživateli typu Host zacházejí jinak než s vašimi zaměstnanci.

Podrobnosti o implementaci najdete v článku [Udělení místně spravovaných partnerských účtů přístup ke cloudovým prostředkům pomocí spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Další kroky

- [Udělení přístupu uživatelům B2B ve službě Azure AD k místním aplikacím](hybrid-cloud-to-on-premises.md)
- [Udělte místně spravované partnerské účty přístup ke cloudovým prostředkům pomocí spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md)


