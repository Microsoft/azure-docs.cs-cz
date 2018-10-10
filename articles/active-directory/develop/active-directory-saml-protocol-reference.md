---
title: Jak Azure AD používá protokol SAML | Dokumentace Microsoftu
description: Tento článek obsahuje přehled profily jednotného přihlašování a jednotné odhlašování SAML v Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: b9ae7cf9d6fd383c7cdfa3957b5d9b94c7207d50
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900651"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Jak Azure AD používá protokol SAML

Azure Active Directory (Azure AD) pomocí SAML 2.0 protokol umožňují aplikacím poskytovat jednotné přihlašování svým uživatelům. [Single Sign-On](single-sign-on-saml-protocol.md) a [jedním odhlašování](single-sign-out-saml-protocol.md) SAML profilů služby Azure AD vysvětlují, jak používat kontrolní výrazy SAML, protokoly a vazby ve službě zprostředkovatele identity.

Protokol SAML vyžaduje zprostředkovatele identity (Azure AD) a poskytovatelem služeb (aplikace) k výměně informací o sami.

Když aplikace je zaregistrován ve službě Azure AD, vývojář aplikace registruje informací souvisejících s federační službou Azure AD. Tyto informace zahrnují **identifikátor URI pro přesměrování** a **metadat URI** aplikace.

Azure AD používá cloudovou službu **metadat URI** a získejte podpisový klíč a identifikátor URI odhlášení. Pokud aplikace nepodporuje metadata identifikátor URI, vývojář musí kontaktovat podporu společnosti Microsoft kvůli odhlášení identifikátor URI a podpisový klíč.

Azure Active Directory zpřístupňuje specifickým pro tenanta a běžné (nezávislé na tenanta) jednotné přihlašování a jednotné odhlašování koncové body. Tyto adresy URL představují adresovatelný umístění – nejsou jenom identifikátory – takže můžete přejít ke koncovému bodu ke čtení metadat.

* Koncový bod specifickým pro tenanta se nachází na `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. *<TenantDomainName>* Zastupuje registrovaný název domény nebo identifikátor GUID ID Tenanta tenanta služby Azure AD. Je například federační metadata tenanta contoso.com, na: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Koncový bod nezávislé na tenanta se nachází na `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Tuto adresu koncového bodu **běžné** se zobrazí místo název domény klienta nebo ID.

Informace o dokumentů metadat federace, které publikuje Azure AD najdete v tématu [federačních metadat](azure-ad-federation-metadata.md).
