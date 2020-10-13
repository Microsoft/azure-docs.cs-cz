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
ms.openlocfilehash: 9559e22a8396de93bbe46f20b76855d9ff00f753
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763716"
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


