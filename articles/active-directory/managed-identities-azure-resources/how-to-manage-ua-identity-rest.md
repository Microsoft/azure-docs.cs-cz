---
title: Správa spravovaných identit přiřazených uživatelem pomocí REST – Azure AD
description: Podrobné pokyny, jak vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem pro REST API volání.
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
ms.date: 06/26/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37fad118fe314b1392c31906a3f0a0989e39d876
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969408"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí REST API volání

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují službám Azure možnost ověřování pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem pomocí OBLÉ k provedení REST API volání.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Všechny příkazy v tomto článku můžete spustit buď v cloudu, nebo místně:
    - Pro spuštění v cloudu použijte [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Chcete-li spustit místně, nainstalujte [oblé](https://curl.haxx.se/download.html) a [Azure CLI](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Získání přístupového tokenu nosiče

1. Pokud používáte místně, přihlaste se do Azure prostřednictvím Azure CLI:

    ```
    az login
    ```

1. Získání přístupového tokenu pomocí [AZ Account Get-Access-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Hlavičky požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |

**Text žádosti**

|Název  |Popis  |
|---------|---------|
|location     | Povinná hodnota. Umístění prostředku.        |

## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

> [!NOTE]
> Odstraněním spravované identity přiřazené uživatelem se neodebere odkaz z žádného prostředku, ke kterému byl přiřazen. Pokud chcete odebrat uživatelem přiřazenou identitu z virtuálního počítače pomocí technologie KUDRLINKOU, podívejte se na téma [Odebrání uživatelsky přiřazené identity z virtuálního počítače Azure](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přiřadit uživatelsky spravovanou identitu k VIRTUÁLNÍmu počítači Azure/VMSS pomocí sady prostředků, najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí volání REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) a [Konfigurace spravovaných identit pro prostředky Azure ve službě škálování virtuálního počítače pomocí REST API volání](qs-configure-rest-vmss.md#user-assigned-managed-identity).
