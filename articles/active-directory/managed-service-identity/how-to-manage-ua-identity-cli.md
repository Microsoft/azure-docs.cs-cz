---
title: Jak spravovat uživatele přiřazené spravované služby Identity (MSI) pomocí rozhraní příkazového řádku Azure
description: Podrobné pokyny, jak vytvářet, seznamu a odstraňovat uživatele přiřazené spravované služby identitu pomocí rozhraní příkazového řádku Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: ebd250a7006c1235b30eb195f4bf7383b6293022
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Vytvoření seznamu nebo odstranění uživatele přiřazené identitu pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje Azure služby spravovanou identitu ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku zjistěte, jak vytvářet, seznamu a odstraňovat uživatele přiřazené identitu pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

- Pokud chcete spustit skript příklady rozhraní příkazového řádku, máte tři možnosti:

    - Použití [prostředí cloudu Azure](../../cloud-shell/overview.md) z portálu Azure (viz další část).
    - Používání embedded prostředí cloudu Azure prostřednictvím "Zkuste ho" tlačítko, umístěné v pravém horním rohu každé blok kódu.
    - [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. Přihlaste se k Azure pomocí `az login`, pomocí účtu, který je přidružen k předplatnému Azure, pod kterou chcete nasadit identity uživatele přiřazené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření uživatele přiřazené spravované identity 

K vytvoření identity uživatele, které jsou přiřazeny, použijte [vytvoření az identity](/cli/azure/identity#az-identity-create) příkaz. `-g` Parametr určuje skupinu prostředků, kde k vytvoření identity uživatele přiřazené a `-n` parametr určuje její název. Nahraďte `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` hodnoty parametrů s vlastními hodnotami:

> [!IMPORTANT]
> Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Seznam přiřazeného identity uživatele

Seznam identit uživatelů, které jsou přiřazeny, použijte [seznam identity az](/cli/azure/identity#az-identity-list) příkaz.  `-g` Parametr určuje skupinu prostředků, které bylo vytvořeno přiřazeného identity uživatele.  Nahraďte `<RESOURCE GROUP>` vlastní hodnotou:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
V odpovědi json identit uživatelů mají `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnota vrácená pro klíč `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Odstranění uživatele přiřazené identity

Pokud chcete odstranit identitu uživatele, které jsou přiřazeny, použijte [az identity odstranit](/cli/azure/identity#az-identity-delete) příkaz.  Parametr - n Určuje její název a parametr -g Určuje skupinu prostředků, které bylo vytvořeno přiřazeného identity uživatele.  Nahraďte `<USER ASSIGNED IDENTITY NAME>` a `<RESOURCE GROUP>` hodnot parametrů s vlastními hodnotami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Odstranění uživatele přiřazené identity neodebere odkaz, z libovolného zdroje, kterému byla přiřazena. Odeberte z virtuálního počítače nebo VMSS pomocí `az vm/vmss identity remove` příkaz

## <a name="related-content"></a>Související obsah

Úplný seznam příkazů identity rozhraní příkazového řádku Azure najdete v tématu [az identity](/cli/azure/identity).

Informace o tom, jak přiřadit identitu uživatele přiřazené k virtuálnímu počítači Azure najdete [nakonfigurovat spravované služby Identity (MSI) pomocí rozhraní příkazového řádku Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
