---
title: Odebrání přístupu k delegování
description: Naučte se, jak odebrat přístup k prostředkům, které byly delegované pro poskytovatele služeb pro Azure Lighthouse.
ms.date: 12/03/2020
ms.topic: how-to
ms.openlocfilehash: d3442bb9fd2f6e7423fd4bf28cace1f7fd91ad80
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608448"
---
# <a name="remove-access-to-a-delegation"></a>Odebrání přístupu k delegování

Po delegování předplatného nebo skupiny prostředků zákazníka k poskytovateli služeb pro [Azure Lighthouse](../overview.md)se delegování dá v případě potřeby odebrat. Po odebrání delegace se už nebude vztahovat přístup [správy delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md) , který byl dříve udělen uživatelům v tenantovi poskytovatele služeb.

Odebrání delegování může provést uživatel v tenantovi zákazníka nebo v tenantovi poskytovatele služeb, pokud má uživatel příslušná oprávnění.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](../concepts/enterprise.md) , používat stejné procesy.

## <a name="customers"></a>Zákazníci

Uživatelé v tenantovi zákazníka, kteří mají [předdefinovanou roli](../../role-based-access-control/built-in-roles.md#owner) předplatného pro předplatné, můžou odebrat přístup poskytovatele služeb k tomuto předplatnému (nebo ke skupinám prostředků v tomto předplatném). K tomu může uživatel v tenantovi zákazníka přejít na [stránku poskytovatelé služeb](view-manage-service-providers.md#add-or-remove-service-provider-offers) Azure Portal, najít nabídku na obrazovce **nabídky poskytovatel služeb** a vybrat ikonu odpadkového koše na řádku této nabídky.

Po potvrzení odstranění nebudou mít žádní uživatelé v tenantovi poskytovatele služeb přístup k prostředkům, které už byly delegované.

## <a name="service-providers"></a>Poskytovatelé služeb

Uživatelé ve spravovaném tenantovi můžou odebrat přístup k delegovaným prostředkům, pokud jim byla udělená [role odstranit přiřazení registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) pro prostředky zákazníka. Pokud tato role není přiřazená k žádnému uživateli poskytovatele služeb, delegování může odebrat jenom uživatel v tenantovi zákazníka.

Níže uvedený příklad ukazuje přiřazení, které uděluje **přiřazení registrace spravovaných služeb** , která může být součástí souboru parametrů během [procesu připojování](onboard-customer.md):

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Tato role se dá vybrat taky při **autorizaci** při [vytváření nabídky spravované služby](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) pro publikování na Azure Marketplace.

Uživatel s tímto oprávněním může odebrat delegování jedním z následujících způsobů.

### <a name="azure-portal"></a>Azure Portal

1. Přejděte na [stránku Moji zákazníci](view-manage-customers.md).
2. Vyberte **delegování**.
3. Vyhledejte delegování, které chcete odebrat, a pak vyberte ikonu odpadkového koše, která se zobrazí na jeho řádku.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Další kroky

- Další informace o [správě delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md)
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
- Přečtěte si, jak [aktualizovat předchozí delegování](update-delegation.md).
