---
title: Vytvoření služby application gateway s ukončení protokolu SSL - rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak přidat certifikát pro ukončení protokolu SSL pomocí Azure CLI a vytvoření služby application gateway.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/18/2018
ms.author: victorh
ms.openlocfilehash: cdc24d0b95e30f762eb202ce08222ccde34424e9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Vytvoření služby application gateway s ukončení protokolu SSL pomocí Azure CLI

Rozhraní příkazového řádku Azure můžete použít k vytvoření [Aplikační brána](application-gateway-introduction.md) s certifikátem pro [ukončení protokolu SSL](application-gateway-backend-ssl.md) používající [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro back-end serverů. V tomto příkladu byly sadou škálování obsahuje dvě instance virtuálních počítačů, které jsou přidány do fondu back-end výchozí aplikační brány.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem
> * Nastavení sítě
> * Vytvoření služby application gateway s certifikátem
> * Vytvoření škálování virtuálních počítačů, nastavit výchozí fond back-end

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem

Pro použití v provozním prostředí Importujte platný certifikát podepsaný službou důvěryhodného zprostředkovatele. V tomto kurzu vytvoříte certifikát podepsaný svým držitelem a pomocí příkazu openssl souboru pfx.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Zadejte hodnoty, které dávají smysl pro svůj certifikát. Můžete přijmout výchozí hodnoty.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Zadejte heslo pro certifikát. V tomto příkladu *Azure123456!* se používá.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvoření skupiny prostředků pomocí [vytvořit skupinu az](/cli/azure/group#create).

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupAG* v *eastus* umístění.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Vytvořit virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet* pomocí [vytvoření sítě vnet az](/cli/azure/network/vnet#az_net). Poté můžete přidat podsíť s názvem *myBackendSubnet* to vyžaduje back-end serverů pomocí [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Vytvoření veřejné IP adresy s názvem *myAGPublicIPAddress* pomocí [vytvoření veřejné sítě az-ip](/cli/azure/public-ip#az_network_public_ip_create).

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

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

Můžete použít [az brány aplikace-vytvořit](/cli/azure/application-gateway#create) k vytvoření aplikační brány. Při vytváření služby application gateway pomocí Azure CLI, je třeba zadat informace o konfiguraci, například kapacitu, sku a nastavení HTTP. 

Službu application gateway je přiřazena k *myAGSubnet* a *myAGPublicIPAddress* kterou jste vytvořili. V tomto příkladu přidružíte certifikát, který jste vytvořili a jeho heslo při vytváření služby application gateway. 

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

 Ho může trvat několik minut pro službu application gateway, který se má vytvořit. Po vytvoření služby application gateway se zobrazí tyto nové funkce:

- *appGatewayBackendPool* -aplikační brány musí mít alespoň jeden fond adres back-end.
- *appGatewayBackendHttpSettings* -Určuje, že port 80 a protokol HTTP se používá pro komunikaci.
- *appGatewayHttpListener* -přidružený naslouchací proces výchozí *appGatewayBackendPool*.
- *appGatewayFrontendIP* -přiřadí *myAGPublicIPAddress* k *appGatewayHttpListener*.
- *rule1 New* – výchozí směrování pravidlo, které souvisí s *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která poskytuje servery pro výchozí fond back-end v aplikační brány. Jsou virtuální počítače ve škálovací sadě přidružené *myBackendSubnet* a *appGatewayBackendPool*. Chcete-li vytvořit měřítko nastavte, můžete použít [vytvořit az vmss](/cli/azure/vmss#az_vmss_create).

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

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Chcete-li získat veřejnou IP adresu brány, aplikace, můžete použít [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Upozornění zabezpečení](./media/application-gateway-ssl-cli/application-gateway-secure.png)

Chcete-li přijímat upozornění, pokud použijete certifikát podepsaný svým držitelem zabezpečení, vyberte **podrobnosti** a potom **přejděte na webovou stránku**. Následně se zobrazí váš zabezpečený web NGINX, jak je znázorněno v následujícím příkladu:

![Otestovat základní adresu URL v aplikační brány](./media/application-gateway-ssl-cli/application-gateway-nginx.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem
> * Nastavení sítě
> * Vytvoření služby application gateway s certifikátem
> * Vytvoření škálování virtuálních počítačů, nastavit výchozí fond back-end

Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.
