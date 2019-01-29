---
title: Přidat víceklientské aplikaci do Galerie aplikací Azure AD | Dokumentace Microsoftu
description: Vysvětluje, jak můžete vytvořit seznam vyvinuté víceklientské aplikace v galerii aplikací Azure AD.
services: active-directory
documentationCenter: na
author: CelesteDG
manager: mtillman
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: jeedes
ms.openlocfilehash: 073f6d925a6868937410972a9400daefa802876d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152038"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Přidat víceklientské aplikaci do Galerie aplikací Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je Galerie aplikací Azure AD?

Azure Active Directory (Azure AD) je služba identit v cloudu. [Galerie aplikací Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) je v obchodě Azure Marketplace, kde jsou všechny konektory aplikace publikována pro jednotné přihlašování a zřizování uživatelů. Zákazníci, kteří používají Azure AD jako zprostředkovatele identity najít různých konektorů aplikací SaaS publikované. Správci IT přidat konektory v galerii aplikací a potom nakonfigurujte a konektory slouží pro jednotné přihlašování a zřizování. Azure AD podporuje všechny hlavní federační protokolů, jako jsou třeba SAML 2.0, OpenID Connect, OAuth a WS-Fed pro jednotné přihlašování. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Pokud vaše aplikace podporuje SAML nebo OpenIDConnect
Pokud máte víceklientské aplikaci, která chcete, aby uvedená v galerii aplikací Azure AD, je nejprve třeba zkontrolovat, že vaše aplikace podporuje jednu z následujících jednotné přihlašování technologií:

- **OpenID Connect**: Pokud chcete, aby vaši aplikaci, vytvořte víceklientskou aplikaci ve službě Azure AD a implementaci [rozhraní Azure AD pro udělování souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) pro vaši aplikaci. Odesílejte žádost o přihlášení na společný koncový bod tak, aby každý zákazník se může poskytnout souhlas pro aplikaci. Můžete řídit přístup uživatelů na základě ID tenanta a hlavním názvem uživatele přijata v tokenu. Odešlete aplikaci pomocí procesu uvedených v [výpis vaší aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Pokud vaše aplikace podporuje SAML 2.0, můžete aplikace uvedená v galerii. Postupujte podle pokynů v [výpis vaší aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Pokud aplikace nepodporuje SAML nebo OpenIDConnect
Aplikace, které nepodporují SAML nebo OpenIDConnect stále možné integrovat do Galerie aplikací prostřednictvím heslo jednotné přihlašování – technologie.

Heslo jednotného přihlašování, taky jako ukládání hesel do trezoru, můžete ke správě přístupu uživatelů a hesel do webové aplikace, které nepodporují federace identit. Je také užitečné pro scénáře, ve kterých několik uživatelé potřebují sdílet jeden účet, jako například účtům sociálních médií aplikace vaší organizace. 

Pokud chcete zařaďte svoji aplikaci s touto technologií:
1. Vytvoření webové aplikace, který má přihlašovací stránku HTML konfigurace [heslem jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Odeslat požadavek, jak je popsáno v [výpis vaší aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalaci

Žádné eskalaci, pošlete e-mail na adresu [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) a ozveme se vám co nejdříve.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [uvedení aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
