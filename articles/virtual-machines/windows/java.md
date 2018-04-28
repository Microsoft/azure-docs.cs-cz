---
title: Vytvářet a spravovat virtuální počítač Azure se používá Java | Microsoft Docs
description: Použijte k nasazení virtuálního počítače a všechny její Podpůrné prostředky Java a Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: e4c77514bb38b6c3c5766bdb4af22e5271c9bffa
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Vytvářet a spravovat virtuální počítače Windows v Azure pomocí Java

[Virtuální počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) vyžaduje několik podpůrných prostředků Azure. Tento článek popisuje vytváření, správu a odstraňování prostředky virtuálních počítačů pomocí Java. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte projekt Maven
> * Přidat závislosti
> * Vytvořit přihlašovací údaje
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Proveďte tyto kroky trvá asi 20 minut.

## <a name="create-a-maven-project"></a>Vytvořte projekt Maven

1. Pokud jste tak již neučinili, nainstalujte [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
2. Nainstalujte [Maven](http://maven.apache.org/download.cgi).
3. Vytvořte novou složku a projekt:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Přidat závislosti

1. V části `testAzureApp` složku, otevřete `pom.xml` souboru a přidejte konfiguraci sestavení na &lt;projektu&gt; umožňující vytvářet aplikace:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Přidáte závislosti, které jsou potřebné pro přístup k Azure Java SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency> 
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Uložte soubor.

## <a name="create-credentials"></a>Vytvořit přihlašovací údaje

Než začnete tento krok, ujistěte se, zda máte přístup k [objektu služby Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Také byste měli zaznamenat ID aplikace, ověřovací klíč a ID klienta, který budete potřebovat později.

### <a name="create-the-authorization-file"></a>Vytvoření souboru autorizace

1. Vytvořte soubor s názvem `azureauth.properties` a do ní přidejte tyto vlastnosti:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Nahraďte **&lt;id předplatného&gt;** s ID vašeho předplatného **&lt;id aplikace&gt;** s identifikátor aplikace služby Active Directory **&lt;ověřovací klíč&gt;** s klíč aplikace a **&lt;id klienta&gt;** s identifikátorem klienta.

2. Uložte soubor.
3. Nastavte proměnnou prostředí s názvem AZURE_AUTH_LOCATION ve vašem prostředí s úplnou cestou k souboru ověřování.

### <a name="create-the-management-client"></a>Vytvoření klienta správy

1. Otevřete `App.java` souboru pod `src\main\java\com\fabrikam` a zajistěte, aby tento příkaz balíček je v horní části:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. V části příkaz balíček přidat importovat příkazy:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Pokud chcete vytvořit přihlašovací údaje služby Active Directory, které je třeba, aby žádosti, tento kód vložte do hlavní metodu třídy aplikace:
   
    ```java
    try {    
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Vytvoření prostředků

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Musí být všechny prostředky obsažené v [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md).

Chcete-li zadat hodnoty pro aplikaci a vytvořte skupinu prostředků, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Vytvořit sadu dostupnosti

[Skupiny dostupnosti](tutorial-availability-sets.md) umožňují snadnější zachování virtuálních počítačů, které používá vaše aplikace.

Pokud chcete vytvořit skupinu dostupnosti, přidejte do bloku try v hlavní metodu tento kód:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Vytvoření veřejné IP adresy

A [veřejnou IP adresu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) je potřeba ke komunikaci s virtuálním počítačem.

Pokud chcete vytvořit veřejnou IP adresu pro virtuální počítač, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Virtuální počítač musí být v podsíti [virtuální síť](../../virtual-network/virtual-networks-overview.md).

Pokud chcete vytvořit podsíť a virtuální síť, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Vytvořit rozhraní sítě

Virtuální počítač vyžaduje síťové rozhraní pro komunikaci ve virtuální síti.

Pokud chcete vytvořit síťové rozhraní, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Teď, když jste vytvořili doprovodné materiály, můžete vytvořit virtuální počítač.

Pokud chcete vytvořit virtuální počítač, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> V tomto kurzu vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru ostatní Image, najdete v tématu [vyhledání a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a rozhraní příkazového řádku Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Pokud chcete použít stávající disk, místo image pořízenou prostřednictvím marketplace, použijte tento kód: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd") 
    .withSizeInGB(128) 
    .withSku(DiskSkuTypes.PremiumLRS) 
    .create(); 

azure.virtualMachines.define("myVM") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withExistingPrimaryNetworkInterface(networkInterface) 
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows) 
    .withExistingAvailabilitySet(availabilitySet) 
    .withSize(VirtualMachineSizeTypes.StandardDS1) 
    .create(); 
``` 

## <a name="perform-management-tasks"></a>Provádění úloh správy

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete vytvořit kód pro automatizaci úloh opakovaných nebo komplexní.

Když potřebujete udělat nic s virtuálním Počítačem, budete muset získat instanci ho. Tento kód vložte do bloku try hlavní metody:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

Chcete-li získat informace o virtuálním počítači, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();   
```

### <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Můžete zastavit virtuální počítač a ponechat jeho nastavení, ale nadále účtovat poplatek za ho nebo můžete zastavit virtuální počítač a jeho navrácení. Při zrušení jeho přidělení virtuálního počítače jsou končí deallocated a fakturace pro ni také všechny prostředky, které jsou s ním spojená.

Pokud chcete zastavit virtuální počítač bez rušení přidělení ho, přidejte do bloku try v hlavní metodu tento kód:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Pokud chcete zrušit přidělení virtuálního počítače, změna volání PowerOff tento kód:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Spusťte virtuální počítač

Pokud chcete spustit virtuální počítač, tento kód vložte do bloku try v hlavní metodu:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Změnit velikost virtuálního počítače

Mnoho aspektů nasazení měli zvážit při rozhodování o velikosti pro virtuální počítač. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md).  

Chcete-li změnit velikost virtuálního počítače, přidejte do bloku try v hlavní metodu tento kód:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Přidat datový disk k virtuálnímu počítači

Chcete-li přidat datový disk k virtuálnímu počítači, který je 2 GB velikost, protože má logické jednotky 0 a typu ukládání do mezipaměti v režimu ReadWrite, přidejte do bloku try v hlavní metodu tento kód:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtovat prostředky využívané v Azure, vždycky je dobrým zvykem odstranit prostředky, které už nejsou potřeba. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, je vše, co musíte udělat, odstraňte skupinu prostředků.

1. Pokud chcete odstranit skupinu prostředků, přidejte do bloku try v hlavní metodu tento kód:
   
```java
System.out.println("Deleting resources...");
azure.resourceGroups().deleteByName("myResourceGroup");
```

2. Soubor App.java uložte.

## <a name="run-the-application"></a>Spuštění aplikace

Dokončit má trvat přibližně pět minut, než tato Konzolová aplikace spustit úplně od začátku.

1. Ke spuštění aplikace, použijte tento příkaz Maven:

    ```
    mvn compile exec:java
    ```

2. Před stisknutím klávesy **Enter** zahájíte odstranění prostředků, může trvat několik minut na ověření vytváření prostředků na portálu Azure. Klikněte na tlačítko Stav nasazení zobrazíte informace o tomto nasazení.


## <a name="next-steps"></a>Další postup
* Další informace o používání [knihovny Azure libraries for Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

