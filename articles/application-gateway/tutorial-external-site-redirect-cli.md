---
title: Vytvoření služby application gateway s přesměrování externího provozu – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
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
ms.date: 01/24/2018
ms.author: victorh
ms.openlocfilehash: 696047479a4d61398e0f978b89a6453b4ea5ea34
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000462"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Vytvoření služby application gateway pomocí externího přesměrování pomocí Azure CLI

Rozhraní příkazového řádku Azure můžete použít ke konfiguraci [přesměrování webového provozu](application-gateway-multi-site-overview.md) při vytváření [služba application gateway](application-gateway-introduction.md). V tomto kurzu nakonfigurujete naslouchací proces a pravidlo, které přesměruje webový provoz přicházející ve službě application gateway na externí web.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit síť
> * Vytvořit pravidlo naslouchací proces a přesměrování
> * Vytvoření služby Application Gateway

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#create).

V následujícím příkladu vytvoříte skupinu prostředků s názvem *myResourceGroupAG* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků 

Pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az-net) vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. Pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az-network_public_ip_create) vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*. Tyto prostředky slouží k síťovému připojení k aplikační bráně a jejím přidruženým prostředkům.

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

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway#create). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazená dříve vytvořené podsíti *myAGSubnet* a adrese *myPublicIPAddress*. 

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

Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto její nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

### <a name="add-the-redirection-configuration"></a>Přidat konfiguraci přesměrování

Přidat konfiguraci přesměrování, který odesílá síťový provoz z aplikační brány pro *bing.com* pomocí [az network application-gateway přesměrování config vytvořit](/cli/azure/network/application-gateway/redirect-config#az-network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Přidat naslouchací proces a pravidlo směrování

Naslouchací proces je nutné povolit aplikační brány pro směrování provozu odpovídajícím způsobem. Vytvořte naslouchací proces, pomocí [az network application-gateway-naslouchací proces protokolu http vytvořit](/cli/azure/network/application-gateway#az-network_application_gateway_http_listener_create) s front-endový port vytvořené pomocí [az network application-gateway front-endu port vytvořit](/cli/azure/network/application-gateway#az-network_application_gateway_frontend_port_create). Pravidlo je vyžadováno pro naslouchací proces vědět, kam má odesílat příchozí provoz. Vytvořte základní pravidlo s názvem *redirectRule* pomocí [az network application-gateway pravidlo vytvořte](/cli/azure/network/application-gateway#az-network_application_gateway_rule_create) s konfigurací přesměrování.

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

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy aplikační brány můžete použít příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

Měli byste vidět *bing.com* se zobrazí v prohlížeči.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> * Nastavit síť
> * Vytvořit pravidlo naslouchací proces a přesměrování
> * Vytvoření služby Application Gateway

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](./application-gateway-introduction.md)