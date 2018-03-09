---
title: "Vytvoření virtuální síti Azure partnerského vztahu - různé modely nasazení - stejného předplatného. | Microsoft Docs"
description: "Naučte se vytvořit virtuální síť partnerský vztah mezi virtuální sítě vytvořené pomocí modelech nasazení různých Azure, které existují ve stejném předplatném Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: 8558593633de47ce67adbfe28dc8973fd21f1a4b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Vytvořit virtuální síť partnerský vztah - různé modely nasazení, stejného předplatného. 

V tomto kurzu zjistíte vytvořit virtuální síť partnerský vztah mezi virtuální sítě vytvořené pomocí různé modely nasazení. Obě virtuální sítě existovat ve stejném předplatném. Partnerský vztah dva prostředky umožňuje virtuální sítě v různých virtuálních sítích ke komunikaci mezi sebou stejným šířky pásma a latence, jako by byl prostředky ve stejné virtuální síti. Další informace o [partnerský vztah virtuální sítě](virtual-network-peering-overview.md). 

Postup vytvoření virtuální sítě partnerského vztahu se liší v závislosti na tom, jestli virtuální sítě jsou ve stejné nebo jiné, odběry a které [modelu nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě se vytvářejí pomocí. Naučte se vytvořit virtuální síť partnerský vztah v jiných scénářích kliknutím na scénář z v následující tabulce:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[I Resource Manager](tutorial-connect-virtual-networks-portal.md) |stejné|
|[I Resource Manager](create-peering-different-subscriptions.md) |Odlišné|
|[Jeden Resource Manager, jeden classic](create-peering-different-deployment-models-subscriptions.md) |Odlišné|

Virtuální síť partnerský vztah nelze vytvořit mezi dvěma virtuálními sítěmi nasazené prostřednictvím modelu nasazení classic. Pokud potřebujete připojení virtuální sítě, které byly obě vytvořené pomocí modelu nasazení classic, můžete použít Azure [brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) připojení virtuální sítě. 

V tomto kurzu partnerský vztah virtuálních sítí ve stejné oblasti. Schopnost peer virtuální sítě v různých oblastech je aktuálně ve verzi preview. Proveďte kroky v [zaregistrovat pro partnerský vztah globální virtuální sítě](#register) před pokusem o partnerský uzel virtuálních sítí v různých oblastech nebo partnerského vztahu selže. Možnost propojit virtuální sítě v různých oblastech s Azure [brány VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) je všeobecně dostupná a nevyžaduje registraci.

Můžete použít [portál Azure](#portal), Azure [rozhraní příkazového řádku](#cli) (CLI) Azure [prostředí PowerShell](#powershell), nebo [šablony Azure Resource Manageru](#template)vytvoření virtuální sítě partnerského vztahu. Klikněte na libovolný předchozí odkaz nástroj přejít přímo na kroky pro vytvoření virtuální sítě partnerský vztah nástroji vašeho výběru.

## <a name="create-peering---azure-portal"></a>Vytvoření partnerského vztahu – portál Azure

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Najdete v článku [oprávnění](#permissions) tohoto článku podrobnosti.
2. Klikněte na tlačítko **+ nový**, klikněte na tlačítko **sítě**, pak klikněte na tlačítko **virtuální síť**.
3. V **vytvořit virtuální síť** okno, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:
    - **Název**: *myVnet1*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroup*
    - **Umístění**: *východní USA*
4. Klikněte na **+ Nový**. V **vyhledávání na webu Marketplace** zadejte *virtuální síť*. Klikněte na tlačítko **virtuální síť** při zobrazí ve výsledcích hledání. 
5. V **virtuální síť** vyberte **Classic** v **vybrat model nasazení** pole a pak klikněte na **vytvořit**.
6. V **vytvořit virtuální síť** okno, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:
    - **Název**: *myVnet2*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné
    - **Skupina prostředků**: vyberte **použít existující** a vyberte *myResourceGroup*
    - **Umístění**: *východní USA*
7. V **vyhledávání prostředků** pole v horní části portálu, typ *myResourceGroup*. Klikněte na tlačítko **myResourceGroup** při zobrazí ve výsledcích hledání. Zobrazí okno **myresourcegroup** skupinu prostředků. Skupina prostředků obsahuje dvě virtuální sítě vytvořené v předchozích krocích.
8. Klikněte na tlačítko **myVNet1**.
9. V **myVnet1** okno, které se zobrazí, klikněte na tlačítko **partnerských vztahů** ze seznamu svislé možností na levé straně okna.
10. V **myVnet1 - partnerských vztahů** okno, které se zobrazily, klikněte na tlačítko **+ přidat**
11. V **partnerský vztah přidat** okno, které se zobrazí, zadejte, nebo vyberte následující možnosti a potom klikněte na tlačítko **OK**:
     - **Název**: *myVnet1ToMyVnet2*
     - **Virtuální síť modelu nasazení**: vyberte **Classic**. 
     - **Předplatné**: Vyberte předplatné
     - **Virtuální síť**: klikněte na tlačítko **vyberte virtuální síť**, pak klikněte na tlačítko **myVnet2**.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že **povoleno** je vybrána.
    Žádné další nastavení použitá v tomto kurzu. Další informace o všech nastaveních partnerského vztahu, přečtěte si [spravovat virtuální sítě partnerských vztahů](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknutí na **OK** v předchozím kroku, **partnerský vztah přidat** okno se zavře a zobrazí **myVnet1 - partnerských vztahů** okno znovu. Za několik sekund partnerského vztahu, kterou jste vytvořili se zobrazí v okně. **Připojení** , je uvedena ve **stav partnerského vztahu** sloupec pro **myVnet1ToMyVnet2** partnerského vztahu, můžete vytvořit.

    Partnerského vztahu je nyní vytvořeno. Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
13. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
14. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-portal) tohoto článku.

## <a name="cli"></a>Vytvoření partnerského vztahu - rozhraní příkazového řádku Azure

1. [Nainstalujte](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 1.0 rozhraní příkazového řádku Azure k vytvoření virtuální sítě (klasické).
2. Otevřete relaci příkazového řádku a přihlaste se k Azure pomocí `azure login` příkaz.
3. Spuštění rozhraní příkazového řádku v režimu správy služby tak, že zadáte `azure config mode asm` příkaz.
4. Zadejte následující příkaz pro vytvoření virtuální sítě (klasické):
 
    ```azurecli
    azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

5. Vytvořte skupinu prostředků a virtuální síť (Resource Manager). Můžete použít rozhraní příkazového řádku 1.0 nebo 2.0 ([nainstalovat](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)). V tomto kurzu se používá 2.0 rozhraní příkazového řádku k vytvoření virtuální sítě (Resource Manager), protože 2.0 je možné použít k vytvoření partnerského vztahu. Provést následující bash rozhraní příkazového řádku skript z místního počítače pomocí rozhraní příkazového řádku verze 2.0.4 nainstalovaný nebo novější. Možnosti na spuštění bash skripty rozhraní příkazového řádku na klientovi Windows, najdete v části [běžící ve Windows Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Můžete také spustit skript prostředí cloudu Azure. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Klikněte **vyzkoušet** tlačítko ve skriptu, který způsobem, které vyvolá prostředí cloudu, který vám umožní přihlásit se k účtu Azure s. Chcete-li spustit skript, klikněte na tlačítko **kopie** tlačítko a vložení, její obsah do své cloudové prostředí, stiskněte `Enter`.

    ```azurecli-interactive
    #!/bin/bash

    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus

    # Create the virtual network (Resource Manager).
    az network vnet create \
      --name myVnet1 \
      --resource-group myResourceGroup \
      --location eastus \
      --address-prefix 10.0.0.0/16
    ```

6. Vytvoření virtuální sítě partnerský vztah mezi dvě virtuální sítě vytvořené pomocí různé modely nasazení. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<subscription id>` s ID vašeho předplatného. Pokud si nejste jisti Id předplatného, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je ID vašeho předplatného. Vložte upravené skriptu v relaci příkazového řádku a stiskněte klávesu `Enter`.

    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group myResourceGroup \
      --name myVnet1 \
      --query id --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
      --allow-vnet-access
    ```
7. Po spuštění skriptu, zkontrolujte partnerský vztah pro virtuální síť (Resource Manager). Zkopírujte následující příkaz, vložte jej v relaci příkazového řádku a stiskněte klávesu `Enter`:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    Výstup ukazuje **připojeno** v **PeeringState** sloupce. 

    Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
8. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
9. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-cli) v tomto článku.

## <a name="powershell"></a>Vytvoření partnerského vztahu – prostředí PowerShell

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduly. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci prostředí PowerShell.
3. V PowerShellu se přihlaste k Azure zadáním příkazu `Add-AzureAccount`.
4. Vytvoření virtuální sítě (klasické) pomocí prostředí PowerShell, musíte vytvořit novou nebo upravit existující, konfigurační soubor sítě. Zjistěte, jak [exportovat, aktualizovat a import konfiguračních souborů síť](virtual-networks-using-network-configuration-file.md). Soubor musí zahrnovat následující **VirtualNetworkSite** element pro virtuální síť použít v tomto kurzu:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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
5. Přihlaste se k Azure a vytvoření virtuální sítě (Resource Manager) tak, že zadáte `login-azurermaccount` příkaz. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Najdete v článku [oprávnění](#permissions) tohoto článku podrobnosti.
6. Vytvořte skupinu prostředků a virtuální síť (Resource Manager). Zkopírujte skript, vložte jej do prostředí PowerShell a potom stiskněte klávesu `Enter`.

    ```powershell
    # Create a resource group.
      New-AzureRmResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Vytvoření virtuální sítě partnerský vztah mezi dvě virtuální sítě vytvořené pomocí různé modely nasazení. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<subscription id>` s ID vašeho předplatného. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota **Id** ve vrácené výstupu je ID vašeho předplatného. Spustit skript, zkopírujte upravené skript z textového editoru a pak klikněte pravým tlačítkem v relaci prostředí PowerShell a potom stiskněte klávesu `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po spuštění skriptu, zkontrolujte partnerský vztah pro virtuální síť (Resource Manager). Zkopírujte následující příkaz, vložte ho do relace prostředí PowerShell a potom stiskněte klávesu `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Výstup ukazuje **připojeno** v **PeeringState** sloupce.

    Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

9. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
10. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-powershell) v tomto článku.
 
## <a name="permissions"></a>Oprávnění

Účty, které použijete k vytvoření virtuální sítě partnerského vztahu musí mít potřebné role nebo oprávnění. Například pokud dvě virtuální sítě s názvem myVnet1 a myVnet2 byly partnerský vztah, musí váš účet se přiřazenou následující minimální role nebo oprávnění pro každou virtuální síť:
    
|Virtuální síť|Model nasazení|Role|Oprávnění|
|---|---|---|---|
|myVnet1|Resource Manager|[Přispěvatel sítě](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classic|[Přispěvatel klasických sítí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|neuvedeno|
|myVnet2|Resource Manager|[Přispěvatel sítě](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classic|[Přispěvatel klasických sítí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Další informace o [předdefinované role](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) a přiřazení specifické oprávnění pro [vlastní role](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (pouze Resource Manager).

## <a name="delete"></a>Odstraňte prostředky
Po dokončení tohoto kurzu, můžete chtít odstranit z prostředků, které jste vytvořili v tomto kurzu, aby vám zbytečně nenabíhaly poplatky za používání. Odstranění skupiny prostředků se také odstraní všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Portál Azure

1. V dialogovém okně hledání portálu zadejte **myResourceGroup**. Ve výsledcích hledání klikněte na tlačítko **myResourceGroup**.
2. Na **myResourceGroup** okně klikněte **odstranit** ikonu.
3. Potvrďte odstranění, v **název skupiny prostředků typu** zadejte **myResourceGroup**a potom klikněte na **odstranit**.

### <a name="delete-cli"></a>Azure CLI

1. Pomocí Azure CLI 2.0 odstranit virtuální síť (Resource Manager) pomocí následujícího příkazu:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Pomocí Azure CLI 1.0 odstranit virtuální síť (klasická) pomocí následujících příkazů:

    ```azurecli
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Zadejte následující příkaz k odstranění virtuální sítě (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Pokud chcete odstranit virtuální sítě (klasické) v prostředí PowerShell, je třeba upravit existující konfigurační soubor sítě. Zjistěte, jak [exportovat, aktualizovat a import konfiguračních souborů síť](virtual-networks-using-network-configuration-file.md). Odeberte následující element VirtualNetworkSite virtuální sítě používá v tomto kurzu:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
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

## <a name="register"></a>Registrace pro partnerského vztahu preview globální virtuální sítě

Vytváření partnerských vztahů virtuálních sítí ve stejné oblasti je všeobecně dostupné. Partnerský vztah virtuální sítě v různých oblastech je aktuálně ve verzi preview. V tématu [aktualizace virtuální sítě](https://azure.microsoft.com/en-us/updates/?product=virtual-network) pro dostupné oblasti. Rovnocenných počítačů virtuálních sítí v oblastech, nejprve je nutné zaregistrovat verzi Preview, pomocí následujících kroků (v rámci předplatného je každá virtuální síť chcete rovnocenných počítačů v) pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure:

### <a name="powershell"></a>PowerShell

1. Nainstalujte nejnovější verzi modulu [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) pro PowerShell. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci prostředí PowerShell a přihlaste se k Azure pomocí `Login-AzureRmAccount` příkaz.
3. Registrujte předplatné, které každý virtuální síť, které chcete peer se ve verzi Preview zadáním následujících příkazů:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Potvrďte, že jste zaregistrováni ve verzi preview tak, že zadáte následující příkaz:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Neprovádějte kroky v části šablony portálu, rozhraní příkazového řádku Azure, PowerShell nebo správce prostředků tohoto článku, dokud **RegistrationState** výstupu se zobrazí po zadání příkazu předchozí je **registrovaná**  pro oba odběry.

### <a name="azure-cli"></a>Azure CLI

1. [Instalace a konfigurace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Ujistěte se, že používáte verzi 2.0.18 nebo vyšší rozhraní příkazového řádku Azure tak, že zadáte `az --version` příkaz. Pokud si nejste, nainstalujte nejnovější verzi.
3. Přihlaste se k Azure pomocí `az login` příkaz.
4. Registrovat verzi Preview zadáním následujících příkazů:

    ```azurecli-interactive
    az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
    az provider register --name Microsoft.Network
    ```

5. Potvrďte, že jste zaregistrováni ve verzi preview tak, že zadáte následující příkaz:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    Neprovádějte kroky v části šablony portálu, rozhraní příkazového řádku Azure, PowerShell nebo správce prostředků tohoto článku, dokud **RegistrationState** výstupu se zobrazí po zadání příkazu předchozí je **registrovaná**  pro oba odběry.

## <a name="next-steps"></a>Další postup

- Důkladně Seznamte se s důležité [partnerského vztahu omezení virtuální sítě a chování](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením virtuální sítě partnerský vztah pro produkční použití.
- Další informace o všech [partnerského vztahu nastavení virtuální sítě](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvořit rozbočovače a příčkou topologie sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah virtuální sítě.
