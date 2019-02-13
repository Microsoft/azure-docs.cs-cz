---
title: Jak změnit dobu životnosti tokenu výchozí nastavení pro vlastní vyvinuté aplikaci | Dokumentace Microsoftu
description: Jak aktualizovat zásady životnosti tokenu pro vaši aplikaci, kterou vyvíjíte v Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c0700ff40063bfb7709b583f849eed179648306
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203569"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí nastavení životnosti tokenu pro vlastní vyvinuté aplikaci

Azure AD Premium umožňuje vývojářům aplikací a správce tenanta ke konfiguraci životnosti tokenů vydaných pro-důvěrní klienti. Zásady životnosti tokenu jsou nastaveny na základě celého tenanta nebo prostředky, ke kterému přistupujete.

1. Pokud chcete nastavit zásady životnosti tokenu, budete muset stáhnout [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spustit **Connect-AzureAD – potvrďte** příkazu.

    Tady je příklad zásady, který nastaví token obnovení jednomu faktoru maximálního stáří. Vytvoření zásad: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Další postup

* Zobrazit [konfigurovatelné životností tokenů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) postup konfigurace životnosti tokenů vydaných službou Azure AD, jak nastavit Životnost tokenů pro všechny aplikace ve vaší organizaci, pro aplikace s více tenanty nebo pro konkrétní službu objekt zabezpečení ve vaší organizaci. 
* [Odkaz tokenu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

