---
title: Přidání víceklientské aplikace do galerie aplikací Azure AD
description: Vysvětluje, jak můžete seznam vlastní vyvinuté víceklientské aplikace v galerii aplikací Azure AD.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: 90f4d22ba782fd71460595f69a52e70be87a3047
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883249"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Přidání víceklientské aplikace do galerie aplikací Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je galerie aplikací Azure AD?

Azure Active Directory (Azure AD) je cloudová služba identit. [Galerie aplikací Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) je v obchodě s aplikacemi Azure Marketplace, kde se publikují všechny konektory aplikací pro jednotné přihlašování a zřizování uživatelů. Zákazníci, kteří používají Azure AD jako zprostředkovatele identity najít různé konektory aplikace SaaS publikováno zde. Správci IT přidávají konektory z galerie aplikací a pak nakonfigurují a používají konektory pro jednotné přihlašování a zřizování. Azure AD podporuje všechny hlavní federační protokoly, včetně SAML 2.0, OpenID Connect, OAuth a WS-Fed pro jednotné přihlašování. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Pokud vaše aplikace podporuje SAML nebo OpenIDConnect
Pokud máte víceklientskou aplikaci, která je uvedena v galerii aplikací Azure AD, musíte se nejprve ujistit, že vaše aplikace podporuje jednu z následujících technologií jednotného přihlašování:

- **OpenID Connect**: Chcete-li mít svou aplikaci v seznamu, vytvořte víceklientské aplikace ve službě Azure AD a implementujte [rámec souhlasu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) pro vaši aplikaci. Odešlete žádost o přihlášení do společného koncového bodu, aby každý zákazník mohl poskytnout souhlas s aplikací. Přístup uživatele můžete řídit na základě ID klienta a hlavního uživatele přijatého v tokenu. Odešlete aplikaci pomocí procesu popsaného v [části Výpis aplikace v galerii aplikací Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Pokud vaše aplikace podporuje SAML 2.0, aplikace může být uvedena v galerii. Postupujte podle pokynů v [části Výpis aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Pokud vaše aplikace nepodporuje SAML nebo OpenIDConnect
Aplikace, které nepodporují SAML nebo OpenIDConnect, lze stále integrovat do galerie aplikací prostřednictvím technologie jednotného přihlašování heslem.

Jednotné přihlašování pomocí hesla, nazývané také trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je také užitečné pro scénáře, ve kterých několik uživatelů potřebuje sdílet jeden účet, například s účty aplikací sociálních médií vaší organizace. 

Pokud chcete uvést aplikaci pomocí této technologie:
1. Vytvořte webovou aplikaci, která má přihlašovací stránku HTML pro konfiguraci [jednotného přihlášení hesla](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Odešlete žádost, jak je popsáno v [seznamu vaší aplikace v galerii aplikací Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalace

Pro jakékoli eskalace, pošlete e-mail na [Azure AD Přispoávací tým integrace a](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) my se vám ozveme co nejdříve.

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [uvést aplikaci v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
