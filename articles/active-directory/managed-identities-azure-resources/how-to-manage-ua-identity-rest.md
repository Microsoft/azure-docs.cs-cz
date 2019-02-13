---
title: Správa uživatelsky přiřazené spravované identity Azure pomocí rozhraní REST
description: Krok za krokem pokyny o tom, jak vytvořit, vypsat a odstranit uživatel přiřazenou spravované identity pro volání rozhraní REST API.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 948873c38d7e681015efd87061b23d689c64f569
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209384"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovanou identitu volání rozhraní REST API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravovat identity pro prostředky Azure poskytuje služby Azure možnost ověření, že ověřování podpory služby Azure AD pro služby, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku zjistíte, jak vytvořit, vypsat a odstranit spravovanou identitu přiřazená uživatelem pomocí příkazu CURL k volání rozhraní REST API.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud používáte Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- Pokud používáte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [operačním systémem distribuce Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [nainstalovat místní konzoly Azure CLI](/cli/azure/install-azure-cli).
- Pokud používáte místní konzoly příkazového řádku Azure, přihlaste se do Azure s využitím `az login` pomocí účtu, který je přidružený k předplatnému Azure chcete nasadit nebo načíst informace o uživatelsky přiřazené spravovaná identita.
- Získat nosný přístup k tokenu pomocí `az account get-access-token` provádět následující operace uživatelsky přiřazené spravovaná identita.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Pro vytvoření uživatelsky přiřazené identity spravované, musí váš účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

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

**Hlavičky žádosti**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        |

**Text žádosti**

|Název  |Popis  |
|---------|---------|
|location     | Povinná hodnota. Umístění prostředku.        |

## <a name="list-user-assigned-managed-identities"></a>Seznam uživatelsky přiřazené spravované identity

Do seznamu/čtení uživatelsky přiřazené spravovanou identitu, musí váš účet [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Pokud chcete odstranit spravovanou identitu uživatele přiřazeny, musí váš účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

> [!NOTE]
> Odstraňuje se uživatel přiřazenou spravovanou identitu nedojde k odebrání odkazu ze všech prostředků, který byl přiřazen. Odebrat uživatelsky přiřazené spravovaná identita z virtuálního počítače pomocí příkazu CURL najdete v článku [odebrání virtuálního počítače Azure uživatelsky přiřazené identity](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Hlavička požadavku  |Popis  |
|---------|---------|
|*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        |

## <a name="next-steps"></a>Další postup

Informace o tom, jak přiřadit uživateli přiřazena spravované identity do Azure VM/VMSS pomocí příkazu CURL naleznete v tématu, [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí volání rozhraní REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) a [spravované konfigurace pro prostředky Azure na virtuální počítač škálovací sady s použitím volání rozhraní REST API identit](qs-configure-rest-vmss.md#user-assigned-managed-identity).
