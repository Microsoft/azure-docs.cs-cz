---
title: Vytvoření partnerského vztahu virtuální sítě Azure – různé modely nasazení – stejné předplatné | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení Azure, které existují ve stejném předplatném Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023255"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Vytvoření partnerského vztahu virtuální sítě – různé modely nasazení, stejné předplatné

V tomto kurzu se naučíte vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Obě virtuální sítě existují ve stejném předplatném. Partnerský vztah dvou virtuálních sítí umožňuje prostředkům v různých virtuálních sítích vzájemnou komunikaci se stejnou šířkou pásma a latencí, jako by byly prostředky ve stejné virtuální síti. Další informace o [partnerském vztahu virtuální sítě](virtual-network-peering-overview.md).

Kroky k vytvoření partnerského vztahu virtuální sítě se liší v závislosti na tom, zda virtuální sítě jsou ve stejné nebo jiné, odběry a které [model nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě jsou vytvořeny prostřednictvím. Zjistěte, jak vytvořit partnerský vztah virtuální sítě v jiných scénářích kliknutím na scénář z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manager](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Obě Resource Manager](create-peering-different-subscriptions.md) |Různé|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models-subscriptions.md) |Různé|

Partnerský vztah virtuální sítě nelze vytvořit mezi dvěma virtuálními sítěmi nasazenými prostřednictvím klasického modelu nasazení. Pokud potřebujete připojit virtuální sítě, které byly vytvořeny prostřednictvím klasického modelu nasazení, můžete k připojení virtuálních sítí použít bránu Azure [VPN Gateway.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Tento kurz partnery virtuálnísítě ve stejné oblasti. Můžete také partnerské virtuální sítě v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region). Doporučujeme seznámit se s [požadavky a omezeními partnerského vztahu](virtual-network-manage-peering.md#requirements-and-constraints) před partnerským vztahem virtuálních sítí.

K vytvoření partnerského vztahu virtuální sítě můžete použít portál Azure, [rozhraní příkazového řádku](#cli) Azure (CLI), Azure [PowerShell](#powershell)nebo šablonu Azure Resource Manageru. Kliknutím na některý z předchozích odkazů na nástroje přejdete přímo k krokům pro vytvoření partnerského vztahu virtuální sítě pomocí zvoleného nástroje.

## <a name="create-peering---azure-portal"></a>Vytvoření partnerského vztahu – portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Účet, pomocí kterých se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klepněte na tlačítko **+ Nový**, klepněte na **položku Síť**a potom na **položku Virtuální síť**.
3. V okně **Vytvořit virtuální síť** zadejte nebo vyberte hodnoty pro následující nastavení a klikněte na **Vytvořit**:
    - **Jméno**: *myVnet1*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte *myResourceGroup*
    - **Umístění**: *Východní USA*
4. Klikněte na **+ Nový**. Do pole **Hledat na marketplace** zadejte virtuální *síť*. Když se zobrazí ve výsledcích hledání, klikněte na **Virtuální síť.**
5. V okně **Virtuální síť** vyberte **klasické** v poli Vybrat **model nasazení** a klepněte na tlačítko **Vytvořit**.
6. V okně **Vytvořit virtuální síť** zadejte nebo vyberte hodnoty pro následující nastavení a klikněte na **Vytvořit**:
    - **Jméno**: *myVnet2*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné
    - **Skupina prostředků**: Vyberte **Použít existující** a vyberte *myResourceGroup*
    - **Umístění**: *Východní USA*
7. Do pole **Hledat zdroje** v horní části portálu zadejte *myResourceGroup*. Klikněte na **myResourceGroup,** když se zobrazí ve výsledcích hledání. Pro skupinu prostředků **myresourcegroup** se zobrazí okno. Skupina prostředků obsahuje dvě virtuální sítě vytvořené v předchozích krocích.
8. Klikněte na **myVNet1**.
9. V okně **myVnet1,** které se zobrazí, klepněte na **položku Partnerské listy** ze svislého seznamu možností na levé straně okna.
10. V okně **myVnet1 - Peerings,** které se objevilo, klikněte na **+ Přidat**
11. V okně **Přidat partnerský vztah,** který se zobrazí, zadejte nebo vyberte následující možnosti, klepněte na **OK**:
     - **Název**: *myVnet1ToMyVnet2*
     - **Model nasazení virtuální sítě**: Vyberte **možnost Classic**.
     - **Předplatné**: Vyberte předplatné
     - **Virtuální síť**: Klikněte na **Vybrat virtuální síť**, pak klikněte na **myVnet2**.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že je vybrána možnost **Povoleno.**
    V tomto kurzu se nepoužívají žádná další nastavení. Informace o všech nastaveních partnerského vztahu najdete v části [Správa partnerských partnerských uživatelů virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
12. Po klepnutí na **tlačítko OK** v předchozím kroku se zavře okno Přidat **partnerský vztah** a znovu uvidíte okno **myVnet1 - Peerings.** Po několika sekundách se v okně zobrazí vytvořený partnerský vztah. **Připojeno** je uvedeno ve **sloupci PEERING STATUS** pro partnerský vztah **myVnet1ToMyVnet2,** který jste vytvořili.

    Partnerský vztah je nyní vytvořen. Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Volitelné:** I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
14. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [Odstranit prostředky](#delete-portal) tohoto článku.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Vytvoření partnerského vztahu – Azure CLI

Pomocí klasického příkazového příkazového příkazu Azure a příkazového příkazového příkazového příkazu Azure postupujte jako příkazové nebo úplné. Kroky z prostředí Azure Cloud Shell můžete provést tak, že vyberete tlačítko **Try It** v některém z následujících kroků nebo instalací [klasického příkazového příkazu příkazového příkazu](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) a [příkazového příkazu](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) a spuštěním příkazů v místním počítači.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2, protože Cloud Shell vás automaticky přihlásí do Azure. Otevřete relaci příkazů a přihlaste se k Azure pomocí příkazu. `azure login`
2. Spusťte příkaz cli v `azure config mode asm` režimu správy služeb zadáním příkazu.
3. Chcete-li vytvořit virtuální síť (klasickou), zadejte následující příkaz:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Spusťte následující bash CLI skript pomocí příkazového příkazového příkazu, nikoli klasické CLI. Možnosti spuštění skriptů bash CLI v počítači se systémem Windows naleznete [v tématu Instalace příkazového příkazového příkazu Azure v systému Windows](/cli/azure/install-azure-cli-windows).

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

5. Vytvořte partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení pomocí vodicí ho. Zkopírujte následující skript do textového editoru v počítači. Nahraďte `<subscription id>` id předplatného. Pokud ID předplatného neznáte, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je vaše ID předplatného. Vložte upravený skript do relace příkazového `Enter`příkazu a stiskněte klávesu .

   ```azurecli-interactive
   # Get the ID for VNet1.
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

6. Po spuštění skriptu zkontrolujte partnerský vztah pro virtuální síť (Resource Manager). Zkopírujte následující příkaz, vložte jej do relace `Enter`příkazu cli a stiskněte klávesu :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Výstup zobrazuje **Připojeno** ve sloupci **PeeringState.**

   Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Volitelné:** I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
8. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranění prostředků](#delete-cli) v tomto článku.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

1. Nainstalujte nejnovější verzi modulů PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [Az.](https://www.powershellgallery.com/packages/Az/) Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci Prostředí PowerShell.
3. V PowerShellu se přihlaste `Add-AzureAccount` k Azure zadáním příkazu. Účet, pomocí kterých se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#requirements-and-constraints).
4. Chcete-li vytvořit virtuální síť (klasickou) s prostředím PowerShell, musíte vytvořit nový nebo upravit existující konfigurační soubor sítě. Přečtěte [si, jak exportovat, aktualizovat a importovat konfigurační síťové soubory](virtual-networks-using-network-configuration-file.md). Soubor by měl obsahovat následující prvek **VirtualNetworkSite** pro virtuální síť použitou v tomto kurzu:

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
    > Import změněného konfiguračního souboru sítě může způsobit změny stávajících virtuálních sítí (klasické) ve vašem předplatném. Ujistěte se, že přidáte jenom předchozí virtuální síť a že z předplatného nezměníte ani neodeberete žádné existující virtuální sítě.
5. Přihlaste se k Azure a vytvořte virtuální `Connect-AzAccount` síť (Správce prostředků) zadáním příkazu. Účet, pomocí kterých se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#requirements-and-constraints).
6. Vytvořte skupinu prostředků a virtuální síť (Správce prostředků). Zkopírujte skript, vložte ho do `Enter`Prostředí PowerShell a stiskněte klávesu .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Vytvořte partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Zkopírujte následující skript do textového editoru v počítači. Nahraďte `<subscription id>` id předplatného. Pokud ID předplatného neznáte, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je Vaše ID předplatného. Chcete-li skript spustit, zkopírujte upravený skript z textového editoru, klepněte `Enter`pravým tlačítkem myši v relaci prostředí PowerShell a stiskněte klávesu .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po spuštění skriptu zkontrolujte partnerský vztah pro virtuální síť (Resource Manager). Zkopírujte následující příkaz, vložte ho do relace `Enter`PowerShellu a stiskněte klávesu :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Výstup zobrazuje **Připojeno** ve sloupci **PeeringState.**

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Volitelné:** I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
10. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranění prostředků](#delete-powershell) v tomto článku.

## <a name="delete-resources"></a><a name="delete"></a>Odstranění prostředků

Po dokončení tohoto kurzu můžete chtít odstranit prostředky, které jste vytvořili v kurzu, takže vám nebudou účtovány poplatky za využití. Odstraněním skupiny prostředků také odstraníte všechny prostředky, které jsou ve skupině prostředků.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portál Azure

1. Do vyhledávacího pole portálu zadejte **myResourceGroup**. Ve výsledcích hledání klikněte na **myResourceGroup**.
2. V okně **myResourceGroup** klikněte na ikonu **Odstranit.**
3. Chcete-li odstranění potvrdit, zadejte do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** položku **myResourceGroup**a klepněte na tlačítko **Odstranit**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Pomocí azure cli odstranit virtuální síť (Resource Manager) s následujícím příkazem:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Pomocí klasického příkazového příkazu k odstranění virtuální sítě (klasické) pomocí následujících příkazů:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Chcete-li odstranit virtuální síť (Správce prostředků), zadejte následující příkaz:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Chcete-li odstranit virtuální síť (klasickou) pomocí prostředí PowerShell, je nutné upravit existující konfigurační soubor sítě. Přečtěte [si, jak exportovat, aktualizovat a importovat konfigurační síťové soubory](virtual-networks-using-network-configuration-file.md). Odeberte následující prvek VirtualNetworkSite pro virtuální síť použitou v tomto kurzu:

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
    > Import změněného konfiguračního souboru sítě může způsobit změny stávajících virtuálních sítí (klasické) ve vašem předplatném. Ujistěte se, že odeberete jenom předchozí virtuální síť a že z předplatného nezměníte ani neodeberete žádné jiné existující virtuální sítě.

## <a name="next-steps"></a>Další kroky

- Před vytvořením partnerského vztahu virtuální sítě pro produkční použití se důkladně seznamte s důležitými [omezeními a chováním partnerského vztahu](virtual-network-manage-peering.md#requirements-and-constraints) virtuální sítě.
- Informace o všech [nastaveních partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvořit topologii sítě rozbočovače a paprsku](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) pomocí partnerského vztahu virtuální sítě.
