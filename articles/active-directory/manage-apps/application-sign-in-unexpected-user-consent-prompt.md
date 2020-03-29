---
title: Výzva k neočekávanému souhlasu při přihlášení k aplikaci | Dokumenty společnosti Microsoft
description: Jak řešit potíže, když uživatel uvidí výzvu k souhlasu pro aplikaci, kterou jste integrovali s Azure AD, kterou jste nečekali
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
ms.openlocfilehash: 83d043ecef152f977437e21e2caec40d1c40ce0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781153"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Výzva k neočekávanému souhlasu při přihlášení k aplikaci

Mnoho aplikací, které se integrují se službou Azure Active Directory, vyžaduje ke spuštění oprávnění k různým prostředkům. Když jsou tyto prostředky integrované s Azure Active Directory, oprávnění k přístupu k nim se požaduje pomocí rámce souhlasu Azure AD. 

Výsledkem je, že se při prvním použití aplikace zobrazí výzva k souhlasu, což je často jednorázová operace. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scénáře, ve kterých se uživatelům zobrazí výzvy k souhlasu

Další výzvy lze očekávat v různých scénářích:

* Sada oprávnění vyžadovaná aplikací byla změněna.

* Uživatel, který původně souhlasil s aplikací, nebyl správcem a nyní jiný (ne-admin) Uživatel používá aplikaci poprvé.

* Uživatel, který původně souhlasil s aplikací, byl správcem, ale nesouhlasil jménem celé organizace.

* Aplikace používá [přírůstkový a dynamický souhlas](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) k vyžádání dalších oprávnění po udělení souhlasu. To se často používá, když volitelné funkce aplikace další vyžadovat oprávnění nad rámec těch, které jsou požadovány pro základní funkce.

* Souhlas byl zrušen poté, co byl původně udělen.

* Vývojář nakonfiguroval aplikaci tak, aby vyžadovala výzvu k souhlasu při každém použití (poznámka: to není osvědčený postup).

## <a name="next-steps"></a>Další kroky

-   [Aplikace, oprávnění a souhlas ve službě Azure Active Directory (koncový bod v1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Obory, oprávnění a souhlas ve službě Azure Active Directory (koncový bod v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


