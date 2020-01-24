---
title: Přidat víceklientské aplikace do Galerie aplikací Azure AD | Microsoft Docs
description: Vysvětluje, jak můžete v galerii aplikací Azure AD vytvořit vlastní víceklientské aplikace.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: be660ad42c1336d479f1793b20d2994682db1225
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702755"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Přidání víceklientské aplikace do Galerie aplikací Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je galerie aplikací Azure AD?

Azure Active Directory (Azure AD) je cloudová služba identit. [Galerie aplikací Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) je ve službě Azure Marketplace App Store, kde jsou všechny konektory aplikací publikované pro jednotné přihlašování a zřizování uživatelů. Zákazníci, kteří používají Azure AD jako zprostředkovatele identity, vyhledají různé konektory aplikací SaaS, které jsou tady publikované. Správci IT přidávají konektory z Galerie aplikací a pak tyto konektory nakonfigurují a používají pro jednotné přihlašování a zřizování. Azure AD podporuje všechny hlavní federační protokoly, včetně SAML 2,0, OpenID Connect, OAuth a WS-dodávání pro jednotné přihlašování. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Pokud vaše aplikace podporuje SAML nebo OpenIDConnect
Pokud máte víceklientské aplikace, kterou chcete uvést v galerii aplikací Azure AD, musíte nejdřív zajistit, aby vaše aplikace podporovala jednu z následujících technologií jednotného přihlašování:

- **OpenID Connect**: Pokud chcete svou aplikaci zobrazit, vytvořte víceklientské aplikace v Azure AD a implementujte rozhraní pro [vyjádření souhlasu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) pro vaši aplikaci. Odešle žádost o přihlášení ke společnému koncovému bodu, aby mohl každý zákazník poskytnout souhlas aplikaci. Přístup uživatele můžete řídit na základě ID tenanta a hlavního názvu uživatele (UPN) přijatého v tokenu. Odešlete aplikaci pomocí procesu popsanýho v [části výpis vaší aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Pokud vaše aplikace podporuje SAML 2,0, může být aplikace uvedená v galerii. Postupujte podle pokynů v [části výpis vaší aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Pokud vaše aplikace nepodporuje SAML ani OpenIDConnect
Aplikace, které nepodporují SAML nebo OpenIDConnect, můžou být i nadále integrované do Galerie aplikací prostřednictvím technologie jednotného přihlašování k heslům.

Jednotné přihlašování k heslům, označované taky jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné ve scénářích, ve kterých si několik uživatelů potřebuje sdílet jeden účet, jako jsou například účty aplikací sociálních médií vaší organizace. 

Pokud chcete zobrazit seznam aplikací pomocí této technologie:
1. Vytvořte webovou aplikaci, která má přihlašovací stránku HTML pro konfiguraci [jednotného přihlašování pomocí hesla](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Odešlete žádost, jak je popsáno v [části výpis vaší aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Eskalací zákazníka

Pro všechny eskalace odešlete e-mail do [týmového týmu jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) a my se vám pošle zpět na co možná nejdříve.

## <a name="next-steps"></a>Další kroky
Naučte se, jak [Zobrazit seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
