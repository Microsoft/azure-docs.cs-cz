---
title: Spolupráce B2B pro hybridní organizace – Azure Active Directory | Dokumentace Microsoftu
description: Partnerům poskytnout přístup k místním a cloudovým prostředkům se spoluprací Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 773ae5aefab59e79822c0320773753b47cfefdeb
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983865"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Spolupráce Azure Active Directory s B2B pro hybridní organizace

Spolupráce Azure Active Directory (Azure AD) B2B usnadňuje externím partnerům poskytnout přístup k aplikacím a prostředkům ve vaší organizaci. To platí dokonce i v hybridní konfiguraci kde máte místní i cloudové prostředky. Pokud aktuálně spravovat externí partnerské účty místně do vašeho místního systému identit, nebo pokud spravujete externí účty v cloudu jako Azure AD B2B uživatelé nezáleží. Můžete teď tyto uživatelům udělit přístup k prostředkům v některém umístění pomocí stejných přihlašovacích údajů přihlásit v obou prostředích.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Uživatelům udělit B2B v Azure AD přístup k místním aplikacím

Pokud vaše organizace používá možnosti spolupráce B2B ve službě Azure AD se pozvat uživatele typu Host z partnerských organizací do služby Azure AD, můžete nyní zadat tyto přístupu uživatelům B2B k místním aplikacím.

Pro aplikace, které používají ověřování založené na SAML můžete provádět tyto aplikace dostupné uživatelům B2B prostřednictvím webu Azure portal, ověřování pomocí Azure AD Application Proxy.

Pro aplikace, které používají integrované ověřování Windows (IWA) s omezené delegování protokolu Kerberos (KCD) je také použít Proxy služby Azure AD pro ověřování. Pro autorizaci pro práci, ale objekt uživatele se vyžaduje v místním systému Windows Server Active Directory. Existují dvě metody, která vám pomůže vytvořit místní uživatelské objekty, které představují vaše uživatele typu Host B2B.

- Microsoft Identity Manageru (MIM) 2016 SP1 a agenta pro správu MIM můžete použít pro Microsoft Graph.
- Můžete použít skript prostředí PowerShell. (Toto řešení nevyžaduje, aby MIM.)

Podrobnosti o způsobu implementace těchto řešení najdete v tématu [uživatele udělit B2B ve službě Azure AD přístup k místním aplikacím](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Udělit přístup k místně spravované partnerské účty ke cloudovým prostředkům

Než Azure AD organizace s místními systémy identit tradičně spravovanými partnerské účty v jejich v místním adresáři. Pokud jste tyto organizace, budete chtít zkontrolujte, že vaše partnery i nadále mít přístup k přesunu vašich aplikací a dalších prostředků do cloudu. V ideálním případě má tyto uživatele bude možné použít stejnou sadu přihlašovacích údajů pro přístup k cloudových a místních prostředků. 

Jsme teď nabídka metody, kde můžete použít Azure AD Connect pro synchronizaci tyto místní účty v cloudu jako "uživatele typu Host,", kde účty chovají stejně, jako jsou uživatelé Azure AD B2B.

K ochraně dat vaší společnosti, můžete řídit přístup ke správné prostředky a nakonfigurujte zásady autorizace, které za tyto uživatele typu Host odlišně od zaměstnanců.

Podrobnosti implementace, naleznete v tématu [udělení místně spravované partnerské účty přístup ke cloudovým prostředkům využitím spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Další postup

- [Uživatelům udělit B2B v Azure AD přístup k místním aplikacím](hybrid-cloud-to-on-premises.md)
- [Udělit partnera místně spravované účty přístup ke cloudovým prostředkům využitím spolupráce Azure AD B2B](hybrid-on-premises-to-cloud.md)


