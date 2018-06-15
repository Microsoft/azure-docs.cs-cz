---
title: Vytvoření veřejného nástroje pro vyrovnávání zatížení – šablona Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit veřejný nástroj pro vyrovnávání zatížení v Resource Manageru pomocí šablony
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 8452d3a6e165bbcd6007d9dc2261e458746b475a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
ms.locfileid: "30262955"
---
# <a name="creating-a-public-load-balancer-using-a-template"></a>Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí šablony

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-internet-arm-template.md)



[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Nasazení šablony pomocí metody Click to Deploy

Ukázková šablona, která je k dispozici ve veřejném úložišti, používá soubor parametrů obsahující výchozí hodnoty, které se použijí k vygenerování výše popsaného scénáře. Pokud chcete nasadit tuto šablonu pomocí metody Click to Deploy, pokračujte na [tento odkaz](http://go.microsoft.com/fwlink/?LinkId=544801), klikněte na **Nasadit do Azure**, v případě potřeby nahraďte výchozí hodnoty parametrů, a pokračujte podle pokynů na portálu.

## <a name="deploy-the-template-by-using-powershell"></a>Nasazení šablony pomocí prostředí PowerShell

Pokud chcete nasadit šablonu, kterou jste stáhli, pomocí prostředí PowerShell, použijte následující postup.

1. Pokud jste prostředí Azure PowerShell nikdy nepoužívali, přejděte na téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview) a proveďte všechny pokyny, abyste se mohli přihlásit k Azure a vybrat své předplatné.
2. Spusťte rutinu **New-AzureRmResourceGroupDeployment**, která pomocí šablony vytvoří skupinu prostředků.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Nasazení šablony pomocí rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu pomocí rozhraní příkazového řádku Azure, použijte následující postup.

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přejděte na téma [Instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy můžete vybrat svůj účet a předplatné Azure.
2. Spuštěním příkazu **azure config mode** přejděte do režimu Resource Manager, jak vidíte níže.

    ```azurecli
    azure config mode arm
    ```

    Toto je očekávaný výstup výše uvedeného příkazu:

        info:    New mode is arm

3. V prohlížeči přejděte na [Šablonu pro rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), zkopírujte obsah souboru JSON a vložte jej do nového souboru v počítači. V tomto scénáři byste zkopírovali níže uvedené hodnoty do souboru s názvem **c:\lb\azuredeploy.parameters.json**.
4. Spuštěním rutiny **azure group deployment create** nasaďte nový nástroj pro vyrovnávání zatížení pomocí šablony a souborů parametrů, které jste stáhli a upravili v předchozích krocích. Seznam uvedený za výstupem vysvětluje použité parametry.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Další kroky

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
