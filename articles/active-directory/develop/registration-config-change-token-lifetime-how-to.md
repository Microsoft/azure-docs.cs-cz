---
title: Změna výchozích hodnot životnosti tokenu pro vlastní aplikace Azure AD | Dokumenty společnosti Microsoft
description: Jak aktualizovat zásady Token Lifetime pro vaši aplikaci, kterou vyvíjíte ve službě Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 431f18b9babb52b5000d3bf4cca75a0f5e29bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702806"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí hodnoty životnosti tokenu pro vlastní vyvinutou aplikaci

Tento článek ukazuje, jak pomocí Prostředí Azure AD PowerShell nastavit zásady životnosti tokenu. Azure AD Premium umožňuje vývojářům aplikací a správcům tenantů konfigurovat životnost tokenů vydaných pro klienty, kteří nemají důvěrnou povahu. Zásady životnosti tokenu jsou nastaveny na základě celého tenanta nebo prostředky, ke které se přistupuje.

1. Chcete-li nastavit zásady životnosti tokenu, je třeba stáhnout [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spusťte příkaz **Connect-AzureAD -Confirm.**

    Tady je příklad zásady, která nastavuje token aktualizace s maximálním věkem pro jeden faktor. Vytvořte zásadu:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Další kroky

* Informace o konfiguraci životnosti tokenů vydaných službou Azure AD najdete v [tématu Konfigurovatelné životnosti tokenů ve službě Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) včetně způsobu nastavení životnosti tokenů pro všechny aplikace ve vaší organizaci, pro víceklientskou aplikaci nebo pro konkrétní instanční objekt ve vaší organizaci. 
* [Odkaz na token azure reklamy](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
