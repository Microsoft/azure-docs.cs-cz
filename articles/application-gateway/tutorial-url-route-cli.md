---
title: Kurz – Směrování webového provozu na základě adresy URL – Azure CLI
description: Přečtěte si, jak používat Azure CLI ke směrování webového provozu na základě adresy URL na konkrétní škálovatelné fondy serverů.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a774d491de4ca1dfdb96181ff13fa644a061cd65
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434210"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Kurz: Použití Azure CLI ke směrování webového provozu na základě adresy URL

Azure CLI můžete použít ke konfiguraci směrování webového provozu na konkrétní škálovatelné fondy serverů na základě adresy URL pro přístup k aplikaci. V tomto kurzu použijete službu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) k vytvoření aplikační brány [Azure Application Gateway](application-gateway-introduction.md) se třemi back-endovými fondy. Každý back-endový fond má určitý účel, třeba běžná data, obrázky nebo videa.  Směrování provozu na samostatné fondy zajistí, aby zákazníci získali požadované informace v okamžiku, kdy je potřebují.

Pokud chcete směrování provozu povolit, vytvořte [pravidla směrování](application-gateway-url-route-overview.md) přiřazená naslouchacím procesům, které naslouchají na konkrétních portech. Tím zajistíte, že se webový provoz dostane na správné servery ve fondech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvořit naslouchací procesy, mapu cest adres URL a pravidla
> * Vytvořit škálovatelné back-endové fondy


![Příklad směrování na základě adresy URL](./media/tutorial-url-route-cli/scenario.png)

Pokud chcete, můžete k dokončení tohoto kurzu použít [Azure PowerShell](tutorial-url-route-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat CLI místně, potřebujete k tomuto kurzu verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, který se používá k nasazení a správě prostředků Azure. Pomocí příkazu [az group create](/cli/azure/group#create) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-net) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Potom přidejte podsíť s názvem *myBackendSubnet*, kterou potřebují back-endové servery. Použijte k tomu příkaz [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create). Pomocí příkazu [az network public-ip create](/cli/azure/public-ip#az-network_public_ip_create) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

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

## <a name="create-the-application-gateway-with-url-map"></a>Vytvoření aplikační brány s mapou adres URL

Příkaz [az network application-gateway create](/cli/azure/application-gateway#create) použijte k vytvoření aplikační brány s názvem *myAppGateway*. Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. 

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

 Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.


### <a name="add-image-and-video-backend-pools-and-port"></a>Přidání back-endových fondů a portu pro obrázky a videa

Přidejte do aplikační brány back-endové fondy pojmenované *imagesBackendPool* a *videoBackendPool* příkazem [az network application-gateway address-pool create](/cli/azure/application-gateway#az-network_application_gateway_address-pool_create). Přidejte front-endový port fondů příkazem [az network application-gateway frontend-port create](/cli/azure/application-gateway#az-network_application_gateway_frontend_port_create). 

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

### <a name="add-backend-listener"></a>Přidání back-endového naslouchacího procesu

Přidejte back-endový naslouchací proces s názvem *backendListener*, který je potřeba ke směrování provozu, příkazem [az network application-gateway http-listener create](/cli/azure/application-gateway#az-network_application_gateway_http_listener_create).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>Přidání mapy cest adres URL

Mapy cest adres URL zajišťují přesměrování konkrétních adres URL na konkrétní back-endové fondy. Vytvořte mapy cest adres URL pojmenované *imagePathRule* a *videoPathRule*. Použijte k tomu příkazy [az network application-gateway url-path-map create](/cli/azure/application-gateway#az-network_application_gateway_url_path_map_create) a [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az-network_application_gateway_url_path_map_rule_create).

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

### <a name="add-routing-rule"></a>Přidání pravidla směrování

Pravidlo směrování přidruží mapy adres URL k dříve vytvořenému naslouchacímu procesu. Přidejte pravidlo *rule2* příkazem [az network application-gateway rule create](/cli/azure/application-gateway#az-network_application_gateway_rule_create).

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

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

V tomto příkladu vytvoříte tři škálovací sady virtuálních počítačů, které podporují tři vytvořené back-endové fondy. Vytvořené škálovací sady se jmenují *myvmss1*, *myvmss2* a *myvmss3*. Každá škálovací sada obsahuje dvě instance virtuálních počítačů, na které nainstalujete server NGINX.

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
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Příklad: *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* nebo *http://40.121.222.19:8080/video/test.htm*.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Otestování základní adresy URL v aplikační bráně](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Změňte adresu URL na http://&lt;ip-adresa&gt;:8080/images/test.htm, kde za &lt;ip-adresa&gt; dosadíte svoji IP adresu. Výsledek by měl být podobný jako na následujícím příkladu:

![Testování adresy URL obrázků v aplikační bráně](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Změňte adresu URL na http://&lt;ip-adresa&gt;:8080/video/test.htm, kde za &lt;ip-adresa&gt; dosadíte svoji IP adresu. Výsledek by měl být podobný jako na následujícím příkladu.

![Testování adresy URL videa v aplikační bráně](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete skupinu prostředků, aplikační bránu a další související prostředky potřebovat, odeberte je.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvořit naslouchací procesy, mapu cest adres URL a pravidla
> * Vytvořit škálovatelné back-endové fondy

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány se směrováním založeným na cestě URL](./tutorial-url-redirect-cli.md)
