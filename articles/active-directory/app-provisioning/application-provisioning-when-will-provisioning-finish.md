---
title: Zřizování uživatelů v aplikaci Galerie Azure AD trvá několik hodin.
description: Jak zjistit, proč zřizování vaší aplikace může trvat déle, než jste očekávali.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781969"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů pro aplikaci Galerie Azure AD trvá několik hodin.

Když poprvé povolíte automatické zřizování pro aplikaci, počáteční cyklus může trvat až 20 minut až několik hodin, a to v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. 

Následná synchronizace po počátečním cyklu, protože služba zřizování ukládá meze, které reprezentují stav obou systémů po počátečním cyklu, což zvyšuje výkon následných synchronizací.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční cyklus trvá déle než pár hodin, můžete k vylepšení výkonu udělat jednu z těchto věcí:

-   **Filtry oboru uživatele.** Filtry oborů vám umožňují vyladit data, která služba zřizování z Azure AD extrahuje, filtrováním uživatelů na základě konkrétních hodnot atributů. Další informace o filtrech oborů najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

