---
title: 'Úvodní příručka: Vytvoření nástrojů pro vyrovnávání zatížení – šablona Azure'
titleSuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit nástroje pro vyrovnávání zatížení pomocí šablony Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 1f681a9a8d8547589873cff055d9f59973c27355
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78295781"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Úvodní příručka: Vytvoření nástroje pro vyrovnávání zatížení pro vyrovnávání zatížení virtuálních počítačů pomocí šablony Azure Resource Manager

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. Tento rychlý start ukazuje, jak nasadit šablonu Azure Resource Manager, která vytvoří standardní vyrovnávání zatížení pro vyrovnávání zatížení virtuálních počítačů. Použití šablony Správce prostředků provede méně kroků ve srovnání s jinými metodami nasazení.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vyrovnávání zatížení a veřejné IP sloky musí odpovídat. Při vytváření standardního vykladače zatížení je nutné vytvořit také novou standardní veřejnou IP adresu, která je nakonfigurována jako front-end pro standardní vyrovnávání zatížení. Pokud chcete vytvořit základní vyrovnávání zatížení, použijte [tuto šablonu](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Společnost Microsoft doporučuje používat standardní skladovou položku pro produkční úlohy.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je z [azure quickstart šablony](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-150" highlight="58-122":::

V šabloně bylo definováno více prostředků Azure:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): pro nástroj pro vyrovnávání zatížení a pro každý ze tří virtuálních počítačů.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 z nich)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 z nich)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 z nich): slouží ke konfiguraci služby IIS a webových stránek

Další šablony, které souvisejí s Azure Load Balancer, najdete v [tématu Azure QuickStart Šablony](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte **Vyzkoušet** z následujícího bloku kódu otevřít Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení do Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Počkejte, až se zobrazí výzva z konzole.

1. Vyberte **Kopírovat** z předchozího bloku kódu, chcete-li zkopírovat skript PowerShellu.

1. Klepněte pravým tlačítkem myši na podokno konzoly prostředí a vyberte příkaz **Vložit**.

1. Zadejte hodnoty.

   Nasazení šablony vytvoří tři zóny dostupnosti. Zóny dostupnosti jsou podporovány pouze v [určitých oblastech](../availability-zones/az-overview.md). Použijte jednu z podporovaných oblastí. Pokud si nejste jisti, zadejte **centralus**.

   Název skupiny zdrojů je název projektu s **rg** připojen. V další části potřebujete název skupiny prostředků.

Nasazení šablony trvá přibližně 10 minut. Po dokončení je výstup podobný:

![Výstup nasazení PowerShellu šablonou Správce prostředků nástroje Azure Standard Pro vyrovnávání zatížení](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell se používá k nasazení šablony. Kromě Azure PowerShellu můžete taky použít portál Azure, rozhraní API Azure a rozhraní REST API. Další metody nasazení najdete v [tématu Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků.**

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny zdrojů je název projektu s **připojeným rg.**

1. Vyberte vykladač zatížení. Jeho výchozí název je název projektu s **-lb** připojen.

1. Zkopírujte pouze část veřejné IP adresy IP adresy IP a vložte ji do adresního řádku prohlížeče.

   ![Veřejná IP adresa šablony správce prostředků standardního nástroje pro vyrovnávání zatížení Azure](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Prohlížeč zobrazí výchozí stránku webového serveru Internetové informační služby (IIS).

   ![Webový server služby IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Chcete-li zobrazit vyrovnávání zatížení distribuovat provoz napříč všemi třemi virtuálními počítači, můžete vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je již nepotřebujete, odstraňte skupinu prostředků, vyrovnávání zatížení a všechny související prostředky. Chcete-li tak učinit, přejděte na portál Azure, vyberte skupinu prostředků, která obsahuje vyrovnávání zatížení a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili standardní systém vyrovnávání zatížení, připojili k němu virtuální počítače, nakonfigurovali pravidlo provozu vyvažovače zatížení, provedli sondu stavu a poté otestovali systém vyrovnávání zatížení.

Další informace najdete v kurzech pro vykladač zatížení.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
