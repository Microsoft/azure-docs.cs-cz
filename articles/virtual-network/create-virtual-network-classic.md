---
title: Vytvoření virtuální sítě Azure (Classic) s více podsítěmi | Microsoft Docs
description: Naučte se, jak vytvořit virtuální síť (Classic) s několika podsítěmi v Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 50054379a3032a368a10932e15396373a3817cff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978915"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Vytvoření virtuální sítě (klasické) s několika podsítěmi

> [!IMPORTANT]
> Azure má dva [různé modely nasazení](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření prostředků a práci s nimi: Správce prostředků a Classic. Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje vytvořit většinu nových virtuálních sítí prostřednictvím modelu nasazení [Správce prostředků](quick-create-portal.md) .

V tomto kurzu se dozvíte, jak vytvořit základní virtuální síť Azure (Classic), která má samostatné veřejné a privátní podsítě. V podsíti můžete vytvořit prostředky Azure, jako jsou virtuální počítače a cloudové služby. Prostředky vytvořené ve virtuálních sítích (Classic) můžou vzájemně komunikovat a s prostředky v jiných sítích připojených k virtuální síti.

Přečtěte si další informace o všech nastaveních [virtuální sítě](manage-virtual-network.md) a [podsítě](virtual-network-manage-subnet.md) .

> [!WARNING]
> Pokud [je předplatné zakázané](../cost-management-billing/manage/subscription-disabled.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit), služba Azure okamžitě odstraní virtuální sítě (Classic). Virtuální sítě (Classic) se odstraní bez ohledu na to, jestli prostředky existují ve virtuální síti. Pokud později předplatné znovu povolíte, je nutné znovu vytvořit prostředky, které existovaly ve virtuální síti.

Virtuální síť (Classic) můžete vytvořit pomocí [Azure Portal](#portal), [rozhraní příkazového řádku Azure (CLI) 1,0](#azure-cli)nebo [PowerShellu](#powershell).

## <a name="portal"></a>Portál

1. V internetovém prohlížeči přejdete na [Azure Portal](https://portal.azure.com). Přihlaste se pomocí svého [účtu Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klikněte na **vytvořit prostředek** na portálu.
3. Do pole **Hledat na Marketplace** v horní části **nového** podokna, které se zobrazí, zadejte *virtuální síť* . Po zobrazení ve výsledcích hledání klikněte na **virtuální síť** .
4. V rozevíracím seznamu **Vyberte model nasazení** v zobrazeném podokně **Virtual Network** vyberte **Classic** a pak klikněte na **vytvořit**. 
5. V podokně **vytvořit virtuální síť (Classic)** zadejte následující hodnoty a potom klikněte na **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Name (Název)|myVnet|
    |Adresní prostor|10.0.0.0/16|
    |Název podsítě|Veřejné|
    |Rozsah adres podsítě|10.0.0.0/24|
    |Skupina prostředků|Nechejte možnost **vytvořit nové** a pak zadejte **myResourceGroup**.|
    |Předplatné a umístění|Vyberte své předplatné a umístění.

    Pokud s Azure začínáte, přečtěte si další informace o [skupinách prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [předplatných](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)a [umístěních](https://azure.microsoft.com/regions) (označovaných také jako *oblasti*).
4. Na portálu můžete při vytváření virtuální sítě vytvořit jenom jednu podsíť. V tomto kurzu vytvoříte druhou podsíť po vytvoření virtuální sítě. Později můžete vytvořit prostředky přístupné z Internetu ve **veřejné** podsíti. Také můžete vytvořit prostředky, které nejsou přístupné z Internetu v **privátní** podsíti. Druhou podsíť vytvoříte tak, že do pole **Hledat prostředky** v horní části stránky zadáte **myVnet** . Po zobrazení ve výsledcích hledání klikněte na **myVnet** .
5. Klikněte na **podsítě** (v části **Nastavení** ) v zobrazeném podokně **vytvořit virtuální síť (Classic)** .
6. Klikněte na **+ Přidat** v podokně **myVnet-podsítě** , které se zobrazí.
7. Jako **název** zadejte **Private** v podokně **Přidat podsíť** . Jako **Rozsah adres**zadejte **10.0.1.0/24** .  Klikněte na **OK**.
8. V podokně **myVnet – podsítě** můžete zobrazit **veřejné** a **privátní** podsítě, které jste vytvořili.
9. **Volitelné**: po dokončení tohoto kurzu možná budete chtít odstranit prostředky, které jste vytvořili, takže se vám neúčtují poplatky za použití:
    - V podokně **myVnet** klikněte na **Přehled** .
    - V podokně **myVnet** klikněte na ikonu **Odstranit** .
    - Kliknutím na **Ano** v poli **odstranit virtuální síť** potvrďte odstranění.

## <a name="azure-cli"></a>Azure CLI

1. Můžete buď [nainstalovat a nakonfigurovat rozhraní příkazového řádku Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo použít rozhraní příkazového řádku v rámci Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Chcete-li získat nápovědu pro příkazy rozhraní příkazového řádku, zadejte `azure <command> --help`. 
2. V relaci rozhraní příkazového řádku se přihlaste k Azure pomocí příkazu, který následuje. Pokud kliknete na tlačítko **vyzkoušet** v níže uvedeném poli, otevře se Cloud Shell. Můžete se přihlásit k předplatnému Azure bez zadání následujícího příkazu:

    ```azurecli-interactive
    azure login
    ```

3. Pokud chcete zajistit, aby se rozhraní příkazového řádku v režimu správy služby, zadejte následující příkaz:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Vytvořte virtuální síť s privátní podsítí:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Vytvoření veřejné podsítě v rámci virtuální sítě:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Zkontrolujte virtuální síť a podsítě:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Volitelné**: možná budete chtít odstranit prostředky, které jste vytvořili při dokončení tohoto kurzu, takže se vám neúčtují poplatky za použití:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> I když nemůžete zadat skupinu prostředků pro vytvoření virtuální sítě (Classic) pomocí rozhraní příkazového řádku, Azure vytvoří virtuální síť ve skupině prostředků s názvem *výchozí-síť*.

## <a name="powershell"></a>PowerShell

1. Nainstalujte nejnovější verzi modulu PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) . Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci PowerShellu.
3. V PowerShellu se přihlaste k Azure zadáním příkazu `Add-AzureAccount`.
4. Podle potřeby změňte následující cestu a název souboru a exportujte existující konfigurační soubor sítě:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Chcete-li vytvořit virtuální síť s veřejnými a soukromými podsítěmi, použijte libovolný textový editor a přidejte element **VirtualNetworkSite** , který následuje do konfiguračního souboru sítě.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Zkontrolujte úplné [schéma souboru konfigurace sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importovat konfigurační soubor sítě:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Import změněného konfiguračního souboru sítě může způsobit změny ve stávajících virtuálních sítích (Classic) v rámci vašeho předplatného. Ujistěte se, že jste přidali jenom předchozí virtuální síť a že v rámci předplatného neměníte ani neodebíráte žádné existující virtuální sítě. 

7. Zkontrolujte virtuální síť a podsítě:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Volitelné**: možná budete chtít odstranit prostředky, které jste vytvořili při dokončení tohoto kurzu, takže se vám neúčtují poplatky za využití. Pokud chcete virtuální síť odstranit, proveďte kroky 4-6 znovu, tentokrát odstraňte element **VirtualNetworkSite** , který jste přidali v kroku 5.
 
> [!NOTE]
> I když nemůžete zadat skupinu prostředků pro vytvoření virtuální sítě (Classic) v prostředí PowerShell, Azure vytvoří virtuální síť ve skupině prostředků s názvem *výchozí-síť*.

---

## <a name="next-steps"></a>Další kroky

- Další informace o všech nastaveních virtuální sítě a podsítě najdete v tématech [Správa virtuálních sítí](manage-virtual-network.md) a [Správa podsítí virtuální sítě](virtual-network-manage-subnet.md). Máte různé možnosti, jak používat virtuální sítě a podsítě v produkčním prostředí pro splnění různých požadavků.
- Vytvořte virtuální počítač s [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a připojte ho k existující virtuální síti.
- Pokud chcete propojit dvě virtuální sítě ve stejném umístění Azure, vytvořte [partnerský vztah virtuální sítě](create-peering-different-deployment-models.md) mezi virtuálními sítěmi. Můžete vytvořit partnerský vztah k virtuální síti (Správce prostředků) k virtuální síti (Classic), ale nemůžete vytvořit partnerský vztah mezi dvěma virtuálními sítěmi (Classic).
- Připojte virtuální síť k místní síti pomocí [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo okruhu [ExpressRoute Azure](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
