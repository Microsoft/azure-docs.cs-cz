---
title: Připojení k Kafka pomocí virtuálních sítí – Azure HDInsight
description: Naučte se, jak se přímo připojit k Kafka v HDInsight prostřednictvím Azure Virtual Network. Přečtěte si, jak se připojit k Kafka z vývojářských klientů pomocí brány VPN nebo z klientů v místní síti pomocí zařízení brány VPN.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-python
ms.date: 03/04/2020
ms.openlocfilehash: ad802b2bdf08a8e43179beece5f52d869513aff3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933045"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Připojení k Apache Kafka ve službě HDInsight přes virtuální síť Azure

Naučte se, jak se přímo připojit k Apache Kafka v HDInsight přes Azure Virtual Network. Tento dokument poskytuje informace o připojení k Kafka pomocí následujících konfigurací:

* Z prostředků v místní síti. Toto připojení je vytvořeno pomocí zařízení VPN (softwaru nebo hardwaru) v místní síti.
* Z vývojového prostředí pomocí klientského softwaru sítě VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architektura a plánování

HDInsight neumožňuje přímé připojení k Kafka přes veřejný Internet. Místo toho musí Kafka klienti (producenti a spotřebitelé) používat jednu z následujících metod připojení:

* Spusťte klienta ve stejné virtuální síti jako Kafka ve službě HDInsight. Tato konfigurace se používá v dokumentu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) . Klient se spustí přímo na uzlech clusteru HDInsight nebo na jiném virtuálním počítači ve stejné síti.

* Připojte k virtuální síti privátní síť, jako je například vaše místní síť. Tato konfigurace umožňuje klientům v místní síti pracovat přímo s Kafka. Chcete-li povolit tuto konfiguraci, proveďte následující úlohy:

  1. Vytvořte virtuální síť.
  2. Vytvořte bránu VPN, která používá konfiguraci typu Site-to-site. Konfigurace použitá v tomto dokumentu se připojuje k zařízení brány VPN v místní síti.
  3. Vytvořte ve virtuální síti server DNS.
  4. Nakonfigurujte přesměrování mezi serverem DNS v každé síti.
  5. Vytvořte ve virtuální síti cluster HDInsight Kafka.

     Další informace najdete v části [připojení k Apache Kafka v místní síti](#on-premises) .

* Připojte jednotlivé počítače k virtuální síti pomocí brány VPN a klienta VPN. Chcete-li povolit tuto konfiguraci, proveďte následující úlohy:

  1. Vytvořte virtuální síť.
  2. Vytvořte bránu VPN, která používá konfiguraci Point-to-site. Tato konfigurace se dá použít u klientů s Windows i MacOS.
  3. Vytvořte ve virtuální síti cluster HDInsight Kafka.
  4. Nakonfigurujte Kafka pro reklamu protokolu IP. Tato konfigurace umožňuje klientovi, aby se připojovali pomocí IP adres zprostředkovatele místo názvů domén.
  5. Stáhněte a používejte klienta VPN ve vývojovém systému.

     Další informace najdete v části [připojení k Apache Kafka s klientem VPN](#vpnclient) .

     > [!WARNING]  
     > Tato konfigurace se doporučuje pro účely vývoje z důvodu následujících omezení:
     >
     > * Každý klient musí být připojen pomocí klientského softwaru sítě VPN.
     > * Klient VPN neprojde požadavky na překlad IP adres do virtuální sítě, takže musíte ke komunikaci s Kafka použít přidělování IP adres. Komunikace IP vyžaduje další konfiguraci v clusteru Kafka.

Další informace o používání služby HDInsight ve virtuální síti najdete v tématu [plánování virtuální sítě pro clustery Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a> Připojení k Apache Kafka z místní sítě

Pokud chcete vytvořit cluster Kafka, který komunikuje s vaší místní sítí, postupujte podle kroků v části [připojení HDInsight k místní síťovému](./../connect-on-premises-network.md) dokumentu.

> [!IMPORTANT]  
> Při vytváření clusteru HDInsight vyberte typ clusteru __Kafka__ .

Tyto kroky vytvoří následující konfiguraci:

* Azure Virtual Network
* Brána VPN typu Site-to-site
* Účet Azure Storage (používaný službou HDInsight)
* Kafka v HDInsightu

Pokud chcete ověřit, jestli se klient Kafka může připojit ke clusteru z místního prostředí, použijte postup uvedený v části [příklad klienta Pythonu](#python-client) .

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a> Připojení k Apache Kafka pomocí klienta VPN

Pomocí kroků v této části vytvoříte následující konfiguraci:

* Azure Virtual Network
* Brána sítě VPN typu Point-to-site
* Účet Azure Storage (používaný službou HDInsight)
* Kafka v HDInsightu

1. Postupujte podle kroků v části [práce s certifikáty podepsanými svým držitelem pro dokument připojení Point-to-site](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) . Tento dokument vytvoří certifikáty potřebné pro bránu.

2. Otevřete příkazový řádek PowerShellu a pomocí následujícího kódu se přihlaste ke svému předplatnému Azure:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Použijte následující kód k vytvoření proměnných, které obsahují informace o konfiguraci:

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

4. Pomocí následujícího kódu vytvořte skupinu prostředků Azure a virtuální síť:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
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
    > Dokončení tohoto procesu může trvat několik minut.

5. Pomocí následujícího kódu vytvořte Azure Storage účet a kontejner objektů BLOB:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Pomocí následujícího kódu vytvořte cluster HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
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
   > Dokončení tohoto procesu trvá přibližně 15 minut.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurace Kafka pro reklamu protokolu IP

Ve výchozím nastavení funkce Apache Zookeeper vrátí název domény pro zprostředkovatele Kafka na klienty. Tato konfigurace nefunguje s klientským softwarem sítě VPN, protože pro entity ve virtuální síti nelze použít překlad názvů. Pro tuto konfiguraci pomocí následujících kroků nakonfigurujte Kafka, aby inzerovala IP adresy místo názvů domén:

1. Pomocí webového prohlížeče přejděte na `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte `CLUSTERNAME` názvem Kafka v clusteru HDInsight.

    Po zobrazení výzvy použijte uživatelské jméno a heslo HTTPS pro daný cluster. Zobrazí se webové uživatelské rozhraní Ambari pro cluster.

2. Pokud chcete zobrazit informace o Kafka, vyberte v seznamu vlevo možnost __Kafka__ .

    ![Seznam služeb s zvýrazněným Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Pokud chcete zobrazit konfiguraci Kafka, v horním __rohu vyberte konfigurace__ .

    ![Konfigurace služeb Apache Ambari](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Pokud chcete najít konfiguraci __Kafka-ENV__ , zadejte `kafka-env` do pole __filtru__ v pravém horním rohu.

    ![Konfigurace Kafka pro Kafka-ENV](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Pokud chcete nakonfigurovat Kafka pro inzerování IP adres, přidejte následující text do dolní části pole __Kafka-ENV-Template__ :

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Chcete-li nakonfigurovat rozhraní, na kterém naslouchá Kafka, zadejte `listeners` do pole __filtru__ v pravém horním rohu.

7. Chcete-li nakonfigurovat Kafka pro naslouchání na všech síťových rozhraních, změňte hodnotu v poli __naslouchací procesy__ na `PLAINTEXT://0.0.0.0:9092` .

8. Chcete-li uložit změny konfigurace, použijte tlačítko __Uložit__ . Zadejte textovou zprávu popisující změny. Po uložení změn klikněte na __OK__ .

    ![Konfigurace pro uložení konfigurace Apache Ambari](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Chcete-li zabránit chybám při restartování Kafka, použijte tlačítko __Akce služby__ a vyberte __zapnout režim údržby__. Kliknutím na tlačítko OK dokončete tuto operaci.

    ![Akce služby s zvýrazněnou možností zapnout údržbu](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Pokud chcete restartovat Kafka, použijte tlačítko __restartovat__ a vyberte __restartovat všechny ovlivněné__. Potvrďte restartování a potom po dokončení operace použijte tlačítko __OK__ .

    ![Tlačítko restartovat s zvýrazněnou možností restartovat všechny ovlivněné](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Chcete-li zakázat režim údržby, použijte tlačítko __Akce služby__ a vyberte možnost __vypnout režim údržby__. Kliknutím na **tlačítko OK** dokončete tuto operaci.

### <a name="connect-to-the-vpn-gateway"></a>Připojení k bráně VPN

Pokud se chcete připojit k bráně VPN, použijte část __připojit k Azure__ v dokumentu [Konfigurace připojení typu Point-to-site](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) .

## <a name="example-python-client"></a><a id="python-client"></a> Příklad: klient Pythonu

Pokud chcete ověřit připojení k Kafka, pomocí následujícího postupu vytvořte a spusťte producenta Pythonu a příjemce:

1. K načtení plně kvalifikovaného názvu domény (FQDN) a IP adresy uzlů v clusteru Kafka použijte jednu z následujících metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

    V dalších krocích uložte vrácené informace pro použití.

2. K instalaci klienta [Kafka-Python](https://kafka-python.readthedocs.io/) použijte následující postup:

    ```bash
    pip install kafka-python
    ```

3. K odeslání dat do Kafka použijte následující kód Pythonu:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Nahraďte `'kafka_broker'` položky adresami vrácenými z kroku 1 v této části:

   * Pokud používáte __softwarového klienta sítě VPN__, nahraďte `kafka_broker` položky IP adresou vašich pracovních uzlů.

   * Pokud jste __povolili překlad IP adres pomocí vlastního serveru DNS__, nahraďte `kafka_broker` položky názvem FQDN pracovních uzlů.

     > [!NOTE]
     > Tento kód odešle řetězec `test message` do tématu `testtopic` . Výchozí konfigurací Kafka ve službě HDInsight je vytvoření tématu, pokud neexistuje.

4. Chcete-li načíst zprávy ze Kafka, použijte následující kód Pythonu:

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

    Nahraďte `'kafka_broker'` položky adresami vrácenými z kroku 1 v této části:

    * Pokud používáte __softwarového klienta sítě VPN__, nahraďte `kafka_broker` položky IP adresou vašich pracovních uzlů.

    * Pokud jste __povolili překlad IP adres pomocí vlastního serveru DNS__, nahraďte `kafka_broker` položky názvem FQDN pracovních uzlů.

## <a name="next-steps"></a>Další kroky

Další informace o používání služby HDInsight s virtuální sítí najdete v dokumentu [Plánování nasazení virtuální sítě pro clustery Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) .

Další informace o vytvoření Virtual Network Azure s bránou VPN typu Point-to-site najdete v následujících dokumentech:

* [Konfigurace připojení typu Point-to-site pomocí Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurace připojení typu Point-to-site pomocí Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Další informace o práci s Apache Kafka v HDInsight najdete v následujících dokumentech:

* [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md)
* [Použití zrcadlení s Apache Kafka v HDInsight](apache-kafka-mirroring.md)
