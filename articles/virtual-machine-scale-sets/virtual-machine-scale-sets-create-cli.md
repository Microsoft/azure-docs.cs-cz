---
title: "Vytvoření škálovací sady virtuálních počítačů pomocí Azure CLI 2.0 | Dokumentace Microsoftu"
description: "Zjistěte, jak rychle vytvořit škálovací sadu virtuálních počítačů pomocí Azure PowerShellu."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 8794ea7d998293e7ea88ba780f67ef8a021f2298
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Vytvoření škálovací sady virtuálních počítačů pomocí Azure CLI 2.0
Škálovací sada virtuálních počítačů umožňuje nasadit a spravovat sadu identických virtuálních počítačů s automatickým škálováním. Všechny virtuální počítače ve škálovací sadě můžete škálovat ručně nebo můžete definovat pravidla pro automatické škálování podle využití prostředků, například podle požadavků na CPU a paměť nebo podle provozu. V tomto článku Rychlý start vytvoříte škálovací sadu virtuálních počítačů pomocí Azure CLI 2.0. Škálovací sadu můžete vytvořit také pomocí [Azure PowerShellu](virtual-machine-scale-sets-create-powershell.md) nebo webu [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI ve verzi 2.0.20 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss#az_vmss_create). Následující příklad vytvoří škálovací sadu *myScaleSet* a vygeneruje klíče SSH, pokud ještě neexistují:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="install-nginx-webserver"></a>Instalace webového serveru NGINX
Pokud chcete svou škálovací sadu otestovat, pomocí rozšíření vlastních skriptů stáhněte a spusťte skript, který na instance virtuálních počítačů nainstaluje server NGINX. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/linux/extensions-customscript.md).

Následujícím způsobem použijte rozšíření vlastních skriptů, které nainstaluje server NGINX:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Povolení webového provozu
Pokud chcete webovému serveru povolit příjem provozu, vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create). Následující příklad vytvoří pravidlo *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Otestování webového serveru
Pokud chcete vidět svůj webový server v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Následující příklad získá IP adresu *myScaleSetLBPublicIP* vytvořenou jako součást škálovací sady:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Výchozí webová stránka na serveru NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, škálovací sady a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az_group_delete), jak je znázorněno níže:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Další kroky
V tomto článku Rychlý start jste vytvořili základní škálovací sadu a pomocí rozšíření vlastních skriptů jste na instance virtuálních počítačů nainstalovali základní webový server NGINX. Pokud chcete zlepšit škálovatelnost a automatizaci, rozšiřte svou škálovací sadu pomocí některého z těchto článků s postupy:

- [Nasazení aplikace do škálovací sady virtuálních počítačů](virtual-machine-scale-sets-deploy-app.md)
- Automatické škálování pomocí [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShellu](virtual-machine-scale-sets-autoscale-powershell.md) nebo webu [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Použití automatických upgradů operačního systému pro instance virtuálních počítačů ve škálovací sadě](virtual-machine-scale-sets-automatic-upgrade.md)