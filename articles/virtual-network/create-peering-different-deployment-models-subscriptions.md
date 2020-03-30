---
title: Vytvoření partnerského vztahu virtuální sítě Azure – různé modely nasazení – různá předplatná
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení Azure, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 6823514e284f75f0580578dcabaa1b1bdcbe2f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239839"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Vytvoření partnerského vztahu virtuální sítě – různé modely nasazení a předplatná

V tomto kurzu se naučíte vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Virtuální sítě existují v různých předplatných. Partnerský vztah dvou virtuálních sítí umožňuje prostředkům v různých virtuálních sítích vzájemnou komunikaci se stejnou šířkou pásma a latencí, jako by byly prostředky ve stejné virtuální síti. Další informace o [partnerském vztahu virtuální sítě](virtual-network-peering-overview.md).

Kroky k vytvoření partnerského vztahu virtuální sítě se liší v závislosti na tom, zda virtuální sítě jsou ve stejné nebo jiné, odběry a které [model nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě jsou vytvořeny prostřednictvím. Zjistěte, jak vytvořit partnerský vztah virtuální sítě v jiných scénářích kliknutím na scénář z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manager](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Obě Resource Manager](create-peering-different-subscriptions.md) |Různé|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models.md) |Stejné|

Partnerský vztah virtuální sítě nelze vytvořit mezi dvěma virtuálními sítěmi nasazenými prostřednictvím klasického modelu nasazení. Tento kurz používá virtuální sítě, které existují ve stejné oblasti. Tento kurz partnery virtuálnísítě ve stejné oblasti. Můžete také partnerské virtuální sítě v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region). Doporučujeme seznámit se s [požadavky a omezeními partnerského vztahu](virtual-network-manage-peering.md#requirements-and-constraints) před partnerským vztahem virtuálních sítí.

Při vytváření partnerského vztahu virtuální sítě mezi virtuálními sítěmi, které existují v různých předplatných, musí být předplatná přidružena ke stejnému tenantovi služby Azure Active Directory. Pokud ještě nemáte klienta Služby Azure Active Directory, můžete ho rychle [vytvořit](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Virtuální sítě můžete propojit v různých předplatných a různých tenantech Azure Active Directory pomocí brány Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

K vytvoření partnerského vztahu virtuální sítě můžete použít [portál Azure](#portal), [rozhraní příkazového řádku](#cli) Azure (CLI) nebo Azure [PowerShell.](#powershell) Kliknutím na některý z předchozích odkazů na nástroje přejdete přímo k krokům pro vytvoření partnerského vztahu virtuální sítě pomocí zvoleného nástroje.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Vytvoření partnerského vztahu – portál Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z portálu a přeskočit kroky pro přiřazení oprávnění jiného uživatele k virtuálním sítím.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako UserA. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
2. Klepněte na tlačítko **+ Nový**, klepněte na **položku Síť**a potom na **položku Virtuální síť**.
3. V okně **Vytvořit virtuální síť** zadejte nebo vyberte hodnoty pro následující nastavení a klikněte na **Vytvořit**:
    - **Jméno**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné A.
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte *myResourceGroupA*
    - **Umístění**: *Východní USA*
4. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetA*. Klikněte na **myVnetA,** když se zobrazí ve výsledcích hledání. Pro virtuální síť **myVnetA** se zobrazí okno.
5. V okně **myVnetA,** které se zobrazí, klepněte na **tlačítko Řízení přístupu (IAM)** ze svislého seznamu možností na levé straně okna.
6. V okně **myVnetA - Access control (IAM),** které se zobrazí, klikněte na **+ Přidat přiřazení role**.
7. V okně **Přidat přiřazení role,** které se zobrazí, vyberte v poli **Role** **položku Přispěvatel sítě.**
8. Do pole **Select** vyberte UserB nebo zadejte e-mailovou adresu UserB, kterou chcete vyhledat. Zobrazený seznam uživatelů pochází ze stejného klienta Služby Azure Active Directory jako virtuální síť, pro kterou nastavujete partnerský vztah. Klikněte na UserB, když se zobrazí v seznamu.
9. Klikněte na **Uložit**.
10. Odhlaste se z portálu jako UserA a potom se přihlaste jako UserB.
11. Klikněte na **+ Nový**, zadejte *virtuální síť* do pole Hledat **na Marketplace** a ve výsledcích hledání klikněte na **Virtuální síť.**
12. V okně **Virtuální síť,** které se zobrazí, vyberte v poli **Vybrat model nasazení položku** **Klasický** a klepněte na tlačítko **Vytvořit**.
13. Do pole Vytvořit virtuální síť (klasickou), které se zobrazí, zadejte následující hodnoty:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné B.
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte *myResourceGroupB.*
    - **Umístění**: *Východní USA*

14. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetB*. Klikněte na **myVnetB,** když se zobrazí ve výsledcích hledání. Pro virtuální síť **myVnetB** se zobrazí okno.
15. V okně **myVnetB,** které se zobrazí, klepněte na **příkaz Vlastnosti** ze svislého seznamu možností na levé straně okna. Zkopírujte **ID prostředku**, které se používá v pozdějším kroku. ID prostředku je podobné následujícímu příkladu:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Proveďte kroky 5-9 pro myVnetB a zadali **usera** v kroku 8.
17. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
18. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetA*. Klikněte na **myVnetA,** když se zobrazí ve výsledcích hledání. Pro virtuální síť **myVnet** se zobrazí okno.
19. Klikněte na **myVnetA**.
20. V okně **myVnetA,** které se zobrazí, klepněte na **položku Partnerské listy** ze svislého seznamu možností na levé straně okna.
21. V okně **myVnetA - Peerings,** které se objevilo, klikněte na **+ Přidat**
22. V okně **Přidat partnerský vztah,** který se zobrazí, zadejte nebo vyberte následující možnosti, klepněte na **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Model nasazení virtuální sítě**: Vyberte **možnost Classic**.
     - **Znám ID svého zdroje**: Zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku myVnetB z kroku 15.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že je vybrána možnost **Povoleno.**
    V tomto kurzu se nepoužívají žádná další nastavení. Informace o všech nastaveních partnerského vztahu najdete v části [Správa partnerských partnerských uživatelů virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
23. Po klepnutí na **tlačítko OK** v předchozím kroku se okno Přidat **partnerský vztah** zavře a znovu uvidíte okno **myVnetA - Peerings.** Po několika sekundách se v okně zobrazí vytvořený partnerský vztah. **Připojeno** je uvedeno ve **sloupci PEERING STATUS** pro partnerský vztah **myVnetAToMyVnetB,** který jste vytvořili. Partnerský vztah je nyní vytvořen. Není nutné peer virtuální sítě (klasické) do virtuální sítě (Resource Manager).

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
25. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [Odstranit prostředky](#delete-portal) tohoto článku.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Vytvoření partnerského vztahu – Azure CLI

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech uživatelská jména, která používáte pro UserA a UserB. Pomocí klasického příkazového příkazového příkazu Azure a příkazového příkazového příkazového příkazu Azure postupujte jako příkazové nebo úplné. Kroky z prostředí Azure Cloud Shell můžete provést tak, že vyberete tlačítko **Try It** v některém z následujících kroků nebo instalací [klasického příkazového příkazu příkazového příkazu](/cli/azure/install-classic-cli) a [příkazového příkazu](/cli/azure/install-azure-cli) a spuštěním příkazů v místním počítači.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2, protože Cloud Shell vás automaticky přihlásí do Azure. Otevřete relaci příkazů a přihlaste se k Azure pomocí příkazu. `azure login`
2. Spusťte klasické příkazové příkazové `azure config mode asm` příkazové příkazové příkazy v režimu správy služeb zadáním příkazu.
3. Chcete-li vytvořit virtuální síť (klasickou), zadejte následující klasický příkaz příkazu příkazu PŘÍKAZ PŘÍKAZ:

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. Zbývající kroky musí být dokončeny pomocí bash shell u Azure CLI (ne klasické CLI).
5. Zkopírujte následující skript do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` id předplatného. Pokud Id předplatného neznáte, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je vaše ID předplatného. Zkopírujte upravený skript, vložte jej `Enter`do relace příkazového příkazu a stiskněte klávesu .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Když jste vytvořili virtuální síť (klasickou) v kroku 4, Azure vytvořil virtuální síť ve skupině prostředků *výchozí sítě.*
6. Odhlásit UserB z Azure a přihlásit se jako UserA v rozhraní příkazového příkazu.
7. Vytvořte skupinu prostředků a virtuální síť (Správce prostředků). Zkopírujte následující skript, vložte jej do relace `Enter`příkazového příkazu k příkazu a stiskněte klávesu .

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

8. Vytvořte partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Zkopírujte následující skript do textového editoru v počítači. Nahraďte `<SubscriptionB-id>` id předplatného. Pokud Id předplatného neznáte, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je vaše ID předplatného. Azure vytvořil virtuální síť (klasické) jste vytvořili v kroku 4 ve skupině prostředků s názvem *Default-Networking*. Vložte upravený skript do relace příkazového `Enter`příkazu a stiskněte klávesu .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Po spuštění skriptu zkontrolujte partnerský vztah pro virtuální síť (Resource Manager). Zkopírujte následující skript a vložte ho do relace příkazového příkazu:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    Výstup zobrazuje **Připojeno** ve sloupci **PeeringState.**

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
11. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranění prostředků](#delete-cli) v tomto článku.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech uživatelská jména, která používáte pro UserA a UserB. 

1. Nainstalujte nejnovější verzi modulů PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [Az.](https://www.powershellgallery.com/packages/Az) Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci Prostředí PowerShell.
3. V PowerShellu se přihlaste k předplatnému UserB jako UserB zadáním příkazu. `Add-AzureAccount` Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
4. Chcete-li vytvořit virtuální síť (klasickou) s prostředím PowerShell, musíte vytvořit nový nebo upravit existující konfigurační soubor sítě. Přečtěte [si, jak exportovat, aktualizovat a importovat konfigurační síťové soubory](virtual-networks-using-network-configuration-file.md). Soubor by měl obsahovat následující prvek **VirtualNetworkSite** pro virtuální síť použitou v tomto kurzu:

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
    > Import změněného konfiguračního souboru sítě může způsobit změny stávajících virtuálních sítí (klasické) ve vašem předplatném. Ujistěte se, že přidáte jenom předchozí virtuální síť a že z předplatného nezměníte ani neodeberete žádné existující virtuální sítě. 

5. Přihlaste se k předplatnému UserB jako UserB a `Connect-AzAccount` použijte příkazy Správce prostředků zadáním příkazu.
6. Přiřaďte oprávnění UserA virtuální síti B. Zkopírujte následující `<SubscriptionB-id>` skript do textového editoru v počítači a nahraďte id předplatného B. Pokud Id předplatného neznáte, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je Vaše ID předplatného. Azure vytvořil virtuální síť (klasickou), kterou jste vytvořili v kroku 4 ve skupině prostředků s názvem *Default-Networking*. Chcete-li skript spustit, zkopírujte upravený skript, vložte `Enter`jej do prostředí PowerShell a stiskněte klávesu .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Odhlaste se z Azure jako UserB a přihlaste se `Connect-AzAccount` k předplatnému UserA jako UserA zadáním příkazu. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
8. Vytvořte virtuální síť (Správce prostředků) zkopírováním následujícího skriptu, jeho `Enter`vložením do prostředí PowerShell a stisknutím klávesy :

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

9. Přiřaďte oprávnění UserB společnosti myVnetA. Zkopírujte následující skript do textového editoru v počítači a nahraďte `<SubscriptionA-Id>` id předplatného A. Pokud Id předplatného neznáte, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je Vaše ID předplatného. Vložte upravenou verzi skriptu do prostředí `Enter` PowerShell a stisknutím klávesy ji spusťte.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Zkopírujte následující skript do textového editoru `<SubscriptionB-id>` v počítači a nahraďte id předplatného B. Chcete-li peer myVnetA na myVNetB, zkopírujte upravený skript, `Enter`vložte jej do prostředí PowerShell a stiskněte klávesu .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Zobrazení stavu partnerského vztahu myVnetA zkopírováním následujícího skriptu, vložením do prostředí PowerShell a stisknutím klávesy `Enter`.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stav je **Připojeno**. Změní se na **Připojeno,** jakmile nastavíte partnerský vztah na myVnetA z myVnetB.

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
13. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranění prostředků](#delete-powershell) v tomto článku.

## <a name="delete-resources"></a><a name="delete"></a>Odstranění prostředků
Po dokončení tohoto kurzu můžete chtít odstranit prostředky, které jste vytvořili v kurzu, takže vám nebudou účtovány poplatky za využití. Odstraněním skupiny prostředků také odstraníte všechny prostředky, které jsou ve skupině prostředků.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portál Azure

1. Do vyhledávacího pole portálu zadejte **myResourceGroupA**. Ve výsledcích hledání klikněte na **myResourceGroupA**.
2. V okně **myResourceGroupA** klikněte na ikonu **Odstranit.**
3. Chcete-li odstranění potvrdit, zadejte do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** položku **myResourceGroupA**a klepněte na tlačítko **Odstranit**.
4. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetB*. Klikněte na **myVnetB,** když se zobrazí ve výsledcích hledání. Pro virtuální síť **myVnetB** se zobrazí okno.
5. V okně **myVnetB** klepněte na tlačítko **Odstranit**.
6. Odstranění potvrdíte klepnutím na tlačítko **Ano** v poli **Odstranit virtuální síť.**

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Přihlaste se do Azure pomocí příkazového příkazu k odstranění virtuální sítě (Správce prostředků) pomocí následujícího příkazu:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Přihlaste se k Azure pomocí klasického příkazového příkazu k odstranění virtuální sítě (klasické) pomocí následujících příkazů:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Na příkazovém řádku Prostředí PowerShell zadejte následující příkaz pro odstranění virtuální sítě (Správce prostředků):

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Chcete-li odstranit virtuální síť (klasickou) pomocí prostředí PowerShell, je nutné upravit existující konfigurační soubor sítě. Přečtěte [si, jak exportovat, aktualizovat a importovat konfigurační síťové soubory](virtual-networks-using-network-configuration-file.md). Odeberte následující prvek VirtualNetworkSite pro virtuální síť použitou v tomto kurzu:

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
   > Import změněného konfiguračního souboru sítě může způsobit změny stávajících virtuálních sítí (klasické) ve vašem předplatném. Ujistěte se, že odeberete jenom předchozí virtuální síť a že z předplatného nezměníte ani neodeberete žádné jiné existující virtuální sítě. 

## <a name="next-steps"></a>Další kroky

- Před vytvořením partnerského vztahu virtuální sítě pro produkční použití se důkladně seznamte s důležitými [omezeními a chováním partnerského vztahu](virtual-network-manage-peering.md#requirements-and-constraints) virtuální sítě.
- Informace o všech [nastaveních partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvořit topologii sítě rozbočovače a paprsku](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) pomocí partnerského vztahu virtuální sítě.