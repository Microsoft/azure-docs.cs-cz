---
title: Nakonfigurujte virtuální síť Azure (klasický) - sítě konfigurační soubor | Microsoft Docs
description: Naučte se vytvářet a upravovat virtuální sítě (klasické) exportováním, změna a Import konfiguračního souboru sítě.
services: virtual-network
documentationcenter: ''
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 4349f6ef28ef8667802a81df1fee133ff3f56e51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurace virtuální sítě (klasické) pomocí konfiguračního souboru sítě
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala model nasazení Resource Manager.

Můžete vytvořit a nakonfigurovat virtuální síť (klasická) soubor konfigurace sítě pomocí rozhraní příkazového řádku Azure (CLI) 1.0 nebo Azure PowerShell. Nelze vytvořit nebo upravit virtuální sítě pomocí modelu nasazení Azure Resource Manager pomocí konfiguračního souboru sítě. Portál Azure k vytvoření nebo úprava virtuální sítě (klasické) pomocí konfiguračního souboru sítě, ale na portálu Azure můžete použít k vytvoření virtuální sítě (klasické), nelze použít bez použití konfiguračního souboru sítě.

Vytváření a konfigurace virtuální sítě (klasické) s konfiguračním souborem sítě vyžaduje export, změna a import souboru.

## <a name="export"></a>Exportovat soubor konfigurace sítě

Prostředí PowerShell nebo rozhraní příkazového řádku Azure slouží k exportu konfigurace souborů v síti. Prostředí PowerShell exportuje soubor XML, zatímco Azure CLI exportuje soubor json.

### <a name="powershell"></a>PowerShell
 
1. [Nainstalovat Azure PowerShell a přihlaste se k Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Změňte adresář (a ujistěte se, existuje) a název souboru v následujícím příkazu podle potřeby, spusťte příkaz pro export konfiguračního souboru sítě:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Nainstalovat Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dokončete zbývající kroky z příkazového řádku Azure CLI 1.0.
2. Přihlaste se k Azure tak, že zadáte `azure login` příkaz.
3. Zkontrolujte, zda pracujete v režimu asm zadáním `azure config mode asm` příkaz.
4. Změňte adresář (a ujistěte se, existuje) a název souboru v následujícím příkazu podle potřeby, spusťte příkaz pro export konfiguračního souboru sítě:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Vytvářet nebo upravovat soubor konfigurace sítě

Soubor konfigurace sítě je soubor XML (při použití prostředí PowerShell) nebo soubor json (při použití Azure CLI). Můžete upravit soubor v textu nebo editoru XML nebo json. [Síťová nastavení schéma konfiguračního souboru](https://msdn.microsoft.com/library/azure/jj157100.aspx) článek obsahuje podrobnosti pro všechna nastavení. Další vysvětlení nastavení najdete v tématu [zobrazit virtuální sítě a nastavení](manage-virtual-network.md#view-virtual-networks-and-settings). Změny provedené u souboru:

- Musíte dodržovat schéma nebo Import konfiguračního souboru sítě selžou.
- Přepsat všechny existující nastavení sítě pro vaše předplatné, proto buďte velmi opatrní při provádění změn. Například referenční příklad sítě konfigurační soubory, které podle. Řekněme, původní soubor obsahoval dvě **VirtualNetworkSite** instance a změnil, jak je znázorněno v příkladech. Při importu souboru Azure odstraní virtuální sítě **VirtualNetworkSite** instance, které jste odebrali v souboru. Tento zjednodušený scénář předpokládá, že nebyly žádné prostředky ve virtuální síti, jako kdyby existovalo, virtuální síť se nepodařilo odstranit, a dojde k selhání importu.

> [!IMPORTANT]
> Azure zvažuje podsíť, která se má něco nasadili do ní jako **používá**. Pokud podsíť je používána, nemůže být upraven. Před změnou informace o podsíti v konfiguračním souboru sítě, přesuňte všechny položky, které jste nasadili do podsítě do jiné podsítě, kterou nemění. V tématu [přesunutí virtuálního počítače nebo Role Instance na jiné podsíti](virtual-networks-move-vm-role-to-subnet.md) podrobnosti.

### <a name="example-xml-for-use-with-powershell"></a>Příklad XML pro použití v prostředí PowerShell

Konfiguračního souboru sítě následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s adresním prostorem z *10.0.0.0/16* v *východní USA* oblast Azure. Virtuální síť obsahuje jednu podsíť s názvem *mySubnet* s předponu adresy z *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Obsahuje-li konfiguračního souboru sítě, které jste exportovali žádný obsah, můžete zkopírovat soubor XML v předchozím příkladu a vložte jej do nového souboru.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Příklad JSON pro použití s Azure CLI 1.0

Konfiguračního souboru sítě následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s adresním prostorem z *10.0.0.0/16* v *východní USA* oblast Azure. Virtuální síť obsahuje jednu podsíť s názvem *mySubnet* s předponu adresy z *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Obsahuje-li konfiguračního souboru sítě, které jste exportovali žádný obsah, můžete zkopírovat json v předchozím příkladu a vložte jej do nového souboru.

## <a name="import"></a>Importovat soubor konfigurace sítě

Import konfiguračního souboru sítě můžete použít PowerShell nebo rozhraní příkazového řádku Azure. Prostředí PowerShell importuje soubor XML, zatímco Azure CLI importuje soubor json. Pokud se import nezdaří, zkontrolujte, že soubor splňuje [schéma konfigurace sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Nainstalovat Azure PowerShell a přihlaste se k Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Změňte adresář a název souboru v následujícím příkazu podle potřeby, spusťte příkaz pro import konfiguračního souboru sítě:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Nainstalovat Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dokončete zbývající kroky z příkazového řádku Azure CLI 1.0.
2. Přihlaste se k Azure tak, že zadáte `azure login` příkaz.
3. Zkontrolujte, zda pracujete v režimu asm zadáním `azure config mode asm` příkaz.
4. Změňte adresář a název souboru v následujícím příkazu podle potřeby, spusťte příkaz pro import konfiguračního souboru sítě:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
