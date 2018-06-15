---
title: Spolupráce B2B pro hybridní organizace – Azure Active Directory | Microsoft Docs
description: Poskytnout partnerům přístup k místní a cloudové prostředky s spolupráce Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b9a74a4e44fefd389a309b776e50d76d362ee9d8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267386"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Spolupráce Azure Active Directory s B2B pro hybridní organizace

Spolupráce Azure Active Directory (Azure AD) s B2B snadno poskytnout externími partnery přístup k aplikacím a prostředkům ve vaší organizaci. To platí i v hybridní konfigurace kde máte místní i cloudové prostředky. Pokud aktuálně spravovat účty externí partnera místně v systému identity na místě, nebo pokud spravujete externích účtech v cloudu jako uživatele Azure AD s B2B nezáleží. Můžete teď udělit těmto uživatelům přístup k prostředkům v některém umístění pomocí stejných přihlašovacích údajů přihlásit v obou prostředích.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Uživatelům udělit B2B ve službě Azure AD přístup k místní aplikace

Pokud vaše organizace používá možnosti spolupráce Azure AD B2B pozvaným uživatele typu Host z partnerských organizací do služby Azure AD, že teď můžete zadat tyto B2B uživatelům přístup k místní aplikace.

Pro aplikace, které používají ověřování na základě SAML můžete provádět tyto aplikace k dispozici B2B uživatele prostřednictvím portálu Azure pomocí Azure AD Application Proxy pro ověřování.

Pro aplikace, které používají integrované ověřování systému Windows (IWA) s omezeným delegováním protokolu Kerberos použitím (KCD) můžete také použít Azure AD Proxy pro ověřování. K autorizaci fungovat, ale objekt uživatele je vyžadována v místní Windows Server Active Directory. Existují dvě metody, které můžete použít k vytváření objektů místního uživatele, které představují vaše uživatele typu Host B2B.

- Můžete použít Microsoft Identity Manager (MIM) 2016 SP1 a agenta pro správu MIM pro Microsoft Graph.
- Můžete použít skript prostředí PowerShell. (Toto řešení se nevyžaduje MIM).

Podrobnosti o způsobu implementace těchto řešení najdete v tématu [uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udělení partnera místně spravované účty přístupu k prostředkům cloudu

Před Azure AD, organizace s místních systémů identit mít tradičně partnera účty spravované ve své místní adresář. Pokud jste tyto organizace, budete chtít Ujistěte se, že vaši partneři dál mají přístup do cloudu přesouváte aplikace a dalším prostředkům. V ideálním případě chcete tyto uživatele bude možné použít stejnou sadu pověření pro přístup k prostředkům cloudu a místně. 

Jsme teď nabídka metody, kde můžete použít Azure AD Connect k synchronizaci těchto místních účtů do cloudu jako "uživatele typu Host,", kde účty chovají stejně, jako uživatele Azure AD s B2B.

K ochraně dat vaší společnosti, můžete řídit přístup ke správné prostředky a nakonfigurujte zásady autorizace, které považovat tyto uživatele typu Host odlišně od zaměstnanců.

Podrobnosti implementace najdete v tématu [účtů místně spravované partnera udělit přístup k prostředkům cloudu pomocí spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Další postup

- [Uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím](hybrid-cloud-to-on-premises.md)
- [Udělení partnera místně spravované účty přístupu k cloudových prostředků pomocí spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md)


