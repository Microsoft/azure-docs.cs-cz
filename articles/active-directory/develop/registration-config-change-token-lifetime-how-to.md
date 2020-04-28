---
title: Změna výchozích hodnot životnosti tokenů pro vlastní aplikace Azure AD
description: Postup aktualizace zásad životnosti tokenů pro aplikaci, kterou vyvíjíte ve službě Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883349"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí hodnoty životnosti tokenů pro aplikaci vytvořenou pro vlastní nastavení

V tomto článku se dozvíte, jak pomocí Azure AD PowerShellu nastavit zásady životnosti tokenů. Azure AD Premium umožňuje vývojářům aplikací a správcům tenantů nakonfigurovat životnost tokenů vydaných pro nedůvěrné klienty. Zásady životnosti tokenů se nastavují na úrovni tenanta nebo prostředků, ke kterým se přistupoval.

1. Pokud chcete nastavit zásady životnosti tokenů, musíte si stáhnout [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spusťte příkaz **Connect-AzureAD-Confirm** .

    Tady je příklad zásady, která nastavuje maximální stáří pro jeden faktor obnovení. Vytvořte zásadu:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Další kroky

* V tématu [konfigurovatelné životnosti tokenů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) se dozvíte, jak nakonfigurovat životnost tokenů vydaných službou Azure AD, včetně nastavení životnosti tokenů pro všechny aplikace ve vaší organizaci, pro víceklientské aplikace nebo pro konkrétní instanční objekt ve vaší organizaci. 
* [Reference k tokenům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
