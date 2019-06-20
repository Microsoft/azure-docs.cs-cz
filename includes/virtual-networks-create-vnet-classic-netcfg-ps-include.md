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
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174924"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Vytvoření virtuální sítě pomocí souboru konfigurace sítě v Powershellu
Azure používá soubor xml k definování všechny virtuální sítě, které jsou k dispozici na předplatné. Stažení tohoto souboru, upravte ho a upravíte nebo odstraníte existující virtuální sítě a vytvářet nové virtuální sítě. V tomto kurzu se naučíte, jak stáhnout tento soubor říká soubor konfigurace (nebo netcfg) sítě a upravte ho a vytvořte novou virtuální síť. Další informace o konfiguračním souboru sítě, najdete v článku [schéma konfigurace virtuální sítě Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Vytvoření virtuální sítě pomocí souboru netcfg pomocí Powershellu, proveďte následující kroky:

1. Pokud jste prostředí Azure PowerShell nikdy nepoužívali, proveďte kroky v [instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) článek, a přihlaste se k Azure a vyberte své předplatné.
2. Z konzoly Azure Powershellu, použijte **Get-AzureVnetConfig** rutina pro stažení konfiguračního souboru sítě do adresáře v počítači spuštěním následujícího příkazu: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Očekávaný výstup:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Otevřete soubor, který jste si uložili v kroku 2 pomocí jakékoli aplikace XML nebo textovém editoru a vyhledejte  **\<VirtualNetworkSites >** elementu. Pokud máte jakékoli sítě již vytvořené, každá síť se zobrazí jako vlastní  **\<VirtualNetworkSite >** elementu.
4. Vytvoření virtuální sítě je popsáno v tomto scénáři, přidejte následující kód XML přímo pod  **\<VirtualNetworkSites >** element:

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
6. Z konzoly Azure Powershellu, použijte **Set-AzureVnetConfig** rutiny nahrát soubor konfigurace sítě spuštěním následujícího příkazu: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Vrátí výstup:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Pokud **stav OperationStatus** není *Succeeded* ve výstupu vráceného souboru xml pro chyby a dokončení kroku 6 znovu zkontrolovat.

7. Z konzoly Azure Powershellu, použijte **Get-AzureVnetSite** rutiny ověřte, že byla přidána nová síť spuštěním následujícího příkazu: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Vrácené (zkrácený) výstup zahrnuje následující text:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
