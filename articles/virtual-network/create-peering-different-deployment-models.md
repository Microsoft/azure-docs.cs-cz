---
title: Vytvoření Azure partnerský vztah virtuální sítě – různé modely nasazení, stejné předplatné | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi vytvořenými pomocí různých Azure modelů nasazení, které existují ve stejném předplatném Azure.
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
ms.date: 11/15/2018
ms.author: jdial;anavin
ms.openlocfilehash: bd2efce831fa98b3a4543d67d247d04dc8fc9d04
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853179"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Vytvoření partnerského vztahu virtuálních sítí – různých modelů nasazení, stejné předplatné

V tomto kurzu zjistíte, jak vytvořit virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi vytvořenými pomocí různých modelů nasazení. Obě virtuální sítě existovat ve stejném předplatném. Partnerský vztah dvou virtuálních sítí umožňuje prostředků v různých virtuálních sítích komunikovat mezi sebou stejnou šířku pásma a čekací doba jakoby byly ve stejné virtuální síti. Další informace o [partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

Postup vytvoření partnerského vztahu virtuálních sítí se liší v závislosti na tom, jestli jsou virtuální sítě ve stejné nebo jiné, předplatných a které [model nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě se vytvářejí prostřednictvím. Zjistěte, jak vytvořit virtuální síť vytvoření partnerského vztahu v jiných scénářích kliknutím na scénář z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manageru](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Obě Resource Manageru](create-peering-different-subscriptions.md) |Odlišné|
|[Jedna Resource Manager, druhá classic](create-peering-different-deployment-models-subscriptions.md) |Odlišné|

Partnerský vztah virtuální sítě nejde vytvořit mezi dvěma virtuálními sítěmi, které jsou nasazené prostřednictvím modelu nasazení classic. Pokud potřebujete k propojení virtuálních sítí, které byly obě vytvořené prostřednictvím modelu nasazení classic, můžete použít Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) k propojení virtuálních sítí.

V tomto kurzu partnerský vztah virtuálních sítí ve stejné oblasti. Můžete také vytvořit partnerský vztah virtuálních sítí v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region). Je doporučeno, aby měli seznámit s [partnerského vztahu požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints) před partnerský vztah virtuálních sítí.

Můžete použít [webu Azure portal](#portal), Azure [rozhraní příkazového řádku](#cli) (CLI) Azure [PowerShell](#powershell), nebo [šablony Azure Resource Manageru](#template)k vytvoření partnerského vztahu virtuálních sítí. Klikněte na tlačítko Předchozí odkazy nástroje můžete přejít přímo na kroky pro vytvoření partnerského vztahu virtuálních sítí pomocí nástrojů podle výběru.

## <a name="create-peering---azure-portal"></a>Vytvoření partnerského vztahu – Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#requirements-and-constraints).
2. Klikněte na tlačítko **+ nová**, klikněte na tlačítko **sítě**, pak klikněte na tlačítko **virtuální síť**.
3. V **vytvořit virtuální síť** okno, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:
    - **Název**: *myVnet1*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: vyberte své předplatné
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroup*
    - **Umístění**: *USA – východ*
4. Klikněte na **+ Nový**. V **Hledat na Marketplace** zadejte *virtuální síť*. Klikněte na tlačítko **virtuální síť** když se zobrazí ve výsledcích hledání.
5. V **virtuální síť** okně vyberte **Classic** v **vybrat model nasazení** pole a potom klikněte na tlačítko **vytvořit**.
6. V **vytvořit virtuální síť** okno, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:
    - **Název**: *myVnet2*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: vyberte své předplatné
    - **Skupina prostředků**: vyberte **použít existující** a vyberte *myResourceGroup*
    - **Umístění**: *USA – východ*
7. V **vyhledat prostředky** pole v horní části portálu zadejte *myResourceGroup*. Klikněte na tlačítko **myResourceGroup** když se zobrazí ve výsledcích hledání. Zobrazí se okno pro **myresourcegroup** skupinu prostředků. Skupina prostředků obsahuje dvě virtuální sítě vytvořené v předchozích krocích.
8. Klikněte na tlačítko **myVNet1**.
9. V **myVnet1** okno, které se zobrazí, klikněte na tlačítko **partnerské vztahy** z svislý seznam možností na levé straně okna.
10. V **myVnet1 - partnerské vztahy** okno, které se objevily, klikněte na tlačítko **+ přidat**
11. V **přidat partnerský vztah** okno, které se zobrazí, zadejte, nebo vyberte následující možnosti a potom klikněte na tlačítko **OK**:
     - **Název**: *myVnet1ToMyVnet2*
     - **Model nasazení virtuální sítě**: vyberte **Classic**.
     - **Předplatné**: vyberte své předplatné
     - **Virtuální síť**: klikněte na tlačítko **zvolit virtuální síť**, pak klikněte na tlačítko **myVnet2**.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že **povoleno** zaškrtnuto.
    Žádná ostatní nastavení se používají v tomto kurzu. Další informace o všech nastavení partnerského vztahu, přečtěte si [spravovat partnerské vztahy virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
12. Po kliknutí na tlačítko **OK** v předchozím kroku, **přidat partnerský vztah** zavře se okno a zobrazí **myVnet1 - partnerské vztahy** okno znovu. Po několika sekundách se partnerský vztah, kterou jste vytvořili se zobrazí v okně. **Připojené** je uveden v **stav partnerského vztahu** sloupce **myVnet1ToMyVnet2** partnerský vztah, můžete vytvořit.

    Partnerský vztah je nyní navázán. Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, nejsou mezi virtuálními sítěmi překládat názvy prostředků ve virtuálních sítích. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
13. **Volitelné**: když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvořit virtuální počítač v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověřit připojení.
14. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, dokončete kroky [odstraňte prostředky](#delete-portal) části tohoto článku.

## <a name="cli"></a>Vytvoření partnerského vztahu – rozhraní příkazového řádku Azure

Proveďte následující kroky pomocí Azure classic CLI a Azure CLI. Kroky v Azure Cloud Shell, můžete dokončit právě výběrem **vyzkoušet** tlačítko v některém z následujících kroků, nebo po instalaci [rozhraní příkazového řádku classic](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) a [rozhraní příkazového řádku](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) a spuštění příkazů v místním počítači.

1. Pokud používáte Cloud Shell, přejděte ke kroku 2, protože službě Cloud Shell se automaticky přihlásí můžete do Azure. Otevřete relaci příkazového řádku a přihlášení do Azure s využitím `azure login` příkazu.
2. Spuštění rozhraní příkazového řádku v režimu správy služby tak, že zadáte `azure config mode asm` příkazu.
3. Zadejte následující příkaz k vytvoření virtuální sítě (classic):

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Spusťte následující skript rozhraní příkazového řádku prostředí bash pomocí rozhraní příkazového řádku, nikoli classic rozhraní příkazového řádku. Možnosti na spuštěný bash skripty rozhraní příkazového řádku na počítači s Windows, najdete v části [nainstalovat rozhraní příkazového řádku Azure na Windows](/cli/azure/install-azure-cli-windows).

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

5. Vytvořte virtuální síť vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení pomocí rozhraní příkazového řádku. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<subscription id>` svým ID předplatného. Pokud si nejste jisti ID vašeho předplatného, zadejte `az account show` příkazu. Hodnota pro **id** ve výstupu je ID vašeho předplatného. Vložte upravený skript v relaci příkazového řádku a potom stiskněte klávesu `Enter`.

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

6. Po spuštění skriptu zkontrolujte partnerský vztah virtuální sítě (Resource Manager). Zkopírujte následující příkaz, vložte ho do relace prostředí rozhraní příkazového řádku a potom stiskněte klávesu `Enter`:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   Zobrazí se výstup **připojeno** v **PeeringState** sloupce.

   Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, nejsou mezi virtuálními sítěmi překládat názvy prostředků ve virtuálních sítích. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
7. **Volitelné**: když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvořit virtuální počítač v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověřit připojení.
8. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, dokončete kroky v [odstraňte prostředky](#delete-cli) v tomto článku.

## <a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) a [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) moduly. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci Powershellu.
3. V prostředí PowerShell, přihlaste se k Azure zadáním `Add-AzureAccount` příkazu. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#requirements-and-constraints).
4. Vytvoření virtuální sítě pomocí prostředí PowerShell (classic), musíte vytvořit novou nebo upravit existující soubor konfigurace sítě. Zjistěte, jak [export, aktualizovat a import konfiguračních souborech sítě](virtual-networks-using-network-configuration-file.md). Soubor by měl obsahovat následující **VirtualNetworkSite** – element pro virtuální sítě použité v tomto kurzu:

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
    > Import souboru konfigurace sítě změněné může způsobit změny do existující virtuální sítě (classic) v rámci vašeho předplatného. Ujistěte se, jenom přidat předchozí virtuální síť a změníte nebo odeberte všechny existující virtuální sítě z předplatného.
5. Přihlásit k Azure a vytvořte virtuální síť (Resource Manager) tak, že zadáte `Connect-AzureRmAccount` příkazu. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#requirements-and-constraints).
6. Vytvořte skupinu prostředků a virtuální síť (Resource Manager). Zkopírujte skript, vložte ho do prostředí PowerShell a potom stiskněte klávesu `Enter`.

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

7. Vytvořte virtuální síť vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<subscription id>` svým ID předplatného. Pokud si nejste jisti ID vašeho předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota pro **Id** ve vrácené výstupu je ID vašeho předplatného. Spustit skript, zkopírujte upravený skript z textový editor a pak klikněte pravým tlačítkem v relaci Powershellu a stiskněte klávesu `Enter`.

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Po spuštění skriptu zkontrolujte partnerský vztah virtuální sítě (Resource Manager). Zkopírujte následující příkaz, vložte ho do relace prostředí PowerShell a potom stiskněte klávesu `Enter`:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Zobrazí se výstup **připojeno** v **PeeringState** sloupce.

    Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, nejsou mezi virtuálními sítěmi překládat názvy prostředků ve virtuálních sítích. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

9. **Volitelné**: když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvořit virtuální počítač v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověřit připojení.
10. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, dokončete kroky v [odstraňte prostředky](#delete-powershell) v tomto článku.

## <a name="delete"></a>Odstranit prostředky

Po dokončení tohoto kurzu, můžete chtít odstranit prostředky, které jste vytvořili v tomto kurzu, takže se vám neúčtovaly poplatky za využívání. Odstranění skupiny prostředků se odstraní také všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Azure Portal

1. Portálového vyhledávacího pole zadejte **myResourceGroup**. Ve výsledcích hledání klikněte na tlačítko **myResourceGroup**.
2. Na **myResourceGroup** okna, klikněte na tlačítko **odstranit** ikonu.
3. Potvrďte odstranění, v **zadejte název skupiny prostředků** zadejte **myResourceGroup**a potom klikněte na tlačítko **odstranit**.

### <a name="delete-cli"></a>Azure CLI

1. Pomocí rozhraní příkazového řádku Azure CLI můžete odstranit virtuální síť (Resource Manager) pomocí následujícího příkazu:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Pomocí rozhraní příkazového řádku classic odstranit virtuální síť (klasická) pomocí následujících příkazů:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. Zadejte následující příkaz, který odstranit virtuální síť (Resource Manager):

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroup -Force
    ```

2. Pokud chcete odstranit virtuální síť (classic) pomocí prostředí PowerShell, je třeba upravit existující konfigurační soubor sítě. Zjistěte, jak [export, aktualizovat a import konfiguračních souborech sítě](virtual-networks-using-network-configuration-file.md). Odeberte virtuální sítě v tomto kurzu používá následující element VirtualNetworkSite:

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
    > Import souboru konfigurace sítě změněné může způsobit změny do existující virtuální sítě (classic) v rámci vašeho předplatného. Ujistěte se, jenom odebrat předchozí virtuální sítě a změníte nebo z předplatného odebrat ostatní existující virtuální sítě.

## <a name="next-steps"></a>Další postup

- Důkladně Seznamte se s důležité [omezení partnerských vztahů virtuálních sítí a chování](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením partnerský vztah pro produkční použití.
- Další informace o všech [nastavení partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvoření hvězdicové síťové topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s využitím partnerského vztahu virtuální sítě.
