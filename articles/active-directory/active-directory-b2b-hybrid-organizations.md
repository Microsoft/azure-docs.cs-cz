---
title: Spolupráce B2B pro hybridní organizace – Azure Active Directory | Microsoft Docs
description: Poskytnout partnerům přístup k místní a cloudové prostředky s spolupráce Azure AD B2B.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/20/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 0ccf3eb381f42849b48f3d149942be13380b3670
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Spolupráce Azure Active Directory s B2B pro hybridní organizace

Spolupráce Azure Active Directory (Azure AD) s B2B snadno poskytnout externími partnery přístup k aplikacím a prostředkům ve vaší organizaci. To platí i v hybridní konfigurace kde máte místní i cloudové prostředky. Pokud aktuálně spravovat účty externí partnera místně v systému identity na místě, nebo pokud spravujete externích účtech v cloudu jako uživatele Azure AD s B2B nezáleží. Můžete teď udělit těmto uživatelům přístup k prostředkům v některém umístění pomocí stejných přihlašovacích údajů přihlásit v obou prostředích.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Uživatelům udělit B2B ve službě Azure AD přístup k místní aplikace

Pokud vaše organizace používá možnosti spolupráce Azure AD B2B pozvaným uživatele typu Host z partnerských organizací do služby Azure AD, že teď můžete zadat tyto B2B uživatelům přístup k místní aplikace.

Pro aplikace, které používají ověřování na základě SAML můžete provádět tyto aplikace k dispozici B2B uživatele prostřednictvím portálu Azure pomocí Azure AD Application Proxy pro ověřování.

Pro aplikace, které používají integrované ověřování systému Windows (IWA) s omezeným delegováním protokolu Kerberos použitím (KCD) můžete také použít Azure AD Proxy pro ověřování. K autorizaci fungovat, ale objekt uživatele je vyžadována v místní Windows Server Active Directory. Existují dvě metody, které můžete použít k vytváření objektů místního uživatele, které představují vaše uživatele typu Host B2B.

- Můžete použít Microsoft Identity Manager (MIM) 2016 SP1 a agenta pro správu MIM pro Microsoft Graph. (To vyžaduje předplatné služby Azure AD Premium 1.)
- Můžete použít skript prostředí PowerShell. (Toto řešení nevyžaduje MIM nebo AD Premium.)

Podrobnosti o způsobu implementace těchto řešení najdete v tématu [uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím](active-directory-b2b-hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udělení partnera místně spravované účty přístupu k prostředkům cloudu

Před Azure AD, organizace s místních systémů identit mít tradičně partnera účty spravované ve své místní adresář. Pokud jste tyto organizace, budete chtít Ujistěte se, že vaši partneři dál mají přístup do cloudu přesouváte aplikace a dalším prostředkům. V ideálním případě chcete tyto uživatele bude možné použít stejnou sadu pověření pro přístup k prostředkům cloudu a místně. 

Jsme teď nabídka metody, kde můžete použít Azure AD Connect k synchronizaci těchto místních účtů do cloudu jako "uživatele typu Host,", kde účty chovají stejně, jako uživatele Azure AD s B2B. Toto řešení funguje, i v případě, že máte v místním identity systému, která umožňuje partnerům použít vlastní externí e-mailové adresy jako jejich přihlašovací jméno.

K ochraně dat vaší společnosti, můžete řídit přístup ke správné prostředky a nakonfigurujte zásady autorizace, které považovat tyto uživatele typu Host odlišně od zaměstnanců.

Podrobnosti implementace najdete v tématu [účtů místně spravované partnera udělit přístup k prostředkům cloudu pomocí spolupráce Azure AD B2B](active-directory-b2b-hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Další postup

- [Uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím](active-directory-b2b-hybrid-cloud-to-on-premises.md)
- [Udělení partnera místně spravované účty přístup k prostředkům cloudu pomocí spolupráce Azure AD B2B](active-directory-b2b-hybrid-on-premises-to-cloud.md).

