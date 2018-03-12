---
title: "Postup pro přístup k přiřazený uživatelem MSI přiřadit prostředek služby Azure, pomocí rozhraní příkazového řádku Azure"
description: "Podrobné pokyny pro přiřazení MSI přiřazený uživatelem na jeden prostředek, přístup k jiný prostředek, pomocí rozhraní příkazového řádku Azure."
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
ms.openlocfilehash: 8cdfd80f436a9ef39426da9d3f2f76ae59677847
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Přiřadit přístup k přiřazený uživatelem identita spravované služby (MSI) prostředku, pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Po vytvoření MSI se přiřazený uživatelem, můžete zajistit přístup MSI pro jiný prostředek, stejně jako libovolný zaregistrovaný objekt zabezpečení. Tento příklad ukazuje, jak poskytnout přiřazený uživatelem MSI přístup k účtu úložiště Azure, pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

V tomto kurzu spustit příklady skriptu rozhraní příkazového řádku, máte dvě možnosti:

- Použití [prostředí cloudu Azure](~/articles/cloud-shell/overview.md) z portálu Azure nebo prostřednictvím tlačítko "Zkuste ho", umístěný v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. Potom se přihlaste k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit přiřazený uživatelem MSI a virtuálních počítačů:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Používat funkci RBAC přiřazení přístupu MSI pro jiný prostředek

Abyste mohli používat MSI pro přístup k přihlášení nebo prostředků s prostředku hostitele (například počítač), soubor MSI musí nejprve mít udělen přístup k prostředkům prostřednictvím přiřazení role. Můžete provést před s hostitelem nebo po přidružení soubor MSI. Úplné kroky při vytváření a přidružení k virtuálnímu počítači, najdete v části [konfigurace MSI se přiřazené pro virtuální počítač, pomocí rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md).

V následujícím příkladu se instalační služby MSI přiřazený uživatelem poskytnut přístup k účtu úložiště.  

1. Aby bylo možné poskytnout přístup MSI, je nutné ID klienta (také označované jako ID aplikace) MSI instanční objekt. Klient poskytl ID [vytvoření az identity](/cli/azure/identity#az_identity_create), při zřizování soubor MSI a jeho instanční objekt (tady zobrazené pro referenci):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Úspěšná odpověď obsahuje ID klienta služby MSI přiřazený uživatelem v hlavní `clientId` vlastnost:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. Jakmile se označuje ID klienta, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az_role_assignment_create) přiřazení přístupu MSI pro jiný prostředek. Je nutné používat vaše ID klienta pro `--assignee` parametr a odpovídající skupina ID a prostředků předplatného název, jak ho vrátila při spuštění `az identity create`. Soubor MSI je zde přiřadit "Čtečky" přístup k účet storage s názvem "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Úspěšná odpověď bude vypadat podobně jako následující výstup:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>Další postup

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pokud chcete povolit přiřadit uživatele MSI ve virtuálním počítači Azure, najdete v části [konfigurace přiřazený uživatelem spravované služby Identity (MSI) pro virtuální počítač, pomocí rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.

