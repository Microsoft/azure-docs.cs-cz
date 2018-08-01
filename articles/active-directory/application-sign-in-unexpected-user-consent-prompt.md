---
title: Výzva k povolení spuštění neočekávané při přihlašování k aplikaci | Dokumentace Microsoftu
description: Jak řešit potíže, když se uživateli zobrazí výzva souhlasu pro aplikaci, kterou jste integrovali s Azure AD, která jste neočekávali
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
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
ms.openlocfilehash: a5bf4de00073abb5edbbd5db74d8ee3ebce16913
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365327"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Výzva k povolení spuštění neočekávané při přihlašování k aplikaci

Mnoho aplikací, které se integrují s Azure Active Directory vyžaduje oprávnění k různým prostředkům, aby bylo možné spustit. Když jsou tyto prostředky také integrované s Azure Active Directory, oprávnění pro přístup k nim je požadována pomocí rozhraní Azure AD pro udělování souhlasu. 

V důsledku vyjádření souhlasu výzvy se zobrazí při prvním aplikaci používají, což je často o jednorázovou operaci. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Souhlas scénáře, ve kterých se uživatelům zobrazí výzvy

Další výzvy můžete očekávat v různých scénářích:

* Sada oprávnění vyžadované aplikací se změnily.

* Uživatel, který původně vyjádřil souhlas pro aplikaci nebyl správce, a teď různých (bez oprávnění správce) uživatel používá aplikaci poprvé.

* Uživatel, který původně odsouhlasený. aplikace byla správcem, ale jejich neudělil souhlas jménem celé organizace.

* Aplikace používá [přírůstkové a dynamické souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) s žádostí o další oprávnění po byl zpočátku udělení souhlasu. To se často používá při volitelné funkce další aplikace vyžadují oprávnění nad rámec těch, vyžaduje se pro základní funkce.

* Vyjádření souhlasu bylo zrušeno po udělením původně.

* Vývojář nakonfiguroval aplikaci tak, aby vyžadovala výzva k povolení spuštění pokaždé, když se používá (Poznámka: se nejedná o osvědčený postup).

## <a name="next-steps"></a>Další postup

-   [Aplikace, oprávnění a vyjádření souhlasu v Azure Active Directory (koncový bod verze 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Rozsahy, oprávnění a vyjádření souhlasu v Azure Active Directory (koncový bod v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


