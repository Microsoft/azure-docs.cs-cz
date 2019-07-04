---
title: 'Rychlý start: Vytvoření load balanceru úrovně Standard – šablony Azure Resource Manageru'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit load balanceru úrovně Standard s využitím šablony Azure Resource Manageru.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 00dbb29cac30f2a3bbecf71727c79617e24af3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441301"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Rychlý start: Vytvoření load balanceru úrovně Standard pro vyrovnávání zatížení virtuálních počítačů pomocí šablony Azure Resource Manageru

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto rychlém startu se dozvíte, jak nasadit šablonu Azure Resource Manageru, která vytvoří nástroj pro vyrovnávání zatížení Standard pro vyrovnávání zatížení virtuálních počítačů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-standard-load-balancer"></a>Vytvořte nástroj pro vyrovnávání zatížení Standard

Load balancer úrovně standard podporuje pouze standardní veřejnou IP adresu. Když vytvoříte nástroj pro vyrovnávání zatížení Standard, musíte vytvořit také novou standardní veřejnou IP adresu, který je nakonfigurovaný jako front-endu nástroje pro vyrovnávání zatížení Standard.

Mnoho metod můžete použít k vytvoření load balanceru úrovně Standard. V tomto rychlém startu použijete Azure PowerShell k nasazení [šablony Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit.

Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, najdete v článku [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/). Další šablony, které se vztahují k nástroji pro vyrovnávání zatížení Azure najdete v tématu [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Vyberte **vyzkoušet** z následující blok kódu a otevřete Azure Cloud Shell, postupujte podle pokynů pro přihlášení k Azure.

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

   Počkejte, až se zobrazí výzva z konzoly.

1. Vyberte **kopírování** z předchozí blok kódu zkopírujte skript prostředí PowerShell.

1. Klikněte pravým tlačítkem na panelu konzoly prostředí a potom vyberte **vložit**.

1. Zadejte hodnoty.

   Nasazení šablony vytvoří tři zóny dostupnosti. Zóny dostupnosti jsou podporovány pouze v [určitých oblastech](../availability-zones/az-overview.md). Použijte jeden z podporovaných oblastí. Pokud si nejste jistí, zadejte **centralus**.

   Název skupiny prostředků je název projektu s **rg** připojí. Budete potřebovat název skupiny prostředků v další části.

Pokud chcete nasadit šablonu trvá asi 10 minut.

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **skupiny prostředků** v levém podokně.

1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části. Výchozí název skupiny prostředků je název projektu s **rg** připojí.

1. Vyberte nástroje pro vyrovnávání zatížení. Výchozí název je název projektu s **-lb** připojí.

1. Zkopírujte pouze část adresy IP veřejné IP adresy a vložte ji do adresního řádku prohlížeče. Prohlížeč zobrazí výchozí stránka webového serveru Internetové informační služby (IIS).

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Chcete-li zobrazit nástroje pro vyrovnávání zatížení distribuuje provoz mezi všechny tři virtuální počítače, můžete vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je budete potřebovat, odstraňte skupinu prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků. Uděláte to tak, přejděte na portál Azure Portal, vyberte skupinu prostředků, který obsahuje nástroje pro vyrovnávání zatížení a potom vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili nástroj pro vyrovnávání zatížení Standard, připojené virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení, nebyla sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali.

Další informace najdete dál v kurzech pro nástroj pro vyrovnávání zatížení.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 