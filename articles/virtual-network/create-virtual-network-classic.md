---
title: Vytvoření virtuální sítě Azure (klasické) s několika podsítěmi | Microsoft Docs
description: Naučte se vytvořit virtuální síť s více podsítěmi v Azure (klasické).
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
ms.date: 07/31/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: a2e2075a735b63588352f3bcdc97e2d6410660fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365419"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Vytvoření virtuální sítě (klasické) s několika podsítěmi

> [!IMPORTANT]
> Azure má dva [různé modely nasazení](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro vytváření a práci s prostředky: Resource Manager a Klasický model. Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje vytvoření většina nové virtuální sítě prostřednictvím [Resource Manager](quick-create-portal.md) modelu nasazení.

V tomto kurzu zjistěte, jak vytvořit základní virtuální síť Azure (klasické) s oddělené veřejné a privátní podsítě. Můžete vytvořit prostředky Azure, jako jsou virtuální počítače a cloudové služby v podsíti. Prostředky vytvořené ve virtuální sítě (klasické) mohou komunikovat navzájem a s prostředky v jiných sítích připojené k virtuální síti.

Další informace o všech [virtuální sítě](manage-virtual-network.md) a [podsíť](virtual-network-manage-subnet.md) nastavení.

> [!WARNING]
> Virtuální sítě (klasické) se okamžitě odstraní Azure při [předplatné je zakázané](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Bez ohledu na to, jestli existují prostředky ve virtuální síti, se odstraní virtuální sítě (klasické). Pokud později znovu povolíte předplatné, je nutné znovu vytvořit prostředky, které existovaly ve virtuální síti.

Vytvoříte virtuální sítě (klasické) pomocí [portál Azure](#portal), [rozhraní příkazového řádku Azure (CLI) 1.0](#azure-cli), nebo [prostředí PowerShell](#powershell).

## <a name="portal"></a>Portál

1. V internetovém prohlížeči, přejděte na [portál Azure](https://portal.azure.com). Přihlaste se pomocí vaší [účet Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klikněte na tlačítko **vytvořit prostředek** na portálu.
3. Zadejte *virtuální síť* v **vyhledávání na webu Marketplace** pole v horní části **nový** podokně, který se zobrazí. Klikněte na tlačítko **virtuální síť** při zobrazí ve výsledcích hledání.
4. Vyberte **Classic** v **vybrat model nasazení** pole **virtuální sítě** podokně, který se zobrazí, pak klikněte na tlačítko **vytvořit**. 
5. Zadejte následující hodnoty **vytvořit virtuální síť (klasická)** podokně a pak klikněte na **vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVnet|
    |Adresní prostor|10.0.0.0/16|
    |Název podsítě|Public|
    |Rozsah adres podsítě|10.0.0.0/24|
    |Skupina prostředků|Nechte **vytvořit nový** vybrané a potom zadejte **myResourceGroup**.|
    |Předplatné a umístění|Vyberte vaše předplatné a umístění.

    Pokud jste Azure ještě nepoužívali, další informace o [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [odběry](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), a [umístění](https://azure.microsoft.com/regions) (také označuje jako *oblasti*).
4. Na portálu můžete vytvořit jenom jednu podsíť, když vytvoříte virtuální síť. V tomto kurzu vytvoříte druhou podsíť po vytvoření virtuální sítě. Můžete vytvořit později přístupné z Internetu prostředky v **veřejné** podsítě. Můžete také vytvořit prostředky, které nejsou přístupné z Internetu v **privátní** podsítě. Chcete-li vytvořit druhou podsíť, zadejte **myVnet** v **vyhledávání prostředků** pole v horní části stránky. Klikněte na tlačítko **myVnet** při zobrazí ve výsledcích hledání.
5. Klikněte na tlačítko **podsítě** (v **nastavení** část) na **vytvořit virtuální síť (klasická)** podokně, který se zobrazí.
6. Klikněte na tlačítko **+ přidat** na **myVnet - podsítě** podokně, který se zobrazí.
7. Zadejte **privátní** pro **název** na **přidat podsíť** podokně. Zadejte **10.0.1.0/24** pro **rozsahu adres**.  Klikněte na **OK**.
8. Na **myVnet - podsítě** podokně se zobrazí **veřejné** a **privátní** podsítě, které jste vytvořili.
9. **Volitelné**: Po dokončení tohoto kurzu můžete chtít odstranit prostředky, které jste vytvořili, tak, aby vám zbytečně nenabíhaly poplatky za používání:
    - Klikněte na tlačítko **přehled** na **myVnet** podokně.
    - Klikněte **odstranit** ikonu na **myVnet** podokně.
    - Potvrďte odstranění, klikněte na tlačítko **Ano** v **virtuální sítě odstranit** pole.

## <a name="azure-cli"></a>Azure CLI

1. Můžete buď [instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo pomocí rozhraní příkazového řádku v prostředí cloudu Azure. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Chcete-li získat nápovědu pro příkazy rozhraní příkazového řádku, zadejte `azure <command> --help`. 
2. V relaci příkazového řádku přihlaste se do Azure pomocí příkazu, který následuje dále. Pokud kliknete na tlačítko **vyzkoušet** do pole níže, otevře se prostředí cloudu. K předplatnému Azure, se můžete přihlásit bez zadáte následující příkaz:

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
    
6. Zkontrolujte virtuální sítě a podsítě:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Volitelné**: můžete chtít odstranit prostředky, které jste vytvořili po dokončení tohoto kurzu, tak, aby vám zbytečně nenabíhaly poplatky za používání:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> I když nelze zadat skupinu prostředků pro vytvoření virtuální sítě (klasické) pomocí rozhraní příkazového řádku, Azure vytvoří virtuální síť ve skupině prostředků s názvem *výchozí sítě*.

## <a name="powershell"></a>PowerShell

1. Nainstalujte nejnovější verzi prostředí PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) modulu. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci prostředí PowerShell.
3. V PowerShellu se přihlaste k Azure zadáním příkazu `Add-AzureAccount`.
4. Změnit následující cestu a název souboru, podle potřeby, a poté exportovat existující konfigurační soubor sítě:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Pokud chcete vytvořit virtuální síť s veřejné a privátní podsítě, pomocí libovolného textového editoru přidejte **VirtualNetworkSite** prvek, který následuje do konfiguračního souboru sítě.

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

    Přečtěte si celý [schéma konfiguračního souboru sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Import konfiguračního souboru sítě:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Import konfiguračního souboru změněné sítě může způsobit změny existující virtuální sítě (klasické) v rámci vašeho předplatného. Ujistěte se, můžete přidat pouze předchozí virtuální sítě a změníte nebo odeberte všechny existující virtuální sítě ze svého předplatného. 

7. Zkontrolujte virtuální sítě a podsítě:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Volitelné**: můžete chtít odstranit prostředky, které jste vytvořili po dokončení tohoto kurzu, tak, aby vám zbytečně nenabíhaly poplatky za používání. Pokud chcete odstranit virtuální síť, dokončení kroky 4 až 6 znovu, tento čas odebrání **VirtualNetworkSite** elementu, které jste přidali v kroku 5.
 
> [!NOTE]
> I když nelze zadat skupinu prostředků pro vytvoření virtuální sítě (klasické) pomocí prostředí PowerShell, Azure vytvoří virtuální síť ve skupině prostředků s názvem *výchozí sítě*.

---

## <a name="next-steps"></a>Další postup

- Další informace o všech virtuální síť a podsíť nastavení najdete v tématu [spravovat virtuální sítě](manage-virtual-network.md) a [spravovat podsítě virtuální sítě](virtual-network-manage-subnet.md). Máte různé možnosti pro splňují odlišné požadavky pomocí virtuální sítě a podsítě v produkčním prostředí.
- Vytvoření [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuálního počítače a připojte ho k existující virtuální síť.
- Pro připojení dvou virtuálních sítí ve stejné oblasti Azure, vytvořte [partnerský vztah virtuální sítě](create-peering-different-deployment-models.md) mezi virtuálními sítěmi. Virtuální síť (Resource Manager) může rovnocenných počítačů k virtuální síti (klasické), ale nemůžete vytvořit partnerský vztah mezi dvěma virtuálními sítěmi (klasické).
- Připojit virtuální síť k místní síti pomocí [brány VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) okruh.
