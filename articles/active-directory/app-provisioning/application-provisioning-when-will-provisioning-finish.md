---
title: Zřizování uživatelů do aplikace Azure AD Gallery trvá hodiny nebo déle
description: Jak zjistit, proč zřizování do vaší aplikace může trvat déle, než jste očekávali
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522641"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů do aplikace Azure AD Gallery trvá hodiny nebo víc

Při prvním povolení automatické zřizování pro aplikaci počáteční cyklus může trvat od 20 minut do několika hodin, v závislosti na velikosti adresáře Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizace po počátečním cyklu být rychlejší, jako zřizovací služba ukládá vodoznaky, které představují stav obou systémů po počátečnícyklus, zlepšení výkonu následné synchronizace.

## <a name="how-to-improve-provisioning-performance"></a>Jak zlepšit výkon zřizování

Pokud počáteční cyklus trvá déle než několik hodin, je jedna věc, kterou můžete udělat pro zlepšení výkonu:

-   **Filtry oborů uživatelů.** Filtry oborů umožňují doladit data, která služba zřizování extrahuje z Azure AD filtrováním uživatelů na základě konkrétních hodnot atributů. Další informace o filtrech oborů naleznete v [tématu Zřizování aplikací založených na atributech pomocí filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

