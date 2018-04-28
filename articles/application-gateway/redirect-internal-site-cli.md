---
title: Vytvoření služby application gateway s interním přesměrování - rozhraní příkazového řádku Azure | Microsoft Docs
description: Postup vytvoření služby application gateway, který přesměruje interní webové přenosy do fondu odpovídající pomocí rozhraní příkazového řádku Azure.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2017
ms.author: davidmu
ms.openlocfilehash: 20f54796f1dd088fa9d6446866151e6ce8ce03f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Vytvoření služby application gateway s interním přesměrování pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [webové přenosy přesměrování](multiple-site-overview.md) při vytváření [Aplikační brána](overview.md). V tomto kurzu definujete fondu back-end pomocí škálovací sadu virtuálních počítačů. Nakonfigurujete naslouchací procesy a pravidla založená na domény, které vlastníte a ujistěte se, že web přenos dorazí na odpovídajícímu fondu adres. V tomto kurzu se předpokládá, že vlastníte více domén a používá příklady *www.contoso.com* a *www.contoso.org*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby Application Gateway
> * Přidejte naslouchací procesy a pravidlo přesměrování
> * Vytvořte nastavení fondu back-end škálování virtuálního počítače
> * Vytvořte záznam CNAME ve vaší doméně

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

Vytvořit virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet* pomocí [vytvoření sítě vnet az](/cli/azure/network/vnet#az_net). Poté můžete přidat podsíť s názvem *myBackendSubnet* , je potřeba fondu back-end serverů pomocí [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Vytvoření veřejné IP adresy s názvem *myAGPublicIPAddress* pomocí [vytvoření veřejné sítě az-ip](/cli/azure/public-ip#az_network_public_ip_create).

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

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

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


## <a name="add-listeners-and-rules"></a>Přidejte naslouchací procesy a pravidla 

Naslouchací proces je nutný pro povolení služby application gateway pro směrování provozu správně do fondu back-end. V tomto kurzu vytvoříte dva naslouchací procesy pro dvě domény. V tomto příkladu jsou vytvořeny naslouchací procesy pro doménách *www.contoso.com* a *www.contoso.org*.

Přidejte naslouchací procesy back-end, které jsou potřebné ke směrování provozu pomocí [az sítě Aplikační brána-naslouchací proces protokolu http vytvořit](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

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

Přidat konfiguraci přesměrování, který odesílá provoz z *www.consoto.org* pro naslouchací proces pro *www.contoso.com* v aplikace pomocí brány [az sítě aplikační brány vytvořit přesměrování config](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

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

### <a name="add-routing-rules"></a>Přidat pravidla směrování

Pravidla se zpracovávají v pořadí, ve kterém jsou vytvořeny a provoz se přesměruje pomocí první pravidlo, které odpovídá adrese URL odesílání aplikační brány. Například, pokud máte pravidlo pomocí základní naslouchací proces a pravidla pomocí více lokalit naslouchací proces obou na stejném portu, musí být uvedené pravidlo s několika lokalitami naslouchací proces před pravidlo základní naslouchací proces, aby pravidlo více lokalit a fungovat podle očekávání. 

V tomto příkladu vytvoříte dvě nová pravidla a odstranit výchozí pravidlo, které bylo vytvořeno.  Můžete přidat pomocí pravidel [vytvořit pravidlo Aplikační brána sítě az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

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

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření sady škálování virtuálního počítače

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která podporuje fondu back-end, který jste vytvořili. Sada škálování, který vytvoříte jmenuje *myvmss* a obsahuje dvě instance virtuálního počítače, na kterých instalujete NGINX.

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Vytvořit záznam CNAME ve vaší doméně

Po vytvoření služby application gateway s veřejnou IP adresu můžete získat adresu serveru DNS a používat ho vytvořit záznam CNAME ve vaší doméně. Můžete použít [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show) k získání adresy DNS aplikační brány. Kopírování *plně kvalifikovaný název domény* hodnotu DNSSettings a používejte ho jako hodnota záznam CNAME, který vytvoříte. Použití záznamů A není doporučeno, protože VIP může změnit při restartování služby application gateway.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Zadejte název domény do panelu Adresa prohlížeče. Například http://www.contoso.com.

![Testování serveru contoso v aplikační brány](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Změnit adresu na vaší domény, například http://www.contoso.org a měli byste vidět, že provoz byl přesměrován zpět na naslouchací proces pro www.contoso.com.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> * Nastavení sítě
> * Vytvoření služby Application Gateway
> * Přidejte naslouchací procesy a pravidlo přesměrování
> * Vytvořte nastavení fondu back-end škálování virtuálního počítače
> * Vytvořte záznam CNAME ve vaší doméně