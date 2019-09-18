---
title: Konfigurace Azure Virtual Network (Classic) – soubor konfigurace sítě | Microsoft Docs
description: Naučte se vytvářet a upravovat virtuální sítě (klasické) pomocí exportu, změny a importu konfiguračního souboru sítě.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: ab4faa0f727469e27eb30af54f24036292ec3118
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058702"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurace virtuální sítě (klasické) pomocí konfiguračního souboru sítě
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a klasický](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby většina nových nasazení používala model nasazení Správce prostředků.

Můžete vytvořit a nakonfigurovat virtuální síť (Classic) pomocí konfiguračního souboru sítě pomocí rozhraní příkazového řádku (CLI) Azure Classic nebo Azure PowerShell. Pomocí konfiguračního souboru sítě nemůžete vytvořit nebo upravit virtuální síť pomocí modelu nasazení Azure Resource Manager. Azure Portal nelze použít k vytvoření nebo úpravě virtuální sítě (klasické) pomocí konfiguračního souboru sítě, můžete však použít Azure Portal k vytvoření virtuální sítě (Classic) bez použití konfiguračního souboru sítě.

Vytvoření a konfigurace virtuální sítě (Classic) se souborem konfigurace sítě vyžaduje export, změnu a import souboru.

## <a name="export"></a>Export konfiguračního souboru sítě

K exportu konfiguračního souboru sítě můžete použít PowerShell nebo rozhraní příkazového řádku Azure Classic. PowerShell exportuje soubor XML, zatímco Azure Classic CLI exportuje soubor JSON.

### <a name="powershell"></a>PowerShell
 
1. [Nainstalujte Azure PowerShell a přihlaste se k Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Změňte adresář (a ujistěte se, že existuje) a v následujícím příkazu zadejte název souboru a potom spusťte příkaz pro export konfiguračního souboru sítě:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure Classic CLI

1. [Nainstalujte rozhraní příkazového řádku Azure Classic](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Dokončete zbývající kroky z klasického příkazového řádku CLI.
2. Přihlaste se k Azure zadáním `azure login` příkazu.
3. Zajistěte, abyste byli v režimu ASM `azure config mode asm` zadáním příkazu.
4. Změňte adresář (a ujistěte se, že existuje) a v následujícím příkazu zadejte název souboru a potom spusťte příkaz pro export konfiguračního souboru sítě:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Vytvoření nebo úprava konfiguračního souboru sítě

Konfigurační soubor sítě je soubor XML (při použití PowerShellu) nebo soubor JSON (při použití klasického rozhraní příkazového řádku). Soubor můžete upravit v libovolném textovém editoru nebo editoru XML/JSON. Článek [Nastavení schématu soubor konfigurace sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx) obsahuje podrobnosti o všech nastaveních. Další vysvětlení nastavení najdete v tématu [Zobrazení virtuálních sítí a nastavení](manage-virtual-network.md#view-virtual-networks-and-settings). Změny, které provedete v souboru:

- Musí vyhovovat schématu nebo importovat konfigurační soubor sítě, se nezdaří.
- Přepište všechna stávající nastavení sítě pro vaše předplatné, proto při provádění úprav používejte mimořádně opatrní. Například odkazují na příklady souborů konfigurace sítě, které následují. Řekněme, že původní soubor obsahoval dvě instance **VirtualNetworkSite** , a Vy jste ho změnili, jak je znázorněno v příkladech. Při importu souboru Azure odstraní virtuální síť pro instanci **VirtualNetworkSite** , kterou jste odebrali v souboru. Tento zjednodušený scénář předpokládá, že ve virtuální síti nebyly žádné prostředky, jako kdyby existovala, virtuální síť se nepovedlo odstranit a import by se nezdařil.

> [!IMPORTANT]
> Azure považuje podsíť, která má něco nasazovat, jak se **používá**. Pokud se podsíť používá, nedá se změnit. Před úpravou informací o podsíti v konfiguračním souboru sítě přesuňte cokoliv, co jste nasadili do podsítě, do jiné podsítě, kterou neupravujete. Podrobnosti najdete v tématu [přesun virtuálního počítače nebo instance role do jiné podsítě](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="example-xml-for-use-with-powershell"></a>Příklad XML pro použití s prostředím PowerShell

Následující ukázkový konfigurační soubor sítě vytvoří virtuální síť s názvem *myVirtualNetwork* s adresním prostorem *10.0.0.0/16* v oblasti *východní USA* Azure. Virtuální síť obsahuje jednu podsíť s názvem *mySubnet* s předponou adresy *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

Pokud soubor konfigurace sítě, který jste exportovali, neobsahuje žádný obsah, můžete zkopírovat kód XML v předchozím příkladu a vložit ho do nového souboru.

### <a name="example-json-for-use-with-the-classic-cli"></a>Příklad formátu JSON pro použití s klasickým rozhraním CLI

Následující ukázkový konfigurační soubor sítě vytvoří virtuální síť s názvem *myVirtualNetwork* s adresním prostorem *10.0.0.0/16* v oblasti *východní USA* Azure. Virtuální síť obsahuje jednu podsíť s názvem *mySubnet* s předponou adresy *10.0.0.0/24*.

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

Pokud soubor konfigurace sítě, který jste exportovali, neobsahuje žádný obsah, můžete v předchozím příkladu zkopírovat JSON a vložit ho do nového souboru.

## <a name="import"></a>Import konfiguračního souboru sítě

K importu konfiguračního souboru sítě můžete použít PowerShell nebo rozhraní příkazového řádku Classic. PowerShell importuje soubor XML, zatímco klasický CLI naimportuje soubor JSON. Pokud import neproběhne úspěšně, zkontrolujte, zda soubor odpovídá [schématu konfigurace sítě](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Nainstalujte Azure PowerShell a přihlaste se k Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Podle potřeby změňte v následujícím příkazu adresář a název souboru a potom spusťte příkaz pro import konfiguračního souboru sítě:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure Classic CLI

1. [Nainstalujte rozhraní příkazového řádku Azure Classic](/cli/azure/install-classic-cli). Dokončete zbývající kroky z klasického příkazového řádku CLI.
2. Přihlaste se k Azure zadáním `azure login` příkazu.
3. Zajistěte, abyste byli v režimu ASM `azure config mode asm` zadáním příkazu.
4. Podle potřeby změňte v následujícím příkazu adresář a název souboru a potom spusťte příkaz pro import konfiguračního souboru sítě:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
