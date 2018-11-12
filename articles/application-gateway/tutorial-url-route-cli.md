---
title: Kurz – Směrování webového provozu na základě adresy URL – Azure CLI
description: V tomto kurzu zjistíte, jak pomocí Azure CLI směrovat webový provoz na základě adresy URL do konkrétních škálovatelných fondů serverů.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 10/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 68532ec4ae7e6d6b496ece8d08755555f756a60e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413447"
---
# <a name="tutorial-route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Kurz: Použití Azure CLI ke směrování webového provozu na základě adresy URL

Jako správce IT, který má na starosti správu webového provozu, chcete zákazníkům nebo uživatelům pomoct co nejrychleji získat informace, které potřebují. Jedním ze způsobů, jak můžete jejich prostředí optimalizovat, je směrovat různé druhy webového provozu do různých prostředků serveru. V tomto kurzu se dozvíte, jak pomocí Azure CLI nastavit a nakonfigurovat směrování služby Application Gateway pro různé typy provozu z vaší aplikace. Toto směrování pak bude na základě adresy URL směrovat provoz do různých fondů serverů.

![Příklad směrování na základě adresy URL](./media/tutorial-url-route-cli/scenario.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků pro síťové prostředky, které budete potřebovat
> * Vytvoření síťových prostředků
> * Vytvoření aplikační brány pro provoz přicházející z vaší aplikace
> * Určení fondů serverů a pravidel směrování pro různé typy provozu
> * Vytvoření škálovacích sad pro jednotlivé fondy, aby se mohly automaticky škálovat
> * Spuštění testu, abyste mohli ověřit směrování různých typů provozu do správných fondů

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure PowerShellu](tutorial-url-route-powershell.md) nebo webu [Azure Portal](create-url-route-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat CLI místně, potřebujete k tomuto kurzu verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, který se používá k nasazení a správě prostředků Azure. Skupinu prostředků vytvoříte pomocí příkazu `az group create`.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Pomocí příkazu `az network vnet create` vytvořte virtuální síť *myVNet* a podsíť *myAGSubnet*. Potom pomocí příkazu `az network vnet subnet create` přidejte podsíť *myBackendSubnet*, kterou potřebují back-endové servery. Pomocí příkazu `az network public-ip create` vytvořte veřejnou IP adresu *myAGPublicIPAddress*.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Vytvoření aplikační brány s mapou adres URL

Pomocí příkazu `az network application-gateway create` vytvořte aplikační bránu *myAppGateway*. Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*.

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


|Funkce  |Popis  |
|---------|---------|
|appGatewayBackendPool     |Aplikační brána musí mít alespoň jeden back-endový fond adres.|
|appGatewayBackendHttpSettings     |Určuje, že se ke komunikaci používá port 80 a protokol HTTP.|
|appGatewayHttpListener     |Výchozí naslouchací proces přidružený k fondu appGatewayBackendPool.|
|appGatewayFrontendIP     |Přiřadí adresu myAGPublicIPAddress k naslouchacímu procesu appGatewayHttpListener.|
|rule1     |Výchozí pravidlo směrování přidružené k naslouchacímu procesu appGatewayHttpListener.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Přidání back-endových fondů a portu pro obrázky a videa

Pomocí příkazu `az network application-gateway address-pool create` přidejte do aplikační brány back-endové fondy *imagesBackendPool* a *videoBackendPool*. Pomocí příkazu `az network application-gateway frontend-port create` přidejte pro fondy front-endový port.

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

### <a name="add-a-backend-listener"></a>Přidání back-endového naslouchacího procesu

Pomocí příkazu `az network application-gateway http-listener create` přidejte back-endový naslouchací proces *backendListener*, který je potřeba ke směrování provozu.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>Přidání mapy cest adres URL

Mapy cest adres URL zajišťují přesměrování konkrétních adres URL na konkrétní back-endové fondy. Pomocí příkazů `az network application-gateway url-path-map create` a `az network application-gateway url-path-map rule create` vytvořte mapy cest adres URL *imagePathRule* a *videoPathRule*.

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

### <a name="add-a-routing-rule"></a>Přidání pravidla směrování

Pravidlo směrování přidruží mapy adres URL k dříve vytvořenému naslouchacímu procesu. Pomocí příkazu `az network application-gateway rule create` přidejte pravidlo *rule2*.

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

## <a name="create-vm-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

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

K získání veřejné IP adresy aplikační brány použijte příkaz az network public-ip show. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Příklad: *http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm* nebo *http://40.121.222.19:8080/video/test.htm*.

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

Pokud už je nepotřebujete, odeberte skupinu prostředků, aplikační bránu a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření aplikační brány se směrováním založeným na cestě URL](./tutorial-url-redirect-cli.md)
