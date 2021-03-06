---
title: Jak najít ID tenanta – Azure Active Directory
description: Pokyny k vyhledání a Azure Active Directory ID tenanta pro existující předplatné Azure.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764346"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Jak najít ID tenanta Azure Active Directory

Předplatná Azure mají vztah důvěryhodnosti s Azure Active Directory (Azure AD). Azure AD je důvěryhodný k ověřování uživatelů, služeb a zařízení pro předplatné. Každé předplatné má přidružené ID tenanta a existuje několik způsobů, jak získat ID tenanta pro vaše předplatné.

## <a name="find-tenant-id-through-the-azure-portal"></a>Najít ID tenanta prostřednictvím Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
 
1. Vyberte **Azure Active Directory**.

1. Vyberte **Vlastnosti**.

1. Pak se posuňte dolů k poli **ID tenanta** . Vaše ID tenanta bude v poli.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Properties-ID tenanta – pole ID tenanta":::

## <a name="find-tenant-id-with-powershell"></a>Vyhledání ID tenanta pomocí PowerShellu

Klienta můžete také najít programově. Pokud chcete najít ID tenanta pomocí Azure PowerShell, použijte rutinu `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Další informace najdete v tématu Azure PowerShell Reference k rutině pro [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Najít ID tenanta pomocí rozhraní příkazového řádku
Pokud chcete najít ID tenanta pomocí rozhraní příkazového řádku, můžete to udělat pomocí [Azure CLI](/cli/azure/install-azure-cli) nebo [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/). 

V případě Azure CLI použijte jeden z příkazů **AZ Login**, **AZ Account list** nebo **AZ Account tenant list** , jak je znázorněno v následujícím příkladu. Všimněte si, že vlastnost **tenantId** pro každé z vašich předplatných ve výstupu z každého příkazu.

```azurecli-interactive
az login
az account list
az account tenant list
```

Další informace najdete v tématech [AZ Login](/cli/azure/reference-index#az_login) Command reference, [AZ Account](/cli/azure/ext/account/account) Command reference nebo [AZ Account tenant](/cli/azure/ext/account/account/tenant) reference reference.


Pro Microsoft 365 CLI použijte **ID tenanta** rutiny, jak je znázorněno v následujícím příkladu:
 
```cli
m365 tenant id get
```

Další informace najdete v tématu informace o příkazu pro [ID tenanta Microsoft 365 Get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) .


## <a name="next-steps"></a>Další kroky

- Pokud chcete vytvořit nového tenanta Azure AD, přečtěte si téma [rychlý Start: vytvoření nového tenanta v Azure Active Directory](active-directory-access-create-new-tenant.md).

- Informace o tom, jak přidružit nebo přidat předplatné ke klientovi, najdete v tématu [přidružení nebo přidání předplatného Azure ke svému tenantovi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).

- Informace o tom, jak najít ID objektu, najdete v tématu [Vyhledání ID objektu uživatele](/partner-center/find-ids-and-domain-names#find-the-user-object-id).
