---
title: Vytvoření aplikační brány s interním přesměrování – Azure CLI | Microsoft Docs
description: Naučte se, jak vytvořit Aplikační bránu, která přesměruje vnitřní webový provoz do příslušného fondu pomocí Azure CLI.
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
ms.openlocfilehash: 6a4a47f93054c4c93043b5215371b5eea7244d46
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73834994"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Vytvoření aplikační brány s interním přesměrování pomocí Azure CLI

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [přesměrování webového provozu](application-gateway-multi-site-overview.md) při vytváření [aplikační brány](application-gateway-introduction.md). V tomto kurzu vytvoříte back-end fond pomocí sady škálování virtuálních počítačů. Pak můžete nakonfigurovat naslouchací procesy a pravidla na základě domén, které vlastníte, aby se zajistilo, že webový provoz přijde do příslušného fondu. V tomto kurzu se předpokládá, že vlastníte více domén a používáte příklady *webových\.contoso.com* a *www\.contoso.org*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření Application Gateway
> * Přidat naslouchací procesy a pravidlo přesměrování
> * Vytvoření sady škálování virtuálních počítačů s back-end fondem
> * Vytvořit záznam CNAME v doméně

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group).

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu *az network vnet create* vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem [myAGSubnet](/cli/azure/network/vnet). Pak můžete přidat podsíť s názvem *myBackendSubnet* , kterou potřebuje back-end fond serverů, pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet). Pomocí příkazu *az network public-ip create* vytvořte veřejnou IP adresu s názvem [myAGPublicIPAddress](/cli/azure/network/public-ip).

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

K vytvoření aplikační brány s názvem [myAppGateway](/cli/azure/network/application-gateway) použijte příkaz *az network application-gateway create*. Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k již vytvořené podsíti *myAGSubnet* a adrese *myAGPublicIPAddress*. 

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

Naslouchací proces je potřebný k tomu, aby aplikační brána správně směrovala provoz na back-endový fond. V tomto kurzu vytvoříte dva naslouchací procesy pro dvě domény. V tomto příkladu se vytvoří naslouchací procesy pro domény *www\.contoso.com* a *www\.contoso.org*.

Přidejte back-endové naslouchací procesy, které jsou potřeba ke směrování provozu, příkazem [az network application-gateway http-listener create](/cli/azure/network/application-gateway).

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

Přidejte konfiguraci přesměrování, která odesílá provoz ze služby *www\.consoto.org* do naslouchacího procesu pro *webovou\.contoso.com* ve službě Application Gateway pomocí funkce [AZ Network Application-Gateway redirect-config Create](/cli/azure/network/application-gateway/redirect-config).

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

Pravidla se zpracovávají v pořadí, ve kterém jsou vytvořená, a provoz se směruje pomocí prvního pravidla, které odpovídá adrese URL odeslané do aplikační brány. Výchozí pravidlo Basic, které bylo vytvořeno, není v tomto kurzu nutné. V tomto příkladu vytvoříte dvě nová pravidla s názvem *contosoComRule* a *contosoOrgRule* a odstraníte vytvořené výchozí pravidlo.  Pravidla můžete přidat pomocí [AZ Network Application-Gateway Rule Create](/cli/azure/network/application-gateway).

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

V tomto příkladu vytvoříte sadu škálování virtuálního počítače, která podporuje výchozí vytvořený fond back-end. Sada škálování, kterou vytvoříte, má název *myvmss* a obsahuje dvě instance virtuálních počítačů, na kterých instalujete Nginx.

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

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. K získání adresy DNS aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip). Zkopírujte hodnotu *fqdn* z objektu DNESettings a použijte ji jako hodnotu vytvořeného záznamu CNAME. Použití záznamů typu A se nedoporučuje, protože virtuální IP adresa se při restartování aplikační brány může změnit.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Do adresního řádku prohlížeče zadejte název domény. Příklad: `http://www.contoso.com`.

![Testování webu Contoso v aplikační bráně](./media/tutorial-internal-site-redirect-cli/application-gateway-nginxtest.png)

Změňte adresu na jinou doménu, například http://www.contoso.org a měli byste vidět, že přenos byl přesměrován zpět na naslouchací proces pro webovou\.contoso.com.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvoření Application Gateway
> * Přidat naslouchací procesy a pravidlo přesměrování
> * Vytvoření sady škálování virtuálních počítačů s back-end fondem
> * Vytvořit záznam CNAME v doméně

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](./application-gateway-introduction.md)
