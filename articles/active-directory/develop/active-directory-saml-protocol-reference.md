---
title: Referenční informace o protokolu Azure AD SAML | Microsoft Docs
description: Tento článek obsahuje přehled jednotného přihlašování a jeden Sign-Out SAML profily v Azure Active Directory.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: priyamo
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 3a38d5e7a33a681c2e6d4964863d25f5cfbd6725
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Jak Azure Active Directory používá protokol SAML
Azure Active Directory (Azure AD) používá SAML 2.0 protokol umožnit aplikacím jednotnému přihlašování svým uživatelům. [Jednotné přihlašování](active-directory-single-sign-on-protocol-reference.md) a [jedním odhlašování](active-directory-single-sign-out-protocol-reference.md) SAML profilů Azure AD vysvětlují, jak se používají SAML kontrolní výrazy, protokoly a vazby ve službě zprostředkovatele identity.

Protokol SAML vyžaduje zprostředkovatele identity (Azure AD) a poskytovatelem služeb (aplikace) k výměně informací o sami.

Při registraci aplikace s Azure AD, vývojáři aplikace zaregistruje informací souvisejících s federační službou Azure AD. To zahrnuje **identifikátor URI pro přesměrování** a **Metadata URI** aplikace.

Používá Azure AD **Metadata URI** cloudové služby získat podpisový klíč a odhlášení URI cloudové služby. Pokud aplikace nepodporuje metadata identifikátor URI, musí vývojář kontaktujte podporu společnosti Microsoft zajistit odhlášení URI a podpisového klíče.

Azure Active Directory zpřístupní konkrétního klienta a běžných (nezávislé na klienta) jeden přihlášení a jeden odhlašování koncových bodů. Představují tyto adresy URL adresovatelné umístění – nejsou právě identifikátory –, můžete přejít na koncový bod číst metadata.

* Koncový bod konkrétního klienta se nachází v `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. <TenantDomainName> Zástupný symbol představuje registrovaný název domény nebo TenantID GUID klienta Azure AD. Federační metadata contoso.com klienta je třeba na: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Koncový bod nezávislé na klienta se nachází v `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. V této adresa koncového bodu **běžné** se zobrazí místo název domény klienta nebo ID.

Informace o dokumentech federačních metadat, která publikuje Azure AD najdete v tématu [federačních metadat](active-directory-federation-metadata.md).
