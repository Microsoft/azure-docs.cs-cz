---
title: 'Rychlý start: Vytvořit Standard pro vyrovnávání zatížení – šablona Azure Resource Manageru'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit standardní nástroj pro vyrovnávání zatížení pomocí šablony Azure Resource Manageru.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a95bedc8b2b395f856512ec49bae630666fe36da
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272885"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Rychlý start: Vytvoření Load balanceru úrovně Standard pro vyrovnávání zatížení virtuálních počítačů pomocí šablony Azure Resource Manageru

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto rychlém startu se dozvíte, jak nasadit šablonu Azure Resource Manageru k vytvoření load balanceru úrovně standard pro vyrovnávat zatížení virtuálních počítačů (VM).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Load balanceru úrovně Standard musíte vytvořit také novou standardní veřejnou IP adresu, který je nakonfigurovaný jako front-endu pro Load balancer úrovně Standard. Existuje mnoho metod, které slouží k vytvoření load balanceru úrovně standard. V tomto rychlém startu použijete Azure PowerShell k nasazení [šablony Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Šablony Resource Manageru jsou soubory JSON, které definují, jaké prostředky je pro řešení potřeba nasadit. Abyste porozuměli konceptům spojeným s nasazením a správou řešení Azure, najdete v článku [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/). Další související šablony nástroje pro vyrovnávání zatížení Azure najdete v tématu [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Vyberte **vyzkoušet** z následující blok kódu a otevřete Azure Cloud shell, postupujte podle pokynů pro přihlášení k Azure.

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
2. Vyberte **kopírování** z předchozí blok kódu zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na panelu konzoly prostředí a potom vyberte **vložit**.
4. Zadejte hodnoty.

   Nasazení šablony vytvoří tři zóny dostupnosti.  Zóny dostupnosti jsou podporovány pouze v [určitých oblastech](../availability-zones/az-overview.md). Použijte jeden z podporovaných oblastí. Pokud si nejste jisti, zadejte **centralus**.

   Název skupiny prostředků je název projektu s **rg** připojí. Budete potřebovat název skupiny prostředků v další části.

Pokud chcete nasadit šablonu trvá asi 10 minut.

## <a name="test-the-load-balancer"></a>Otestování nástroje pro vyrovnávání zatížení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **skupiny prostředků** v levém podokně.
1. Vyberte skupinu prostředků, kterou jste vytvořili v předchozí části.  Výchozí název skupiny prostředků je název projektu s **rg** připojí.
1. Vyberte nástroje pro vyrovnávání zatížení.  Existuje pouze jedna služba Vyrovnávání zatížení. Výchozí název je název projektu s **-lb** připojí.
1. Zkopírujte veřejnou IP adresu (pouze část IP adresy) a vložte ji do adresního řádku prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Chcete-li zobrazit nástroje pro vyrovnávání zatížení distribuuje provoz mezi všechny tři virtuální počítače, můžete můžete vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků, který obsahuje nástroje pro vyrovnávání zatížení na webu Azure Portal a potom vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Load balanceru úrovně Standard, připojené virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
