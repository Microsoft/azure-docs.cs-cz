---
title: Konfigurace spravovaných identit pomocí konfigurace aplikací Azure
description: Zjistěte, jak spravované identity fungují v konfiguraci aplikací Azure a jak nakonfigurovat spravovanou identitu
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623033"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Jak používat spravované identity pro konfiguraci aplikací Azure

Toto téma ukazuje, jak vytvořit spravovanou identitu pro konfiguraci aplikací Azure. Spravovaná identita z Azure Active Directory (AAD) umožňuje konfiguraci aplikací Azure snadný přístup k dalším prostředkům chráněným AAD, jako je například Azure Key Vault. Identita je spravovaná platformou Azure. Nevyžaduje, abyste zrozovávat nebo otáčet žádné tajné klíče. Další informace o spravovaných identitách ve službě AAD najdete v [tématu Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Vaší žádosti lze udělit dva typy identit:

- Systémově **přiřazená identita** je svázána s úložištěm konfigurace. Je odstraněn, pokud je odstraněn úložiště konfigurace. Úložiště konfigurace může mít pouze jednu identitu přiřazenou systému.
- **Identita přiřazená uživatelem** je samostatný prostředek Azure, který lze přiřadit k vašemu úložišti konfigurace. Úložiště konfigurace může mít více identit přiřazených uživatelem.

## <a name="adding-a-system-assigned-identity"></a>Přidání systémově přiřazené identity

Vytvoření úložiště konfigurace aplikace se systémem přiřazenou identitou vyžaduje další vlastnost, která má být nastavena v úložišti.

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Chcete-li nastavit spravovanou identitu pomocí příkazového příkazu Azure CLI, použijte příkaz [přiřazení identity az appconfig] proti existujícímu úložišti konfigurace. Máte tři možnosti pro spuštění příkladů v této části:

- Azure [Cloud Shell](../cloud-shell/overview.md) můžete používat z portálu Azure.
- Pomocí integrovaného prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu níže.
- [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 nebo novější), pokud dáváte přednost použití místní konzoly PŘÍKAZCLI.

Následující kroky vás provedou vytvořením úložiště konfigurace aplikací a přiřazením identity pomocí příkazového příkazu k registraci:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login]. Použijte účet, který je přidružený k vašemu předplatnému Azure:

    ```azurecli-interactive
    az login
    ```

1. Vytvořte úložiště konfigurace aplikace pomocí cli. Další příklady použití příkazového příkazového příkazu s konfigurací aplikace Azure najdete v [tématu ukázky příkazového příkazu konfigurace aplikace](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Spusťte příkaz [přiřazení identity az appconfig] k vytvoření systémově přiřazené identity pro toto úložiště konfigurace:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Přidání identity přiřazené uživateli

Vytvoření úložiště konfigurace aplikace s identitou přiřazenou uživatelem vyžaduje vytvoření identity a přiřazení jejího identifikátoru prostředku k úložišti.

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Chcete-li nastavit spravovanou identitu pomocí příkazového příkazu Azure CLI, použijte příkaz [přiřazení identity az appconfig] proti existujícímu úložišti konfigurace. Máte tři možnosti pro spuštění příkladů v této části:

- Azure [Cloud Shell](../cloud-shell/overview.md) můžete používat z portálu Azure.
- Pomocí integrovaného prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu níže.
- [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 nebo novější), pokud dáváte přednost použití místní konzoly PŘÍKAZCLI.

Následující kroky vás provedou vytvořením identity přiřazené uživatelem a úložištěm konfigurace aplikací a následným přiřazením identity do obchodu pomocí rozhraní příkazového příkazu:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login]. Použijte účet, který je přidružený k vašemu předplatnému Azure:

    ```azurecli-interactive
    az login
    ```

1. Vytvořte úložiště konfigurace aplikace pomocí cli. Další příklady použití příkazového příkazového příkazu s konfigurací aplikace Azure najdete v [tématu ukázky příkazového příkazu konfigurace aplikace](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Vytvořte uživatelem přiřazenou `myUserAssignedIdentity` identitu volanou pomocí rozhraní se klis.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Ve výstupu tohoto příkazu poznamenejte hodnotu vlastnosti. `id`

1. Spusťte příkaz [přiřazení identity az appconfig] a přiřaďte tomuto úložišti konfigurace novou uživatelem přiřazenou identitu. Použijte hodnotu `id` vlastnosti, kterou jste zaznamenali v předchozím kroku.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Odebrání identity

Systémem přiřazenou identitu lze odebrat zakázáním funkce pomocí příkazu [odebrání identity az appconfig](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) v příkazu Azure CLI. Uživatelem přiřazené identity lze odebrat jednotlivě. Odebráním systémově přiřazené identity tímto způsobem ji také odstraníte z aad. Systémem přiřazené identity jsou také automaticky odebrány z AAD při odstranění prostředku aplikace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření aplikace ASP.NET Core s konfigurací aplikací Azure](quickstart-aspnet-core-app.md)

[přiřazení identity az appconfig]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
