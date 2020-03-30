---
title: Jak Azure AD používá protokol SAML | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled profilů SAML jednotného přihlášení a jednotného přihlášení ve službě Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dc7771f29fb5d00aedfe5162a98f5f0c14544a7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161167"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Jak Azure AD využívá protokol SAML

Azure Active Directory (Azure AD) používá protokol SAML 2.0 k povolení aplikací poskytovat uživatelům jednotné přihlašování. Jednotné [přihlášení](single-sign-on-saml-protocol.md) a [jednotné přihlášení](single-sign-out-saml-protocol.md) SAML profily Azure AD vysvětlit, jak saml kontrolní výrazy, protokoly a vazby se používají ve službě zprostředkovatele identity.

Saml Protocol vyžaduje, aby si poskytovatel identity (Azure AD) a poskytovatel služeb (aplikace) vyměňovali informace o sobě.

Když je aplikace registrovaná ve službě Azure AD, vývojář aplikace zaregistruje informace související s federací s Azure AD. Tyto informace zahrnují **identifikátor URI přesměrování** a identifikátor URI **metadat** aplikace.

Azure AD používá identifikátor URI metadat cloudové **služby** k načtení podpisového klíče a identifikátoru URI odhlášení. Zákazník může otevřít aplikaci v **Azure AD -> App Registration** a potom v Nastavení **-> vlastnosti**, může aktualizovat adresu URL odhlášení. Tímto způsobem Azure AD můžete odeslat odpověď na správnou adresu URL. 

Azure Active Directory zpřístupňuje specifické pro klienta a společné (nezávislé na tenantovi) jednotné přihlašovací a jednotné koncové body pro odhlášení. Tyto adresy URL představují adresovatelná umístění – nejsou to pouze identifikátory – takže můžete přejít na koncový bod a přečíst si metadata.

* Koncový bod specifický pro klienta se nachází na adrese `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. Zástupný symbol * \<TenantDomainName>* představuje registrovaný název domény nebo identifikátor GUID tenanta Tenant AD klienta Azure. Například metadata federace contoso.com tenanta je na adrese:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Koncový bod nezávislý na tenantovi se nachází na adrese `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. V této adrese koncového bodu se místo názvu domény klienta nebo ID zobrazí **běžné.**

Informace o dokumentech metadat federace, které publikuje Azure AD, najdete v [tématu metadata federace](../azuread-dev/azure-ad-federation-metadata.md).
