---
title: Vytvoření interního Load Balancer – šablona Azure
titleSuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí šablony v Resource Manageru
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 0d7cc4d571ddeb0b57fd4f025b8cbf7b204f61e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456960"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Vytvoření interního nástroje pro vyrovnávání zatížení pomocí šablony

> [!div class="op_single_selector"]
> * [Portál Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [Prostředí](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Nasazení šablony pomocí metody Click to Deploy

Ukázková šablona, která je k dispozici ve veřejném úložišti, používá soubor parametrů obsahující výchozí hodnoty, které se použijí k vygenerování výše popsaného scénáře. Pokud chcete nasadit tuto šablonu pomocí metody Click to Deploy, pokračujte na [tento odkaz](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), klikněte na **Nasadit do Azure**, v případě potřeby nahraďte výchozí hodnoty parametrů, a pokračujte podle pokynů na portálu.

## <a name="deploy-the-template-by-using-powershell"></a>Nasazení šablony pomocí prostředí PowerShell

Pokud chcete nasadit šablonu, kterou jste stáhli, pomocí prostředí PowerShell, použijte následující postup.

1. Pokud jste nikdy nepoužili Azure PowerShell, přečtěte si [článek Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview) a postupovat podle pokynů, jak na to, abyste se mohli přihlásit k Azure a vybrat své předplatné.
2. Stáhněte si soubor parametrů na místní disk.
3. Upravte soubor a uložte ho.
4. Spuštěním rutiny **New-AzResourceGroupDeployment** vytvořte skupinu prostředků pomocí šablony.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Nasazení šablony pomocí rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu pomocí rozhraní příkazového řádku Azure, použijte následující postup.

1. Pokud jste rozhraní příkazového řádku Azure nikdy nepoužívali, přečtěte si téma [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md) a postupujte podle pokynů až do chvíle, kdy vyberete svůj účet a předplatné Azure.
2. [https://shell.azure.com](https://shell.azure.com) V prohlížeči otevřete Cloud Shell. Spuštěním příkazu **azure config mode** přejděte do režimu Resource Manager, jak vidíte níže.

    ```console
    azure config mode arm
    ```

    Toto je očekávaný výstup výše uvedeného příkazu:

        info:    New mode is arm

3. Otevřete soubor parametrů, vyberte jeho obsah a ten uložte do souboru ve svém počítači. V tomto příkladu jsme uložili soubor parametrů do souboru *parameters.json*.
4. Spuštěním příkazu **azure group deployment create** nasaďte nový interní nástroj pro vyrovnávání zatížení pomocí šablony a souborů parametrů, které jste stáhli a upravili v předchozích krocích. Seznam uvedený za výstupem vysvětluje použité parametry.

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>Další kroky

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení pomocí spřažení se zdrojovou IP adresou](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)

Syntaxi a vlastnosti služby Vyrovnávání zatížení v šabloně najdete v tématu [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).