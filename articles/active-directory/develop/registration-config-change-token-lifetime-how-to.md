---
title: Jak změnit dobu životnosti tokenu výchozí nastavení pro vlastní vyvinuté aplikaci | Dokumentace Microsoftu
description: Jak aktualizovat zásady životnosti tokenu pro vaši aplikaci, kterou vyvíjíte v Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 059920c710b202e22a22f8431c536c5dfa19f2b9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724019"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí nastavení životnosti tokenu pro vlastní vyvinuté aplikaci

Azure AD Premium umožňuje vývojářům aplikací a správce tenanta ke konfiguraci životnosti tokenů vydaných pro-důvěrní klienti. Zásady životnosti tokenu jsou nastaveny na základě celého tenanta nebo prostředky, ke kterému přistupujete.

 * Pokud chcete nastavit zásady životnosti tokenu, budete muset stáhnout [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Spustit **Connect-AzureAD – potvrďte** příkazu.

 * Tady je příklad zásady, který nastaví token obnovení jednomu faktoru maximálního stáří. Vytvoření zásad: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Rezervace [životnost tokenu konfigurace](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentu se naučíte vytvořit další vlastní.

## <a name="next-steps"></a>Další postup
[Konfigurace životnosti tokenu](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Odkaz tokenu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

