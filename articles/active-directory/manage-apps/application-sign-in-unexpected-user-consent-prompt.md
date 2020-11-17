---
title: Neočekávaná výzva k vyjádření souhlasu při přihlašování k aplikaci | Microsoft Docs
description: Postup řešení potíží, když se uživateli zobrazí výzva k vyjádření souhlasu s aplikací, kterou jste v Azure AD zaznamenali, že jste neočekávali
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa74424dddd7577eb942f72f038f5bd56854abac
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648187"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Neočekávaná výzva k vyjádření souhlasu při přihlášení k aplikaci

Mnoho aplikací, které jsou integrovány s Azure Active Directory, vyžaduje oprávnění k různým prostředkům, aby bylo možné je spustit. Pokud jsou tyto prostředky integrovány i Azure Active Directory, vyžadují se oprávnění k přístupu k nim pomocí rozhraní pro vyjádření souhlasu Azure AD. 

To má za následek zobrazení výzvy k vyjádření souhlasu při prvním použití aplikace, což je často jednorázová operace. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scénáře, ve kterých uživatelé uvidí výzvy ke souhlasu

Další výzvy můžete očekávat v různých scénářích:

* Sada oprávnění požadovaná aplikací se změnila.

* Uživatel, který byl původně odsouhlasen do aplikace, nebyl správcem a teď jiný uživatel (bez oprávnění správce) používá aplikaci poprvé.

* Uživatel, který byl původně odsouhlasen do aplikace, byl správcem, ale nesouhlasí se jménem celé organizace.

* Aplikace používá [přírůstkové a dynamické vyjádření](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) k vyžádání dalších oprávnění po prvním udělení souhlasu. Tato možnost se často používá v případě, že volitelné funkce aplikace vyžadují oprávnění kromě těch, které jsou potřeba pro základní funkce.

* Po prvním udělení souhlasu byl souhlas odvolán.

* Vývojář nakonfiguroval aplikaci tak, aby při každém použití zobrazila výzva k zadání souhlasu (Poznámka: Toto není osvědčený postup).

## <a name="next-steps"></a>Další kroky

-   [Aplikace, oprávnění a souhlas v Azure Active Directory (koncový bod 1.0)](../develop/quickstart-register-app.md)

-   [Rozsahy, oprávnění a souhlas v Azure Active Directory (koncový bod verze 2.0)](../develop/v2-permissions-and-consent.md)