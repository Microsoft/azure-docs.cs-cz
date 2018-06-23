---
title: Jak změnit dobu životnosti tokenu výchozí nastavení pro aplikaci zákaznických | Microsoft Docs
description: Postup aktualizace životnost tokenu zásady pro aplikace, které vyvíjíte na Azure AD
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: bd963253ee796fb56405e8da855475df2d2f5d08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335335"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí dobu životnosti tokenu aplikace vyvinuté vlastní

Azure AD Premium umožňuje vývojáři aplikace a správci klientů nakonfigurovat životnost tokeny vydané pro-důvěrné klienty. Životnost tokenu zásady jsou nastaveny na základě klienta celou nebo prostředkům přistupuje.

 * Pokud chcete nastavit dobu životnosti tokenu zásady, budete muset stáhnout [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Spustit **Connect-AzureAD-potvrďte** příkaz.

 * Tady je příklad zásady, která nastaví maximální stáří jeden faktor obnovovací token. Vytvořte zásadu: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Najdete v článku věnovaném [životnost tokenu konfigurace](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentu se dozvíte, jak vytvořit další vlastní.

## <a name="next-steps"></a>Další postup
[Konfigurace životnost tokenu](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Odkaz tokenu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

