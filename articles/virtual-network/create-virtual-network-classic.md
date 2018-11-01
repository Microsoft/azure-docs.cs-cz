---
title: Vytvoření virtuální sítě Azure (classic) s několika podsítěmi | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální síť (classic) s několika podsítěmi v Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
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
ms.openlocfilehash: e40648ef47b108050486d43eefdb1564786c053e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50421386"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Vytvoření virtuální sítě (klasické) s několika podsítěmi

> [!IMPORTANT]
> Azure má dva [různých modelů nasazení](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření a práci s nimi: Resource Manager a classic. Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje vytváření většina nové virtuální sítě [Resource Manageru](quick-create-portal.md) modelu nasazení.

V tomto kurzu zjistěte, jak vytvořit základní virtuální síť Azure (classic), který má samostatné veřejné a privátní podsítě. Nemůžete vytvářet prostředky Azure, jako jsou virtuální počítače a cloudové služby v podsíti. Prostředky vytvořené v rámci virtuální sítě (classic) můžou komunikovat mezi sebou a s prostředky v jiných sítích připojené k virtuální síti.

Další informace o všech [virtuální sítě](manage-virtual-network.md) a [podsítě](virtual-network-manage-subnet.md) nastavení.

> [!WARNING]
> Virtuální sítě (klasické) se okamžitě odstraní Azure při [předplatné je zakázané](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Bez ohledu na to, zda existují prostředky ve virtuální síti se odstraní virtuální sítě (classic). Pokud budete později předplatné znovu aktivovat, musí se znovu vytvořit prostředky, které existovaly ve virtuální síti.

Můžete vytvořit virtuální síť (classic) pomocí [webu Azure portal](#portal), [rozhraní příkazového řádku Azure (CLI) 1.0](#azure-cli), nebo [Powershellu](#powershell).

## <a name="portal"></a>Portál

1. V internetovém prohlížeči přejděte [webu Azure portal](https://portal.azure.com). Přihlaste se pomocí vaší [účtu Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klikněte na tlačítko **vytvořit prostředek** na portálu.
3. Zadejte *virtuální síť* v **Hledat na Marketplace** pole v horní části **nový** podokno, které se zobrazí. Klikněte na tlačítko **virtuální síť** když se zobrazí ve výsledcích hledání.
4. Vyberte **Classic** v **vybrat model nasazení** pole **virtuální sítě** podokno, které se zobrazí, klikněte na **vytvořit**. 
5. Zadejte následující hodnoty **vytvořit virtuální síť (classic)** podokně a pak klikněte na tlačítko **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVnet|
    |Adresní prostor|10.0.0.0/16|
    |Název podsítě|Public|
    |Rozsah adres podsítě|10.0.0.0/24|
    |Skupina prostředků|Ponechte **vytvořit nový** vybrali a pak zadejte **myResourceGroup**.|
    |Předplatné a umístění|Vyberte svoje předplatné a umístění.

    Pokud se službou Azure teprve začínáte, další informace o [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [předplatná](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), a [umístění](https://azure.microsoft.com/regions) (také označuje jako *oblastech*).
4. Na portálu můžete vytvořit jenom jednu podsíť, při vytváření virtuální sítě. V tomto kurzu vytvořte druhou podsíť po vytvoření virtuální sítě. Můžete například vytvořit později přístupné z Internetu prostředky v **veřejné** podsítě. Můžete také vytvořit prostředky, které nejsou přístupné z Internetu do **privátní** podsítě. Chcete-li vytvořit druhou podsíť, zadejte **myVnet** v **vyhledat prostředky** pole v horní části stránky. Klikněte na tlačítko **myVnet** když se zobrazí ve výsledcích hledání.
5. Klikněte na tlačítko **podsítě** (v **nastavení** části) na **vytvořit virtuální síť (classic)** podokno, které se zobrazí.
6. Klikněte na tlačítko **+ přidat** na **myVnet – podsítě** podokno, které se zobrazí.
7. Zadejte **privátní** pro **název** na **přidat podsíť** podokně. Zadejte **10.0.1.0/24** pro **rozsah adres**.  Klikněte na **OK**.
8. Na **myVnet – podsítě** podokně, zobrazí se **veřejné** a **privátní** podsítě, které jste vytvořili.
9. **Volitelné**: Po dokončení tohoto kurzu, můžete chtít odstranit prostředky, které jste vytvořili, tak, aby se vám neúčtovaly poplatky za využití:
    - Klikněte na tlačítko **přehled** na **myVnet** podokně.
    - Klikněte na tlačítko **odstranit** na ikonu **myVnet** podokně.
    - Potvrďte odstranění kliknutím na **Ano** v **odstranění virtuální sítě** pole.

## <a name="azure-cli"></a>Azure CLI

1. Můžete buď [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo pomocí rozhraní příkazového řádku v rámci Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Chcete-li získat nápovědu pro příkazy rozhraní příkazového řádku, zadejte `azure <command> --help`. 
2. V relaci příkazového řádku přihlaste se k Azure pomocí následujícího příkazu. Vyberete-li **vyzkoušet** do následujícího pole se otevře Cloud Shell. Ke svému předplatnému Azure, se můžete přihlásit bez zadáte následující příkaz:

    ```azurecli-interactive
    azure login
    ```

3. K zajištění, že rozhraní příkazového řádku je v režimu správy služby, zadejte následující příkaz:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Vytvoření virtuální sítě s privátní podsítě:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Vytvoření veřejné podsítě v rámci virtuální sítě:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Projděte si virtuální síť a podsítě:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Volitelné**: můžete chtít odstranit prostředky, které jste vytvořili po dokončení tohoto kurzu, aby se vám neúčtovaly poplatky za využívání:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> I když nelze zadat skupinu prostředků k vytvoření virtuální sítě (classic) pomocí rozhraní příkazového řádku, Azure vytvoří virtuální síť ve skupině prostředků s názvem *výchozí sítě*.

## <a name="powershell"></a>PowerShell

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modulu. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci Powershellu.
3. V PowerShellu se přihlaste k Azure zadáním příkazu `Add-AzureAccount`.
4. Přejít následující cestu a název souboru, podle potřeby, a potom exportovat existující konfigurační soubor sítě:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Vytvoření virtuální sítě se veřejné a privátní podsítě, můžete použít libovolný textový editor pro přidání **VirtualNetworkSite** prvek, který následuje soubor konfigurace sítě.

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

    Projděte si kompletní [schéma konfiguračního souboru sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importujte soubor konfigurace sítě:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Import souboru konfigurace sítě změněné může způsobit změny do existující virtuální sítě (classic) v rámci vašeho předplatného. Ujistěte se, jenom přidat předchozí virtuální síť a změníte nebo odeberte všechny existující virtuální sítě z předplatného. 

7. Projděte si virtuální síť a podsítě:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Volitelné**: můžete chtít odstranit prostředky, které jste vytvořili po dokončení tohoto kurzu, aby se vám neúčtovaly poplatky za využívání. Odstranit virtuální síť, dokončete kroků 4 až 6 znovu odebrat tento čas **VirtualNetworkSite** elementu, které jste přidali v kroku 5.
 
> [!NOTE]
> I když nelze zadat skupinu prostředků k vytvoření virtuální sítě (classic) v powershellu, Azure vytvoří virtuální síť ve skupině prostředků s názvem *výchozí sítě*.

---

## <a name="next-steps"></a>Další postup

- Další informace o všech nastavení podsítě a virtuální sítě najdete v tématu [Správa virtuálních sítí](manage-virtual-network.md) a [spravovat podsítě virtuální sítě](virtual-network-manage-subnet.md). Máte různé možnosti použití virtuální sítě a podsítě v produkčním prostředí pro splnění různých požadavků.
- Vytvoření [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálního počítače a pak ho připojíte k existující virtuální sítě.
- Pokud chcete propojit dvě virtuální sítě ve stejném umístění Azure, vytvořte [partnerský vztah virtuální sítě](create-peering-different-deployment-models.md) mezi virtuálními sítěmi. Vytvořit virtuální síť (Resource Manager) může partnerský vztah virtuální sítě (classic), ale nemůžete vytvořit partnerský vztah mezi dvěma virtuálními sítěmi (classic).
- Připojení virtuální sítě k místní síti pomocí [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) okruhu.
