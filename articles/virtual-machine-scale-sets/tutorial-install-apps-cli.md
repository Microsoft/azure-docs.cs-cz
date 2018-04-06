---
title: Kurz – Instalace aplikací ve škálovací sadě pomocí Azure CLI 2.0 | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI 2.0 instalovat aplikace do škálovacích sad virtuálních počítačů s využitím rozšíření vlastních skriptů.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5db044471c324a3707198ab57ee9b9b6528e121d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Kurz: Instalace aplikací ve škálovacích sadách virtuálních počítačů pomocí Azure CLI 2.0
Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. V předchozím kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače k nasazení instancí virtuálních počítačů. Tato vlastní image zahrnovala ruční instalaci a konfiguraci aplikací. Můžete automatizovat také instalaci aplikací do škálovací sady po nasazení všech instancí virtuálních počítačů nebo aktualizaci aplikace, která je již ve škálovací sadě spuštěná. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.29 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="what-is-the-azure-custom-script-extension"></a>Co je rozšíření vlastních skriptů Azure?
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal.

Rozšíření vlastních skriptů se integruje s šablonami Azure Resource Manageru a je možné ho používat také s Azure CLI 2.0, Azure PowerShellem, webem Azure Portal nebo rozhraním REST API. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/linux/extensions-customscript.md).

Rozšíření vlastních skriptů v Azure CLI použijete tak, že vytvoříte soubor JSON definující, které soubory se mají získat a které příkazy se mají provést. Tyto definice JSON je možné opakovaně používat napříč nasazeními škálovací sady, aby se zajistilo používání konzistentních instalací aplikací.


## <a name="create-custom-script-extension-definition"></a>Vytvoření definice rozšíření vlastních skriptů
Abychom se podívali na rozšíření vlastních skriptů v akci, vytvoříme škálovací sadu, která nainstaluje webový server NGINX a vypíše název hostitele instance virtuálního počítače ve škálovací sadě. Následující definice rozšíření vlastních skriptů stáhne ukázkový skript z GitHubu, nainstaluje požadované balíčky a pak vypíše název hostitele instance virtuálního počítače na základní stránce HTML.

V aktuálním prostředí vytvořte soubor *customConfig.json* a vložte do něj následující konfiguraci. Soubor vytvořte například v Cloud Shellu, pokud nepracujete na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloudConfig.json` ve službě Cloud Shell soubor vytvořte a zobrazte seznam dostupných editorů.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss#create). Následující příklad vytvoří škálovací sadu *myScaleSet* a vygeneruje klíče SSH, pokud ještě neexistují:

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


## <a name="apply-the-custom-script-extension"></a>Použití rozšíření vlastních skriptů
Použijte konfiguraci rozšíření vlastních skriptů na instance virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss extension set](/cli/azure/vmss/extension#set). Následující příklad použije konfiguraci *customConfig.json* na instance virtuálních počítačů ve škálovací sadě *myScaleSet* a ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Každá instance virtuálního počítače ve škálovací sadě stáhne a spustí skript z GitHubu. Ve složitějším příkladu se můžou nainstalovat komponenty a soubory několika aplikací. Pokud dojde k vertikálnímu navýšení kapacity škálovací sady, nové instance virtuálních počítačů automaticky použijí stejnou definici rozšíření vlastních skriptů a nainstalují požadovanou aplikaci.


## <a name="test-your-scale-set"></a>Test škálovací sady
Pokud chcete webovému serveru povolit příjem provozu, vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule#create). Následující příklad vytvoří pravidlo *myLoadBalancerRuleWeb*:

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

Pokud chcete vidět svůj webový server v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip#show). Následující příklad získá IP adresu *myScaleSetLBPublicIP* vytvořenou jako součást škálovací sady:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Základní webová stránka na serveru NGINX](media/tutorial-install-apps-cli/running-nginx.png)

Nechte webový prohlížeč otevřený, abyste v dalším kroku viděli aktualizovanou verzi.


## <a name="update-app-deployment"></a>Aktualizace nasazení aplikace
V průběhu životního cyklu škálovací sady možná budete potřebovat nasadit aktualizovanou verzi své aplikace. Díky rozšíření vlastních skriptů můžete přidat odkaz na aktualizovaný skript nasazení a pak znovu použít rozšíření na škálovací sadu. Při vytvoření škálovací sady v předchozím kroku se hodnota `--upgrade-policy-mode` nastavila na *automatic* (automaticky). Toto nastavení umožňuje instancím virtuálních počítačů ve škálovací sadě automaticky aktualizovat a používat nejnovější verzi vaší aplikace.

V aktuálním prostředí vytvořte soubor *customConfigv2.json* a vložte do něj následující konfiguraci. Tato definice spustí aktualizovanou verzi *v2* instalačního skriptu aplikace:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Opět použijte konfiguraci rozšíření vlastních skriptů na instance virtuálních počítačů ve škálovací sadě pomocí příkazu [az vmss extension set](/cli/azure/vmss/extension#set). Soubor *customConfigv2.json* slouží k použití aktualizované verze aplikace:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Na všech instancích virtuálních počítačů ve škálovací sadě se ukázková webová stránka automaticky aktualizuje na nejnovější verzi. Pokud chcete zobrazit aktualizovanou verzi, aktualizujte webovou stránku ve svém prohlížeči:

![Aktualizovaná webová stránka na serveru NGINX](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group#az_group_delete). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak automaticky instalovat a aktualizovat aplikace ve škálovací sadě pomocí Azure CLI 2.0:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

V dalším kurzu se dozvíte, jak škálovací sadu automaticky škálovat.

> [!div class="nextstepaction"]
> [Automatické škálování škálovacích sad](tutorial-autoscale-cli.md)