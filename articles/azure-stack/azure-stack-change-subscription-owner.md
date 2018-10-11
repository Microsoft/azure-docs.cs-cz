---
title: Aktualizace vlastníka na předplatné uživatele Azure stacku | Dokumentace Microsoftu
description: Změňte vlastníka fakturace pro předplatná Azure STack uživatelů.
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
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: a784f169bfdf23255b27d50f0e135384db6b2b88
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077625"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Změnit vlastníka předplatného uživatele Azure stacku

Operátoři Azure stacku pomocí prostředí PowerShell můžete změnit vlastníka fakturace na předplatné uživatele. Jedním z důvodů, chcete-li změnit vlastníka je nahradit jako uživatel, který vaši organizaci opustí.   

Existují dva typy *vlastníky* , které jsou přiřazeny k předplatnému:

- **Vlastník fakturace** – ve výchozím nastavení, vlastník fakturace je uživatelský účet, který získá předplatné z nabídky a pak vlastní fakturační vztah pro dané předplatné. Tento účet je také správcem předplatného.  Pouze jeden uživatelský účet může mít toto označení na příslušný odběr. Jako vlastníka fakturace je často týmu nebo organizace zájemce. 

  Použijte rutinu prostředí PowerShell **Set-AzsUserSubscription** změnit vlastníka fakturace.  

- **Vlastníci přidaného přes role RBAC** – dalším uživatelům můžete udělit pomocí role vlastník [řízení přístupu na základě rolí](azure-stack-manage-permissions.md) systému (RBAC).  Jako vlastníky návrzích vlastníka fakturace lze přidat libovolný počet dalších uživatelských účtů. Další vlastníky jsou také správci předplatného a mají všechna oprávnění pro odběr s výjimkou oprávnění k odstranění vlastníka fakturace. 

  Můžete použít PowerShell spravovat další vlastníky naleznete v tématu [prostředí Azure PowerShell ke správě řízení přístupu na základě rolí]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="change-the-billing-owner"></a>Změnit vlastníka fakturace
Spusťte následující skript, který změnit vlastníka fakturace na předplatné uživatele.  Počítač, který používáte ke spuštění skriptu musíte připojit ke službě Azure Stack a spustit modul Azure Stack Powershellu 1.3.0 nebo novější. Další informace najdete v tématu [instalace Azure Stack Powershellu](azure-stack-powershell-install.md). 

> [!Note]  
>  V Azure stacku víceklientských nový vlastník musí být ve stejném adresáři jako stávající vlastník. Než budete moct poskytovat vlastnictví předplatného na uživatele, který je v jiném adresáři, musíte nejdřív [pozvat uživatele jako Host do adresáře](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Nahraďte následující hodnoty ve skriptu, před jejím spuštěním: 
 
- **$ArmEndpoint** – zadejte koncový bod Resource Manageru pro vaše prostředí.  
- **$TenantId** – zadejte ID Tenanta. 
- **$SubscriptionId** – zadejte ID svého předplatného.
- **$OwnerUpn** -zadání účtu jako *user@example.com* přidat jako nový vlastník fakturace.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>Další postup
[Správa řízení přístupu na základě rolí](azure-stack-manage-permissions.md)
