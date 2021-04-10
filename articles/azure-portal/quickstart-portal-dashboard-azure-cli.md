---
title: Vytvoření řídicího panelu Azure Portal pomocí Azure CLI
description: 'Rychlý Start: Naučte se vytvořit řídicí panel v Azure Portal pomocí rozhraní příkazového řádku Azure CLI. Řídicí panel je cílené a uspořádané zobrazení vašich cloudových prostředků.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ddfee1932c6887c6ca7593ca7a28c03e68e09899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104613183"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Rychlý Start: Vytvoření řídicího panelu Azure Portal pomocí Azure CLI

Řídicí panel v Azure Portal je cílené a organizované zobrazení vašich cloudových prostředků. Tento článek se zaměřuje na proces použití rozhraní příkazového řádku Azure k vytvoření řídicího panelu.
Řídicí panel zobrazuje výkon virtuálního počítače a také některé statické informace a odkazy.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se budou prostředky fakturovat.
Vyberte předplatné pomocí příkazu [AZ Account set](/cli/azure/account#az_account_set) :

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) nebo použijte existující skupinu prostředků:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm#az_vm_create) :

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Heslo musí být složité.
> Toto je nové uživatelské jméno a heslo.
> Nejedná se například o účet, který používáte k přihlášení do Azure.
> Další informace najdete v tématu [požadavky na uživatelské jméno](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) a [požadavky na heslo](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Nasazení se spustí a obvykle trvá několik minut, než se dokončí.
Po dokončení nasazení přejděte k další části.

## <a name="download-the-dashboard-template"></a>Stažení šablony řídicího panelu

Vzhledem k tomu, že řídicí panely Azure jsou prostředky, mohou být reprezentovány jako JSON.
Další informace najdete v tématu [Struktura řídicích panelů Azure](./azure-portal-dashboards-structure.md).

Stáhněte si následující soubor: [portal-dashboard-template-testvm.jsna](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Staženou šablonu si můžete přizpůsobit tak, že změníte následující hodnoty na hodnoty:

* `<subscriptionID>`: Vaše předplatné
* `<rgName>`: Skupina prostředků, například `myResourceGroup`
* `<vmName>`: Název virtuálního počítače, například `SimpleWinVM`
* `<dashboardTitle>`: Název řídicího panelu, například `Simple VM Dashboard`
* `<location>`: Vaše oblast Azure, například `centralus`

Další informace najdete v referenčních informacích k [šabloně řídicích panelů portálu Microsoft Portal](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Nasazení šablony řídicího panelu

Tuto šablonu teď můžete nasadit v rámci Azure CLI.

1. Spusťte příkaz [AZ Portal Dashboard Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) a nasaďte šablonu:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Ověřte, že se řídicí panel úspěšně vytvořil, spuštěním příkazu [AZ Portal Dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) :

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Pokud chcete zobrazit všechny řídicí panely aktuálního předplatného, použijte příkaz [AZ Portal Dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Můžete také zobrazit všechny řídicí panely pro skupinu prostředků:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Řídicí panel můžete aktualizovat pomocí příkazu [AZ Portal Dashboard Update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) :

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat virtuální počítač a přidružený řídicí panel, odstraňte skupinu prostředků, která je obsahuje.

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurecli
az group delete --name myResourceGroup
```

Pokud chcete odebrat jenom řídicí panel, použijte příkaz [AZ Portal Dashboard Delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) :

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Další kroky

Další informace o podpoře Azure CLI pro řídicí panely najdete v tématu [AZ Portal Dashboard](/cli/azure/ext/portal/portal/dashboard).
