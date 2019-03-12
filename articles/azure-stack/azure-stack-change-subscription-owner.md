---
title: Aktualizace vlastníka na předplatné uživatele Azure stacku | Dokumentace Microsoftu
description: Změňte vlastníka fakturace pro předplatná Azure Stack uživatelů.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 02c5d063246844d5ed8945aba38aaada87691627
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763193"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Změnit vlastníka předplatného uživatele Azure stacku

Operátoři Azure stacku pomocí prostředí PowerShell můžete změnit fakturační vlastník předplatného uživatele. Jedním z důvodů, chcete-li změnit vlastníka, je třeba nahradit jako uživatel, který vaši organizaci opustí.

Existují dva typy *vlastníky* , které jsou přiřazeny k předplatnému:

- **Vlastník fakturace**: Ve výchozím nastavení vlastník fakturace je uživatelský účet, který získá předplatné z nabídky a pak vlastní fakturační vztah pro dané předplatné. Tento účet je také správcem předplatného. Pouze jeden uživatelský účet může mít toto označení na příslušný odběr. Fakturační vlastník je často týmu nebo organizace zájemce.

  Můžete použít rutinu Powershellu [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) Změna fakturační vlastníka.  

- **Vlastníci přidaného přes role RBAC** – dalším uživatelům můžete udělit **vlastníka** role pomocí [řízení přístupu na základě rolí](azure-stack-manage-permissions.md) systému (RBAC). Jako vlastníky návrzích fakturační vlastníka lze přidat libovolný počet dalších uživatelských účtů. Další vlastníky jsou také správci předplatného a mají všechna oprávnění pro předplatné, s výjimkou oprávnění k odstranění fakturační vlastníka.

  Prostředí PowerShell můžete použít ke správě další vlastníky. Další informace najdete v [tomto článku](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Změny fakturace vlastníka

Spusťte následující skript, který změnit vlastníka fakturace na předplatné uživatele. Počítač, který používáte ke spuštění skriptu musíte připojit ke službě Azure Stack a spustit modul Azure Stack Powershellu 1.3.0 nebo novější. Další informace najdete v tématu [instalace Azure Stack Powershellu](azure-stack-powershell-install.md).

>[!NOTE]
>Ve více tenantů Azure Stack nový vlastník musí být ve stejném adresáři jako stávající vlastník. Než budete moct poskytovat vlastnictví předplatného na uživatele, který je v jiném adresáři, musíte nejdřív [pozvat uživatele jako Host do adresáře](../active-directory/b2b/add-users-administrator.md).

Nahraďte následující hodnoty ve skriptu, před jejím spuštěním:

- **$ArmEndpoint**: Koncový bod Resource Manageru pro vaše prostředí.
- **$TenantId**: ID vašeho Tenanta.
- **$SubscriptionId**: ID vašeho předplatného.
- **$OwnerUpn**: Účet, například **user@example.com**, chcete-li přidat jako nový vlastník fakturace.

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Další postup

- [Správa řízení přístupu na základě rolí](azure-stack-manage-permissions.md)
