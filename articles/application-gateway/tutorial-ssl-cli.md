---
title: Vytvoření aplikační brány s ukončením protokolu SSL – Azure CLI
description: Přečtěte si, jak vytvořit aplikační bránu a přidat certifikát pro ukončení protokolu SSL pomocí Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 4/27/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 9c52eb99d76253298af858ed8748929874896523
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Kurz: Vytvoření aplikační brány s ukončením protokolu SSL pomocí Azure CLI

Pomocí Azure CLI můžete vytvořit [aplikační bránu](overview.md) s certifikátem pro [ukončení protokolu SSL](ssl-overview.md), která pro back-endové servery používá [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). V tomto příkladu obsahuje škálovací sada dvě instance virtuálních počítačů přidané do výchozího back-endového fondu aplikační brány.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Nastavit síť
> * Vytvořit aplikační bránu s certifikátem
> * Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

Pokud chcete, můžete tento kurz absolvovat v [Azure PowerShellu](tutorial-ssl-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat CLI místně, potřebujete k tomuto kurzu verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem. Pro účely tohoto kurzu vytvoříte certifikát podepsaný svým držitelem (self-signed certificate) a soubor pfx pomocí příkazu openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Zadejte požadované hodnoty certifikátu. Můžete také použít výchozí hodnoty.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Zadejte heslo pro certifikát. V tomto příkladu se používá *Azure123456!* .

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [az group create](/cli/azure/group#create) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_net) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Potom můžete přidat podsíť s názvem *myBackendSubnet*, kterou potřebují back-endové servery. Použijte k tomu příkaz [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Pomocí příkazu [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

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
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Vytvoření aplikační brány

K vytvoření aplikační brány můžete použít příkaz [az network application-gateway create](/cli/azure/application-gateway#create). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. 

Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. Při vytváření aplikační brány v tomto příkladu přidružíte certifikát, který jste vytvořili, a heslo. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto její nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně poskytuje servery pro back-endový fond. Virtuální počítače jsou ve škálovací sadě přidruženy k podsíti *myBackendSubnet* a back-endovému fondu *appGatewayBackendPool*. K vytvoření škálovací sady použijte příkaz [az vmss create](/cli/azure/vmss#az_vmss_create).

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

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy aplikační brány můžete použít příkaz [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Zabezpečené upozornění](./media/tutorial-ssl-cli/application-gateway-secure.png)

Pokud chcete přijímat upozornění zabezpečení v případě použití certifikátu podepsaného svým držitelem (self-signed certificate), vyberte **Podrobnosti** a potom **Pokračovat na web**. Následně se zobrazí váš zabezpečený web NGINX, jak je znázorněno v následujícím příkladu:

![Otestování základní adresy URL v aplikační bráně](./media/tutorial-ssl-cli/application-gateway-nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete skupinu prostředků, aplikační bránu a další související prostředky potřebovat, odeberte je.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Nastavit síť
> * Vytvořit aplikační bránu s certifikátem
> * Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány, která hostuje více webů](./tutorial-multiple-sites-cli.md)
