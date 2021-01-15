---
title: Vytvoření partnerského vztahu virtuální sítě Azure – různé modely nasazení – různá předplatná
titlesuffix: Azure Virtual Network
description: Naučte se, jak vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení Azure, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 29a64931ac92eacf8948ae067118b6b25198c85d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223461"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Vytvoření partnerského vztahu virtuálních sítí – různé modely nasazení a odběry

V tomto kurzu se naučíte vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Virtuální sítě existují v různých předplatných. Partnerský vztah dvou virtuálních sítí umožňuje prostředkům v různých virtuálních sítích vzájemně komunikovat se stejnou šířkou pásma a latencí, jako kdyby byly prostředky ve stejné virtuální síti. Přečtěte si další informace o [partnerském vztahu virtuálních sítí](virtual-network-peering-overview.md).

Postup vytvoření partnerského vztahu virtuální sítě se liší v závislosti na tom, jestli jsou virtuální sítě ve stejném nebo jiném předplatném, a na [modelu nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , prostřednictvím kterého se virtuální sítě vytvářejí. Přečtěte si, jak vytvořit partnerský vztah virtuální sítě v jiných scénářích kliknutím na scénář z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manager](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Obě Resource Manager](create-peering-different-subscriptions.md) |Různé|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models.md) |Stejné|

Partnerský vztah virtuální sítě nelze vytvořit mezi dvěma virtuálními sítěmi nasazenými prostřednictvím modelu nasazení Classic. Tento kurz používá virtuální sítě, které existují ve stejné oblasti. Tento kurz je virtuálními sítěmi ve stejné oblasti. Můžete také vytvořit partnerský vztah virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region). Doporučujeme, abyste se seznámili s [požadavky a omezeními partnerských vztahů](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením partnerského vztahu virtuálních sítí.

Při vytváření partnerského vztahu virtuální sítě mezi virtuálními sítěmi, které existují v různých předplatných, mohou být odběry přidruženy ke stejnému Azure Active Directory tenantovi. Pokud ještě nemáte klienta Azure Active Directory, můžete [ho rychle vytvořit](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant).

K vytvoření partnerského vztahu virtuálních sítí můžete použít [Azure Portal](#portal), [rozhraní příkazového řádku](#cli) Azure (CLI) nebo [prostředí Azure PowerShell](#powershell) . Klikněte na některý z předchozích odkazů nástrojů a přejděte přímo k postupu při vytváření partnerského vztahu virtuálních sítí pomocí vašeho nástroje podle vlastního výběru.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Vytvoření partnerských vztahů – Azure Portal

V tomto kurzu se pro každé předplatné používá jiný účet. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z portálu a přeskočit kroky pro přiřazení dalších uživatelských oprávnění k virtuálním sítím.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako Usera. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
2. Klikněte na **+ Nový**, pak na **sítě** a pak na **virtuální síť**.
3. V okně **vytvořit virtuální síť** zadejte nebo vyberte hodnoty pro následující nastavení a pak klikněte na **vytvořit**:
    - **Název**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: vyberte předplatné A.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte *myResourceGroupA* .
    - **Umístění**: *východní USA*
4. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetA*. Po zobrazení ve výsledcích hledání klikněte na **myVnetA** . Zobrazí se okno pro virtuální síť **myVnetA** .
5. V zobrazeném okně **myVnetA** klikněte na **řízení přístupu (IAM)** ze svislého seznamu možností na levé straně okna.
6. V okně **myVnetA – řízení přístupu (IAM)** , které se zobrazí, klikněte na **+ Přidat přiřazení role**.
7. V okně **Přidat přiřazení role** , které se zobrazí, vyberte v poli **role** možnost **Přispěvatel sítě** .
8. V poli **Vybrat** vyberte UserB nebo zadejte e-mailovou adresu UserB, kterou chcete vyhledat. Seznam zobrazených uživatelů je ze stejného klienta Azure Active Directory jako virtuální síť, pro kterou nastavujete partnerský vztah. Po zobrazení v seznamu klikněte na UserB.
9. Klikněte na **Uložit**.
10. Odhlaste se z portálu jako UserA a pak se přihlaste jako UserB.
11. Klikněte na **+ Nový**, do pole **Hledat na Marketplace** zadejte *virtuální síť* a potom ve výsledcích hledání klikněte na **virtuální síť** .
12. V zobrazeném okně **Virtual Network** v poli **Vybrat model nasazení** vyberte **Classic** a pak klikněte na **vytvořit**.
13. Do zobrazeného pole vytvořit virtuální síť (Classic) zadejte následující hodnoty:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: vyberte předplatné B.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte *myResourceGroupB* .
    - **Umístění**: *východní USA*

14. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetB*. Po zobrazení ve výsledcích hledání klikněte na **myVnetB** . Zobrazí se okno pro virtuální síť **myVnetB** .
15. V zobrazeném okně **myVnetB** klikněte na **vlastnosti** ze svislého seznamu možností na levé straně okna. Zkopírujte **ID prostředku**, které se používá v pozdějším kroku. ID prostředku je podobné jako v následujícím příkladu: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Proveďte kroky 5-9 pro myVnetB, a to zadáním **Usera** v kroku 8.
17. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
18. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetA*. Po zobrazení ve výsledcích hledání klikněte na **myVnetA** . Zobrazí se okno pro virtuální síť **myVnet** .
19. Klikněte na **myVnetA**.
20. V zobrazeném okně **myVnetA** klikněte na **partnerské vztahy** ze svislého seznamu možností na levé straně okna.
21. V okně **myVnetA – partnerské vztahy** , které se zobrazily, klikněte na **+ Přidat** .
22. V zobrazeném okně **Přidat partnerský vztah** zadejte nebo vyberte následující možnosti a pak klikněte na **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Model nasazení virtuální sítě**: vyberte **klasický**.
     - **Znám moje ID prostředku**: zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku myVnetB z kroku 15.
     - **Povolení přístupu k virtuální síti:** Ujistěte se, že je vybraná možnost **povoleno** .
    V tomto kurzu se žádná další nastavení nepoužívají. Pokud se chcete dozvědět o všech nastaveních partnerských vztahů, přečtěte si téma [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknutí na **OK** v předchozím kroku se zavře okno **Přidat partnerský vztah** a znovu se zobrazí okno **myVnetA-peering** . Po několika sekundách se partnerský vztah, který jste vytvořili, zobrazí v okně. **Připojeno** se zobrazí ve sloupci **stav partnerského vztahu** pro partnerský vztah **myVnetAToMyVnetB** , který jste vytvořili. Partnerský vztah se teď navázal. Není nutné, aby byla virtuální síť (Classic) partnerského vztahu k virtuální síti (Správce prostředků).

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nebudou schopné překládat názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
25. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-portal) v tomto článku.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Vytvoření partnerského vztahu – Azure CLI

V tomto kurzu se pro každé předplatné používá jiný účet. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech s uživatelskými jmény, která používáte pro Usera a UserB. Pomocí klasického rozhraní příkazového řádku Azure a rozhraní příkazového řádku Azure CLI proveďte následující kroky. Kroky z Azure Cloud Shell můžete dokončit tak, že jednoduše vyberete tlačítko **vyzkoušet** v některém z následujících kroků nebo nainstalujete rozhraní příkazového [řádku Classic](/cli/azure/install-classic-cli) a [CLI](/cli/azure/install-azure-cli) a spustíte příkazy na místním počítači.

1. Pokud používáte Cloud Shell, přeskočte na krok 2, protože Cloud Shell vás automaticky přihlásí k Azure. Otevřete relaci příkazu a přihlaste se k Azure pomocí `azure login` příkazu.
2. Spusťte klasický příkaz CLI v režimu správy služby zadáním `azure config mode asm` příkazu.
3. Chcete-li vytvořit virtuální síť (Classic), zadejte následující klasický příkaz rozhraní příkazového řádku:

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Zbývající kroky musí být dokončeny pomocí prostředí bash s rozhraním příkazového řádku Azure CLI (ne klasické rozhraní příkazového řádku).
5. Zkopírujte následující skript do textového editoru na svém počítači. Nahraďte `<SubscriptionB-Id>` ID vašeho předplatného. Pokud své ID předplatného neznáte, zadejte `az account show` příkaz. Hodnota **ID** ve výstupu je vaše ID vašeho předplatného. zkopírujte upravený skript, vložte ho do relace CLI a pak stiskněte klávesu `Enter` .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Při vytváření virtuální sítě (Classic) v kroku 4 vytvořila Azure virtuální síť ve skupině prostředků *výchozí-síť* .
6. Protokoluje se UserB z Azure a přihlaste se jako UserA v rozhraní příkazového řádku.
7. Vytvořte skupinu prostředků a virtuální síť (Správce prostředků). Zkopírujte následující skript, vložte ho do relace CLI a pak stiskněte klávesu `Enter` .

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Vytvořte partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Zkopírujte následující skript do textového editoru na svém počítači. Nahraďte `<SubscriptionB-id>` ID vašeho předplatného. Pokud své ID předplatného neznáte, zadejte `az account show` příkaz. Hodnota **ID** ve výstupu je vaše ID vašeho předplatného. Azure vytvořil virtuální síť (Classic), kterou jste vytvořili v kroku 4, ve skupině prostředků s názvem *výchozí-síť*. Vložte upravený skript do relace rozhraní příkazového řádku a potom stiskněte klávesu `Enter` .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Po spuštění skriptu zkontrolujte partnerský vztah pro virtuální síť (Správce prostředků). Zkopírujte následující skript a vložte ho do relace CLI:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    Výstup se zobrazí ve sloupci **PeeringState** ( **připojeno** ).

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nebudou schopné překládat názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
11. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-cli) v tomto článku.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

V tomto kurzu se pro každé předplatné používá jiný účet. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech s uživatelskými jmény, která používáte pro Usera a UserB. 

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [AZ](https://www.powershellgallery.com/packages/Az) Modules. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci PowerShellu.
3. V PowerShellu se přihlaste k předplatnému UserB jako UserB zadáním `Add-AzureAccount` příkazu. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
4. Pokud chcete vytvořit virtuální síť (Classic) pomocí PowerShellu, musíte vytvořit novou nebo upravit existující soubor konfigurace sítě. Naučte se [exportovat, aktualizovat a importovat síťové konfigurační soubory](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Soubor by měl obsahovat následující element **VirtualNetworkSite** pro virtuální síť, která se používá v tomto kurzu:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Import změněného konfiguračního souboru sítě může způsobit změny ve stávajících virtuálních sítích (Classic) v rámci vašeho předplatného. Ujistěte se, že jste přidali jenom předchozí virtuální síť a že v rámci předplatného neměníte ani neodebíráte žádné existující virtuální sítě. 

5. Přihlaste se k předplatnému UserB jako UserB, abyste mohli použít příkazy Správce prostředků zadáním `Connect-AzAccount` příkazu.
6. Přiřaďte UserA oprávnění k virtuální síti B. Zkopírujte následující skript do textového editoru na svém počítači a nahraďte `<SubscriptionB-id>` ID předplatného b. Pokud neznáte ID předplatného, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je vaše ID vašeho předplatného. Azure vytvořil virtuální síť (Classic), kterou jste vytvořili v kroku 4, ve skupině prostředků s názvem *Default-Networking*. Skript spustíte tak, že zkopírujete upravený skript, vložíte ho do PowerShellu a pak stisknete `Enter` .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Přihlaste se z Azure jako UserB a přihlaste se k předplatnému UserA jako UserA zadáním `Connect-AzAccount` příkazu. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
8. Vytvořte virtuální síť (Správce prostředků) tak, že zkopírujete následující skript, vložíte ho do PowerShellu a potom stisknete `Enter` :

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Přiřaďte oprávnění UserB k myVnetA. Zkopírujte následující skript do textového editoru na počítači a nahraďte `<SubscriptionA-Id>` ID předplatného a. Pokud neznáte ID předplatného, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je vaše ID vašeho předplatného. Vložte upravenou verzi skriptu do PowerShellu a potom `Enter` ho spusťte stisknutím klávesy.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Zkopírujte následující skript do textového editoru na svém počítači a nahraďte `<SubscriptionB-id>` ID předplatného B. Aby se myVnetA na myVNetB, zkopírujte upravený skript, vložte ho do PowerShellu a pak stiskněte `Enter` .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Zobrazte stav partnerského vztahu myVnetA zkopírováním následujícího skriptu a jeho vložením do PowerShellu a stisknutím `Enter` .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stav je **připojen**. Po nastavení partnerského vztahu pro myVnetA z myVnetB se změní na **připojeno** .

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nebudou schopné překládat názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
13. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-powershell) v tomto článku.

## <a name="delete-resources"></a><a name="delete"></a>Odstranění prostředků
Po dokončení tohoto kurzu možná budete chtít odstranit prostředky, které jste v tomto kurzu vytvořili, takže se vám neúčtují poplatky za využití. Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jsou ve skupině prostředků.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Do vyhledávacího pole portálu zadejte **myResourceGroupA**. Ve výsledcích hledání klikněte na **myResourceGroupA**.
2. V okně **myResourceGroupA** klikněte na ikonu **Odstranit** .
3. Odstranění potvrďte tak, že do pole **Zadejte název skupiny prostředků** zadáte **myResourceGroupA** a pak kliknete na **Odstranit**.
4. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetB*. Po zobrazení ve výsledcích hledání klikněte na **myVnetB** . Zobrazí se okno pro virtuální síť **myVnetB** .
5. V okně **myVnetB** klikněte na **Odstranit**.
6. Kliknutím na **Ano** v poli **odstranit virtuální síť** potvrďte odstranění.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Přihlaste se k Azure pomocí rozhraní příkazového řádku k odstranění virtuální sítě (Správce prostředků) pomocí následujícího příkazu:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Přihlaste se k Azure pomocí klasického rozhraní příkazového řádku a odstraňte virtuální síť (Classic) následujícími příkazy:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Na příkazovém řádku prostředí PowerShell zadejte následující příkaz k odstranění virtuální sítě (Správce prostředků):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Pokud chcete odstranit virtuální síť (Classic) pomocí PowerShellu, musíte upravit existující konfigurační soubor sítě. Naučte se [exportovat, aktualizovat a importovat síťové konfigurační soubory](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file). Odeberte následující element VirtualNetworkSite pro virtuální síť, která se používá v tomto kurzu:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
     <AddressSpace>
       <AddressPrefix>10.1.0.0/16</AddressPrefix>
     </AddressSpace>
     <Subnets>
       <Subnet name="default">
         <AddressPrefix>10.1.0.0/24</AddressPrefix>
       </Subnet>
     </Subnets>
   </VirtualNetworkSite>
   ```

   > [!WARNING]
   > Import změněného konfiguračního souboru sítě může způsobit změny ve stávajících virtuálních sítích (Classic) v rámci vašeho předplatného. Ujistěte se, že jste odebrali jenom předchozí virtuální síť a že neměníte ani neodebíráte žádné jiné existující virtuální sítě z vašeho předplatného. 

## <a name="next-steps"></a>Další kroky

- Před vytvořením partnerského vztahu virtuálních sítí pro produkční použití důkladně se seznamte s důležitými [omezeními a chováním partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints) .
- Přečtěte si o [nastaveních partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
- Naučte se [vytvářet síťové topologie centra a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s využitím partnerského vztahu virtuálních sítí.