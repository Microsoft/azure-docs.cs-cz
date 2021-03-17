---
title: Konfigurace klasifikací oprávnění pomocí Azure AD
description: Naučte se spravovat klasifikace delegovaných oprávnění.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 12a4ffb311e01ebb78b1ae392d1243c5d67eff6b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644560"
---
# <a name="configure-permission-classifications"></a>Konfigurace klasifikací oprávnění

Klasifikace oprávnění umožňují určit dopad, který mají různá oprávnění podle zásad a hodnocení rizik vaší organizace. Klasifikace oprávnění můžete například použít v zásadách pro vyjádření souhlasu k identifikaci sady oprávnění, ke kterým můžou uživatelé udělit souhlas.

## <a name="manage-permission-classifications"></a>Spravovat klasifikace oprávnění

V současné době je podporována pouze klasifikace oprávnění "nízká dopad". Pouze delegovaná oprávnění, která nevyžadují souhlas správce, je možné klasifikovat jako "nízký dopad".

> [!TIP]
> Minimální oprávnění potřebná k provedení základních přihlášení jsou,, `openid` `profile` `email` `User.Read` a `offline_access` , která jsou všechna delegovaná oprávnění na Microsoft Graph. Pomocí těchto oprávnění může aplikace číst podrobnosti o plném profilu přihlášeného uživatele a může tento přístup zachovat i v případě, že uživatel už aplikaci nepoužívá.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí následujících kroků můžete klasifikovat oprávnění pomocí Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako [globální správce](../roles/permissions-reference.md#global-administrator), [správce aplikace](../roles/permissions-reference.md#application-administrator)nebo [správce cloudové aplikace](../roles/permissions-reference.md#cloud-application-administrator) .
1. Vyberte **Azure Active Directory**  >  souhlasu **podnikových aplikací**  >  **a**  >  **klasifikace oprávnění**.
1. Zvolením možnosti **Přidat oprávnění** Klasifikujte jiné oprávnění jako "nízký dopad".
1. Vyberte rozhraní API a pak vyberte delegovaná oprávnění.

V tomto příkladu jsme klasifikováni s minimální sadou oprávnění vyžadovaných pro jednotné přihlašování:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Klasifikace oprávnění":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K klasifikaci oprávnění můžete použít nejnovější modul Azure AD PowerShell Preview, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview). Klasifikace oprávnění jsou nakonfigurovány na objektu **ServicePrincipal** rozhraní API, který publikuje oprávnění.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>Vypíše aktuální klasifikace oprávnění pro rozhraní API.

1. Načte objekt **ServicePrincipal** pro rozhraní API. Tady načteme objekt ServicePrincipal pro rozhraní API pro Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Přečtěte si klasifikace delegovaných oprávnění pro rozhraní API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Klasifikace oprávnění jako "nízký dopad"

1. Načte objekt **ServicePrincipal** pro rozhraní API. Tady načteme objekt ServicePrincipal pro rozhraní API pro Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Vyhledejte delegované oprávnění, které byste chtěli klasifikovat:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Nastavte klasifikaci oprávnění pomocí názvu a ID oprávnění:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Odebrání klasifikace delegovaného oprávnění

1. Načte objekt **ServicePrincipal** pro rozhraní API. Tady načteme objekt ServicePrincipal pro rozhraní API pro Microsoft Graph:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Vyhledejte klasifikaci delegovaného oprávnění, kterou chcete odebrat:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Odstraňte klasifikaci oprávnění:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématech:

* [Konfigurovat nastavení souhlasu uživatele](configure-user-consent.md)
* [Konfigurace pracovního postupu pro vyjádření souhlasu správce](configure-admin-consent-workflow.md)
* [Naučte se spravovat souhlas s aplikacemi a hodnotit žádosti o souhlas.](manage-consent-requests.md)
* [Udělení souhlasu správce v rámci celého tenanta aplikaci](grant-admin-consent.md)
* [Oprávnění a souhlas na platformě Microsoft identity](../develop/v2-permissions-and-consent.md)

Pokud chcete získat nápovědu nebo najít odpovědi na své otázky:
* [Azure AD v Microsoft Q&A](/answers/topics/azure-active-directory.html)