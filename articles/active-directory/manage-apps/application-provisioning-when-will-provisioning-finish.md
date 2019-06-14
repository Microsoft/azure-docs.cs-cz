---
title: Zřizování uživatelů pro aplikaci Galerie Azure AD je trvá hodiny nebo víc | Dokumentace Microsoftu
description: Jak zjistit, proč zřizování pro vaši aplikaci může trvá déle, než jste očekávali
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8622a7bbd913710a2173399048baab7067d2fae7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783801"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů pro aplikaci Galerie Azure AD je trvá hodiny nebo víc

Při prvním povolení automatického zřizování pro aplikace, počáteční synchronizace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizuje po počáteční synchronizaci být rychlejší, zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční synchronizace trvá déle než několik hodin, je jednou z věcí, které vám pomůžou zlepšit výkon:

-   **Filtry oborů uživatele.** Filtry oborů vám umožní doladit data, která službě zřizování extrahuje ze služby Azure AD pomocí filtrování podle hodnot atributů pro konkrétní uživatele. Další informace o filtry oborů, naleznete v tématu [zřizování aplikací na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Další postup
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

