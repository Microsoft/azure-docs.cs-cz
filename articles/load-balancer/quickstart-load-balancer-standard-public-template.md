---
title: 'Rychlý Start: vytvoření standardního nástroje pro vyrovnávání zatížení – šablona Azure'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit standardní nástroj pro vyrovnávání zatížení pomocí šablony Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 884ba1106e7b68fefaf91ee4e25cacd05bf96fc0
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048751"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Rychlý Start: vytvoření standardního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí šablony Azure Resource Manager

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto rychlém startu se dozvíte, jak nasadit šablonu Azure Resource Manager, která pro vyrovnávání zatížení virtuálních počítačů vytvoří standardní nástroj pro vyrovnávání zatížení. Použití šablony Správce prostředků přijímá méně kroků v porovnání s jinými metodami nasazení.

[Šablona správce prostředků](../azure-resource-manager/template-deployment-overview.md) je soubor JavaScript Object Notation (JSON), který definuje infrastrukturu a konfiguraci projektu. Šablona používá deklarativní syntaxi, která umožňuje určit, co máte v úmyslu nasadit bez nutnosti napsat sekvenci programovacích příkazů k jeho vytvoření. Pokud chcete získat další informace o vývoji šablon Správce prostředků, přečtěte si téma [dokumentace správce prostředků](/azure/azure-resource-manager/) a [odkaz na šablonu](/azure/templates/microsoft.network/loadbalancers).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Standard Load Balancer podporuje jenom standardní veřejnou IP adresu. Když vytvoříte standardní nástroj pro vyrovnávání zatížení, musíte také vytvořit novou veřejnou IP adresu, která je nakonfigurovaná jako front-end pro nástroj pro vyrovnávání zatížení úrovně Standard.

Šablona použitá v tomto rychlém startu je [šablonou pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

V šabloně bylo definováno více prostředků Azure:

- **Microsoft. Network/loadBalancers**
- **Microsoft. Network/publicIPAddresses**: pro nástroj pro vyrovnávání zatížení.
- **Microsoft.Network/networkSecurityGroups**
- **Microsoft.Network/virtualNetworks**
- **Microsoft. COMPUTE/virutalMachines** (3 z nich)
- **Microsoft. Network/publicIPAddresses** (3 z nich): pro každý ze tří virtuálních počítačů.
- **Microsoft. Network/networkInterfaces** (3 z nich)
- **Microsoft. COMPUTE/VirtualMachine/Extensions** (3 z nich): použijte ke konfiguraci služby IIS a webových stránek

Další šablony, které souvisejí s Azure Load Balancer, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Vyberte **vyzkoušet** z následujícího bloku kódu a otevřete Azure Cloud Shell a pak postupujte podle pokynů pro přihlášení k Azure.

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

   Počkejte, dokud se nezobrazí výzva z konzoly.

1. Pro zkopírování skriptu PowerShellu vyberte **Kopírovat** z předchozího bloku kódu.

1. Klikněte pravým tlačítkem na Podokno konzole prostředí a pak vyberte **Vložit**.

1. Zadejte hodnoty.

   Nasazení šablony vytvoří tři zóny dostupnosti. Zóny dostupnosti se podporují jenom v [určitých oblastech](../availability-zones/az-overview.md). Použijte jednu z podporovaných oblastí. Pokud si nejste jistí, zadejte **centralus**.

   Název skupiny prostředků je název projektu s připojeným **RG** . Název skupiny prostředků budete potřebovat v další části.

Nasazení šablony trvá přibližně 10 minut. Výstup je po dokončení podobný tomuto:

![Výstup nasazení PowerShellu pro Azure Standard Load Balancer Správce prostředků](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell slouží k nasazení šablony. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. Přihlásit se na [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s připojeným **RG** .

1. Vyberte nástroj pro vyrovnávání zatížení. Výchozím názvem je název projektu s připojenou hodnotou **----9,1** .

1. Zkopírujte pouze část veřejné IP adresy IP adresy a vložte ji do adresního řádku prohlížeče.

   ![Azure Load Balancer úrovně Standard – veřejná IP adresa šablony Správce prostředků](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Prohlížeč zobrazí výchozí stránku webového serveru Internetová informační služba (IIS).

   ![Webový server služby IIS](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to tak, že přejdete na Azure Portal, vyberete skupinu prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a pak vyberete **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili standardní nástroj pro vyrovnávání zatížení, připojili jste k němu virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení, provedli sondu stavu a pak otestovali Nástroj pro vyrovnávání zatížení.

Pokud se chcete dozvědět víc, přejděte k kurzům Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
