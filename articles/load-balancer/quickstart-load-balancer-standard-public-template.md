---
title: 'Rychlý start: Vytvoření standardního nástroje pro vyrovnávání zatížení – šablona Azure Resource Manager'
titlesuffix: Azure Load Balancer
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
ms.date: 06/19/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e1a04cad7fe5c9c95397ffad2faa80c7d657d0f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273800"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Rychlý start: Vytvoření standardního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí šablony Azure Resource Manager

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto rychlém startu se dozvíte, jak nasadit šablonu Azure Resource Manager, která pro vyrovnávání zatížení virtuálních počítačů vytvoří standardní nástroj pro vyrovnávání zatížení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Standard Load Balancer podporuje jenom standardní veřejnou IP adresu. Když vytvoříte standardní nástroj pro vyrovnávání zatížení, musíte také vytvořit novou veřejnou IP adresu, která je nakonfigurovaná jako front-end pro nástroj pro vyrovnávání zatížení úrovně Standard.

K vytvoření standardního nástroje pro vyrovnávání zatížení můžete použít spoustu metod. V tomto rychlém startu použijete Azure PowerShell k nasazení [Správce prostředků šablony](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit.

Další informace o konceptech spojených s nasazením a správou řešení Azure najdete v [dokumentaci k Azure Resource Manager](/azure/azure-resource-manager/). Další šablony, které souvisejí s Azure Load Balancer, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

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

Nasazení šablony trvá přibližně 10 minut.

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně vyberte **skupiny prostředků** .

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s připojeným **RG** .

1. Vyberte nástroj pro vyrovnávání zatížení. Výchozím názvem je název projektu s připojenou hodnotou **----9,1** .

1. Zkopírujte pouze část veřejné IP adresy IP adresy a vložte ji do adresního řádku prohlížeče. Prohlížeč zobrazí výchozí stránku webového serveru Internetová informační služba (IIS).

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástroje pro vyrovnávání zatížení napříč všemi třemi virtuálními počítači, můžete vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to tak, že přejdete na Azure Portal, vyberete skupinu prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a pak vyberete **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili standardní nástroj pro vyrovnávání zatížení, připojili jste k němu virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení, provedli sondu stavu a pak otestovali Nástroj pro vyrovnávání zatížení.

Pokud se chcete dozvědět víc, přejděte k kurzům Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 