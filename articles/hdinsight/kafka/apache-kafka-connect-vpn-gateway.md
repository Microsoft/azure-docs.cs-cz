---
title: Připojení k Kafka pomocí virtuální sítě - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak k přímému připojení k Kafka v HDInsight prostřednictvím virtuální síti Azure. Zjistěte, jak se připojit k Kafka od klientů vývoj pomocí brány sítě VPN nebo z klientů ve vaší místní síti pomocí zařízení brány VPN.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/05/2018
ms.author: larryfr
ms.openlocfilehash: 1ddfbbb5efb73f968c9ef42396be45fce12ff47a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Připojení k Kafka v HDInsight pomocí virtuální sítě Azure

Zjistěte, jak k přímému připojení k Kafka v HDInsight prostřednictvím virtuální síti Azure. Tento dokument obsahuje informace o připojení k Kafka pomocí následující konfigurace:

* Z prostředků v místní síti. Tato připojení pomocí zařízení VPN (softwaru nebo hardwaru) ve vaší místní síti.
* Z prostředí pro vývoj pomocí softwaru klienta VPN.

## <a name="architecture-and-planning"></a>Plánování a architektura

HDInsight neumožňuje přímé připojení k Kafka prostřednictvím veřejného Internetu. Místo toho Kafka klientů (producenti a spotřebitelé) musí používat jednu z následujících metod připojení:

* Spustíte klienta ve stejné virtuální síti jako Kafka v HDInsight. Tato konfigurace se používá v [začínat Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu. Klient spouští přímo na uzlech clusteru HDInsight nebo na jiný virtuální počítač ve stejné síti.

* Privátní síti, například v místní síti připojte k virtuální síti. Tato konfigurace umožňuje klientům ve vaší místní síti pracovat přímo s Kafka. Chcete-li tuto konfiguraci, proveďte následující úlohy:

    1. Vytvořte virtuální síť.
    2. Vytvořte bránu VPN, který používá konfiguraci site-to-site. Konfigurace použitá v tomto dokumentu se připojí k zařízení brány sítě VPN v síti na pracovišti.
    3. Vytvoření serveru DNS ve virtuální síti.
    4. Konfigurace předávání mezi serverem DNS v každé sítě.
    5. Nainstalujte Kafka v HDInsight do virtuální sítě.

    Další informace najdete v tématu [připojení k Kafka z místní sítě](#on-premises) části. 

* Jednotlivé počítače připojte k virtuální síti pomocí klienta VPN a brány VPN. Chcete-li tuto konfiguraci, proveďte následující úlohy:

    1. Vytvořte virtuální síť.
    2. Vytvořte bránu VPN, který používá konfiguraci point-to-site. Tuto konfiguraci můžete použít s klienty systému Windows a systému MacOS.
    3. Nainstalujte Kafka v HDInsight do virtuální sítě.
    4. Nakonfigurujte Kafka pro inzerování IP. Tato konfigurace umožňuje klientům připojení pomocí IP adresy namísto názvů domény.
    5. Stáhnout a použít klienta VPN na vývojovém systému.

    Další informace najdete v tématu [připojit k Kafka u klientů VPN](#vpnclient) části.

    > [!WARNING]
    > Tato konfigurace se doporučuje jenom pro účely vývoje z důvodu následující omezení:
    >
    > * Každý klient musí připojit pomocí softwaru klienta VPN.
    > * Klienta VPN nesplňuje požadavky na rozlišení názvů k virtuální síti, je nutné použít ke komunikaci s Kafka adresování IP adres. Komunikaci IP vyžaduje další konfiguraci v clusteru Kafka.

Další informace o používání HDInsight ve virtuální síti, najdete v části [rozšířit HDInsight pomocí virtuálních sítí Azure](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Připojit k Kafka z místní sítě

Pokud chcete vytvořit cluster Kafka, který komunikuje s vaší místní síti, postupujte podle kroků v [HDInsight připojit k místní síti](./../connect-on-premises-network.md) dokumentu.

> [!IMPORTANT]
> Při vytváření clusteru HDInsight, vyberte __Kafka__ clusteru typu.

Pomocí těchto kroků vytvoříte následující konfiguraci:

* Azure Virtual Network
* Brána Site-to-site VPN
* Účet služby Azure Storage (používá se v prostředí HDInsight)
* Kafka v HDInsightu

Pokud chcete ověřit, že klient Kafka se může připojit ke clusteru z místního, použijte kroky v [příklad: klienta Python](#python-client) části.

## <a id="vpnclient"></a> Připojení k Kafka pomocí klienta VPN

Postupujte podle kroků v této části vytvořit následující konfiguraci:

* Azure Virtual Network
* Brána sítě VPN Point-to-site
* Účet služby Azure Storage (používá se v prostředí HDInsight)
* Kafka v HDInsightu

1. Postupujte podle kroků v [práce s certifikáty podepsané svým držitelem pro připojení Point-to-site](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentu. Tento dokument vytvoří certifikáty potřebné pro bránu.

2. Otevřete příkazovém řádku prostředí PowerShell a použít následující kód k přihlášení k předplatnému Azure:

    ```powershell
    Connect-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Použijte následující kód k vytvoření proměnné, které obsahují informace o konfiguraci:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Použijte následující kód k vytvoření skupiny prostředků Azure a virtuální sítě:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > To může trvat několik minut na dokončení tohoto procesu.

5. Použijte následující kód k vytvoření účtu úložiště Azure a objektů blob kontejneru:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Použijte následující kód k vytvoření clusteru HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Tento proces trvá přibližně 15 minut.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurace Kafka pro inzerování IP

Ve výchozím nastavení Zookeeper vrátí název domény zprostředkovatelé Kafka klientům. Tato konfigurace nefunguje s softwaru klienta VPN, překlad nemůže použít pro entity ve virtuální síti. Pro tuto konfiguraci použijte ke konfiguraci Kafka inzerovat IP adresy namísto názvů domény následující kroky:

1. Pomocí webového prohlížeče, přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ s názvem Kafka na clusteru HDInsight.

    Pokud budete vyzváni, použijte HTTPS uživatelské jméno a heslo pro cluster. Webové uživatelské rozhraní Ambari pro cluster se zobrazí.

2. Chcete-li zobrazit informace o Kafka, vyberte __Kafka__ ze seznamu na levé straně.

    ![Seznam služeb s Kafka zvýrazněná](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Chcete-li zobrazit informace o konfiguraci Kafka, vyberte __konfigurací__ nejvyšší uprostřed.

    ![Konfigurací odkazy pro Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Najít __kafka env__ konfigurace, zadejte `kafka-env` v __filtru__ pole v pravém horním rohu.

    ![Konfigurace Kafka, pro kafka env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Ke konfiguraci Kafka inzerovat IP adres, přidejte následující text k dolní části __kafka. env šablony__ pole:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Pokud chcete konfigurovat rozhraní, které Kafka naslouchá na, zadejte `listeners` v __filtru__ pole v pravém horním rohu.

7. Ke konfiguraci Kafka naslouchat na všech síťových rozhraní, změňte hodnotu v __naslouchací procesy__ do `PLAINTEXT://0.0.0.0:9092`.

8. Chcete-li uložit změny konfigurace, použijte __Uložit__ tlačítko. Zadejte textovou zprávu s popisem změny. Vyberte __OK__ po změny byly uloženy.

    ![Konfigurace tlačítko Uložit](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Chcete-li zabránit chybám při restartování Kafka, použijte __služby akce__ tlačítko a vyberte __zapnout v režimu údržby__. Klepněte na tlačítko OK pro dokončení této operace.

    ![Akce služby s zapněte zvýrazněná údržby](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Chcete-li restartovat Kafka, použijte __restartujte__ tlačítko a vyberte __restartujte všechny vliv__. Potvrďte restartování a potom pomocí __OK__ tlačítko po dokončení operace.

    ![Restartujte tlačítkem s restartem vliv](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Zakázat režim údržby, použijte __služby akce__ tlačítko a vyberte __zapnout vypnout režimu údržby__. Vyberte **OK** pro dokončení této operace.

### <a name="connect-to-the-vpn-gateway"></a>Připojení ke službě VPN gateway

Pro připojení k bráně VPN, použijte __připojit k Azure__ části [konfigurace připojení typu Point-to-Site](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentu.

## <a id="python-client"></a> Příklad: Python klienta

K ověření připojení k Kafka, použijte následující postup k vytvoření a spuštění producent Python a příjemce:

1. K načtení plně kvalifikovaný název domény (FQDN) a IP adresy z uzlů v clusteru Kafka, použijte jednu z následujících metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Tento skript předpokládá, že `$resourceGroupName` je název skupiny prostředků Azure, která obsahuje virtuální síť.

    Uložte vrácené informace pro použití v dalších krocích.

2. Následující informace vám pomůžou nainstalovat [kafka python](http://kafka-python.readthedocs.io/) klienta:

        pip install kafka-python

3. K odesílání dat do Kafka, použijte následující kód Python:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Nahraďte `'kafka_broker'` položky s adresami vrácená z kroku 1 v této části:

    * Pokud používáte __klienta VPN softwaru__, nahraďte `kafka_broker` položky s IP adresou uzly pracovního procesu.

    * Pokud máte __povolen překlad názvů pomocí vlastního serveru DNS__, nahraďte `kafka_broker` položek s uzly pracovního procesu plně kvalifikovaný název domény.

    > [!NOTE]
    > Tento kód odešle řetězec `test message` do tématu `testtopic`. Výchozí konfigurace Kafka v HDInsight se má vytvořit téma, pokud neexistuje.

4. Pro načtení zprávy z Kafka, použijte následující kód Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Nahraďte `'kafka_broker'` položky s adresami vrácená z kroku 1 v této části:

    * Pokud používáte __klienta VPN softwaru__, nahraďte `kafka_broker` položky s IP adresou uzly pracovního procesu.

    * Pokud máte __povolen překlad názvů pomocí vlastního serveru DNS__, nahraďte `kafka_broker` položek s uzly pracovního procesu plně kvalifikovaný název domény.

## <a name="next-steps"></a>Další postup

Další informace o používání HDInsight s virtuální sítě najdete v tématu [rozšíření Azure HDInsight pomocí Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu.

Další informace o vytváření virtuální síť Azure s bránou sítě VPN Point-to-Site najdete v následujících dokumentech:

* [Konfigurace připojení typu Point-to-Site pomocí portálu Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurace připojení typu Point-to-Site pomocí Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Další informace o práci se systémem Kafka v prostředí HDInsight najdete v následujících dokumentech:

* [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md)
* [Použít zrcadlení s Kafka v HDInsight](apache-kafka-mirroring.md)
