---
title: Správa uživatelsky přiřazené spravované identity – Azure CLI – Azure AD
description: Podrobné pokyny, jak vytvořit, zobrazit seznam a odstranit spravovanou identitu přiřazenou uživatelem pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 68f305156645d69049519cd383f06b28ab9b5e03
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184862"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí Azure CLI


Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak pomocí Azure CLI vytvořit, zobrazit a odstranit spravovanou identitu přiřazenou uživatelem.

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](overview.md). Další informace o spravovaných typech identity přiřazených systémem a uživatelem definovaných uživatelů najdete v tématu [spravované identity typu](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Pokud chcete změnit uživatelská oprávnění při používání instančního objektu App Service pomocí rozhraní příkazového řádku, musíte v Azure Graph API AD zadat další oprávnění instančního objektu, protože části CLI provádějí požadavky GET na Graph API. V opačném případě může dodržet zprávu "nedostatečná oprávnění k dokončení operace". Pokud to chcete provést, budete muset přejít do registrace aplikace v Azure Active Directory, vybrat aplikaci, kliknout na oprávnění rozhraní API, přejít dolů a vybrat Azure Active Directory Graph. Z výběru oprávnění aplikace a pak přidejte příslušná oprávnění. 

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Pomocí příkazu [AZ identity Create](/cli/azure/identity#az-identity-create) vytvořte spravovanou identitu přiřazenou uživatelem. `-g`Parametr určuje skupinu prostředků, ve které se má vytvořit spravovaná identita přiřazená uživateli, a `-n` parametr určuje jeho název. Nahraďte `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` hodnoty parametrů a vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Pokud chcete zobrazit seznam spravovaných identit přiřazených uživateli, použijte příkaz [AZ identity list](/cli/azure/identity#az-identity-list) . Nahraďte `<RESOURCE GROUP>` vlastní hodnotou:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

V odpovědi JSON má uživatelem přiřazené spravované identity `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu vrácenou pro klíč, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, použijte příkaz [AZ identity Delete](/cli/azure/identity#az-identity-delete) .  Parametr-n určuje jeho název a parametr-g určuje skupinu prostředků, ve které se vytvořila spravovaná identita přiřazená uživatelem. Nahraďte `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` hodnoty parametrů a vlastními hodnotami:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Odstraněním spravované identity přiřazené uživatelem se neodebere odkaz z libovolného prostředku, ke kterému byl přiřazen. Odeberte je prosím z VM/VMSS pomocí `az vm/vmss identity remove` příkazu.

## <a name="next-steps"></a>Další kroky

Úplný seznam příkazů Azure CLI identity najdete v tématu [AZ identity](/cli/azure/identity).

Informace o přiřazení spravované identity přiřazené uživatelem k virtuálnímu počítači Azure najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity) .
