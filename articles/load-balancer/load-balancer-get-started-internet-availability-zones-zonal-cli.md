---
title: Vytvoření nástroje pro vyrovnávání zatížení s oblastmi front-endu Azure CLI
titleSuffix: Azure Load Balancer
description: Naučte se vytvářet Standard Load Balancer s použitím rozhraní příkazového řádku Azure pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 09c9aa2807fbf8dc862bc1bf91c9e9bc97c49122
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894591"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Vytvoření Standard Load Balancer pomocí rozhraní příkazového řádku Azure ve front-endu

Tento článek vás provede vytvořením veřejné [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) s použitím Zona front-endu. S oblastí front-end znamená, že jakýkoliv příchozí nebo odchozí tok je obsluhován jedinou zónou v oblasti. Můžete vytvořit nástroj pro vyrovnávání zatížení s oblastmi front-endu pomocí standardní veřejné IP adresy v rámci své konfigurace front-endu. Vysvětlení fungování zón dostupnosti s Load balanceru úrovně Standard najdete v tématu [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, ujistěte se, že máte nainstalovanou nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a přihlášení k účtu Azure pomocí [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Vytvoření standardní veřejné IP adresy

Pomocí následujícího příkazu vytvořte standardní veřejnou IP adresu pro oblast:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte veřejnou Standard Load Balancer se standardní veřejnou IP adresou, kterou jste vytvořili v předchozím kroku, pomocí následujícího příkazu:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Vytvoření sondy LB na portu 80

Vytvoření sondy stavu nástroje pro vyrovnávání zatížení pomocí následujícího příkazu:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Vytvoření pravidla LB pro port 80

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí následujícího příkazu:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Další kroky
- Další informace o [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md).



