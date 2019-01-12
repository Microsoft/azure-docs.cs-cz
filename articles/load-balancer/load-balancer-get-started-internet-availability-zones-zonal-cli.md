---
title: Vytvoření Load Balanceru s oblastmi front-endu – rozhraní příkazového řádku Azure
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit Load balanceru úrovně Standard s oblastmi front-endu pomocí Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: c9926b78955a193f750c1e5ca3ff7461b4192df5
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247006"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-cli"></a>Vytvoření Load Balanceru úrovně Standard s oblastmi front-endu pomocí Azure CLI

Tento článek popisuje postup vytvořením veřejné [Load balanceru úrovně Standard](https://aka.ms/azureloadbalancerstandard) s oblastmi front-endu. S oblastmi front-endu znamená, že všechny příchozí nebo odchozí tok je obsluhuje jednu zónu v oblasti. Nástroj pro vyrovnávání zatížení můžete vytvořit s oblastmi front-endu pomocí oblastmi standardní veřejnou IP adresou ve své konfiguraci front-endu. Vysvětlení fungování zón dostupnosti s Load balanceru úrovně Standard najdete v tématu [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, ujistěte se, že máte nainstalovanou nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a přihlášení k účtu Azure pomocí [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Vytvoření standardní veřejné IP adresy

Vytvoření zónového standardní veřejnou IP adresu pomocí následujícího příkazu:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte veřejný Load Balancer Standard s standardní veřejnou IP adresu, kterou jste vytvořili v předchozím kroku, pomocí následujícího příkazu:

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

## <a name="next-steps"></a>Další postup
- Další informace o [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md).



