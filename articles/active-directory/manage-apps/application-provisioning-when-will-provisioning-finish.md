---
title: Zřizování uživatelů pro aplikaci Galerie Azure AD je trvá hodiny nebo víc | Dokumentace Microsoftu
description: Jak zjistit, proč zřizování pro vaši aplikaci může trvá déle, než jste očekávali
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 834e2532354b91410943f5fe2b2e78bca0bd0922
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173041"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů pro aplikaci Galerie Azure AD je trvá hodiny nebo víc

Při prvním povolení automatického zřizování pro aplikace, počáteční synchronizace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizuje po počáteční synchronizaci být rychlejší, zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční synchronizace trvá déle než několik hodin, je jednou z věcí, které vám pomůžou zlepšit výkon:

-   **Filtry oborů uživatele.** Filtry oborů vám umožní doladit data, která službě zřizování extrahuje ze služby Azure AD pomocí filtrování podle hodnot atributů pro konkrétní uživatele. Další informace o filtry oborů, naleznete v tématu [zřizování aplikací na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Další postup
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

