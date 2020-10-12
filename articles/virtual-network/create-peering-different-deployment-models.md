---
title: Vytvoření partnerského vztahu virtuální sítě Azure – různé modely nasazení – stejné předplatné | Microsoft Docs
description: Naučte se, jak vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení Azure, které existují ve stejném předplatném Azure.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 148d57da549e8364620c8417cbd61d975cea1498
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87046096"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Vytvoření partnerského vztahu virtuálních sítí – různé modely nasazení, stejné předplatné

V tomto kurzu se naučíte vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Oba virtuální sítě existují ve stejném předplatném. Partnerský vztah dvou virtuálních sítí umožňuje prostředkům v různých virtuálních sítích vzájemně komunikovat se stejnou šířkou pásma a latencí, jako kdyby byly prostředky ve stejné virtuální síti. Přečtěte si další informace o [partnerském vztahu virtuálních sítí](virtual-network-peering-overview.md).

Postup vytvoření partnerského vztahu virtuální sítě se liší v závislosti na tom, jestli jsou virtuální sítě ve stejném nebo jiném předplatném, a na [modelu nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , prostřednictvím kterého se virtuální sítě vytvářejí. Přečtěte si, jak vytvořit partnerský vztah virtuální sítě v jiných scénářích kliknutím na scénář z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manager](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Obě Resource Manager](create-peering-different-subscriptions.md) |Různé|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models-subscriptions.md) |Různé|

Partnerský vztah virtuální sítě nelze vytvořit mezi dvěma virtuálními sítěmi nasazenými prostřednictvím modelu nasazení Classic. Pokud potřebujete propojit virtuální sítě, které byly vytvořeny prostřednictvím modelu nasazení Classic, můžete k propojení virtuálních sítí použít [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

Tento kurz je virtuálními sítěmi ve stejné oblasti. Můžete také vytvořit partnerský vztah virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region). Doporučujeme, abyste se seznámili s [požadavky a omezeními partnerských vztahů](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením partnerského vztahu virtuálních sítí.

K vytvoření partnerského vztahu virtuálních sítí můžete použít Azure Portal, [rozhraní příkazového řádku](#cli) Azure (CLI), Azure [PowerShell](#powershell)nebo šablonu Azure Resource Manager. Klikněte na některý z předchozích odkazů nástrojů a přejděte přímo k postupu při vytváření partnerského vztahu virtuálních sítí pomocí vašeho nástroje podle vlastního výběru.

## <a name="create-peering---azure-portal"></a>Vytvoření partnerských vztahů – Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Účet, pomocí kterého se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klikněte na **+ Nový**, pak na **sítě**a pak na **virtuální síť**.
3. V okně **vytvořit virtuální síť** zadejte nebo vyberte hodnoty pro následující nastavení a pak klikněte na **vytvořit**:
    - **Název**: *myVnet1*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: vyberte předplatné.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte *myResourceGroup* .
    - **Umístění**: *východní USA*
4. Klikněte na **+ Nový**. Do pole **Hledat na Marketplace** zadejte *Virtual Network*. Po zobrazení ve výsledcích hledání klikněte na **virtuální síť** .
5. V okně **virtuální síť** v poli **Vybrat model nasazení** vyberte **Classic** a pak klikněte na **vytvořit**.
6. V okně **vytvořit virtuální síť** zadejte nebo vyberte hodnoty pro následující nastavení a pak klikněte na **vytvořit**:
    - **Název**: *myVnet2*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: vyberte předplatné.
    - **Skupina prostředků**: vyberte **použít existující** a vyberte *myResourceGroup* .
    - **Umístění**: *východní USA*
7. Do pole **Hledat prostředky** v horní části portálu zadejte *myResourceGroup*. Po zobrazení ve výsledcích hledání klikněte na **myResourceGroup** . Zobrazí se okno pro skupinu prostředků **myresourcegroup** . Skupina prostředků obsahuje dvě virtuální sítě vytvořené v předchozích krocích.
8. Klikněte na **myVNet1**.
9. V zobrazeném okně **myVnet1** klikněte na **partnerské vztahy** ze svislého seznamu možností na levé straně okna.
10. V okně **myVnet1 – partnerské vztahy** , které se zobrazily, klikněte na **+ Přidat** .
11. V zobrazeném okně **Přidat partnerský vztah** zadejte nebo vyberte následující možnosti a pak klikněte na **OK**:
     - **Název**: *myVnet1ToMyVnet2*
     - **Model nasazení virtuální sítě**: vyberte **klasický**.
     - **Předplatné**: vyberte předplatné.
     - **Virtuální síť**: klikněte na **zvolit virtuální síť**a pak klikněte na **myVnet2**.
     - **Povolení přístupu k virtuální síti:** Ujistěte se, že je vybraná možnost **povoleno** .
    V tomto kurzu se žádná další nastavení nepoužívají. Pokud se chcete dozvědět o všech nastaveních partnerských vztahů, přečtěte si téma [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknutí na **OK** v předchozím kroku se zavře okno **Přidat partnerský vztah** a znovu se zobrazí okno **myVnet1-peering** . Po několika sekundách se partnerský vztah, který jste vytvořili, zobrazí v okně. **Připojeno** se zobrazí ve sloupci **stav partnerského vztahu** pro partnerský vztah **myVnet1ToMyVnet2** , který jste vytvořili.

    Partnerský vztah se teď navázal. Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nedokážou přeložit názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Volitelné**: i když se vytváření virtuálních počítačů v tomto kurzu nezabývá, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
14. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-portal) v tomto článku.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Vytvoření partnerského vztahu – Azure CLI

Pomocí klasického rozhraní příkazového řádku Azure a rozhraní příkazového řádku Azure CLI proveďte následující kroky. Kroky z Azure Cloud Shell můžete dokončit tak, že jednoduše vyberete tlačítko **vyzkoušet** v některém z následujících kroků nebo nainstalujete rozhraní příkazového [řádku Classic](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) a [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) a spustíte příkazy na místním počítači.

1. Pokud používáte Cloud Shell, přeskočte na krok 2, protože Cloud Shell vás automaticky přihlásí k Azure. Otevřete relaci příkazu a přihlaste se k Azure pomocí `azure login` příkazu.
2. Spusťte rozhraní příkazového řádku v režimu správy služby zadáním `azure config mode asm` příkazu.
3. Zadáním následujícího příkazu vytvořte virtuální síť (Classic):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Spusťte následující skript bash CLI pomocí rozhraní příkazového řádku, nikoli klasického rozhraní příkazového řádku. Možnosti spouštění skriptů bash CLI v počítačích s Windows najdete v tématu [instalace Azure CLI ve Windows](/cli/azure/install-azure-cli-windows).

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

5. Vytvořte partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení pomocí rozhraní příkazového řádku. Zkopírujte následující skript do textového editoru na svém počítači. Nahraďte `<subscription id>` ID vašeho předplatného. Pokud své ID předplatného neznáte, zadejte `az account show` příkaz. Hodnota pro **ID** ve výstupu je vaše ID vašeho předplatného. Vložte upravený skript do relace CLI a potom stiskněte klávesu `Enter` .

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

6. Po spuštění skriptu zkontrolujte partnerský vztah pro virtuální síť (Správce prostředků). Zkopírujte následující příkaz, vložte ho do relace CLI a pak stiskněte klávesu ENTER `Enter` :

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Výstup se zobrazí ve sloupci **PeeringState** ( **připojeno** ).

   Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nedokážou přeložit názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Volitelné**: i když se vytváření virtuálních počítačů v tomto kurzu nezabývá, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
8. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-cli) v tomto článku.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [AZ](https://www.powershellgallery.com/packages/Az/) Modules. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci PowerShellu.
3. V PowerShellu se přihlaste k Azure zadáním `Add-AzureAccount` příkazu. Účet, pomocí kterého se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints).
4. Pokud chcete vytvořit virtuální síť (Classic) pomocí PowerShellu, musíte vytvořit novou nebo upravit existující soubor konfigurace sítě. Naučte se [exportovat, aktualizovat a importovat síťové konfigurační soubory](virtual-networks-using-network-configuration-file.md). Soubor by měl obsahovat následující element **VirtualNetworkSite** pro virtuální síť, která se používá v tomto kurzu:

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
    > Import změněného konfiguračního souboru sítě může způsobit změny ve stávajících virtuálních sítích (Classic) v rámci vašeho předplatného. Ujistěte se, že jste přidali jenom předchozí virtuální síť a že v rámci předplatného neměníte ani neodebíráte žádné existující virtuální sítě.
5. Přihlaste se k Azure a vytvořte virtuální síť (Správce prostředků) zadáním `Connect-AzAccount` příkazu. Účet, pomocí kterého se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints).
6. Vytvořte skupinu prostředků a virtuální síť (Správce prostředků). Zkopírujte skript, vložte ho do PowerShellu a pak stiskněte klávesu `Enter` .

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

7. Vytvořte partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Zkopírujte následující skript do textového editoru na svém počítači. Nahraďte `<subscription id>` ID vašeho předplatného. Pokud své ID předplatného neznáte, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je vaše ID vašeho předplatného. Pokud chcete skript spustit, zkopírujte upravený skript z textového editoru, potom klikněte pravým tlačítkem myši v relaci PowerShellu a pak stiskněte klávesu `Enter` .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po spuštění skriptu zkontrolujte partnerský vztah pro virtuální síť (Správce prostředků). Zkopírujte následující příkaz, vložte ho do relace PowerShellu a pak stiskněte klávesu ENTER `Enter` :

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Výstup se zobrazí ve sloupci **PeeringState** ( **připojeno** ).

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nedokážou přeložit názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Volitelné**: i když se vytváření virtuálních počítačů v tomto kurzu nezabývá, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
10. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-powershell) v tomto článku.

## <a name="delete-resources"></a><a name="delete"></a>Odstranění prostředků

Po dokončení tohoto kurzu možná budete chtít odstranit prostředky, které jste v tomto kurzu vytvořili, takže se vám neúčtují poplatky za využití. Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jsou ve skupině prostředků.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Do vyhledávacího pole portálu zadejte **myResourceGroup**. Ve výsledcích hledání klikněte na **myResourceGroup**.
2. V okně **myResourceGroup** klikněte na ikonu **Odstranit** .
3. Odstranění potvrďte tak, že do pole **Zadejte název skupiny prostředků** zadáte **myResourceGroup**a pak kliknete na **Odstranit**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Pomocí Azure CLI odstraňte virtuální síť (Správce prostředků) pomocí následujícího příkazu:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Pomocí klasického rozhraní příkazového řádku odstraňte virtuální síť (Classic) následujícími příkazy:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Zadejte následující příkaz k odstranění virtuální sítě (Správce prostředků):

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Pokud chcete odstranit virtuální síť (Classic) pomocí PowerShellu, musíte upravit existující konfigurační soubor sítě. Naučte se [exportovat, aktualizovat a importovat síťové konfigurační soubory](virtual-networks-using-network-configuration-file.md). Odeberte následující element VirtualNetworkSite pro virtuální síť, která se používá v tomto kurzu:

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
    > Import změněného konfiguračního souboru sítě může způsobit změny ve stávajících virtuálních sítích (Classic) v rámci vašeho předplatného. Ujistěte se, že jste odebrali jenom předchozí virtuální síť a že neměníte ani neodebíráte žádné jiné existující virtuální sítě z vašeho předplatného.

## <a name="next-steps"></a>Další kroky

- Před vytvořením partnerského vztahu virtuálních sítí pro produkční použití důkladně se seznamte s důležitými [omezeními a chováním partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints) .
- Přečtěte si o [nastaveních partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
- Naučte se [vytvářet síťové topologie centra a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s využitím partnerského vztahu virtuálních sítí.
