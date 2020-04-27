---
title: Neočekávaná výzva k vyjádření souhlasu při přihlašování k aplikaci | Microsoft Docs
description: Postup řešení potíží, když se uživateli zobrazí výzva k vyjádření souhlasu s aplikací, kterou jste v Azure AD zaznamenali, že jste neočekávali
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "65781153"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Neočekávaná výzva k vyjádření souhlasu při přihlášení k aplikaci

Mnoho aplikací, které jsou integrovány s Azure Active Directory, vyžaduje oprávnění k různým prostředkům, aby bylo možné je spustit. Pokud jsou tyto prostředky integrovány i Azure Active Directory, vyžadují se oprávnění k přístupu k nim pomocí rozhraní pro vyjádření souhlasu Azure AD. 

To má za následek zobrazení výzvy k vyjádření souhlasu při prvním použití aplikace, což je často jednorázová operace. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scénáře, ve kterých uživatelé uvidí výzvy ke souhlasu

Další výzvy můžete očekávat v různých scénářích:

* Sada oprávnění požadovaná aplikací se změnila.

* Uživatel, který byl původně odsouhlasen do aplikace, nebyl správcem a teď jiný uživatel (bez oprávnění správce) používá aplikaci poprvé.

* Uživatel, který byl původně odsouhlasen do aplikace, byl správcem, ale nesouhlasí se jménem celé organizace.

* Aplikace používá [přírůstkové a dynamické vyjádření](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) k vyžádání dalších oprávnění po prvním udělení souhlasu. Tato možnost se často používá v případě, že volitelné funkce aplikace vyžadují oprávnění kromě těch, které jsou potřeba pro základní funkce.

* Po prvním udělení souhlasu byl souhlas odvolán.

* Vývojář nakonfiguroval aplikaci tak, aby při každém použití zobrazila výzva k zadání souhlasu (Poznámka: Toto není osvědčený postup).

## <a name="next-steps"></a>Další kroky

-   [Aplikace, oprávnění a souhlas v Azure Active Directory (koncový bod 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Rozsahy, oprávnění a souhlas v Azure Active Directory (koncový bod verze 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


