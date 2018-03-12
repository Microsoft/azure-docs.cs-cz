---
title: "Postup konfigurace MSI se přiřazené pro virtuální počítač Azure pomocí rozhraní příkazového řádku Azure"
description: "Krok podle podrobné pokyny pro konfiguraci přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač Azure, pomocí rozhraní příkazového řádku Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e8d21aad23782f22b93baf12ce58d1aed4dd5dee
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Konfigurace přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač, pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje Azure služby spravovanou identitu ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI se přiřazené pro virtuální počítač Azure, pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

V tomto kurzu spustit příklady skriptu rozhraní příkazového řádku, máte dvě možnosti:

- Použití [prostředí cloudu Azure](~/articles/cloud-shell/overview.md) z portálu Azure nebo prostřednictvím tlačítko "Zkuste ho", umístěný v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. Potom se přihlaste k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit přiřazený uživatelem MSI a virtuálních počítačů:

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Povolit MSI při vytváření virtuálního počítače Azure

Tato část vás provede procesem vytvoření virtuálního počítače a přiřazení MSI přiřazené k virtuálnímu počítači. Pokud již máte virtuální počítač, který chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou jste chtěli použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro nasazení MSI a členství ve skupině pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<LOCATION>` hodnoty parametrů s vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvoření přiřazený uživatelem MSI pomocí [vytvoření az identity](/cli/azure/identity#az_identity_create).  `-g` Parametr určuje skupinu prostředků, kde se má vytvořit soubor MSI, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` hodnoty parametrů s vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Odpověď obsahuje podrobnosti pro přiřazené uživatele soubor MSI vytvořili, podobný následujícímu. Prostředek `id` hodnotu přiřazenou soubor MSI se používá v dalším kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač přidružené k nové přiřazený uživatelem soubor MSI, podle specifikace `--assign-identity` parametr. Nezapomeňte nahradit `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, a `<`MSI ID >` parameter values with your own values. For `<MSI ID>`, use the user-assigned MSI's resource `id' vlastnost vytvořili v předchozím kroku: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Povolit MSI na existující virtuální počítač Azure

1. Vytvoření přiřazený uživatelem MSI pomocí [vytvoření az identity](/cli/azure/identity#az_identity_create).  `-g` Parametr určuje skupinu prostředků, kde se má vytvořit soubor MSI, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` hodnoty parametrů s vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Odpověď obsahuje podrobnosti pro přiřazené uživatele soubor MSI vytvořili, podobný následujícímu. Prostředek `id` hodnotu přiřazenou soubor MSI se používá v dalším kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Virtuální počítač pomocí přiřadit MSI přiřazený uživatelem [az virtuálních počítačů přiřazení identity](/cli/azure/vm#az_vm_assign_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI ID>` Bude MSI přiřazený uživatelem prostředek `id` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure MSI

1. Odebrání virtuální počítač pomocí Instalační služby MSI přiřazený uživatelem [az virtuálního počítače odeberte identity](/cli/azure/vm#az_vm_remove_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI NAME>` Bude MSI přiřazený uživatelem `name` vlastnost jako dané během `az identity create` příkazu (viz příklady v předchozích částech):

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Další postup

- [Přehled spravované identita služby](msi-overview.md)
- Úplné vytvoření virtuálního počítače Azure Quickstarts najdete v části: 

  - [Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku](~/articles/virtual-machines/linux/quick-create-cli.md) 

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
















