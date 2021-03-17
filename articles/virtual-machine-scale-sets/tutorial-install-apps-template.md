---
title: Kurz – instalace aplikací do škálované sady pomocí šablon Azure
description: Zjistěte, jak pomocí šablon Azure Resource Manageru instalovat aplikace do škálovacích sad virtuálních počítačů s využitím rozšíření vlastních skriptů.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d5eba5486e7d26e62379e0112cd4b95322e6dae1
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705230"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Kurz: Instalace aplikací ve škálovacích sadách virtuálních počítačů pomocí šablony Azure
Pokud chcete spouštět aplikace na instancích virtuálních počítačů ve škálovací sadě, musíte nejprve nainstalovat komponenty aplikace a požadované soubory. V předchozím kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače k nasazení instancí virtuálních počítačů. Tato vlastní image zahrnovala ruční instalaci a konfiguraci aplikací. Můžete automatizovat také instalaci aplikací do škálovací sady po nasazení všech instancí virtuálních počítačů nebo aktualizaci aplikace, která je již ve škálovací sadě spuštěná. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.29 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.


## <a name="what-is-the-azure-custom-script-extension"></a>Co je rozšíření vlastních skriptů Azure?
Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z úložiště Azure nebo z GitHubu, případně je za běhu rozšíření najdete na webu Azure Portal.

Rozšíření vlastních skriptů se integruje s šablonami Azure Resource Manageru a je možné ho používat také s Azure CLI, Azure PowerShellem, webem Azure Portal nebo rozhraním REST API. Další informace najdete v tématu [Přehled rozšíření vlastních skriptů](../virtual-machines/extensions/custom-script-linux.md).

Pokud se chcete podívat na rozšíření vlastních skriptů v akci, vytvořte škálovací sadu, která nainstaluje webový server NGINX a vypíše název hostitele instance virtuálního počítače ve škálovací sadě. Následující definice rozšíření vlastních skriptů stáhne ukázkový skript z GitHubu, nainstaluje požadované balíčky a pak vypíše název hostitele instance virtuálního počítače na základní stránce HTML.


## <a name="create-custom-script-extension-definition"></a>Vytvoření definice rozšíření vlastních skriptů
Pokud definujete škálovací sadu virtuálních počítačů pomocí šablony Azure, poskytovatel prostředků *Microsoft.Compute/virtualMachineScaleSets* může obsahovat část věnovanou rozšířením. *extensionsProfile* obsahuje podrobné informace o tom, co se na instance virtuálních počítačů ve škálovací sadě použije. Pokud chcete použít rozšíření vlastních skriptů, zadejte vydavatele *Microsoft.Azure.Extensions* a typ *CustomScript*.

Vlastnost *fileUris* slouží k definici zdrojových instalačních skriptů nebo balíčků. Požadované skripty pro spuštění procesu instalace jsou definované v části *commandToExecute*. Následující příklad definuje ukázkový skript z GitHubu, který nainstaluje a nakonfiguruje webový server NGINX:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Úplný příklad šablony Azure, která nasazuje sadu škálování a rozšíření vlastních skriptů, najdete v tématu [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json) . Tato ukázková šablona se používá v další části.


## <a name="create-a-scale-set"></a>Vytvoření škálovací sady
Pomocí ukázkové šablony teď vytvoříme škálovací sadu a použijeme rozšíření vlastních skriptů. Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte sadu škálování virtuálního počítače pomocí [AZ Deployment Group Create](/cli/azure/deployment/group). Po zobrazení výzvy zadejte své uživatelské jméno a heslo, které se používají jako přihlašovací údaje jednotlivých instancí virtuálních počítačů:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

Každá instance virtuálního počítače ve škálovací sadě stáhne a spustí skript z GitHubu. Ve složitějším příkladu se můžou nainstalovat komponenty a soubory několika aplikací. Pokud dojde k vertikálnímu navýšení kapacity škálovací sady, nové instance virtuálních počítačů automaticky použijí stejnou definici rozšíření vlastních skriptů a nainstalují požadovanou aplikaci.


## <a name="test-your-scale-set"></a>Test škálovací sady
Pokud chcete vidět svůj webový server v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip). Následující příklad získá IP adresu *myScaleSetPublicIP* vytvořenou jako součást škálovací sady:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení do webového prohlížeče. Nástroj pro vyrovnávání zatížení distribuuje provoz do jedné z vašich instancí virtuálních počítačů, jak je znázorněno v následujícím příkladu:

![Základní webová stránka na serveru NGINX](media/tutorial-install-apps-template/running-nginx.png)

Nechte webový prohlížeč otevřený, abyste v dalším kroku viděli aktualizovanou verzi.


## <a name="update-app-deployment"></a>Aktualizace nasazení aplikace
V průběhu životního cyklu škálovací sady možná budete potřebovat nasadit aktualizovanou verzi své aplikace. Díky rozšíření vlastních skriptů můžete přidat odkaz na aktualizovaný skript nasazení a pak znovu použít rozšíření na škálovací sadu. Když byla sada škálování vytvořená v předchozím kroku, *upgradePolicy* byla nastavena na hodnotu *automaticky*. Toto nastavení umožňuje instancím virtuálních počítačů ve škálovací sadě automaticky aktualizovat a používat nejnovější verzi vaší aplikace.

Pokud chcete aktualizovat definici rozšíření vlastních skriptů, upravte svou šablonu tak, aby odkazovala na nový instalační skript. Je potřeba použít nový název souboru, aby rozšíření vlastních skriptů tuto změnu rozpoznalo. Rozšíření vlastních skriptů nezkoumá obsah skriptu, aby rozpoznalo případné změny. Následující definice používá aktualizovaný instalační skript, k jehož názvu je připojený řetězec *_v2*:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Znovu použijte konfiguraci rozšíření vlastních skriptů na instance virtuálních počítačů ve vaší sadě škálování znovu pomocí [AZ Deployment Group Create](/cli/azure/deployment/group). Tato šablona *azuredeployv2.json* slouží k použití aktualizované verze aplikace. V praxi upravíte stávající šablonu *azuredeploy.json* tak, aby odkazovala na aktualizovaný instalační skript, jak je znázorněno v předchozí části. Po zobrazení výzvy zadejte stejné uživatelské jméno a heslo, které jste použili při počátečním vytvoření škálovací sady:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Na všech instancích virtuálních počítačů ve škálovací sadě se ukázková webová stránka automaticky aktualizuje na nejnovější verzi. Pokud chcete zobrazit aktualizovanou verzi, aktualizujte webovou stránku ve svém prohlížeči:

![Aktualizovaná webová stránka na serveru NGINX](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat sadu škálování a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí [AZ Group Delete](/cli/azure/group). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak automaticky instalovat a aktualizovat aplikace ve škálovací sadě pomocí šablon Azure:

> [!div class="checklist"]
> * Automatická instalace aplikací do škálovací sady
> * Použití rozšíření vlastních skriptů Azure
> * Aktualizace spuštěné aplikace ve škálovací sadě

V dalším kurzu se dozvíte, jak škálovací sadu automaticky škálovat.

> [!div class="nextstepaction"]
> [Automatické škálování škálovacích sad](tutorial-autoscale-template.md)
