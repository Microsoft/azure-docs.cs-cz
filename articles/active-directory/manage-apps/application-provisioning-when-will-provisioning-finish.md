---
title: Zřizování uživatelů pro aplikaci Galerie Azure AD je trvá hodiny nebo víc | Dokumentace Microsoftu
description: Jak zjistit, proč zřizování pro vaši aplikaci může trvá déle, než jste očekávali
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 94859aa57ef6f565c1fe7ed4b199aff997661d0b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478074"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů pro aplikaci Galerie Azure AD je trvá hodiny nebo víc

Při prvním povolení automatického zřizování pro aplikace, počáteční synchronizace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizuje po počáteční synchronizaci být rychlejší, zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční synchronizace trvá déle než několik hodin, je jednou z věcí, které vám pomůžou zlepšit výkon:

-   **Filtry oborů uživatele.** Filtry oborů vám umožní doladit data, která službě zřizování extrahuje ze služby Azure AD pomocí filtrování podle hodnot atributů pro konkrétní uživatele. Další informace o filtry oborů, naleznete v tématu [zřizování aplikací na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Další postup
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

