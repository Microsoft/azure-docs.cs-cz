---
title: Konfigurace předvolby směrování pro službu Azure Kubernetes pomocí Azure CLI
titlesuffix: Azure Virtual Network
description: Naučte se konfigurovat cluster AKS s předvolbumi směrování pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776568"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Konfigurace předvolby směrování pro cluster Kubernetes pomocí Azure CLI

V tomto článku se dozvíte, jak nakonfigurovat předvolby směrování prostřednictvím sítě poskytovatele internetových služeb (možnost **Internet** ) pro cluster Kubernetes pomocí Azure CLI. Preference směrování je nastavená vytvořením veřejné IP adresy typu předvolby směrování * * Internet * * * * a pak ho při vytváření clusteru AKS použít.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.49 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků v **východní USA** oblasti Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Vytvořte veřejnou IP adresu s preferovaným směrováním typu **Internet** pomocí příkazu [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create).

Následující příkaz vytvoří novou veřejnou IP adresu s upřednostněním **internetového** směrování v oblasti **východní USA** Azure.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  V současné době předvolby směrování podporuje jenom veřejné IP adresy IPV4.

## <a name="get-the-id-of-public-ip-address"></a>Získat ID veřejné IP adresy

Následující příkaz vrátí ID veřejné IP adresy vytvořené v předchozí části:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Vytvoření clusteru Kubernetes s veřejnou IP adresou

Následující příkaz vytvoří cluster AKS s veřejnou IP adresou vytvořenou v předchozí části:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Nasazení clusteru AKS trvá několik minut.

Pokud chcete ověřit, vyhledejte veřejnou IP adresu vytvořenou v předchozím kroku v Azure Portal. zobrazí se tato IP adresa je přidružená k nástroji pro vyrovnávání zatížení, který je přidružený ke clusteru Kubernetes, jak je znázorněno níže:

 ![Veřejná IP adresa předvolby směrování pro Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [předvolbách směrování ve veřejných IP adresách](routing-preference-overview.md). 
- [Nakonfigurujte předvolby směrování pro virtuální počítač pomocí Azure CLI](configure-routing-preference-virtual-machine-cli.md).
