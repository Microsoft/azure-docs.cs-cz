---
title: Správa uživatelem přiřazené spravované identity – Azure CLI - Azure AD
description: Krok za krokem pokyny, jak vytvořit, seznam a odstranit uživatelem přiřazenou spravovanou identitu pomocí rozhraní příkazového příkazu Azure.
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
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5defb85547e8750dea9ceaa481217aa40a004e
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639760"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Vytvoření, vypsat nebo odstranit spravovanou identitu přiřazenou uživatelem pomocí rozhraní příkazového příkazu Azure


Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služeb, které podporují ověřování Azure AD, bez nutnosti pověření ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, seznam a odstranit uživatelem přiřazenou spravovanou identitu pomocí rozhraní příkazového příkazu Azure.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit příklady skriptu vykreslování jízdou po nesek, máte tři možnosti:
    - Azure [Cloud Shell](../../cloud-shell/overview.md) použijte z webu Azure Portal (viz další část).
    - Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější), pokud dáváte přednost použití místní konzoly příkazového příkazu. Přihlaste se `az login`k Azure pomocí účtu, který je přidružený k předplatnému Azure, pod kterým chcete nasadit spravovanou identitu přiřazenou uživatelem.


> [!NOTE]
> Chcete-li změnit uživatelská oprávnění při použití hlavního povinného použití hlavního nastavení příkazu k obsluze aplikace pomocí rozhraní příkazového příkazu, musíte poskytnout další oprávnění instančního objektu v rozhraní API grafu Azure AD, protože části rozhraní příkazového příkazu provádějí požadavky GET proti rozhraní API grafu. V opačném případě může dojít k přijetí zprávy "Nedostatečná oprávnění k dokončení operace". K tomu budete muset přejít do registrace aplikace ve službě Azure Active Directory, vyberte aplikaci, klikněte na oprávnění rozhraní API, přejděte dolů a vyberte Azure Active Directory Graph. Odtud vyberte oprávnění aplikace a přidejte příslušná oprávnění. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Chcete-li vytvořit spravovanou identitu přiřazenou uživateli, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create) vytvořte spravovanou identitu přiřazenou uživatelem. Parametr `-g` určuje skupinu prostředků, kde má být vytvořit spravovanou identitu `-n` přiřazenou uživateli, a parametr určuje její název. Nahraďte hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Seznam spravovaných identit přiřazených uživatelem

Chcete-li vypsat/přečíst spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Operátor spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přiřazení přispěvatele spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Chcete-li vypsat spravované identity přiřazené uživatelem, použijte příkaz [az seznam identit.](/cli/azure/identity#az-identity-list) `<RESOURCE GROUP>` Nahraďte jej vlastní hodnotou:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
V odpovědi json mají `"Microsoft.ManagedIdentity/userAssignedIdentities"` uživatelem přiřazené spravované identity vrácenou hodnotu pro klíč . `type`

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Chcete-li odstranit spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Chcete-li odstranit spravovanou identitu přiřazenou uživatelem, použijte příkaz [az identity delete.](/cli/azure/identity#az-identity-delete)  Parametr -n určuje jeho název a parametr -g určuje skupinu prostředků, ve které byla vytvořena spravovaná identita přiřazená uživatelem. Nahraďte hodnoty parametrů `<USER ASSIGNED IDENTITY NAME>` a `<RESOURCE GROUP>` vlastními hodnotami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Odstraněním spravované identity přiřazené uživateli neodeberete odkaz z prostředků, ke kterým byl přiřazen. Odeberte je z Virtuálního mě/VMSS pomocí příkazu `az vm/vmss identity remove`

## <a name="next-steps"></a>Další kroky

Úplný seznam příkazů identity Azure CLI najdete [v tématu az identity](/cli/azure/identity).

Informace o tom, jak přiřadit uživatelem přiřazenou spravovanou identitu k virtuálnímu počítači Azure, [najdete v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
