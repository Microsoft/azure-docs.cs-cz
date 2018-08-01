---
title: Jak změnit dobu životnosti tokenu výchozí nastavení pro vlastní vyvinuté aplikaci | Dokumentace Microsoftu
description: Jak aktualizovat zásady životnosti tokenu pro vaši aplikaci, kterou vyvíjíte v Azure AD
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
ms.openlocfilehash: 0ecb1f55309901abd2c623d2c3d23ef717fb176b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365098"
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

