---
title: Povolení firewallu webových aplikací – Azure CLI
description: Přečtěte si, jak omezit webový provoz pomocí brány firewall webových aplikací na aplikační bránu pomocí Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 08/31/2020
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: a33114dce47ca3df87b1c6c774289c8a8efcf835
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94739849"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Povolení firewallu webových aplikací pomocí rozhraní příkazového řádku Azure

Můžete omezit provoz brány Application Gateway pomocí [brány firewall webových aplikací](ag-overview.md) (WAF). WAF používá k ochraně aplikace pravidla [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Tato pravidla zahrnují ochranu před útoky, jako je injektáž SQL, skriptování mezi weby a krádeže relací.

V tomto článku získáte informace o těchto tématech:

 * Nastavit síť
 * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
 * Vytvoření škálovací sady virtuálních počítačů
 * Vytvoření účtu úložiště a konfigurace diagnostiky

![Příklad brány firewall webových aplikací](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Pokud budete chtít, můžete tento postup dokončit pomocí [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.4 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. K vytvoření skupiny prostředků Azure pojmenované *myResourceGroupAG* použijte příkaz [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Virtuální síť a podsítě umožňují síťové připojení k aplikační bráně a přidruženým prostředkům. Vytvořte virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet*. pak vytvořte veřejnou IP adresu s názvem *myAGPublicIPAddress*.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>Vytvoření aplikační brány s Firewallem webových aplikací

K vytvoření aplikační brány s názvem *myAppGateway* použijte příkaz [az network application-gateway create](/cli/azure/network/application-gateway). Při vytváření aplikační brány pomocí Azure CLI zadáte konfigurační údaje, jako je kapacita, skladová položka nebo nastavení HTTP. Aplikační brána je přiřazena k *myAGSubnet* a *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Vytvoření aplikační brány může trvat několik minut. Po vytvoření aplikační brány se zobrazí tyto její nové funkce:

- *appGatewayBackendPool* – aplikační brána musí mít aspoň jeden back-endový fond adres.
- *appGatewayBackendHttpSettings* – určuje, že se ke komunikaci používá port 80 a protokol HTTP.
- *appGatewayHttpListener* – výchozí naslouchací proces přidružený k *appGatewayBackendPool*.
- *appGatewayFrontendIP* – přiřadí adresu *myAGPublicIPAddress* naslouchacímu procesu *appGatewayHttpListener*.
- *rule1* – výchozí pravidlo směrování přidružené k naslouchacímu procesu *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně poskytuje dva servery back-endového fondu. Virtuální počítače jsou ve škálovací sadě přidruženy k podsíti *myBackendSubnet*. K vytvoření škálovací sady použijte příkaz [az vmss create](/cli/azure/vmss#az-vmss-create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvoření účtu úložiště a konfigurace diagnostiky

V tomto článku používá Aplikační brána účet úložiště k ukládání dat pro účely detekce a prevence. K zaznamenávání dat můžete použít také protokoly Azure Monitor nebo centra událostí. 

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

K vytvoření účtu úložiště s názvem *myagstore1* použijte příkaz [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Nakonfigurujte diagnostiku, aby se data zaznamenávala do protokolů ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog. Nahraďte `<subscriptionId>` identifikátorem předplatného a pak nakonfigurujte diagnostiku pomocí [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

K získání veřejné IP adresy aplikační brány použijte příkaz [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testování základní adresy URL v aplikační bráně](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete skupinu prostředků, aplikační bránu a další související prostředky potřebovat, odeberte je.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Další kroky

[Přizpůsobení pravidel firewallu webových aplikací](application-gateway-customize-waf-rules-portal.md)
