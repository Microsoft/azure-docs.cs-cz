---
title: Vytvoření partnerského vztahu virtuální síti Azure – jiné nasazení modelů - různých předplatných | Microsoft Docs
description: Naučte se vytvořit virtuální síť partnerský vztah mezi virtuální sítě vytvořené pomocí různých nasazení Azure modely, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: jdial;anavin
ms.openlocfilehash: dcc733308f4c0441545dc0d03cca2ca1112812ac
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Vytvořit virtuální síť partnerský vztah - různé modely nasazení a odběry

V tomto kurzu zjistíte vytvořit virtuální síť partnerský vztah mezi virtuální sítě vytvořené pomocí různé modely nasazení. Virtuální sítě existovat v různých předplatných. Partnerský vztah dva prostředky umožňuje virtuální sítě v různých virtuálních sítích ke komunikaci mezi sebou stejným šířky pásma a latence, jako by byl prostředky ve stejné virtuální síti. Další informace o [partnerský vztah virtuální sítě](virtual-network-peering-overview.md).

Postup vytvoření virtuální sítě partnerského vztahu se liší v závislosti na tom, jestli virtuální sítě jsou ve stejné nebo jiné, odběry a které [modelu nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě se vytvářejí pomocí. Naučte se vytvořit virtuální síť partnerský vztah v jiných scénářích kliknutím na scénář z v následující tabulce:

|Model nasazení Azure  | předplatné Azure  |
|--------- |---------|
|[I Resource Manager](tutorial-connect-virtual-networks-portal.md) |stejné|
|[I Resource Manager](create-peering-different-subscriptions.md) |Odlišné|
|[Jeden Resource Manager, jeden classic](create-peering-different-deployment-models.md) |stejné|

Virtuální síť partnerský vztah nelze vytvořit mezi dvěma virtuálními sítěmi nasazené prostřednictvím modelu nasazení classic. Tento kurz používá virtuální sítě, které existují ve stejné oblasti. V tomto kurzu partnerský vztah virtuálních sítí ve stejné oblasti. Můžete také peer virtuální sítě v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region). Doporučuje se, že jsou Seznamte se s [partnerského vztahu požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints) před partnerský vztah virtuální sítě.

Při vytváření virtuální sítě partnerský vztah mezi virtuálními sítěmi, které existují v různých předplatných, odběry musí být přidruženy ke stejné klienta Azure Active Directory. Pokud ještě nemáte klienta služby Azure Active Directory, můžete rychle [vytvořit](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Můžete propojit virtuální sítě v různých předplatných a jiné Azure Active Directory klienty pomocí Azure [brány VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Můžete použít [portál Azure](#portal), Azure [rozhraní příkazového řádku](#cli) (CLI), nebo Azure [prostředí PowerShell](#powershell) vytvoření virtuální sítě partnerského vztahu. Klikněte na libovolný předchozí odkaz nástroj přejít přímo na kroky pro vytvoření virtuální sítě partnerský vztah nástroji vašeho výběru.

## <a name="portal"></a>Vytvoření partnerského vztahu – portál Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění pro oba odběry, můžete použít stejný účet pro všechny kroky, přeskočte postup protokolování mimo portál a přeskočit kroky pro přiřazení jiný uživatel oprávnění k virtuálním sítím.

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako uživatele. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
2. Klikněte na tlačítko **+ nový**, klikněte na tlačítko **sítě**, pak klikněte na tlačítko **virtuální síť**.
3. V **vytvořit virtuální síť** okno, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:
    - **Název**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné A.
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroupA*
    - **Umístění**: *východní USA*
4. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetA*. Klikněte na tlačítko **myVnetA** při zobrazí ve výsledcích hledání. Zobrazí okno **myVnetA** virtuální sítě.
5. V **myVnetA** okno, které se zobrazí, klikněte na tlačítko **přístup k ovládacímu prvku (IAM)** ze seznamu svislé možností na levé straně okna.
6. V **myVnetA – řízení přístupu (IAM)** okno, které se zobrazí, klikněte na tlačítko **+ přidat**.
7. V **přidat oprávnění** okno, které se zobrazí, vyberte **Přispěvatel sítě** v **Role** pole.
8. V **vyberte** zaškrtněte, b nebo zadejte e-mailovou adresu společnosti b ji najít. Seznam uživatelů, zobrazí se ze stejné klienta Azure Active Directory jako virtuální síť, kterou jste nastavení pro partnerský vztah. Když se objeví v seznamu, klikněte na tlačítko b.
9. Klikněte na **Uložit**.
10. Odhlaste se z portálu jako uživatele a přihlaste se jako b.
11. Klikněte na tlačítko **+ nový**, typ *virtuální síť* v **vyhledávání na webu Marketplace** pole a pak klikněte na **virtuální síť** ve výsledcích hledání.
12. V **virtuální sítě** okno, které se zobrazí, vyberte **Classic** v **vybrat model nasazení** pole a pak klikněte na **vytvořit**.
13.   Vytvoření virtuální sítě (klasické) pole, která se zobrazí zadejte následující hodnoty:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné B.
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroupB*
    - **Umístění**: *východní USA*

14. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetB*. Klikněte na tlačítko **myVnetB** při zobrazí ve výsledcích hledání. Zobrazí okno **myVnetB** virtuální sítě.
15. V **myVnetB** okno, které se zobrazí, klikněte na tlačítko **vlastnosti** ze seznamu svislé možností na levé straně okna. Kopírování **ID prostředku**, která je použita v pozdější fázi. Podobně jako v následujícím příkladu je ID prostředku: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
16. Proveďte kroky 5 až 9 pro myVnetB, zadávání **uživatele** v kroku 8.
17. Odhlaste se z portálu jako b a přihlaste se jako uživatele.
18. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetA*. Klikněte na tlačítko **myVnetA** při zobrazí ve výsledcích hledání. Zobrazí okno **myVnet** virtuální sítě.
19. Klikněte na tlačítko **myVnetA**.
20. V **myVnetA** okno, které se zobrazí, klikněte na tlačítko **partnerských vztahů** ze seznamu svislé možností na levé straně okna.
21. V **myVnetA - partnerských vztahů** okno, které se zobrazily, klikněte na tlačítko **+ přidat**
22. V **partnerský vztah přidat** okno, které se zobrazí, zadejte, nebo vyberte následující možnosti a potom klikněte na tlačítko **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Virtuální síť modelu nasazení**: vyberte **Classic**.
     - **Vím Moje ID prostředku**: Zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku myVnetB z kroku 15.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že **povoleno** je vybrána.
    Žádné další nastavení použitá v tomto kurzu. Další informace o všech nastaveních partnerského vztahu, přečtěte si [spravovat virtuální sítě partnerských vztahů](virtual-network-manage-peering.md#create-a-peering).
23. Po kliknutí na **OK** v předchozím kroku, **partnerský vztah přidat** okno se zavře a zobrazí **myVnetA - partnerských vztahů** okno znovu. Za několik sekund partnerského vztahu, kterou jste vytvořili se zobrazí v okně. **Připojení** , je uvedena ve **stav partnerského vztahu** sloupec pro **myVnetAToMyVnetB** partnerského vztahu, můžete vytvořit. Partnerského vztahu je nyní vytvořeno. Není nutné rovnocenných počítačů virtuální sítě (klasické) na virtuální síť (Resource Manager).

    Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

24. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
25. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-portal) tohoto článku.

## <a name="cli"></a>Vytvoření partnerského vztahu - rozhraní příkazového řádku Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění pro oba odběry, můžete použít stejný účet pro všechny kroky, přeskočte postup protokolování mimo Azure a odstranit řádky skriptu, které vytvořit přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů s uživatelských jmen, kterou používáte pro uživatele a b. 

1. [Nainstalujte](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 rozhraní příkazového řádku Azure k vytvoření virtuální sítě (klasické).
2. Otevřete relaci rozhraní příkazového řádku a přihlaste se k Azure jako b pomocí `azure login` příkaz. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
3. Spuštění rozhraní příkazového řádku v režimu správy služby tak, že zadáte `azure config mode asm` příkaz.
4. Zadejte následující příkaz pro vytvoření virtuální sítě (klasické):
 
    ```azurecli
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```
5. Zbývající kroky musí dokončit pomocí bash skořápce pomocí Azure CLI verze 2.0.4 nebo novější [nainstalován](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo pomocí prostředí cloudové služby Azure. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Klikněte **vyzkoušet** tlačítko ve skriptech následujících, což otevře prostředí cloudu, který protokoluje události v účtu Azure. Možnosti na spuštění bash skripty rozhraní příkazového řádku v klientovi Windows, najdete v části [běžící ve Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
6. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s vaším ID předplatného. Pokud si nejste jisti Id předplatného, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je ID vašeho předplatného. Zkopírujte skript upravené, vložte jej do relace 2.0 rozhraní příkazového řádku a stiskněte klávesu `Enter`. 

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Když jste vytvořili virtuální sítě (klasické) v kroku 4, Azure vytvoří virtuální síť ve *výchozí sítě* skupinu prostředků.
7. Přihlaste se b mimo Azure a přihlaste se jako uživatele v 2.0 rozhraní příkazového řádku.
8. Vytvořte skupinu prostředků a virtuální síť (Resource Manager). Zkopírujte následující skript, vložte jej do relace rozhraní příkazového řádku a stiskněte klávesu `Enter`. 

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

9. Vytvoření virtuální sítě partnerský vztah mezi dvě virtuální sítě vytvořené pomocí různé modely nasazení. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-id>` s ID vašeho předplatného. Pokud si nejste jisti Id předplatného, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je ID vašeho předplatného. Azure vytvořili virtuální sítě (klasické) jste vytvořili v kroku 4 ve skupině prostředků s názvem *výchozí sítě*. Vložte upravené skript v relaci příkazového řádku a stiskněte klávesu `Enter`.

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

10. Po spuštění skriptu, zkontrolujte partnerský vztah pro virtuální síť (Resource Manager). Zkopírujte následující skript a vložte jej v relaci příkazového řádku:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```
    Výstup ukazuje **připojeno** v **PeeringState** sloupce.

    Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

11. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
12. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-cli) v tomto článku.

## <a name="powershell"></a>Vytvoření partnerského vztahu – prostředí PowerShell

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění pro oba odběry, můžete použít stejný účet pro všechny kroky, přeskočte postup protokolování mimo Azure a odstranit řádky skriptu, které vytvořit přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů s uživatelských jmen, kterou používáte pro uživatele a b. 

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduly. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci prostředí PowerShell.
3. V prostředí PowerShell, přihlaste se k předplatnému b je jako b zadáním `Add-AzureAccount` příkaz. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
4. Vytvoření virtuální sítě (klasické) pomocí prostředí PowerShell, musíte vytvořit novou nebo upravit existující, konfigurační soubor sítě. Zjistěte, jak [exportovat, aktualizovat a import konfiguračních souborů síť](virtual-networks-using-network-configuration-file.md). Soubor musí zahrnovat následující **VirtualNetworkSite** element pro virtuální síť použít v tomto kurzu:

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
    > Import konfiguračního souboru změněné sítě může způsobit změny existující virtuální sítě (klasické) v rámci vašeho předplatného. Ujistěte se, můžete přidat pouze předchozí virtuální sítě a změníte nebo odeberte všechny existující virtuální sítě ze svého předplatného. 

5. Přihlaste se k odběru na b jako b používat příkazy Resource Manager tak, že zadáte `Connect-AzureRmAccount` příkaz.
6. Přiřazení oprávnění uživatele k virtuální síti B. kopie následující skript do textového editoru na počítače a nahraďte `<SubscriptionB-id>` s ID předplatného B. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota **Id** ve vrácené výstupu je ID vašeho předplatného. Azure vytvořili virtuální sítě (klasické) jste vytvořili v kroku 4 ve skupině prostředků s názvem *výchozí sítě*. Spustit skript, zkopírujte upravené skript, vložte jej do prostředí PowerShell a potom stiskněte klávesu `Enter`.
    
    ```powershell 
    New-AzureRmRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Odhlaste se z Azure jako b a přihlaste se k odběru na uživatele jako uživatele tak, že zadáte `Connect-AzureRmAccount` příkaz. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
8. Vytvoření virtuální sítě (Resource Manager) kopírování následující skript, vložením v prostředí PowerShell a stisknutím klávesy `Enter`:

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

9. Přiřaďte oprávnění b myVnetA. Zkopírujte následující skript do textového editoru na počítače a nahraďte `<SubscriptionA-Id>` s ID předplatného A. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota **Id** ve vrácené výstupu je ID vašeho předplatného. Vložte upravenou verzi skriptu v prostředí PowerShell a potom stiskněte klávesu `Enter` ho provést.

    ```powershell
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Zkopírujte následující skript do textového editoru ve vašem počítači a nahraďte `<SubscriptionB-id>` s ID předplatného B. Rovnocenných počítačů myVnetA k myVNetB, zkopírujte upravené skript, vložte jej do prostředí PowerShell a potom stiskněte klávesu `Enter`.

    ```powershell
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Zobrazit stav partnerského vztahu myVnetA kopírování následující skript, vložením do prostředí PowerShell a stisknutím klávesy `Enter`.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stav je **připojeno**. Změní na **připojeno** po nastavení partnerského vztahu k myVnetA z myVnetB.

    Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

12. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
13. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-powershell) v tomto článku.

## <a name="delete"></a>Odstraňte prostředky
Po dokončení tohoto kurzu, můžete chtít odstranit z prostředků, které jste vytvořili v tomto kurzu, aby vám zbytečně nenabíhaly poplatky za používání. Odstranění skupiny prostředků se také odstraní všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Portál Azure

1. V dialogovém okně hledání portálu zadejte **myResourceGroupA**. Ve výsledcích hledání klikněte na tlačítko **myResourceGroupA**.
2. Na **myResourceGroupA** okně klikněte **odstranit** ikonu.
3. Potvrďte odstranění, v **název skupiny prostředků typu** zadejte **myResourceGroupA**a potom klikněte na **odstranit**.
4. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetB*. Klikněte na tlačítko **myVnetB** při zobrazí ve výsledcích hledání. Zobrazí okno **myVnetB** virtuální sítě.
5. V **myVnetB** okně klikněte na tlačítko **odstranit**.
6. Potvrďte odstranění, klikněte na tlačítko **Ano** v **virtuální sítě odstranit** pole.

### <a name="delete-cli"></a>Rozhraní příkazového řádku Azure

1. Přihlaste se k Azure pomocí rozhraní příkazového řádku 2.0 se odstranit virtuální síť (Resource Manager) pomocí následujícího příkazu:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```

2. Přihlaste se k Azure pomocí Azure CLI 1.0 odstranit virtuální sítě (klasické) pomocí následujících příkazů:

    ```azurecli
    azure config mode asm 

    azure network vnet delete --vnet myVnetB --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Na příkazovém řádku prostředí PowerShell zadejte následující příkaz k odstranění virtuální sítě (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -Force
    ```

2. Pokud chcete odstranit virtuální sítě (klasické) v prostředí PowerShell, je třeba upravit existující konfigurační soubor sítě. Zjistěte, jak [exportovat, aktualizovat a import konfiguračních souborů síť](virtual-networks-using-network-configuration-file.md). Odeberte následující element VirtualNetworkSite virtuální sítě používá v tomto kurzu:

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
    > Import konfiguračního souboru změněné sítě může způsobit změny existující virtuální sítě (klasické) v rámci vašeho předplatného. Ujistěte se, jenom odebrat předchozí virtuální sítě a změníte nebo odeberte ostatní existující virtuální sítě ze svého předplatného. 

## <a name="next-steps"></a>Další postup

- Důkladně Seznamte se s důležité [partnerského vztahu omezení virtuální sítě a chování](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením virtuální sítě partnerský vztah pro produkční použití.
- Další informace o všech [partnerského vztahu nastavení virtuální sítě](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvořit rozbočovače a příčkou topologie sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah virtuální sítě.
