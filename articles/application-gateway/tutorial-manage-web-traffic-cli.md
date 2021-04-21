---
title: Správa webového provozu – Azure CLI
description: Přečtěte si, jak s Azure CLI vytvořit aplikační bránu se škálovací sadou virtuálních počítačů, která slouží ke správě webového provozu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/20/2019
ms.author: victorh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01df6a12437808aa903046d2923c735ded5067ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772752"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>Správa webového provozu pomocí aplikační brány a Azure CLI

Aplikační brána se používá ke správě a zabezpečení webového provozu spravovaných serverů. Pomocí rozhraní příkazového řádku Azure můžete vytvořit [Aplikační bránu](overview.md) , která používá [sadu škálování virtuálního počítače](../virtual-machine-scale-sets/overview.md) pro back-end servery. V tomto příkladu sada škálování obsahuje dvě instance virtuálních počítačů. Sada škálování se přidá do výchozího back-endu fondu služby Application Gateway.

V tomto článku získáte informace o těchto tématech:

* Nastavit síť
* Vytvoření brány Application Gateway
* Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

Pokud budete chtít, můžete tento postup dokončit pomocí [Azure PowerShell](tutorial-manage-web-traffic-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.4 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [az group create](/cli/azure/group#az_group_create) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Potom můžete přidat podsíť s názvem *myBackendSubnet*, kterou potřebují back-endové servery. Použijte k tomu příkaz [az network vnet subnet create](/cli/azure/network/vnet/subnet). Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k *myAGSubnet* a *myPublicIPAddress* , které jste vytvořili dříve. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Vytvoření aplikační brány může trvat několik minut. Po vytvoření služby Application Gateway se zobrazí tyto nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně poskytuje servery back-endového fondu. Virtuální počítače jsou ve škálovací sadě přidruženy k podsíti *myBackendSubnet* a back-endovému fondu *appGatewayBackendPool*. K vytvoření škálovací sady použijte příkaz [az vmss create](/cli/azure/vmss#az_vmss_create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Instalace serveru NGINX

Nyní můžete nainstalovat NGINX na škálovací sadu virtuálních počítačů, abyste mohli otestovat připojení HTTP k back-endovému fondu.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testování základní adresy URL v aplikační bráně](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete skupinu prostředků, aplikační bránu a další související prostředky potřebovat, odeberte je.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Další kroky

[Omezení webového provozu Firewallem webových aplikací](../web-application-firewall/ag/tutorial-restrict-web-traffic-cli.md)