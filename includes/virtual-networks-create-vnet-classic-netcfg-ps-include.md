---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 4ae4c3100ae13fdb05e17974b433b247128c1a50
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805049"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Postup vytvoření virtuální sítě pomocí souboru konfigurace sítě z prostředí PowerShell.
Azure používá soubor xml pro definování všechny virtuální sítě, které jsou k dispozici k odběru. Tento soubor stáhnout, upravit nebo odstranit existující virtuální sítě do něj a vytvořit nové virtuální sítě. V tomto kurzu zjistěte, jak ke stažení tohoto souboru, označuje jako soubor konfigurace (nebo netcfg) sítě a upravit ho k vytvoření nové virtuální sítě. Další informace o konfiguračního souboru sítě, najdete v článku [schéma konfigurace virtuální sítě Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Pokud chcete vytvořit virtuální síť s souboru netcfg pomocí prostředí PowerShell, proveďte následující kroky:

1. Pokud jste prostředí Azure PowerShell nikdy nepoužívali, proveďte kroky v [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) článek, a přihlaste se k Azure a vybrat své předplatné.
2. Z konzoly Azure PowerShell, použijte **Get-AzureVnetConfig** rutiny stáhněte soubor konfigurace sítě do adresáře v počítači tak, že spustíte následující příkaz: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Očekávaný výstup:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Otevřete soubor, který jste uložili v kroku 2 pomocí jakékoli aplikace XML nebo textovém editoru a podívejte se **<VirtualNetworkSites>** element. Pokud máte žádné sítě, které už vytvořený, každá síť se zobrazí jako vlastní **<VirtualNetworkSite>** element.
4. Pokud chcete vytvořit virtuální síť v tomto scénáři, přidejte následující kód XML pouze v části **<VirtualNetworkSites>** element:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Uložte soubor konfigurace sítě.
6. Z konzoly Azure PowerShell, použijte **Set-AzureVnetConfig** rutiny nahrát konfiguračního souboru sítě tak, že spustíte následující příkaz: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Vrátí výstup:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Pokud **OperationStatus** není *úspěšné* ve vrácené výstupu, zkontrolujte soubor xml pro chyby a dokončení kroku 6 znovu.

7. Z konzoly Azure PowerShell, použijte **Get-AzureVnetSite** rutiny ověřte, zda bude nová síť byl přidán spuštěním následujícího příkazu: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Vrácený výstup (zkrácení) zahrnuje následující text:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
