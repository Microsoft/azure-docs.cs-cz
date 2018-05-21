---
title: Vytvoření služby application gateway s pravidel adres URL na základě cestu směrování - rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se vytvořit adresu URL na základě cesty pravidla směrování pro aplikací bránu a virtuální počítač sad škálování pomocí rozhraní příkazového řádku Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: ff02688d59f32641319e6816dc55744422c62eeb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Vytvoření služby application gateway s pravidel adres URL na základě cestu směrování pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [pravidla směrování na základě cesty adresy URL](application-gateway-url-route-overview.md) při vytváření [Aplikační brána](application-gateway-introduction.md). V tomto kurzu vytvoříte pomocí back-endové fondy [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Potom můžete vytvořit pravidla směrování pro Ujistěte se, že web přenos dorazí na příslušné servery ve fondech.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby application gateway pomocí adresy URL mapy
> * Vytvoření sady škálování virtuálního počítače s back-endové fondy

![Příklad směrování URL](./media/application-gateway-create-url-route-cli/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

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

## <a name="create-the-application-gateway-with-url-map"></a>Vytvoření aplikační brány s mapování adresy URL

Můžete použít [az brány aplikace-vytvořit](/cli/azure/application-gateway#create) k vytvoření aplikační brány s názvem *myAppGateway*. Při vytváření služby application gateway pomocí Azure CLI, je třeba zadat informace o konfiguraci, například kapacitu, sku a nastavení HTTP. Službu application gateway je přiřazena k *myAGSubnet* a *myAGPublicIPAddress* kterou jste vytvořili. 

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
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Ho může trvat několik minut pro službu application gateway, který se má vytvořit. Po vytvoření služby application gateway se zobrazí tyto nové funkce:

- *appGatewayBackendPool* -aplikační brány musí mít alespoň jeden fond adres back-end.
- *appGatewayBackendHttpSettings* -Určuje, že port 80 a protokol HTTP se používá pro komunikaci.
- *appGatewayHttpListener* -přidružený naslouchací proces výchozí *appGatewayBackendPool*.
- *appGatewayFrontendIP* -přiřadí *myAGPublicIPAddress* k *appGatewayHttpListener*.
- *rule1 New* – výchozí směrování pravidlo, které souvisí s *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Přidání bitové kopie a video back-endové fondy a portu

Můžete přidat back-endové fondy s názvem *imagesBackendPool* a *videoBackendPool* vaší aplikační brány pomocí [az Aplikační brána fond síťových adres-vytvořit](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create). Přidávání portu front-endu pro fondy pomocí [vytvoření aplikační brány sítě az front-endu port](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Přidejte naslouchací proces back-end

Přidejte naslouchací proces back-end s názvem *backendListener* , je potřeba ke směrování provozu pomocí [az sítě Aplikační brána-naslouchací proces protokolu http vytvořit](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Přidat mapu cestu adresy URL

Adresa URL cesty mapy Ujistěte se, že konkrétní adresy URL jsou směrované na konkrétní back-endové fondy. Můžete vytvořit mapy cestu adresy URL s názvem *imagePathRule* a *videoPathRule* pomocí [az Aplikační brána url cesta mapy sítě vytvořit](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) a [az sítě Vytvořit pravidlo adresy url. cesta mapy aplikační brány](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create)

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Přidat pravidlo směrování

Pravidlo směrování URL mapy přidruží naslouchací proces, který jste vytvořili. Můžete přidat pravidlo s názvem *rule2* pomocí [vytvořit pravidlo Aplikační brána sítě az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření sady škálování virtuálního počítače

V tomto příkladu můžete vytvořit tři sady škálování virtuálního počítače, které podporují tři back-endové fondy, které jste vytvořili. Jsou pojmenované sady škálování, které vytvoříte *myvmss1*, *myvmss2*, a *myvmss3*. Každá sada škálování obsahuje dvě instance virtuálního počítače, na kterých instalujete NGINX.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
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
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Instalace serveru NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Chcete-li získat veřejnou IP adresu brány, aplikace, můžete použít [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, nebo *http://40.121.222.19:8080/video/test.htm*.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Otestovat základní adresu URL v aplikační brány](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

Změňte adresu URL k http://<ip-address>:8080/video/test.html na konec základní adresu URL a měli byste vidět něco podobného jako v následujícím příkladu:

![Testovací adresu URL bitové kopie v aplikační brány](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

Změňte adresu URL k http://<ip-address>:8080/video/test.html a měli byste vidět něco podobného jako v následujícím příkladu.

![Adresa URL videa testu v aplikační brány](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby application gateway pomocí adresy URL mapy
> * Vytvoření sady škálování virtuálního počítače s back-endové fondy

Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.
