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
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 90b4468693736068bd54b62082c43a1988202e6f
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509100"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Jak změnit výchozí hodnoty životnosti tokenů pro aplikaci vytvořenou pro vlastní nastavení

V tomto článku se dozvíte, jak pomocí Azure AD PowerShellu nastavit zásady životnosti tokenů. Azure AD Premium umožňuje vývojářům aplikací a správcům tenantů nakonfigurovat životnost tokenů vydaných pro nedůvěrné klienty. Zásady životnosti tokenů se nastavují na úrovni tenanta nebo prostředků, ke kterým se přistupoval.

> [!IMPORTANT]
> Po 2020 května již klienti nebudou moci konfigurovat dobu platnosti tokenů aktualizace a relace.  Azure Active Directory přestane dodržovat stávající konfiguraci tokenu aktualizace a tokenu relace v zásadách po 30. lednu 2021. Po vyřazení můžete i po vyřazení nakonfigurovat životnosti přístupového tokenu. Další informace najdete v tématu [Konfigurace životností tokenů ve službě Azure AD](./active-directory-configurable-token-lifetimes.md).
> Implementovali jsme [Možnosti správy relace ověřování](../conditional-access/howto-conditional-access-session-lifetime.md)   v podmíněném přístupu Azure AD. Tuto novou funkci můžete použít ke konfiguraci životností tokenů aktualizace nastavením frekvence přihlášení.  

Pokud chcete nastavit zásady životnosti tokenů, musíte si stáhnout [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
Spusťte příkaz **Connect-AzureAD-Confirm** .

Tady je příklad zásady, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji. Tato zásada nastavuje dobu života tokenů přístupu a ID a maximální stáří tokenu relace Multi-Factor Service k instančnímu objektu vaší webové aplikace. Vytvořte zásadu a přiřaďte ji k instančnímu objektu. Také je nutné získat ObjectId objektu služby.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Další kroky

* V tématu [konfigurovatelné životnosti tokenů ve službě Azure AD](./active-directory-configurable-token-lifetimes.md) se dozvíte, jak nakonfigurovat životnost tokenů vydaných službou Azure AD, včetně nastavení životnosti tokenů pro všechny aplikace ve vaší organizaci, pro víceklientské aplikace nebo pro konkrétní instanční objekt ve vaší organizaci. 
* [Reference k tokenům Azure AD](./id-tokens.md)