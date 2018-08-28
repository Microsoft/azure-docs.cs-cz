---
title: Připojení k systému Kafka pomocí virtuální sítě – Azure HDInsight
description: Zjistěte, jak k přímému připojení k systému Kafka v HDInsight pomocí Azure Virtual Network. Zjistěte, jak se připojit k systému Kafka od vývoje klientů pomocí VPN gateway nebo od klientů ve vaší místní síti pomocí zařízení brány sítě VPN.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2018
ms.openlocfilehash: 973563a0c9a986bb4dec785b4521566acb657d15
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042390"
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Připojení k systému Kafka v HDInsight pomocí služby Azure Virtual Network

Zjistěte, jak k přímému připojení k systému Kafka v HDInsight pomocí Azure Virtual Network. Tento dokument obsahuje informace o připojení k systému Kafka pomocí následující konfigurace:

* Z prostředků v místní síti. Toto připojení prostřednictvím zařízení VPN (softwaru nebo hardwaru) ve vaší místní síti.
* Z vývojového prostředí pomocí softwarového klienta sítě VPN.

## <a name="architecture-and-planning"></a>Plánování a architektura

HDInsight neumožňuje přímé připojení k systému Kafka přes veřejný internet. Místo toho klienti Kafka (producenti a spotřebitelé) musí používat jednu z následujících metod připojení:

* Spustíte klienta ve stejné virtuální síti jako Kafka v HDInsight. Tato konfigurace se používá v [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu. Klient spustí přímo na uzlech clusteru HDInsight nebo na jiném virtuálním počítači ve stejné síti.

* Připojení privátní sítě, jako je vaše místní sítě k virtuální síti. Tato konfigurace umožňuje klientům ve vaší místní síti a pracovat přímo s využitím Kafka. Pokud chcete povolit tuto konfiguraci, proveďte následující úkoly:

    1. Vytvořte virtuální síť.
    2. Vytvoření brány VPN, který používá konfiguraci site-to-site. Konfigurace použitá v tomto dokumentu se připojí k zařízení brány sítě VPN v místní síti.
    3. Vytvoření serveru DNS ve virtuální síti.
    4. Konfigurace předávání mezi serverem DNS v každé síti.
    5. Nainstalujte Kafka na HDInsight do virtuální sítě.

    Další informace najdete v tématu [připojení k systému Kafka z místní sítě](#on-premises) oddílu. 

* Připojení jednotlivých počítačů k virtuální síti pomocí sítě VPN gateway a klienta VPN. Pokud chcete povolit tuto konfiguraci, proveďte následující úkoly:

    1. Vytvořte virtuální síť.
    2. Vytvoření brány VPN, který používá konfiguraci point-to-site. Tuto konfiguraci můžete použít s klienty Windows a MacOS.
    3. Nainstalujte Kafka na HDInsight do virtuální sítě.
    4. Nakonfigurujte Kafka pro reklamní účely IP. Tato konfigurace umožňuje klientovi připojit se pomocí přidělování IP adres místo názvů domén.
    5. Stažení a použití klienta VPN ve vývojovém systému.

    Další informace najdete v tématu [připojení k systému Kafka s klienta VPN](#vpnclient) oddílu.

    > [!WARNING]
    > Tato konfigurace se doporučuje jenom pro účely vývoje z důvodu následující omezení:
    >
    > * Každý klient musí připojit pomocí softwarového klienta sítě VPN.
    > * Klient VPN nepředává požadavky na název řešení do virtuální sítě, proto je nutné použít IP adres ke komunikaci se systémem Kafka. Komunikace IP vyžaduje další konfiguraci v clusteru Kafka.

Další informace o používání HDInsight ve virtuální síti najdete v tématu [rozšířit HDInsight pomocí Azure Virtual Networks](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Připojení k systému Kafka z místní sítě

K vytvoření clusteru Kafka, který komunikuje s vaší místní sítí, postupujte podle kroků v [HDInsight připojit k místní síti](./../connect-on-premises-network.md) dokumentu.

> [!IMPORTANT]
> Při vytváření clusteru HDInsight, vyberte __Kafka__ typ clusteru.

Tyto kroky vytvoří následující konfiguraci:

* Azure Virtual Network
* Brány VPN typu Site-to-site
* Účet služby Azure Storage (používané HDInsight)
* Kafka v HDInsightu

Pokud chcete ověřit, Kafka klient může připojit ke clusteru v místním, postupujte podle kroků v [příklad: Pythonového klienta](#python-client) části.

## <a id="vpnclient"></a> Připojení k systému Kafka s klienta VPN

Postupujte podle kroků v této části vytvořte následující konfiguraci:

* Azure Virtual Network
* Brány VPN typu Point-to-site
* Účet služby Azure Storage (používané HDInsight)
* Kafka v HDInsightu

1. Postupujte podle pokynů [práce s certifikáty podepsané svým držitelem pro připojení Point-to-site](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentu. Tento dokument vytvoří certifikáty potřebné pro bránu.

2. Otevřete příkazový řádek Powershellu a přihlaste se ke svému předplatnému Azure pomocí následující kód:

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
    > Může trvat několik minut na dokončení tohoto procesu.

5. Použijte následující kód k vytvoření účtu Azure Storage a objektů blob v kontejneru:

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

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurace Kafka pro reklamní účely IP

Ve výchozím nastavení Zookeeper vrátí název domény zprostředkovatelům systému Kafka klientům. Tuto konfiguraci nebude fungovat pomocí softwarového klienta sítě VPN, jak ho nelze použít překlad názvů pro entity ve virtuální síti. Pro tuto konfiguraci použijte ke konfiguraci Kafka inzerovat IP adres místo názvů domény následující kroky:

1. Pomocí webového prohlížeče, přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ s názvem Kafka v clusteru HDInsight.

    Po zobrazení výzvy použijte HTTPS uživatelské jméno a heslo pro cluster. Webové uživatelské rozhraní Ambari pro cluster se zobrazí.

2. Chcete-li zobrazit informace o Kafka, vyberte __Kafka__ ze seznamu na levé straně.

    ![Zvýrazněný seznamu služeb s využitím Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Chcete-li zobrazit informace o konfiguraci Kafka, vyberte __Configs__ z nahoře uprostřed.

    ![Konfigurace odkazů pro systém Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Najít __kafka env__ konfigurace, zadejte `kafka-env` v __filtr__ pole v pravém horním rohu.

    ![Konfigurace Kafka, kafka env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Ke konfiguraci Kafka inzerovat IP adresy, přidejte následující text k dolní části __kafka env šablony__ pole:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Pokud chcete nakonfigurovat rozhraní, které naslouchá Kafka, zadejte `listeners` v __filtr__ pole v pravém horním rohu.

7. Ke konfiguraci Kafka tak, aby naslouchala na všech síťových rozhraních, změňte hodnotu v __naslouchacích procesů__ pole `PLAINTEXT://0.0.0.0:9092`.

8. Chcete-li uložit změny konfigurace, použijte __Uložit__ tlačítko. Zadejte textovou zprávu s popisem změny. Vyberte __OK__ po změny se uložily.

    ![Konfigurace tlačítko Uložit](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Chcete-li zabránit chybám při restartování Kafka, použijte __akce služby__ tlačítko a vyberte __zapnout v režimu údržby__. Klepněte na tlačítko OK pro dokončení této operace.

    ![Akce služby s zapnout zvýrazněnou údržby](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Chcete-li restartovat Kafka, použijte __restartovat__ tlačítko a vyberte __restartujte všechny ovlivněné__. Potvrďte restartování a pak použít __OK__ tlačítka po dokončení operace.

    ![Restartujte zvýrazněným tlačítkem s restartem vliv](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Chcete-li zakázat režim údržby, použijte __akce služby__ tlačítko a vyberte __zapnout vypnout režim údržby__. Vyberte **OK** k dokončení této operace.

### <a name="connect-to-the-vpn-gateway"></a>Připojení k bráně VPN

Pro připojení ke službě VPN gateway, použijte __připojit se k Azure__ část [konfigurace připojení typu Point-to-Site](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentu.

## <a id="python-client"></a> Příklad: Pythonového klienta

K ověření připojení k systému Kafka, použijte následující postup k vytvoření a spuštění Pythonu producenta a konzumenta:

1. K načtení plně kvalifikovaný název domény (FQDN) a IP adresy uzlů v clusteru Kafka, použijte jednu z následujících metod:

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

2. Pomocí následujícího postupu nainstalujte [kafka python](http://kafka-python.readthedocs.io/) klienta:

        pip install kafka-python

3. K odesílání dat do Kafka, použijte následující kód Pythonu:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Nahradit `'kafka_broker'` položky s adresami vrácená z kroku 1 v této části:

    * Pokud používáte __klientského softwaru VPN__, nahraďte `kafka_broker` položky s IP adresou navyšte kapacitu pracovních uzlů.

    * Pokud máte __povolen překlad názvů pomocí vlastního serveru DNS__, nahraďte `kafka_broker` položky s plně kvalifikovaný název domény uzlů pracovního procesu.

    > [!NOTE]
    > Tento kód odešle řetězec `test message` do tématu `testtopic`. Výchozí konfigurace kafka v HDInsight se má vytvořit téma, pokud neexistuje.

4. Pokud chcete načíst zprávy z Kafka, použijte následující kód Pythonu:

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

    Nahradit `'kafka_broker'` položky s adresami vrácená z kroku 1 v této části:

    * Pokud používáte __klientského softwaru VPN__, nahraďte `kafka_broker` položky s IP adresou navyšte kapacitu pracovních uzlů.

    * Pokud máte __povolen překlad názvů pomocí vlastního serveru DNS__, nahraďte `kafka_broker` položky s plně kvalifikovaný název domény uzlů pracovního procesu.

## <a name="next-steps"></a>Další postup

Další informace o používání HDInsight se službou virtual network, najdete v článku [rozšíření Azure HDInsight pomocí Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentu.

Další informace o vytvoření služby Azure Virtual Network pomocí brány VPN typu Point-to-Site najdete v následujících dokumentech:

* [Konfigurace připojení typu Point-to-Site pomocí webu Azure portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurace připojení typu Point-to-Site pomocí Azure Powershellu](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Další informace o práci se systémem Kafka v prostředí HDInsight najdete v následujících dokumentech:

* [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md)
* [Použití zrcadlení s využitím Kafka v HDInsight](apache-kafka-mirroring.md)
