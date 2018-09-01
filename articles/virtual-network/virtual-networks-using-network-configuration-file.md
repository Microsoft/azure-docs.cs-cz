---
title: Konfigurace služby Azure Virtual Network (classic) – soubor konfigurace sítě | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a upravovat virtuální sítě (klasické) Export, změnou a import souboru konfigurace sítě.
services: virtual-network
documentationcenter: ''
author: genlin
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
ms.openlocfilehash: 57ad5541bb7b61f8d26002168bb069fad3058965
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339070"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurace virtuální sítě (classic) pomocí souboru konfigurace sítě
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala model nasazení Resource Manager.

Můžete vytvořit a nakonfigurovat virtuální síť (klasická) s konfiguračním souboru sítě pomocí rozhraní příkazového řádku Azure (CLI) 1.0 nebo Azure Powershellu. Nejde vytvořit nebo upravit virtuální sítě pomocí modelu nasazení Azure Resource Manageru pomocí souboru konfigurace sítě. Na webu Azure portal k vytvoření nebo úpravě virtuální sítě (classic) pomocí souboru konfigurace sítě, ale můžete využívat na webu Azure portal k vytvoření virtuální sítě (classic), nelze použít bez použití konfiguračního souboru sítě.

Vytvoření a konfigurace virtuální sítě (klasické) s konfiguračním souboru sítě vyžaduje export, změna a import souboru.

## <a name="export"></a>Exportovat soubor konfigurace sítě

Chcete-li exportovat soubor konfigurace sítě můžete použít PowerShell nebo rozhraní příkazového řádku Azure. Prostředí PowerShell exportuje soubor XML, zatímco rozhraní příkazového řádku Azure exportuje do souboru json.

### <a name="powershell"></a>PowerShell
 
1. [Instalace Azure Powershellu a přihlaste se k Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Změňte adresář (a zajistit existuje) a název souboru v následujícím příkazu podle potřeby, spusťte příkaz a exportujte soubor konfigurace sítě:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Instalace Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dokončete zbývající kroky z příkazového řádku Azure CLI 1.0.
2. Přihlaste se k Azure zadáním `azure login` příkazu.
3. Zkontrolujte jestli pracujete v režimu asm tak, že zadáte `azure config mode asm` příkazu.
4. Změňte adresář (a zajistit existuje) a název souboru v následujícím příkazu podle potřeby, spusťte příkaz a exportujte soubor konfigurace sítě:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Vytvořte nebo upravte konfigurační soubor sítě

Soubor konfigurace sítě je soubor XML (při použití prostředí PowerShell) nebo soubor json (při použití Azure CLI). Můžete upravit soubor v textu nebo editoru XML nebo json. [Síťová nastavení konfiguračního souboru schématu](https://msdn.microsoft.com/library/azure/jj157100.aspx) článek obsahuje podrobnosti pro všechna nastavení. Další vysvětlení nastavení najdete v tématu [zobrazit virtuální sítě a nastavení](manage-virtual-network.md#view-virtual-networks-and-settings). Změny provedené v souboru:

- Musíte dodržovat schématu nebo import, který soubor konfigurace sítě se nezdaří.
- Přepsat všechny existující nastavení sítě pro vaše předplatné, takže buďte extrémně opatrní při provádění změn. Například odkazovat příklad konfigurační soubory sítě, které následují. Řekněme, že původní soubor obsahoval dvě **VirtualNetworkSite** instance můžete změnit a, jak je znázorněno v příkladu. Při importu souboru Azure odstraní virtuální sítě pro **VirtualNetworkSite** instance, které jste odebrali v souboru. Tento zjednodušený scénář předpokládá, že nebyly žádné prostředky ve virtuální síti, jako kdyby existovalo, virtuální síť se nepodařilo odstranit, a import selže.

> [!IMPORTANT]
> Azure bude považovat za podsíť, která nabízí něco nasadili do ní jako **používá**. Pokud podsíť se používá, nelze změnit. Před úpravou informace o podsíti v konfiguračním souboru sítě, přesuňte cokoli, co jste nasadili do podsítě do jiné podsítě, která se nemění. Zobrazit [přesunutí virtuálního počítače nebo Role Instance k jiné podsíti](virtual-networks-move-vm-role-to-subnet.md) podrobnosti.

### <a name="example-xml-for-use-with-powershell"></a>Ukázkový soubor XML pro použití s využitím Powershellu

Soubor konfigurace sítě následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s adresním prostorem *10.0.0.0/16* v *USA – východ* Azure oblast. Virtuální síť obsahuje jednu podsíť s názvem *mySubnet* s předponou adresy *10.0.0.0/24*.

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

Obsahuje-li soubor konfigurace sítě, který jste exportovali žádný obsah, můžete zkopírovat kód XML v předchozím příkladu a vložte ho do nového souboru.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Ukázkový soubor JSON pro použití s Azure CLI 1.0

Soubor konfigurace sítě následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s adresním prostorem *10.0.0.0/16* v *USA – východ* Azure oblast. Virtuální síť obsahuje jednu podsíť s názvem *mySubnet* s předponou adresy *10.0.0.0/24*.

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

Obsahuje-li soubor konfigurace sítě, který jste exportovali žádný obsah, můžete zkopírujte json z předchozího příkladu a vložte ho do nového souboru.

## <a name="import"></a>Importujte soubor konfigurace sítě

Importovat soubor konfigurace sítě můžete použít PowerShell nebo rozhraní příkazového řádku Azure. Powershellu importuje soubor XML, zatímco rozhraní příkazového řádku Azure importuje soubor json. Pokud nebude import úspěšný, potvrďte, že soubor splňuje [schéma konfigurace sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Instalace Azure Powershellu a přihlaste se k Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Změňte adresář a název souboru v následujícím příkazu podle potřeby, pomocí příkazu importujte soubor konfigurace sítě:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Instalace Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dokončete zbývající kroky z příkazového řádku Azure CLI 1.0.
2. Přihlaste se k Azure zadáním `azure login` příkazu.
3. Zkontrolujte jestli pracujete v režimu asm tak, že zadáte `azure config mode asm` příkazu.
4. Změňte adresář a název souboru v následujícím příkazu podle potřeby, pomocí příkazu importujte soubor konfigurace sítě:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
