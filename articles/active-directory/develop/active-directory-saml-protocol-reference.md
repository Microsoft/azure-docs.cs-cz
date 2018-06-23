---
title: Referenční informace o protokolu Azure AD SAML | Microsoft Docs
description: Tento článek obsahuje přehled jednotného přihlašování a jeden Sign-Out SAML profily v Azure Active Directory.
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
ms.date: 07/21/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock
ms.openlocfilehash: 26984244c0ffa5e665394e903ba95b0487f214a8
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316905"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Jak Azure Active Directory používá protokol SAML
Azure Active Directory (Azure AD) používá SAML 2.0 protokol umožnit aplikacím jednotnému přihlašování svým uživatelům. [Jednotné přihlašování](active-directory-single-sign-on-protocol-reference.md) a [jedním odhlašování](active-directory-single-sign-out-protocol-reference.md) SAML profilů Azure AD vysvětlují, jak používat kontrolní výrazy SAML, protokoly a vazby ve službě zprostředkovatele identity.

Protokol SAML vyžaduje zprostředkovatele identity (Azure AD) a poskytovatelem služeb (aplikace) k výměně informací o sami.

Při registraci aplikace s Azure AD, vývojáři aplikace zaregistruje informací souvisejících s federační službou Azure AD. Tyto informace zahrnují **identifikátor URI pro přesměrování** a **Metadata URI** aplikace.

Cloudová služba používá Azure AD **Metadata URI** načíst podpisový klíč a odhlášení identifikátor URI. Pokud aplikace nepodporuje metadata identifikátor URI, musí vývojář kontaktujte podporu společnosti Microsoft zajistit odhlášení URI a podpisového klíče.

Azure Active Directory zpřístupní konkrétního klienta a běžných (nezávislé na klienta) jeden přihlášení a jeden odhlašování koncových bodů. Představují tyto adresy URL adresovatelné umístění – nejsou jenom identifikátory –, můžete přejít na koncový bod číst metadata.

* Koncový bod konkrétního klienta se nachází v `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. *<TenantDomainName>* Zástupný symbol představuje registrovaný název domény nebo TenantID GUID klienta Azure AD. Federační metadata contoso.com klienta je třeba na: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* Koncový bod nezávislé na klienta se nachází v `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. V této adresa koncového bodu **běžné** se zobrazí místo název domény klienta nebo ID.

Informace o dokumentech federační metadata, která publikuje Azure AD najdete v tématu [federačních metadat](active-directory-federation-metadata.md).
