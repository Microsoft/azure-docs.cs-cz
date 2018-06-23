---
title: Přidání víceklientské aplikace pro galerii aplikací Azure AD | Microsoft Docs
description: Vysvětluje, jak můžete vytvořit seznam zákaznických víceklientské aplikace v galerii aplikací Azure AD.
services: active-directory
documentationCenter: na
author: barbkess
manager: mtillman
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 4ff64847e057a67f0ed3378baefa7762c159aba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333346"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Přidání víceklientské aplikace pro galerii aplikací Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je galerii aplikací Azure AD?

Azure Active Directory (Azure AD) je služba cloudových identit. [Galerii aplikací Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) je v obchodě s aplikacemi Azure Marketplace, kde jsou všechny konektory aplikace publikována pro jednotné přihlašování a zřizování uživatelů. Zákazníci, kteří používají Azure AD jako zprostředkovatele identity najít jiné konektory aplikací SaaS publikované. Správci IT přidejte konektory z Galerie aplikace a potom nakonfigurovat a používat konektory pro jednotné přihlašování a zřizování. Azure AD podporuje všechny hlavní federační protokoly, včetně SAML 2.0, OpenID Connect, OAuth a WS-Fed pro jednotné přihlašování. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Pokud vaše aplikace podporuje SAML nebo OpenIDConnect
Pokud máte víceklientské aplikace, která má uvedené v galerii aplikací Azure AD, musíte nejprve provedete jistotu, že vaše aplikace podporuje jednu z následujících jeden přihlašování technologií:

- **OpenID Connect**: tak, aby měl uvedené aplikace, vytvořte víceklientské aplikaci ve službě Azure AD a implementaci [framework souhlasu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) pro vaši aplikaci. Poslat žádost o přihlášení k běžné koncový bod, tak, aby každý zákazník může poskytnout souhlas k aplikaci. Můžete řídit přístup uživatele na základě ID klienta a uživatele (UPN) přijaté v tokenu. Odeslání aplikace pomocí procesu uvedených v [výpis aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Pokud aplikace podporuje SAML 2.0, může být aplikace uvedený v galerii. Postupujte podle pokynů v [výpis aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Pokud vaše aplikace nepodporuje SAML nebo OpenIDConnect
Do Galerie aplikací prostřednictvím heslo jeden přihlašování technologie stále dají integrovat aplikace, které nepodporují SAML nebo OpenIDConnect.

Heslo jednotné přihlašování, označované taky jako heslo překlenutí vyrovnávací paměti, můžete spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je také užitečné v případech, ve kterých několik uživatelé muset sdílet jeden účet, například k účtům aplikace sociálních médií vaší organizace. 

Pokud chcete uvedení aplikace s touto technologií:
1. Vytvoření webové aplikace, který má přihlašovací stránku HTML konfigurace [heslo jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Odeslání žádosti, jak je popsáno v [výpis aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalací

Pro všechny eskalací odesílat e-mailu [týmu Integrace Azure AD jednotného přihlašování k](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) a ozveme vám co nejdříve.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [uvedení aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
