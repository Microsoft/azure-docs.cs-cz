---
title: Změnit výchozí hodnoty životnosti tokenů pro vlastní aplikace Azure AD | Microsoft Docs
description: Postup aktualizace zásad životnosti tokenů pro aplikaci, kterou vyvíjíte ve službě Azure AD
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702806"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí hodnoty životnosti tokenů pro aplikaci vytvořenou pro vlastní nastavení

V tomto článku se dozvíte, jak pomocí Azure AD PowerShellu nastavit zásady životnosti tokenů. Azure AD Premium umožňuje vývojářům aplikací a správcům tenantů nakonfigurovat životnost tokenů vydaných pro nedůvěrné klienty. Zásady životnosti tokenů se nastavují na úrovni tenanta nebo prostředků, ke kterým se přistupoval.

1. Pokud chcete nastavit zásady životnosti tokenů, musíte si stáhnout [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Spusťte příkaz **Connect-AzureAD-Confirm** .

    Tady je příklad zásady, která nastavuje maximální stáří pro jeden faktor obnovení. Vytvořit zásadu: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Další kroky

* V tématu [konfigurovatelné životnosti tokenů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) se dozvíte, jak nakonfigurovat životnost tokenů vydaných službou Azure AD, včetně nastavení životnosti tokenů pro všechny aplikace ve vaší organizaci, pro víceklientské aplikace nebo pro konkrétní instanční objekt ve vaší organizaci. 
* [Reference k tokenům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
