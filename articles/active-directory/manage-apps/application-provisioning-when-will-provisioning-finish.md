---
title: Zřizování uživatelů pro aplikaci Galerie Azure AD trvá hodiny nebo více | Microsoft Docs
description: Jak zjistit, proč zřizování vaší aplikace může trvat déle, než jste očekávali.
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
ms.openlocfilehash: ca2257a46815dd5e685b9cc746a64cede510d10a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034190"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Zřizování uživatelů pro aplikaci Galerie Azure AD trvá několik hodin.

Když poprvé povolíte automatické zřizování pro aplikaci, počáteční cyklus může trvat až 20 minut až několik hodin, a to v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. 

Následná synchronizace po počátečním cyklu, protože služba zřizování ukládá meze, které reprezentují stav obou systémů po počátečním cyklu, což zvyšuje výkon následných synchronizací.

## <a name="how-to-improve-provisioning-performance"></a>Jak vylepšit výkon zřizování

Pokud počáteční cyklus trvá déle než pár hodin, můžete k vylepšení výkonu udělat jednu z těchto věcí:

-   **Filtry oboru uživatele.** Filtry oborů vám umožňují vyladit data, která služba zřizování z Azure AD extrahuje, filtrováním uživatelů na základě konkrétních hodnot atributů. Další informace o filtrech oborů najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)

