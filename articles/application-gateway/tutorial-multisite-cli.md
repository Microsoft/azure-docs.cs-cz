---
title: Vytvoření služby application gateway s více hostování lokality – rozhraní příkazového řádku Azure | Microsoft Docs
description: Postup vytvoření služby application gateway, který je hostitelem více lokalit pomocí rozhraní příkazového řádku Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 2e1367db9c2c1d47f34cc35fc088a9eecf00247b
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Vytvoření služby application gateway s více lokality hostování pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [hostování více webů](application-gateway-multi-site-overview.md) při vytváření [Aplikační brána](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endové fondy pomocí sady škálování virtuálních počítačů. Nakonfigurujete naslouchací procesy a pravidla založená na domény, které vlastníte a ujistěte se, že web přenos dorazí na příslušné servery ve fondech. V tomto kurzu se předpokládá, že vlastníte více domén a používá příklady *www.contoso.com* a *www.fabrikam.com*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby Application Gateway
> * Vytvořte naslouchací procesy a pravidla směrování
> * Vytvoření sady škálování virtuálního počítače s back-endové fondy
> * Vytvořte záznam CNAME ve vaší doméně

![Příklad směrování více lokalit](./media/tutorial-multisite-cli/scenario.png)

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

## <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

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

### <a name="add-the-backend-pools"></a>Přidat back-endové fondy

Přidat back-endové fondy s názvem *contosoPool* a *fabrikamPool* , jsou potřeba k obsahovat back-end serverů pomocí [az Aplikační brána fond síťových adres-vytvořit](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Přidejte naslouchací procesy

Naslouchací proces je nutný pro povolení služby application gateway pro směrování provozu správně do fondu back-end. V tomto kurzu vytvoříte dva naslouchací procesy pro dvě domény. V tomto příkladu jsou vytvořeny naslouchací procesy pro doménách *www.contoso.com* a *www.fabrikam.com*. 

Přidejte naslouchací procesy s názvem *contosoListener* a *fabrikamListener* , jsou potřeba ke směrování provozu pomocí [az sítě Aplikační brána-naslouchací proces protokolu http vytvořit](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Přidat pravidla směrování

Pravidla se zpracovávají v pořadí, ve kterém jsou vytvořeny a provoz se přesměruje pomocí první pravidlo, které odpovídá adrese URL odesílání aplikační brány. Například, pokud máte pravidlo pomocí základní naslouchací proces a pravidla pomocí více lokalit naslouchací proces obou na stejném portu, musí být uvedené pravidlo s několika lokalitami naslouchací proces před pravidlo základní naslouchací proces, aby pravidlo více lokalit a fungovat podle očekávání. 

V tomto příkladu vytvoříte dvě nová pravidla a odstranit výchozí pravidlo, které byla vytvořena, když jste vytvořili službu application gateway. Můžete přidat pomocí pravidel [vytvořit pravidlo Aplikační brána sítě az](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Vytvoření sady škálování virtuálního počítače

V tomto příkladu vytvoříte tři sady škálování virtuálního počítače, které podporují tři back-endové fondy v aplikační brány. Jsou pojmenované sady škálování, které vytvoříte *myvmss1*, *myvmss2*, a *myvmss3*. Každá sada škálování obsahuje dvě instance virtuálního počítače, na kterých instalujete NGINX.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Vytvořte záznam CNAME ve vaší doméně

Po vytvoření služby application gateway s veřejnou IP adresu můžete získat adresu serveru DNS a používat ho vytvořit záznam CNAME ve vaší doméně. Můžete použít [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show) k získání adresy DNS aplikační brány. Kopírování *plně kvalifikovaný název domény* hodnotu DNSSettings a používejte ho jako hodnota záznam CNAME, který vytvoříte. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Použití záznamů A není doporučeno, protože VIP může změnit při restartování služby application gateway.

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Zadejte název domény do panelu Adresa prohlížeče. Například http://www.contoso.com.

![Testování serveru contoso v aplikační brány](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Změna adresy k jiné doméně a měli byste vidět něco podobného jako v následujícím příkladu:

![Testování serveru fabrikam v aplikační brány](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby Application Gateway
> * Vytvořte naslouchací procesy a pravidla směrování
> * Vytvoření sady škálování virtuálního počítače s back-endové fondy
> * Vytvořte záznam CNAME ve vaší doméně

> [!div class="nextstepaction"]
> [Další informace o co můžete dělat s aplikační brány](application-gateway-introduction.md)