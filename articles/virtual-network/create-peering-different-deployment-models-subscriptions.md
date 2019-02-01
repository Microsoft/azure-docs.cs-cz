---
title: Vytvoření partnerského vztahu virtuální sítí Azure – jiné nasazení modelů - různých předplatných
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi vytvořenými pomocí různých Azure modelů nasazení, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: 6a652b3fa834c2f29f5063f9ba72a3e3d4e75f58
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512444"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Vytvoření partnerského vztahu virtuálních sítí – různé modely nasazení a předplatná

V tomto kurzu zjistíte, jak vytvořit virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi vytvořenými pomocí různých modelů nasazení. Virtuální sítě existovat v různých předplatných. Partnerský vztah dvou virtuálních sítí umožňuje prostředků v různých virtuálních sítích komunikovat mezi sebou stejnou šířku pásma a čekací doba jakoby byly ve stejné virtuální síti. Další informace o [partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

Postup vytvoření partnerského vztahu virtuálních sítí se liší v závislosti na tom, jestli jsou virtuální sítě ve stejné nebo jiné, předplatných a které [model nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě se vytvářejí prostřednictvím. Zjistěte, jak vytvořit virtuální síť vytvoření partnerského vztahu v jiných scénářích kliknutím na scénář z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manageru](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Obě Resource Manageru](create-peering-different-subscriptions.md) |Odlišné|
|[Jedna Resource Manager, druhá classic](create-peering-different-deployment-models.md) |Stejné|

Partnerský vztah virtuální sítě nejde vytvořit mezi dvěma virtuálními sítěmi, které jsou nasazené prostřednictvím modelu nasazení classic. Tento kurz používá virtuálními sítěmi ve stejné oblasti. V tomto kurzu partnerský vztah virtuálních sítí ve stejné oblasti. Můžete také vytvořit partnerský vztah virtuálních sítí v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region). Je doporučeno, aby měli seznámit s [partnerského vztahu požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints) před partnerský vztah virtuálních sítí.

Když vytváříte virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi v různých předplatných, předplatná musí být přidružený ke stejnému tenantovi Azure Active Directory. Pokud ještě nemáte tenanta služby Azure Active Directory, můžete rychle [vytvořit](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Můžete propojit virtuální sítě v různých předplatných a různých Azure Active Directory klienty pomocí Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Můžete použít [webu Azure portal](#portal), Azure [rozhraní příkazového řádku](#cli) (CLI), nebo v Azure [Powershellu](#powershell) k vytvoření partnerského vztahu virtuálních sítí. Klikněte na tlačítko Předchozí odkazy nástroje můžete přejít přímo na kroky pro vytvoření partnerského vztahu virtuálních sítí pomocí nástrojů podle výběru.

## <a name="portal"></a>Vytvoření partnerského vztahu – Azure portal

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočte kroky pro přihlášení z portálu a přeskočte kroky pro přiřazení oprávnění pro jiné uživatele k virtuálním sítím.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako UserA. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
2. Klikněte na tlačítko **+ nová**, klikněte na tlačítko **sítě**, pak klikněte na tlačítko **virtuální síť**.
3. V **vytvořit virtuální síť** okno, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:
    - **Název**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné A.
    - **Skupina prostředků**: Vyberte **vytvořit nový** a zadejte *myResourceGroupA*
    - **Umístění**: *USA – východ*
4. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetA*. Klikněte na tlačítko **myVnetA** když se zobrazí ve výsledcích hledání. Zobrazí se okno pro **myVnetA** virtuální sítě.
5. V **myVnetA** okno, které se zobrazí, klikněte na tlačítko **řízení přístupu (IAM)** z svislý seznam možností na levé straně okna.
6. V **myVnetA – řízení přístupu (IAM)** okno, které se zobrazí, klikněte na tlačítko **+ přidat přiřazení role**.
7. V **přidat přiřazení role** okno, které se zobrazí, vyberte **Přispěvatel sítě** v **Role** pole.
8. V **vyberte** pole, vyberte UserB nebo zadejte e-mailovou adresu vaší UserB ji najít. Seznam uživatelů, zobrazí se ze stejného tenanta služby Azure Active Directory jako při nastavování partnerský vztah pro virtuální síť. Když se objeví v seznamu, klikněte na tlačítko UserB.
9. Klikněte na **Uložit**.
10. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
11. Klikněte na tlačítko **+ nová**, typ *virtuální síť* v **Hledat na Marketplace** pole a potom klikněte na **virtuální síť** ve výsledcích hledání.
12. V **virtuální sítě** okno, které se zobrazí, vyberte **Classic** v **vybrat model nasazení** pole a potom klikněte na **vytvořit**.
13.   Do pole vytvořit virtuální síť (classic), který se zobrazí zadejte následující hodnoty:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné služby serveru B.
    - **Skupina prostředků**: Vyberte **vytvořit nový** a zadejte *myResourceGroupB*
    - **Umístění**: *USA – východ*

14. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetB*. Klikněte na tlačítko **myVnetB** když se zobrazí ve výsledcích hledání. Zobrazí se okno pro **myVnetB** virtuální sítě.
15. V **myVnetB** okno, které se zobrazí, klikněte na tlačítko **vlastnosti** z svislý seznam možností na levé straně okna. Kopírovat **ID prostředku**, který se používá v pozdější fázi. Podobně jako v následujícím příkladu je ID prostředku: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Pro, proveďte kroky 5 až 9 myVnetB zadávání **UserA** v kroku 8.
17. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
18. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetA*. Klikněte na tlačítko **myVnetA** když se zobrazí ve výsledcích hledání. Zobrazí se okno pro **myVnet** virtuální sítě.
19. Klikněte na tlačítko **myVnetA**.
20. V **myVnetA** okno, které se zobrazí, klikněte na tlačítko **partnerské vztahy** z svislý seznam možností na levé straně okna.
21. V **myVnetA - partnerské vztahy** okno, které se objevily, klikněte na tlačítko **+ přidat**
22. V **přidat partnerský vztah** okno, které se zobrazí, zadejte, nebo vyberte následující možnosti a potom klikněte na tlačítko **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Model nasazení virtuální sítě**:  Vyberte **Classic**.
     - **Znám svoje ID prostředku**: Zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku myVnetB z kroku 15.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že **povoleno** zaškrtnuto.
    Žádná ostatní nastavení se používají v tomto kurzu. Další informace o všech nastavení partnerského vztahu, přečtěte si [spravovat partnerské vztahy virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknutí na tlačítko **OK** v předchozím kroku, **přidat partnerský vztah** zavře se okno a zobrazí **myVnetA - partnerské vztahy** okno znovu. Po několika sekundách se partnerský vztah, kterou jste vytvořili se zobrazí v okně. **Připojené** je uveden v **stav partnerského vztahu** sloupce **myVnetAToMyVnetB** partnerský vztah, můžete vytvořit. Partnerský vztah je nyní navázán. Není nutné vytvořit partnerský vztah virtuální sítě (classic) na virtuální síť (Resource Manager).

    Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou překládat názvy mezi virtuálními sítěmi. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
25. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, dokončete kroky [odstraňte prostředky](#delete-portal) části tohoto článku.

## <a name="cli"></a>Vytvoření partnerského vztahu – rozhraní příkazového řádku Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočte kroky pro přihlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů se uživatelská jména, že používáte UserA a UserB. Proveďte následující kroky pomocí Azure classic CLI a Azure CLI. Kroky v Azure Cloud Shell, můžete dokončit právě výběrem **vyzkoušet** tlačítko v některém z následujících kroků, nebo po instalaci [rozhraní příkazového řádku classic](/cli/azure/install-classic-cli) a [rozhraní příkazového řádku](/cli/azure/install-azure-cli) a spuštění příkazů v místním počítači.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2, protože službě Cloud Shell se automaticky přihlásí můžete do Azure. Otevřete relaci příkazového řádku a přihlášení do Azure s využitím `azure login` příkazu.
2. Spuštění rozhraní příkazového řádku classic v režimu správy služby tak, že zadáte `azure config mode asm` příkazu.
3. Zadejte následující příkaz rozhraní příkazového řádku classic k vytvoření virtuální sítě (classic):

    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
4. Zbývající kroky musí dokončit pomocí prostředí bash pomocí rozhraní příkazového řádku Azure (ne rozhraní příkazového řádku classic).
5. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` svým ID předplatného. Pokud si nejste jisti Id předplatného, zadejte `az account show` příkazu. Hodnota pro **id** ve výstupu je vaše ID předplatného. Zkopírujte upravený skript, vložte ji do relace příkazového řádku a potom stiskněte klávesu `Enter`.

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Při vytváření virtuální sítě (classic) v kroku 4, Azure vytvoří virtuální sítě *výchozí sítě* skupinu prostředků.
6. Protokolovat UserB mimo Azure a přihlaste se jako UserA v rozhraní příkazového řádku.
7. Vytvořte skupinu prostředků a virtuální síť (Resource Manager). Zkopírujte následující skript, vložte ji do relace příkazového řádku a potom stiskněte klávesu `Enter`.

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

8. Vytvořte virtuální síť vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-id>` s ID vašeho předplatného. Pokud si nejste jisti Id předplatného, zadejte `az account show` příkazu. Hodnota pro **id** ve výstupu je vaše ID předplatného. Azure vytvoří virtuální síť (klasická), kterou jste vytvořili v kroku 4 ve skupině prostředků s názvem *výchozí sítě*. Vložte upravený skript v relaci příkazového řádku a potom stiskněte klávesu `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Po spuštění skriptu zkontrolujte partnerský vztah virtuální sítě (Resource Manager). Zkopírujte následující skript a následně jej vložte do relace prostředí rozhraní příkazového řádku:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Zobrazí se výstup **připojeno** v **PeeringState** sloupce.

    Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou překládat názvy mezi virtuálními sítěmi. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

10. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
11. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstraňte prostředky](#delete-cli) v tomto článku.

## <a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočte kroky pro přihlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů se uživatelská jména, že používáte UserA a UserB. 

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduly. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci Powershellu.
3. V prostředí PowerShell, přihlaste se k předplatnému na UserB jako UserB tak, že zadáte `Add-AzureAccount` příkazu. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
4. Vytvoření virtuální sítě pomocí prostředí PowerShell (classic), musíte vytvořit novou nebo upravit existující soubor konfigurace sítě. Zjistěte, jak [export, aktualizovat a import konfiguračních souborech sítě](virtual-networks-using-network-configuration-file.md). Soubor by měl obsahovat následující **VirtualNetworkSite** – element pro virtuální sítě použité v tomto kurzu:

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
    > Import souboru konfigurace sítě změněné může způsobit změny do existující virtuální sítě (classic) v rámci vašeho předplatného. Ujistěte se, jenom přidat předchozí virtuální síť a změníte nebo odeberte všechny existující virtuální sítě z předplatného. 

5. Přihlaste se k předplatnému na UserB jako UserB používat příkazy Resource Manageru tak, že zadáte `Connect-AzureRmAccount` příkazu.
6. Přiřazení oprávnění UserA k virtuální síti B. Zkopírujte následující skript do textového editoru na počítačích PC a nahradit `<SubscriptionB-id>` s ID předplatného služby serveru B. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota pro **Id** ve vrácené výstupu je ID vašeho předplatného. Azure vytvoří virtuální síť (klasická), kterou jste vytvořili v kroku 4 ve skupině prostředků s názvem *výchozí sítě*. Spusťte skript, zkopírujte upravený skript, vložte ji do prostředí PowerShell a stiskněte klávesu `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Odhlaste se z Azure jako UserB a přihlaste se k předplatnému na UserA jako UserA tak, že zadáte `Connect-AzureRmAccount` příkazu. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
8. Vytvořte virtuální síť (Resource Manager) kopírování následující skript, udržet v powershellu a následným stisknutím `Enter`:

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Přiřadíte oprávnění uživateli b myVnetA. Zkopírujte následující skript do textového editoru na počítačích PC a nahradit `<SubscriptionA-Id>` s ID předplatného A. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota pro **Id** ve vrácené výstupu je ID vašeho předplatného. Vložte upravenou verzi skriptu prostředí PowerShell a potom stiskněte klávesu `Enter` k jeho provedení.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Zkopírujte následující skript do textového editoru ve vašem počítači a nahraďte `<SubscriptionB-id>` s ID předplatného služby serveru B. Navázání partnerského vztahu mezi myVnetA k myVNetB, zkopírujte upravený skript, vložte ji do prostředí PowerShell a stiskněte klávesu `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Zobrazit stav partnerského vztahu myVnetA kopírování následující skript, vložte ho do prostředí PowerShell a stisknutím klávesy `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Zobrazí se stav **připojeno**. Změní na **připojeno** po nastavení partnerského vztahu pro myVnetA z myVnetB.

    Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou překládat názvy mezi virtuálními sítěmi. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
13. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstraňte prostředky](#delete-powershell) v tomto článku.

## <a name="delete"></a>Odstranit prostředky
Po dokončení tohoto kurzu, můžete chtít odstranit prostředky, které jste vytvořili v tomto kurzu, takže se vám neúčtovaly poplatky za využívání. Odstranění skupiny prostředků se odstraní také všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Azure Portal

1. Portálového vyhledávacího pole zadejte **myResourceGroupA**. Ve výsledcích hledání klikněte na tlačítko **myResourceGroupA**.
2. Na **myResourceGroupA** okna, klikněte na tlačítko **odstranit** ikonu.
3. Potvrďte odstranění, v **zadejte název skupiny prostředků** zadejte **myResourceGroupA**a potom klikněte na tlačítko **odstranit**.
4. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetB*. Klikněte na tlačítko **myVnetB** když se zobrazí ve výsledcích hledání. Zobrazí se okno pro **myVnetB** virtuální sítě.
5. V **myVnetB** okna, klikněte na tlačítko **odstranit**.
6. Potvrďte odstranění kliknutím na **Ano** v **odstranění virtuální sítě** pole.

### <a name="delete-cli"></a>Azure CLI

1. Přihlaste se k Azure pomocí rozhraní příkazového řádku se odstranit virtuální síť (Resource Manager) pomocí následujícího příkazu:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Přihlaste se k Azure pomocí rozhraní příkazového řádku classic odstranit virtuální síť (classic) pomocí následujících příkazů:

   ```azurecli-interactive
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="delete-powershell"></a>PowerShell

1. Příkazovém řádku prostředí PowerShell zadejte následující příkaz se odstranit virtuální síť (Resource Manager):

   ```powershell
   Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
   ```

2. Pokud chcete odstranit virtuální síť (classic) pomocí prostředí PowerShell, je třeba upravit existující konfigurační soubor sítě. Zjistěte, jak [export, aktualizovat a import konfiguračních souborech sítě](virtual-networks-using-network-configuration-file.md). Odeberte virtuální sítě v tomto kurzu používá následující element VirtualNetworkSite:

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
   > Import souboru konfigurace sítě změněné může způsobit změny do existující virtuální sítě (classic) v rámci vašeho předplatného. Ujistěte se, jenom odebrat předchozí virtuální sítě a změníte nebo z předplatného odebrat ostatní existující virtuální sítě. 

## <a name="next-steps"></a>Další postup

- Důkladně Seznamte se s důležité [omezení partnerských vztahů virtuálních sítí a chování](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením partnerský vztah pro produkční použití.
- Další informace o všech [nastavení partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvoření hvězdicové síťové topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s využitím partnerského vztahu virtuální sítě.