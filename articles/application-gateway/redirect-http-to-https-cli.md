---
title: Vytvoření služby application gateway s certifikátem - rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak přidat certifikát pro ukončení protokolu SSL pomocí Azure CLI a vytvoření služby application gateway.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: victorh
ms.openlocfilehash: dc3a15ea66d9ead35247bee1673d120faac631d9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355164"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Vytvoření služby application gateway s protokoly HTTP do HTTPS přesměrování pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít k vytvoření [Aplikační brána](overview.md) s certifikát pro ukončení protokolu SSL. Pravidlo směrování se používá k přesměrování provozu HTTP na port HTTPS v aplikační brány. V tomto příkladu můžete také vytvořit [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro fond back-end aplikační brány, který obsahuje dvě instance virtuálního počítače.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem
> * Nastavení sítě
> * Vytvoření služby application gateway s certifikátem
> * Přidat pravidlo naslouchací proces a přesměrování
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

Můžete použít [az brány aplikace-vytvořit](/cli/azure/network/application-gateway#az_network_application_gateway_create) k vytvoření aplikační brány s názvem *myAppGateway*. Při vytváření služby application gateway pomocí Azure CLI, je třeba zadat informace o konfiguraci, například kapacitu, sku a nastavení HTTP. 

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

## <a name="add-a-listener-and-redirection-rule"></a>Přidat pravidlo naslouchací proces a přesměrování

### <a name="add-the-http-port"></a>Přidat HTTP port

Můžete použít [vytvoření aplikační brány sítě az front-endu port](/cli/azure/network/application-gateway/frontend-port#az_network_application_gateway_frontend_port_create) HTTP port přidáte do služby application gateway.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>Přidejte naslouchací proces protokolu HTTP

Můžete použít [az sítě Aplikační brána-naslouchací proces protokolu http vytvořit](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) přidat naslouchací proces s názvem *myListener* aplikační brány.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Přidat konfiguraci přesměrování

Konfigurace přesměrování HTTPS do aplikace pomocí brány přidat HTTP [vytvoření aplikační brány sítě az přesměrování config](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Přidat pravidlo směrování

Přidat pravidlo směrování s názvem *rule2* s konfigurací přesměrování na aplikace pomocí brány [vytvořit pravidlo Aplikační brána sítě az](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálování virtuálních počítačů, nastavit s názvem *myvmss* poskytuje servery fondu back-end v aplikační brány. Jsou virtuální počítače ve škálovací sadě přidružené *myBackendSubnet* a *appGatewayBackendPool*. Chcete-li vytvořit měřítko nastavte, můžete použít [vytvořit az vmss](/cli/azure/vmss#az_vmss_create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
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

![Upozornění zabezpečení](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Chcete-li přijímat upozornění, pokud použijete certifikát podepsaný svým držitelem zabezpečení, vyberte **podrobnosti** a potom **přejděte na webovou stránku**. Následně se zobrazí váš zabezpečený web NGINX, jak je znázorněno v následujícím příkladu:

![Otestovat základní adresu URL v aplikační brány](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem
> * Nastavení sítě
> * Vytvoření služby application gateway s certifikátem
> * Přidat pravidlo naslouchací proces a přesměrování
> * Vytvoření škálování virtuálních počítačů, nastavit výchozí fond back-end


