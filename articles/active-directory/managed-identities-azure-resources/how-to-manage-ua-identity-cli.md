---
title: Správa uživatelsky přiřazené spravované identity – Azure CLI – Azure AD
description: Podrobné pokyny, jak vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375508"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí Azure CLI


Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak pomocí Azure CLI vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem.

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal (viz další oddíl).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - Pokud upřednostňujete použití místní konzoly CLI, [nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější). Přihlaste se k Azure pomocí `az login`pomocí účtu, který je přidružený k předplatnému Azure, v rámci kterého chcete nasadit spravovanou identitu přiřazenou uživatelem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Pomocí příkazu [AZ identity Create](/cli/azure/identity#az-identity-create) vytvořte spravovanou identitu přiřazenou uživatelem. Parametr `-g` určuje skupinu prostředků, ve které se má vytvořit spravovaná identita přiřazená uživateli, a parametr `-n` určuje jeho název. Nahraďte hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Pokud chcete zobrazit seznam spravovaných identit přiřazených uživateli, použijte příkaz [AZ identity list](/cli/azure/identity#az-identity-list) . Nahraďte `<RESOURCE GROUP>` vlastní hodnotou:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
V odpovědi JSON mají uživatelem přiřazené spravované identity `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu vrácenou pro klíč, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, použijte příkaz [AZ identity Delete](/cli/azure/identity#az-identity-delete) .  Parametr-n určuje jeho název a parametr-g určuje skupinu prostředků, ve které se vytvořila spravovaná identita přiřazená uživatelem. Nahraďte hodnoty parametrů `<USER ASSIGNED IDENTITY NAME>` a `<RESOURCE GROUP>` vlastními hodnotami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Odstraněním spravované identity přiřazené uživatelem se neodebere odkaz z libovolného prostředku, ke kterému byl přiřazen. Odeberte je prosím z VM/VMSS pomocí příkazu `az vm/vmss identity remove`.

## <a name="next-steps"></a>Další kroky

Úplný seznam příkazů Azure CLI identity najdete v tématu [AZ identity](/cli/azure/identity).

Informace o přiřazení spravované identity přiřazené uživatelem k virtuálnímu počítači Azure najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity) .


 
