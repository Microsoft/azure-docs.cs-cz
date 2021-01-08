---
title: 'Rychlý Start: vytvoření vyhrazeného modulu HSM Azure pomocí Azure CLI'
description: Pomocí Azure CLI vytvoříte, ukážete, vypíšete, aktualizujete a odstraníte vyhrazené HSM Azure.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020959"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Rychlý Start: vytvoření vyhrazeného modulu HARDWAROVÉho zabezpečení Azure pomocí rozhraní příkazového řádku Azure

Tento článek popisuje, jak vytvořit a spravovat službu Azure vyhrazený modul HSM pomocí rozšíření [AZ vyhrazené-HSM](/cli/azure/ext/hardware-security-modules/dedicated-hsm) Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. [Bezplatný účet](https://azure.microsoft.com/free/) si můžete vytvořit, pokud ho nemáte.
  
  Pokud máte více než jedno předplatné Azure, nastavte předplatné, které se má použít pro fakturaci, pomocí příkazu [AZ Account set](/cli/azure/account#az_account_set) Azure CLI.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Všechny požadavky splněné pro vyhrazený modul hardwarového zabezpečení (HSM), včetně registrace, schválení a virtuální sítě a virtuálního počítače, které se mají použít pro zřizování Další informace o požadavcích na vyhrazené hardwarové požadavky a požadavky najdete v tématu [kurz: nasazení HSM do existující virtuální sítě pomocí rozhraní příkazového řádku Azure CLI](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/management/overview.md) je logický kontejner pro nasazení a správu prostředků Azure jako skupiny. Pokud ještě nemáte skupinu prostředků pro vyhrazený modul HSM, vytvořte ji pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) . Následující příklad vytvoří skupinu prostředků s názvem `myRG` v `westus` oblasti Azure:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Vytvoření vyhrazeného modulu HSM

Pokud chcete vytvořit vyhrazený modul HSM, použijte příkaz [AZ vyhrazené-HSM Create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) . Následující příklad zřídí vyhrazený modul HARDWAROVÉho zabezpečení s názvem `hsm1` v `westus` oblasti, `myRG` skupině prostředků a určeném předplatném, virtuální síti a podsíti. Požadované parametry jsou `name` , `location` a `resource group` .

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Dokončení nasazení trvá přibližně 25 až 30 minut.

## <a name="get-a-dedicated-hsm"></a>Získání vyhrazeného modulu HSM

Pokud chcete získat aktuální vyhrazený modul hardwarového zabezpečení (HSM), spusťte příkaz [AZ vyhrazené-HSM show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) . Následující příklad získá vyhrazený modul `hsm1` hardwarového zabezpečení ve `myRG` skupině prostředků.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Aktualizace vyhrazeného modulu HSM

Pomocí příkazu [AZ vyhrazené-HSM Update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) aktualizujte vyhrazený modul HSM. Následující příklad aktualizuje vyhrazený modul `hsm1` hardwarového zabezpečení ve `myRG` skupině prostředků a jeho značky:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Vypsat vyhrazené HSM

Spuštěním příkazu [AZ vyhrazené-HSM list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) získáte informace o aktuálních vyhrazených HSM. Následující příklad vypíše vyhrazené HSM ve `myRG` skupině prostředků:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Odebrat vyhrazený modul HSM

Pokud chcete odebrat vyhrazený modul HSM, použijte příkaz [AZ vyhrazené-HSM Delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) . Následující příklad odstraní vyhrazený modul `hsm1` HSM ze `myRG` skupiny prostředků:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

Pokud už skupinu prostředků, kterou jste vytvořili pro vyhrazený modul HSM, nepotřebujete, můžete ji odstranit spuštěním příkazu [AZ Group Delete](/cli/azure/group#az_group_delete) . Tento příkaz odstraní skupinu a všechny prostředky v ní, včetně těch, které nesouvisí s vyhrazeným modulem HSM. Následující příklad odstraní `myRG` skupinu prostředků a všechny její položky:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Další kroky

Další informace o vyhrazeném modulu HARDWAROVÉho zabezpečení Azure najdete v tématu [vyhrazený modul HSM v Azure](overview.md).
