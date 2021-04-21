---
title: Interní přesměrování pomocí rozhraní příkazového řádku
titleSuffix: Azure Application Gateway
description: Naučte se, jak vytvořit Aplikační bránu, která přesměruje vnitřní webový provoz do příslušného fondu pomocí Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: c4aa8927422d1f95a642e1d7c383aebc03a4930e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775768"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Vytvoření aplikační brány s interním přesměrování pomocí Azure CLI

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [přesměrování webového provozu](multiple-site-overview.md) při vytváření [aplikační brány](overview.md). V tomto kurzu definujete back-end fond pomocí sady škálování virtuálních počítačů. Pak můžete nakonfigurovat naslouchací procesy a pravidla na základě domén, které vlastníte, aby se zajistilo, že webový provoz přijde do příslušného fondu. V tomto kurzu se předpokládá, že vlastníte více domén a používáte příklady *webových \. contoso.com* a *www \. contoso.org*.

V tomto článku získáte informace o těchto tématech:

* Nastavit síť
* Vytvoření brány Application Gateway
* Přidat naslouchací procesy a pravidlo přesměrování
* Vytvoření sady škálování virtuálních počítačů s back-end fondem
* Vytvoření záznamu CNAME v doméně

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.4 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [az group create](/cli/azure/group) vytvořte skupinu prostředků.

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Pak můžete přidat podsíť s názvem *myBackendSubnet* , kterou potřebuje back-end fond serverů, pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

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

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. 

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

Naslouchací proces je potřeba k tomu, aby brána Application Gateway mohla správně směrovat provoz na back-endový fond. V tomto kurzu vytvoříte dva naslouchací procesy pro dvě domény. V tomto příkladu se pro domény *webových \. contoso.com* a *www \. contoso.org* vytvoří naslouchací procesy.

Přidejte back-endové naslouchací procesy, které jsou potřeba ke směrování provozu, příkazem [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create).

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

Přidejte konfiguraci přesměrování, která odesílá provoz z *webové služby \. consoto.org* do naslouchacího procesu pro službu *www \. contoso.com* ve službě Application Gateway pomocí funkce [AZ Network Application-Gateway redirect-config Create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Přidání pravidel směrování

Pravidla se zpracovávají v pořadí, ve kterém jsou vytvořená, a provoz se směruje pomocí prvního pravidla, které odpovídá adrese URL odeslané do aplikační brány. Pokud máte například na stejném portu pravidlo založené na základním naslouchacím procesu a pravidlo založené na naslouchacím procesu pro více webů, musí být pravidlo s naslouchacím procesem pro více webů uvedeno před pravidlem se základním naslouchacím procesem, aby fungovalo podle očekávání. 

V tomto příkladu vytvoříte dvě nová pravidla a odstraníte vytvořené výchozí pravidlo.  Pravidlo přidáte příkazem [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

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

V tomto příkladu vytvoříte sadu škálování virtuálního počítače, která podporuje fond back-end, který jste vytvořili. Sada škálování, kterou vytvoříte, má název *myvmss* a obsahuje dvě instance virtuálních počítačů, na kterých instalujete Nginx.

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

Spusťte tento příkaz v okně prostředí:

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

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. K získání adresy DNS aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Zkopírujte hodnotu *fqdn* z objektu DNESettings a použijte ji jako hodnotu vytvořeného záznamu CNAME. Použití záznamů typu A se nedoporučuje, protože virtuální IP adresa se při restartování aplikační brány může změnit.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Do adresního řádku prohlížeče zadejte název domény. Například http: \/ /www.contoso.com.

![Testování webu Contoso v aplikační bráně](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Změňte adresu na jinou doménu, například http: \/ /www.contoso.org, a měli byste vidět, že přenos byl přesměrován zpět na naslouchací proces pro webovou \. contoso.com.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> * Nastavit síť
> * Vytvoření brány Application Gateway
> * Přidat naslouchací procesy a pravidlo přesměrování
> * Vytvoření sady škálování virtuálních počítačů s back-end fondem
> * Vytvoření záznamu CNAME v doméně
