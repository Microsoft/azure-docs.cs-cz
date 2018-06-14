---
title: Neočekávané souhlasu řádku při přihlášení k aplikaci | Microsoft Docs
description: Řešení potíží, když uživatel vidí výzva k povolení spuštění pro aplikace, které mají integrované s Azure AD, která nepoznáváte
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
ms.openlocfilehash: 0f24ca83922cc17e94683ed1bde5ed782b93df7c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26611805"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Neočekávané souhlasu řádku při přihlášení k aplikaci

Mnoho aplikací, které se integrují s Azure Active Directory vyžaduje oprávnění k různým prostředkům, aby bylo možné spustit. Když tyto prostředky jsou také integrované s Azure Active Directory, oprávnění pro přístup k nim je požadována pomocí rozhraní Azure AD souhlasu. 

To vede souhlasu výzva se zobrazí při prvním aplikaci používají, což je často o jednorázovou operaci. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scénáře, ve kterých se uživatelům zobrazí souhlas výzvy

Další výzvy je možné očekávat v různých situacích:

* Změnily se sadu oprávnění, které jsou požadované aplikací.

* Uživatel, který původně souhlasu ze strany aplikace nebyla správce a nyní různých (bez oprávnění správce) uživatel používá aplikaci poprvé.

* Uživatel, který původně dá souhlas k aplikaci byl správce, ale jejich souhlas jménem aplikace v celé organizaci.

* Aplikace používá [přírůstkové a dynamické souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) s žádostí o další oprávnění po začátku udělen souhlas. To se často používá, pokud volitelné funkce další aplikace vyžadují oprávnění nad rámec těch, vyžaduje se pro základní funkce.

* Po udělení původně byl odvolán souhlasu.

* Vývojář nakonfiguroval aplikaci tak, aby vyžadovala výzva k povolení spuštění pokaždé, když se používá (Poznámka: Toto není vhodné).

## <a name="next-steps"></a>Další kroky

-   [Aplikace, oprávnění a souhlasu v Azure Active Directory (koncový bod verze 1.0)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Obory, oprávnění a souhlasu ve službě Azure Active Directory (koncový bod v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


