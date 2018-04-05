---
title: Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí rozhraní příkazového řádku Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: f3f479de8bc3975f4da07a7761ffc99f976db20e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní front-endu pomocí rozhraní příkazového řádku Azure

Tento článek popisuje postup procesem vytvoření veřejné [standardní nástroje pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) s zónově redundantní front-end pomocí standardní veřejné IP adresy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, ujistěte se, že jste nainstalovali nejnovější [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a jsou přihlášení k účtu Azure s [az přihlášení](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Podpora pro dostupnost zóny je k dispozici pro vyberte prostředků Azure a oblasti a rodiny velikost virtuálního počítače. Další informace o tom, jak začít pracovat a které prostředky Azure, oblasti a rodiny velikost virtuálního počítače můžete zkusit dostupnost zóny s, najdete v části [přehled dostupnosti zón](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvoření skupiny prostředků pomocí následujícího příkazu:

```azurecli-interactive
az group create --name myResourceGroupSLB --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Vytvoření veřejné standardní IP

Vytvoření veřejné IP standardní pomocí následujícího příkazu:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte standardní veřejné zatížení na vyrovnávání standardní veřejnou IP adresu, kterou jste vytvořili v předchozím kroku, pomocí následujícího příkazu:

```azurecli-interactive
az network lb create --resource-group myResourceGroupSLB --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Vytvořit test paměti vyrovnáváním zatížení na portu 80

Vytvoření stavu sondu nástroje pro vyrovnávání zatížení pomocí následujícího příkazu:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupSLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Vytvořit pravidlo LB pro port 80

Vytvořte pravidlo služby load balancer pomocí následujícího příkazu:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Další postup
- Další informace o [nástroj pro vyrovnávání zatížení a dostupnost zóny](load-balancer-standard-availability-zones.md).



