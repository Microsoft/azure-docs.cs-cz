---
title: Vytvoření aplikační brány s přesměrováním externích přenosů - rozhraní příkazového řádku Azure | Microsoft Docs
description: Postup vytvoření služby application gateway, který přesměruje interní webové přenosy do fondu odpovídající pomocí rozhraní příkazového řádku Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: 3ded6fc0950c82d0aa36da89fdd5635afef7be0b
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Vytvoření služby application gateway s externí přesměrování pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [webové přenosy přesměrování](application-gateway-multi-site-overview.md) při vytváření [Aplikační brána](application-gateway-introduction.md). V tomto kurzu nakonfigurujete naslouchací proces a pravidel, který přesměruje webových přenosů, které dorazí na službu application gateway na externí web.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření pravidla, naslouchací proces a přesměrování
> * Vytvoření služby Application Gateway

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

Vytvořit virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet* pomocí [vytvoření sítě vnet az](/cli/azure/network/vnet#az_net). Vytvoření veřejné IP adresy s názvem *myAGPublicIPAddress* pomocí [vytvoření veřejné sítě az-ip](/cli/azure/public-ip#az_network_public_ip_create). Tyto prostředky se používají k poskytování síťové připojení k službě application gateway a její přidružené prostředky.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Můžete použít [az brány aplikace-vytvořit](/cli/azure/application-gateway#create) k vytvoření aplikační brány s názvem *myAppGateway*. Při vytváření služby application gateway pomocí Azure CLI, je třeba zadat informace o konfiguraci, například kapacitu, sku a nastavení HTTP. Službu application gateway je přiřazena k *myAGSubnet* a *myPublicIPSddress* kterou jste vytvořili. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Přidat konfiguraci přesměrování

Přidat konfiguraci přesměrování, který odesílá provoz z aplikační brány, aby *vyhledávače bing.com* pomocí [vytvoření aplikační brány sítě az přesměrování config](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Přidejte naslouchací proces a pravidlo směrování

K povolení aplikační brány, aby správně směrování provozu je potřeba naslouchací proces. Vytvořte naslouchací proces pomocí [az sítě Aplikační brána-naslouchací proces protokolu http vytvořit](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) s front-endový port vytvořené pomocí [vytvoření aplikační brány sítě az front-endu port](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). Pravidlo je vyžadována pro naslouchací proces vědět, kam má posílat příchozí přenosy. Vytvořte základní pravidlo s názvem *redirectRule* pomocí [vytvořit pravidlo Aplikační brána sítě az](/cli/azure/application-gateway#az_network_application_gateway_rule_create) s konfigurací přesměrování.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Chcete-li získat veřejnou IP adresu brány, aplikace, můžete použít [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

Měli byste vidět *vyhledávače bing.com* zobrazí v prohlížeči.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> * Nastavení sítě
> * Vytvoření pravidla, naslouchací proces a přesměrování
> * Vytvoření služby Application Gateway

> [!div class="nextstepaction"]
> [Další informace o co můžete dělat s aplikační brány](./application-gateway-introduction.md)