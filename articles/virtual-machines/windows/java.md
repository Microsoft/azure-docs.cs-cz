---
title: Vytvoření a Správa virtuálního počítače Azure pomocí Java
description: Pomocí jazyků Java a Azure Resource Manager nasaďte virtuální počítač a všechny jeho podpůrné prostředky.
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: f571dc5f4f3aee2405d4968b050bd2dd92a709db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012744"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Vytváření a správa virtuálních počítačů s Windows v Azure pomocí Java

[Virtuální počítač Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potřebuje několik pomocných prostředků Azure. Tento článek popisuje vytváření, správu a odstraňování prostředků virtuálních počítačů pomocí jazyka Java. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření projektu Maven
> * Přidat závislosti
> * Vytvořit pověření
> * Vytvoření prostředků
> * Provádění úloh správy
> * Odstranění prostředků
> * Spuštění aplikace

Provedení těchto kroků trvá přibližně 20 minut.

## <a name="create-a-maven-project"></a>Vytvoření projektu Maven

1. Pokud jste to ještě neudělali, nainstalujte [Java](/azure/developer/java/fundamentals/java-jdk-long-term-support).
2. Nainstalujte [Maven](https://maven.apache.org/download.cgi).
3. Vytvořte novou složku a projekt:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Přidat závislosti

1. Ve `testAzureApp` složce otevřete `pom.xml` soubor a přidejte do projektu konfiguraci sestavení, &lt; &gt; aby bylo možné sestavit aplikaci:

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

2. Přidejte závislosti potřebné pro přístup k sadě Azure Java SDK.

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

## <a name="create-credentials"></a>Vytvořit pověření

Než začnete tento krok, ujistěte se, že máte přístup k [instančnímu objektu služby Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Měli byste také zaznamenat ID aplikace, ověřovací klíč a ID tenanta, které budete potřebovat v pozdějším kroku.

### <a name="create-the-authorization-file"></a>Vytvoření autorizačního souboru

1. Vytvořte soubor s názvem `azureauth.properties` a přidejte do něj tyto vlastnosti:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Nahraďte **&lt; ID &gt;** předplatného identifikátorem vašeho předplatného, **&lt; ID &gt; aplikace** s identifikátorem aplikace služby Active **&lt; &gt;** Directory, klíčem pro ověřování a klíčovým klíčem aplikace a **&lt; ID &gt; tenanta** s identifikátorem tenanta.

2. Uložte soubor.
3. V prostředí nastavte proměnnou prostředí s názvem AZURE_AUTH_LOCATION s úplnou cestou k ověřovacímu souboru.

### <a name="create-the-management-client"></a>Vytvořit klienta pro správu

1. Otevřete `App.java` soubor v části a ujistěte se, `src\main\java\com\fabrikam` že je tento příkaz balíčku v horní části:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. V rámci příkazu Package přidejte tyto příkazy importu:
   
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

2. Pokud chcete vytvořit přihlašovací údaje služby Active Directory, které potřebujete k vytvoření žádostí, přidejte tento kód do metody Main třídy App:
   
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

Všechny prostředky musí být obsaženy ve [skupině prostředků](../../azure-resource-manager/management/overview.md).

Chcete-li zadat hodnoty pro aplikaci a vytvořit skupinu prostředků, přidejte tento kód do bloku try v metodě Main:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Vytvoření skupiny dostupnosti

[Skupiny dostupnosti](tutorial-availability-sets.md) usnadňují údržbu virtuálních počítačů používaných vaší aplikací.

Chcete-li vytvořit skupinu dostupnosti, přidejte tento kód do bloku try v metodě Main:

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

[Veřejná IP adresa](../../virtual-network/public-ip-addresses.md) je potřeba ke komunikaci s virtuálním počítačem.

Pro vytvoření veřejné IP adresy pro virtuální počítač přidejte tento kód do bloku try v metodě Main:

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

Virtuální počítač musí být v podsíti [virtuální sítě](../../virtual-network/virtual-networks-overview.md).

Pokud chcete vytvořit podsíť a virtuální síť, přidejte tento kód do bloku try v metodě Main:

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

### <a name="create-the-network-interface"></a>Vytvoření síťového rozhraní

Virtuální počítač potřebuje ke komunikaci s virtuální sítí síťové rozhraní.

Chcete-li vytvořit síťové rozhraní, přidejte tento kód do bloku try v metodě Main:

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

Teď, když jste vytvořili všechny podpůrné prostředky, můžete vytvořit virtuální počítač.

Chcete-li vytvořit virtuální počítač, přidejte tento kód do bloku try v metodě Main:

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
> V tomto kurzu se vytvoří virtuální počítač s verzí operačního systému Windows Server. Další informace o výběru dalších imagí najdete v tématu [navigace a výběr imagí virtuálních počítačů Azure pomocí prostředí Windows PowerShell a rozhraní příkazového řádku Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Pokud chcete použít existující disk místo image Marketplace, použijte tento kód: 

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

Během životního cyklu virtuálního počítače možná budete potřebovat provádět úlohy správy, jako jsou spuštění, zastavení nebo odstranění virtuálního počítače. Kromě toho můžete chtít vytvořit kód pro automatizaci opakujících se nebo složitých úloh.

Pokud potřebujete s virtuálním počítačem něco udělat, musíte získat jeho instanci. Přidejte tento kód do testovaného bloku metody Main:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Získat informace o virtuálním počítači

Chcete-li získat informace o virtuálním počítači, přidejte tento kód do bloku try v metodě Main:

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

Můžete zastavit virtuální počítač a zachovat všechna jeho nastavení, ale nadále se vám bude účtovat, nebo můžete virtuální počítač zastavit a zrušit jeho přidělení. Když je virtuální počítač uvolněný, oddělují se i všechny prostředky, které jsou k němu přidružené, a pro něj končí fakturace.

Chcete-li zastavit virtuální počítač bez jeho přidělení, přidejte tento kód do bloku try v metodě Main:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Pokud chcete zrušit přidělení virtuálního počítače, změňte volání stavu PowerOff na tento kód:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Spuštění virtuálního počítače

Chcete-li spustit virtuální počítač, přidejte tento kód do bloku try v metodě Main:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Změna velikosti virtuálního počítače

Při rozhodování o velikosti vašeho virtuálního počítače je třeba vzít v úvahu mnoho aspektů nasazení. Další informace najdete v tématu [velikosti virtuálních počítačů](../sizes.md).  

Chcete-li změnit velikost virtuálního počítače, přidejte tento kód do bloku try v metodě Main:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Přidání datového disku k virtuálnímu počítači

Chcete-li přidat datový disk do virtuálního počítače, který má velikost 2 GB, má logickou jednotku 0 a typ zápisu do mezipaměti, přidejte tento kód do bloku try v metodě Main:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Odstranění prostředků

Vzhledem k tomu, že se vám účtují prostředky používané v Azure, je vždy vhodné odstranit prostředky, které už nepotřebujete. Pokud chcete odstranit virtuální počítače a všechny podpůrné prostředky, stačí odstranit skupinu prostředků.

1. Pokud chcete odstranit skupinu prostředků, přidejte tento kód do bloku try v metodě Main:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Uložte soubor App. Java.

## <a name="run-the-application"></a>Spuštění aplikace

Spuštění této konzolové aplikace z začátku do konce by mělo trvat přibližně pět minut.

1. Chcete-li spustit aplikaci, použijte tento příkaz Maven:

    ```
    mvn compile exec:java
    ```

2. Než stisknete **ENTER** a začnete odstraňovat prostředky, může trvat několik minut, než se ověří vytváření prostředků v Azure Portal. Kliknutím na stav nasazení zobrazíte informace o nasazení.


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o používání [knihoven Azure pro Java](/java/azure/java-sdk-azure-overview).