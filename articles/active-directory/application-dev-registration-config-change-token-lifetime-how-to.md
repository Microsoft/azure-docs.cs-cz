---
title: Jak změnit dobu životnosti tokenu výchozí nastavení pro aplikaci zákaznických | Microsoft Docs
description: Postup aktualizace životnost tokenu zásady pro aplikace, které vyvíjíte na Azure AD
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
ms.openlocfilehash: 8067ecf3e274f65abe2c82f20dd2f4469344f3b6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26614373"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí dobu životnosti tokenu aplikace vyvinuté vlastní

Azure AD Premium umožňuje vývojáři aplikace a správci klientů nakonfigurovat životnost tokeny vydané pro-důvěrné klienty. Životnost tokenu zásady jsou nastaveny na základě klienta celou nebo prostředkům přistupuje.

 * Pokud chcete nastavit dobu životnosti tokenu zásady, budete muset stáhnout [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Spustit **Connect-AzureAD-potvrďte** příkaz.

 * Tady je příklad zásady, která nastaví maximální stáří jeden faktor obnovovací token. Vytvořte zásadu:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Najdete v článku věnovaném [životnost tokenu konfigurace](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentu se dozvíte, jak vytvořit další vlastní.

## <a name="next-steps"></a>Další kroky
[Konfigurace životnost tokenu](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Odkaz tokenu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

