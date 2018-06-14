---
title: Zřizování uživatelů k aplikaci Azure AD Galerie je pořízení hodiny nebo víc | Microsoft Docs
description: Jak zjistit, proč zajišťování, které vaše aplikace může trvá déle, než jste očekávali
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9ed12569c0adc5ed8625a8d9fc81c9bee874cd4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614062"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů k aplikaci Azure AD Galerie je pořízení hodin nebo více

Při prvním povolení automatické zřizování pro aplikace, počáteční synchronizace může trvat od 20 minut několik hodin v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. 

Následné synchronizace po počáteční synchronizace být rychlejší jako službu zřizování ukládá vodoznaky, které představují stav obou systémů po počáteční synchronizaci, zvýšení výkonu následné synchronizace.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční synchronizace trvá víc než několik hodin, je jednou z věcí, které může provádět ke zlepšení výkonu:

-   **Filtry oboru uživatele.** Oboru filtrů umožňují optimalizovat data, která službu zřizování extrahuje z Azure AD pomocí filtrování uživatelů na základě hodnot určitým atributem. Další informace o filtry oborů najdete v tématu [zřizování aplikace na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Další kroky
[Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](active-directory-saas-app-provisioning.md)

