---
title: Vytvoření služby application gateway s interním přesměrování – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Informace o vytvoření služby application gateway, který přesměruje interní webový provoz do příslušného fondu pomocí Azure CLI.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: 1fee9e511b251648adb412fe3e4ca01c20c7edc5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054210"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Vytvoření služby application gateway s interním přesměrování pomocí Azure CLI

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [přesměrování webového provozu](application-gateway-multi-site-overview.md) při vytváření [služba application gateway](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endového fondu pomocí škálovací sady virtuálních počítačů. Pak nakonfigurujte naslouchací procesy a pravidel založených na doménách, které vlastníte, abyste měli jistotu, že webový provoz dorazí na příslušného fondu. V tomto kurzu se předpokládá, že jste vlastníkem více domén a používá příklady *www.contoso.com* a *www.contoso.org*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření služby Application Gateway
> * Přidat naslouchací procesy a pravidlo přesměrování
> * Vytvoření virtuálního počítače škálovací sady s back-endový fond
> * Vytvořit záznam CNAME v doméně

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#create).

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-net) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Poté můžete přidat podsíť s názvem *myBackendSubnet* , který je potřeba pro back-endový fond serverů pomocí [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet#az-network_vnet_subnet_create). Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

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

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway#create). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. 

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

Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto její nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.


## <a name="add-listeners-and-rules"></a>Přidání naslouchacích procesů a pravidel 

Naslouchací proces je potřebný k tomu, aby aplikační brána správně směrovala provoz na back-endový fond. V tomto kurzu vytvoříte dva naslouchací procesy pro dvě domény. V tomto příkladu se vytvoří naslouchací procesy pro doménách *www.contoso.com* a *www.contoso.org*.

Přidejte back-endové naslouchací procesy, které jsou potřeba ke směrování provozu, příkazem [az network application-gateway http-listener create](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Přidat konfiguraci přesměrování

Přidat konfiguraci přesměrování, který odesílá síťový provoz z *www.consoto.org* pro naslouchací proces pro *www.contoso.com* v aplikační bránu pomocí [az network application-gateway přesměrování config vytvořit](/cli/azure/network/application-gateway/redirect-config#az-network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoComListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Přidání pravidel směrování

Pravidla se zpracovávají v pořadí, ve kterém jsou vytvořeny a provoz se směřuje, že pomocí první pravidlo, které odpovídá adrese URL odeslán ke službě application gateway. V tomto kurzu není potřeba, který byl vytvořen výchozí základní pravidlo. V tomto příkladu vytvoříte dvě nová pravidla s názvem *contosoComRule* a *contosoOrgRule* a odstranit výchozí pravidlo, které bylo vytvořeno.  Můžete přidat pravidla pomocí [az network application-gateway pravidlo vytvořte](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření škálovacích sad virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která podporuje výchozí fond back-end, který byl vytvořen. Škálovací sady, kterou vytvoříte jmenuje *myvmss* a obsahuje dvě instance virtuálních počítačů, na kterých instalujete server NGINX.

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Vytvoření názvu CNAME v doméně

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. K získání adresy DNS aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Zkopírujte hodnotu *fqdn* z objektu DNESettings a použijte ji jako hodnotu vytvořeného záznamu CNAME. Použití záznamů typu A se nedoporučuje, protože virtuální IP adresa se při restartování aplikační brány může změnit.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Do adresního řádku prohlížeče zadejte název domény. Příklad: http://www.contoso.com.

![Testování webu Contoso v aplikační bráně](./media/tutorial-internal-site-redirect-cli/application-gateway-nginxtest.png)

Změňte adresu do jiné domény, například http://www.contoso.org a měli byste vidět, že provoz byl přesměrován zpět na naslouchací proces pro www.contoso.com.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření služby Application Gateway
> * Přidat naslouchací procesy a pravidlo přesměrování
> * Vytvoření virtuálního počítače škálovací sady s back-endový fond
> * Vytvořit záznam CNAME v doméně

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](./application-gateway-introduction.md)