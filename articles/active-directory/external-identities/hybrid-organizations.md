---
title: Spolupráce B2B pro hybridní organizace – Azure AD
description: Poskytněte partnerům přístup k místním i cloudovým prostředkům pomocí spolupráce Azure AD B2B.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908617"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory spolupráce B2B pro hybridní organizace

Spolupráce B2B Azure Active Directory (Azure AD) usnadňuje vaší externí partnerům přístup k aplikacím a prostředkům ve vaší organizaci. To platí i v případě hybridní konfigurace, kde máte místní i cloudové prostředky. Nezáleží na tom, jestli aktuálně spravujete externí partnerské účty místně v místním systému identit nebo pokud spravujete externí účty v cloudu jako uživatele Azure AD B2B. Nyní můžete těmto uživatelům udělit přístup k prostředkům v libovolném umístění pomocí stejných přihlašovacích údajů pro obě prostředí.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Udělení přístupu k místním aplikacím uživatelům B2B v Azure AD

Pokud vaše organizace používá funkce spolupráce Azure AD B2B k pozvání uživatelů typu Host od partnerských organizací ke službě Azure AD, můžete teď těmto uživatelům poskytnout přístup k místním aplikacím.

Pro aplikace, které používají ověřování založené na SAML, můžete tyto aplikace zpřístupnit uživatelům B2B prostřednictvím Azure Portal pomocí Azure Proxy aplikací služby AD pro ověřování.

Pro aplikace, které používají integrované ověřování systému Windows (IWA) s omezeným delegováním Kerberos (KCD), můžete také použít proxy server Azure AD pro ověřování. Aby ověřování fungovalo, musí se v místní službě Windows Server Active Directory používat objekt uživatele. Existují dvě metody, které můžete použít k vytvoření místních uživatelských objektů, které představují uživatele typu Host B2B.

- Pro Microsoft Graph můžete použít Microsoft Identity Manager (MIM) 2016 SP1 a agenta pro správu MIM.
- Můžete použít skript prostředí PowerShell. (Toto řešení nevyžaduje MIM.)

Podrobnosti o tom, jak implementovat tato řešení, najdete v článku [udělení přístupu k místním aplikacím uživatelům B2B v Azure AD](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udělit místně spravované partnerské účty přístup k prostředkům cloudu

Před Azure AD mají organizace s místními systémy identit tradičně spravované partnerské účty ve svém místním adresáři. Pokud jako takovou organizaci chcete mít jistotu, že budou mít vaši partneři přístup i při přesunu svých aplikací a dalších prostředků do cloudu. V ideálním případě budete chtít, aby tito uživatelé používali stejnou sadu přihlašovacích údajů pro přístup k cloudovým i místním prostředkům. 

Teď nabízíme metody, které můžete použít Azure AD Connect k synchronizaci těchto místních účtů s cloudem jako "uživatelé typu host", kde se účty chovají stejně jako uživatelé Azure AD B2B.

Aby bylo možné chránit podniková data, můžete řídit přístup pouze ke správným prostředkům a konfigurovat zásady autorizace, které budou těmto uživatelům typu Host zacházet odlišně od zaměstnanců.

Podrobnosti o implementaci najdete v tématu [přidělení místně spravovaných partnerských účtů k prostředkům cloudu pomocí spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Další kroky

- [Udělení přístupu k místním aplikacím uživatelům B2B v Azure AD](hybrid-cloud-to-on-premises.md)
- [Přidělte místně spravované partnerské účty přístup k prostředkům cloudu pomocí spolupráce Azure AD B2B.](hybrid-on-premises-to-cloud.md)


