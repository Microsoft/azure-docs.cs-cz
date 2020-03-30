---
title: Správa spravovaných identit přiřazených uživatelem pomocí REST – Azure AD
description: Krok za krokem pokyny, jak vytvořit, seznam a odstranit uživatelem přiřazenou spravovanou identitu, aby se volání rozhraní REST API.
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
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547414"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Vytvoření, zobrazení seznamu nebo odstranění spravované identity přiřazené uživatelem pomocí volání rozhraní REST API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje službám Azure možnost ověřování na služby, které podporují ověřování Azure AD, bez nutnosti pověření ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, seznam a odstranit uživatelem přiřazenou spravovanou identitu pomocí CURL k volání rozhraní REST API.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud používáte Windows, nainstalujte [podsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- Pokud používáte [podsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo operační systém [linuxové distribuce](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [nainstalujte místní konzolu Azure CLI](/cli/azure/install-azure-cli).
- Pokud používáte místní konzolu Azure CLI, `az login` přihlaste se k Azure pomocí účtu, který je přidružený k předplatnému Azure, který chcete nasadit nebo načíst uživatelem přiřazené informace o spravované identitě.
- Načtěte přístupový `az account get-access-token` token nosiče pomocí následujících operací spravované identity přiřazených uživatelem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Chcete-li vytvořit spravovanou identitu přiřazenou uživateli, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

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

**Záhlaví požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na `Bearer` platný přístupový token.        |

**Text požadavku**

|Name (Název)  |Popis  |
|---------|---------|
|location     | Povinná hodnota. Umístění zdroje.        |

## <a name="list-user-assigned-managed-identities"></a>Seznam spravovaných identit přiřazených uživatelem

Chcete-li vypsat/přečíst spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Operátor spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přiřazení přispěvatele spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na `Bearer` platný přístupový token.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Chcete-li odstranit spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

> [!NOTE]
> Odstraněním spravované identity přiřazené uživateli neodeberete odkaz z žádného prostředku, ke které byl přiřazen. O odebrání uživatelem přiřazené spravované identity z virtuálního počítače pomocí CURL najdete [v tématu Odebrání identity přiřazené uživateli z virtuálního počítače Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

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
|*Autorizace*     | Povinná hodnota. Nastavte na `Bearer` platný přístupový token.        |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přiřadit uživatelem přiřazenou spravovanou identitu k virtuálnímu počítači Azure/VMSS pomocí CURL, najdete v [tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači azure pomocí volání rozhraní REST](qs-configure-rest-vm.md#user-assigned-managed-identity) API a Konfigurace [spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí volání rozhraní REST API](qs-configure-rest-vmss.md#user-assigned-managed-identity).
