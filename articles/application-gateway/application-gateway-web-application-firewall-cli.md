---
title: Vytvoření služby application gateway s brány firewall webových aplikací – rozhraní příkazového řádku Azure | Microsoft Docs
description: Postup vytvoření služby application gateway pomocí brány firewall webových aplikací pomocí rozhraní příkazového řádku Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 87125b68c81af07d0ecd9693fdf7e2dc00a93324
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-cli"></a>Vytvoření služby application gateway pomocí brány firewall webových aplikací pomocí rozhraní příkazového řádku Azure

Rozhraní příkazového řádku Azure můžete použít k vytvoření [Aplikační brána](application-gateway-introduction.md) s [brány firewall webových aplikací](application-gateway-web-application-firewall-overview.md) (firewall webových aplikací), který používá [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Firewall webových aplikací používá [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) pravidla k ochraně vaší aplikace. Mezi tato pravidla patří ochranu před útoky, například typu Injektáž SQL a útoky skriptování mezi weby a hijacks relace. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby application gateway s povolen firewall webových aplikací
> * Vytvoření škálovací sady virtuálních počítačů
> * Vytvoření účtu úložiště a konfiguraci diagnostiky

![Příklad brány firewall webových aplikací](./media/application-gateway-web-application-firewall-cli/scenario-waf.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků Azure s názvem *myResourceGroupAG* s [vytvořit skupinu az](/cli/azure/group#az_group_create).

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Virtuální sítě a podsítě se používají k poskytování síťové připojení k službě application gateway a její přidružené prostředky. Vytvořit virtuální síť s názvem *myVNet* a podsíť s názvem *myAGSubnet* s [vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create) a [az sítě vnet podsíť vytváření](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Vytvoření veřejné IP adresy s názvem *myAGPublicIPAddress* s [vytvoření veřejné sítě az-ip](/cli/azure/network/public-ip#az_network_public_ip_create).

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
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Vytvoření služby application gateway s firewall webových aplikací

Můžete použít [az brány aplikace-vytvořit](/cli/azure/application-gateway#az_application_gateway_create) k vytvoření aplikační brány s názvem *myAppGateway*. Při vytváření služby application gateway pomocí Azure CLI, je třeba zadat informace o konfiguraci, například kapacitu, sku a nastavení HTTP. Službu application gateway je přiřazena k *myAGSubnet* a *myPublicIPSddress* kterou jste vytvořili.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
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

Ho může trvat několik minut pro službu application gateway, který se má vytvořit. Po vytvoření služby application gateway se zobrazí tyto nové funkce:

- *appGatewayBackendPool* -aplikační brány musí mít alespoň jeden fond adres back-end.
- *appGatewayBackendHttpSettings* -Určuje, že port 80 a protokol HTTP se používá pro komunikaci.
- *appGatewayHttpListener* -přidružený naslouchací proces výchozí *appGatewayBackendPool*.
- *appGatewayFrontendIP* -přiřadí *myAGPublicIPAddress* k *appGatewayHttpListener*.
- *rule1 New* – výchozí směrování pravidlo, které souvisí s *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která poskytuje dva servery pro fond back-end v aplikační brány. Jsou virtuální počítače ve škálovací sadě přidružené *myBackendSubnet* podsítě. Chcete-li vytvořit měřítko nastavte, můžete použít [vytvořit az vmss](/cli/azure/vmss#az_vmss_create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/vhorne/samplescripts/master/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvoření účtu úložiště a konfiguraci diagnostiky

V tomto kurzu se Aplikační brána používá účet úložiště k ukládání dat za účelem odhalování a prevence. Analýzy protokolů nebo Centrum událostí můžete použít také k zaznamenání dat. 

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořit účet úložiště s názvem *myagstore1* s [vytvořit účet úložiště az](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Konfigurace diagnostiky k záznamu dat do ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog protokoly. SUBSTITUTE `<subscriptionId>` s ID vašeho předplatného a pak proveďte konfiguraci diagnostiky s [vytvořit az monitorování diagnostiky nastavení](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)
storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)
az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testování služby application gateway

Chcete-li získat veřejnou IP adresu brány, aplikace, použijte [az sítě veřejné ip zobrazit](/cli/azure/network/public-ip#az_network_public_ip_show). Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Otestovat základní adresu URL v aplikační brány](./media/application-gateway-web-application-firewall-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavení sítě
> * Vytvoření služby application gateway s povolen firewall webových aplikací
> * Vytvoření škálovací sady virtuálních počítačů
> * Vytvoření účtu úložiště a konfiguraci diagnostiky

Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.