---
title: Zřizování uživatelů v aplikaci Galerie Azure AD trvá několik hodin.
description: Jak zjistit, proč zřizování vaší aplikace může trvat déle, než jste očekávali.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: d663962dab058a108faa7d5310a3f2892c345a75
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593892"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů pro aplikaci Galerie Azure AD trvá několik hodin.

Když poprvé povolíte automatické zřizování pro aplikaci, počáteční cyklus může trvat až 20 minut až několik hodin, a to v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. 

Následná synchronizace po počátečním cyklu, protože služba zřizování ukládá meze, které reprezentují stav obou systémů po počátečním cyklu, což zvyšuje výkon následných synchronizací.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční cyklus trvá déle než pár hodin, můžete k vylepšení výkonu udělat jednu z těchto věcí:

-   **Filtry oboru uživatele.** Filtry oborů vám umožňují vyladit data, která služba zřizování z Azure AD extrahuje, filtrováním uživatelů na základě konkrétních hodnot atributů. Další informace o filtrech oborů najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

