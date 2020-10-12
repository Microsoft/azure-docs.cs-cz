---
title: Konfigurace předvolby směrování pro virtuální počítač – Azure CLI
description: Naučte se, jak vytvořit virtuální počítač s veřejnou IP adresou s volbou předvolby směrování pomocí rozhraní příkazového řádku Azure (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49b0df3e750d4d23cb6a64f3f7266613fd2f2981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501827"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Konfigurace předvolby směrování pro virtuální počítač pomocí Azure CLI

V tomto článku se dozvíte, jak nakonfigurovat předvolby směrování pro virtuální počítač. Internetový provoz z virtuálního počítače bude směrován přes síť poskytovatele internetových služeb, pokud zvolíte možnost **Internet** jako předvolby směrování. Výchozí směrování je prostřednictvím globální sítě Microsoftu.

V tomto článku se dozvíte, jak vytvořit virtuální počítač s veřejnou IP adresou, která je nastavená na směrování provozu přes veřejný Internet pomocí Azure CLI.

> [!IMPORTANT]
> Předvolby směrování jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrace funkce pro vaše předplatné
Funkce předvolby směrování je aktuálně ve verzi Preview. Zaregistrujte funkci pro vaše předplatné následujícím způsobem:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
1. Pokud používáte Cloud Shell, přejděte ke kroku 2. Otevřete relaci příkazu a přihlaste se k Azure pomocí `az login` .
2. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků v Východní USA oblasti Azure:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Chcete-li získat přístup k virtuálním počítačům z Internetu, je nutné vytvořit veřejnou IP adresu. Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip). Následující příklad vytvoří veřejnou IP adresu typu předvolby směrování *Internet* v oblasti *východní USA* :

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

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Než nasadíte virtuální počítač, musíte vytvořit podpůrné síťové prostředky – skupinu zabezpečení sítě, virtuální síť a virtuální síťovou kartu.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pro pravidla, která budou řídit příchozí a odchozí komunikaci ve vaší virtuální síti pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) .

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí příkazu [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). Následující příklad vytvoří virtuální síť s názvem *myVNET* s podsítěmi *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Vytvoření síťové karty

Vytvořte virtuální síťovou kartu pro virtuální počítač pomocí [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). Následující příklad vytvoří virtuální síťovou kartu, která bude připojena k virtuálnímu počítači.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří virtuální počítač s Windows serverem 2019 a požadované součásti virtuální sítě, pokud ještě neexistují.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az-group-delete):

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [předvolbách směrování ve veřejných IP adresách](routing-preference-overview.md).
- Přečtěte si další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
