---
title: 'Kurz: přesměrování na základě cesty URL pomocí rozhraní příkazového řádku'
titleSuffix: Azure Application Gateway
description: V tomto kurzu se dozvíte, jak vytvořit Aplikační bránu s přesměrovaným přenosem na základě cesty URL pomocí Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: da6d02e620c33610770c71f0c0e3ae68e70ee317
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397038"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Kurz: Vytvoření aplikační brány s Azure CLI se směrováním založeným na cestě URL

Při vytváření [aplikační brány](./overview.md) můžete Azure CLI použít ke konfiguraci [pravidel směrování podle adres URL](tutorial-url-route-cli.md). V tomto kurzu vytvoříte back-endové fondy pomocí [škálovacích sad virtuálních počítačů](../virtual-machine-scale-sets/overview.md). Pak vytvoříte pravidla směrování adres URL, která zajistí přesměrování webového provozu na správný back-endový fond.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření brány Application Gateway
> * Přidat naslouchací procesy a pravidla směrování
> * Vytvořit pro back-endové fondy škálovací sadu virtuálních počítačů

Na následujícím příkladu je vidět přesměrování webového provozu, který přichází ze dvou portů 8080 a 8081, na stejné back-endové fondy:

![Příklad směrování na základě adresy URL](./media/tutorial-url-redirect-cli/scenario.png)

Pokud chcete, můžete k dokončení tohoto kurzu použít [Azure PowerShell](tutorial-url-redirect-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky 

Pokud se rozhodnete nainstalovat a používat CLI místně, potřebujete k tomuto kurzu verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [az group create](/cli/azure/group) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Potom můžete přidat podsíť s názvem *myBackendSubnet* , kterou potřebují back-endové servery. Použijte k tomu příkaz [az network vnet subnet create](/cli/azure/network/vnet/subnet). Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

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

Pomocí [AZ Network Application-Gateway Create](/cli/azure/network/application-gateway) vytvořte Aplikační bránu s názvem myAppGateway. Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k *myAGSubnet* a *myPublicIPAddress* , které jste vytvořili dříve.

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

 Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.


### <a name="add-backend-pools-and-ports"></a>Přidání back-endových fondů a portů

K aplikační bráně můžete přidat back-endové fondy pojmenované *imagesBackendPool* a *videoBackendPool* příkazem [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool). Přidejte front-endové porty fondů příkazem [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port). 

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
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Přidání naslouchacích procesů a pravidel

### <a name="add-listeners"></a>Přidání naslouchacích procesů

Přidejte back-endové naslouchací procesy s názvem *backendListener* a *redirectedListener* , které jsou potřeba ke směrování provozu, příkazem [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener).


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Přidání výchozí mapy cest adres URL

Mapy cest URL se zajišťují směrování konkrétních adres URL na konkrétní back-endové fondy. Vytvořte mapy cest adres URL pojmenované *imagePathRule* a *videoPathRule*. Použijte k tomu příkazy [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) a [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule).

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Přidání konfigurace přesměrování

Ke konfiguraci přesměrování naslouchacího procesu použijte příkaz [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config).

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Přidání mapy cest adres URL pro přesměrování

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Přidání pravidel směrování

Pravidla směrování přidružují mapy cest adres URL k vytvořeným naslouchacím procesům. Přidejte pravidla pojmenovaná *defaultRule* a *redirectedRule* příkazem [az network application-gateway rule create](/cli/azure/network/application-gateway/rule).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

V tomto příkladu vytvoříte tři škálovací sady virtuálních počítačů, které podporují tři vytvořené back-endové fondy. Vytvořené škálovací sady se budou jmenovat *myvmss1* , *myvmss2* a *myvmss3*. Každá škálovací sada obsahuje dvě instance virtuálních počítačů, na které nainstalujete server NGINX.

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

K získání veřejné IP adresy aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například, `http://40.121.222.19` ,, `http://40.121.222.19:8080/images/test.htm` `http://40.121.222.19:8080/video/test.htm` nebo `http://40.121.222.19:8081/images/test.htm` .

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testování základní adresy URL v aplikační bráně](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Změňte adresu URL na http:// &lt; IP-address &gt; : 8080/images/test.html, nahraďte vaši IP adresu &lt; IP-address &gt; a mělo by se zobrazit něco jako v následujícím příkladu:

![Testování adresy URL obrázků v aplikační bráně](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Změňte adresu URL na http:// &lt; IP-address &gt; : 8080/video/test.html, nahraďte svou IP adresu IP &lt; adresou &gt; a měli byste vidět jako v následujícím příkladu:

![Testování adresy URL videa v aplikační bráně](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Teď změňte adresu URL na http:// &lt; IP-address &gt; : 8081/images/test.htm, nahraďte IP adresu IP &lt; -Address &gt; a měli byste vidět provoz přesměrované zpět do fondu back-endu imagí na &lt; adrese http://IP-address &gt; : 8080/images.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete skupinu prostředků, aplikační bránu a další související prostředky potřebovat, odeberte je.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](./overview.md)