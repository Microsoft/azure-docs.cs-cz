---
title: Konfigurace spravovaných identit pomocí Azure App Configuration
description: Zjistěte, jak spravované identity fungují v konfiguraci aplikací Azure a jak nakonfigurovat spravovanou identitu.
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 5424b776b977d8a6939bbb28c5d4c9c428cac444
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179608"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Jak používat spravované identity pro konfiguraci aplikací Azure

V tomto tématu se dozvíte, jak vytvořit spravovanou identitu pro konfiguraci aplikací Azure. Spravovaná identita z Azure Active Directory (AAD) umožňuje konfiguraci aplikací Azure snadný přístup k dalším prostředkům chráněným službou AAD, jako je třeba Azure Key Vault. Identitu spravuje platforma Azure. Nevyžaduje zřizování ani střídání tajných kódů. Další informace o spravovaných identitách v AAD najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Aplikaci lze udělit dva typy identit:

- **Identita přiřazená systémem** je svázána s vaším úložištěm konfigurace. Odstraní se, pokud je vaše úložiště konfigurace smazáno. Úložiště konfigurace může mít pouze jednu identitu přiřazenou systémem.
- **Identita přiřazená uživatelem** je samostatný prostředek Azure, který se dá přiřadit k úložišti konfigurace. Úložiště konfigurace může mít několik uživatelsky přiřazených identit.

## <a name="adding-a-system-assigned-identity"></a>Přidání identity přiřazené systémem

Vytvoření úložiště konfigurace aplikace s identitou přiřazenou systémem vyžaduje další vlastnost, která má být nastavena v úložišti.

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Pokud chcete nastavit spravovanou identitu pomocí Azure CLI, použijte příkaz [AZ appconfig identity Assign] pro existující úložiště konfigurace. Máte tři možnosti, jak spustit příklady v této části:

- Použijte [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Použijte vložený Azure Cloud Shell pomocí tlačítka "vyzkoušet", které je umístěné v pravém horním rohu každého bloku kódu níže.
- Pokud upřednostňujete použití místní konzoly CLI, [nainstalujte nejnovější verzi Azure CLI](/cli/azure/install-azure-cli) (2,1 nebo novější).

Následující kroky vás provedou vytvořením úložiště konfigurace aplikace a přiřazením identity pomocí rozhraní příkazového řádku:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login]. Použijte účet, který je přidružený k vašemu předplatnému Azure:

    ```azurecli-interactive
    az login
    ```

1. Vytvořte úložiště konfigurace aplikace pomocí rozhraní příkazového řádku. Další příklady použití rozhraní příkazového řádku s konfigurací aplikace Azure najdete v tématu ukázky rozhraní příkazového [řádku pro konfiguraci aplikací](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Spuštěním příkazu [AZ appconfig identity Assign] vytvořte identitu přiřazenou systémem pro toto úložiště konfigurace:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Přidání uživatelsky přiřazené identity

Vytvoření úložiště konfigurace aplikace s identitou přiřazenou uživatelem vyžaduje, abyste vytvořili identitu a pak přiřadili její identifikátor prostředku vašemu úložišti.

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Pokud chcete nastavit spravovanou identitu pomocí Azure CLI, použijte příkaz [AZ appconfig identity Assign] pro existující úložiště konfigurace. Máte tři možnosti, jak spustit příklady v této části:

- Použijte [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Použijte vložený Azure Cloud Shell pomocí tlačítka "vyzkoušet", které je umístěné v pravém horním rohu každého bloku kódu níže.
- Pokud upřednostňujete použití místní konzoly CLI, [nainstalujte nejnovější verzi Azure CLI](/cli/azure/install-azure-cli) (2.0.31 nebo novější).

Následující kroky vás provedou vytvořením identity přiřazené uživatelem a úložištěm konfigurace aplikace a přiřazením identity do úložiště pomocí rozhraní příkazového řádku:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login]. Použijte účet, který je přidružený k vašemu předplatnému Azure:

    ```azurecli-interactive
    az login
    ```

1. Vytvořte úložiště konfigurace aplikace pomocí rozhraní příkazového řádku. Další příklady použití rozhraní příkazového řádku s konfigurací aplikace Azure najdete v tématu ukázky rozhraní příkazového [řádku pro konfiguraci aplikací](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Vytvořte uživatelem přiřazenou identitu nazvanou `myUserAssignedIdentity` pomocí rozhraní příkazového řádku.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Ve výstupu tohoto příkazu si všimněte hodnoty `id` Vlastnosti.

1. Spuštěním příkazu [AZ appconfig identity Assign] přiřaďte novou identitu přiřazenou uživateli do tohoto úložiště konfigurace. Použijte hodnotu `id` vlastnosti, kterou jste si poznamenali v předchozím kroku.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Odebrání identity

Identitu přiřazenou systémem je možné odebrat tak, že ji zakážete pomocí příkazu [AZ appconfig identity Remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) v rozhraní příkazového řádku Azure CLI. Uživatelsky přiřazené identity je možné odebrat jednotlivě. Odebrání identity přiřazené systémem tímto způsobem ji odstraní také z AAD. Identity přiřazené systémem se při odstranění prostředku aplikace taky automaticky odeberou z AAD.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure](quickstart-aspnet-core-app.md)

[AZ appconfig identity Assign]: /cli/azure/appconfig/identity#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
