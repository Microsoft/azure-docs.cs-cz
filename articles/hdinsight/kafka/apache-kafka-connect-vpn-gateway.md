---
title: Připojení k Kafce pomocí virtuálních sítí – Azure HDInsight
description: Přečtěte si, jak se přímo připojit ke Kafce na HDInsightu prostřednictvím virtuální sítě Azure. Zjistěte, jak se připojit k Kafce z vývojových klientů pomocí brány VPN nebo z klientů v místní síti pomocí zařízení brány VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 36ff0d5f1fc96b2013555d37a869ebf629a22be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272120"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Připojení k Apache Kafka ve službě HDInsight přes virtuální síť Azure

Zjistěte, jak se přímo připojit k Apache Kafka na HDInsight prostřednictvím virtuální sítě Azure. Tento dokument obsahuje informace o připojení k Kafce pomocí následujících konfigurací:

* Z prostředků v místní síti. Toto připojení je navázáno pomocí zařízení VPN (softwaru nebo hardwaru) v místní síti.
* Z vývojového prostředí pomocí softwarového klienta VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architektura a plánování

HDInsight neumožňuje přímé připojení k Kafka přes veřejný internet. Místo toho musí klienti Kafka (výrobci a spotřebitelé) používat jednu z následujících metod připojení:

* Spusťte klienta ve stejné virtuální síti jako Kafka na HDInsight. Tato konfigurace se používá v [dokumentu Start with Apache Kafka na HDInsight.](apache-kafka-get-started.md) Klient běží přímo na uzlech clusteru HDInsight nebo na jiném virtuálním počítači ve stejné síti.

* Připojte k virtuální síti privátní síť, například místní síť. Tato konfigurace umožňuje klientům v místní síti přímo pracovat s Kafkou. Chcete-li tuto konfiguraci povolit, proveďte následující úlohy:

  1. Vytvořte virtuální síť.
  2. Vytvořte bránu VPN, která používá konfiguraci mezi lokalitami. Konfigurace použitá v tomto dokumentu se připojuje k zařízení brány VPN v místní síti.
  3. Vytvořte dns server ve virtuální síti.
  4. Nakonfigurujte předávání mezi serverem DNS v každé síti.
  5. Vytvořte kafka v clusteru HDInsight ve virtuální síti.

     Další informace najdete v tématu [Připojení k Apache Kafka z místní sítě](#on-premises) části.

* Připojte jednotlivé počítače k virtuální síti pomocí brány VPN a klienta VPN. Chcete-li tuto konfiguraci povolit, proveďte následující úlohy:

  1. Vytvořte virtuální síť.
  2. Vytvořte bránu VPN, která používá konfiguraci bodu k webu. Tuto konfiguraci lze použít s klienty Windows i MacOS.
  3. Vytvořte kafka v clusteru HDInsight ve virtuální síti.
  4. Konfigurace Kafky pro IP reklamu. Tato konfigurace umožňuje klientovi připojit pomocí zprostředkovatele IP adres namísto názvů domén.
  5. Stáhněte a používejte klienta VPN ve vývojovém systému.

     Další informace najdete v části [Připojit k Apache Kafka s klientem VPN.](#vpnclient)

     > [!WARNING]  
     > Tato konfigurace je doporučena pouze pro účely vývoje z důvodu následujících omezení:
     >
     > * Každý klient se musí připojit pomocí softwarového klienta VPN.
     > * Klient VPN nepředává požadavky na překlad názvů do virtuální sítě, takže ke komunikaci s Kafkou je nutné použít ip adresování. Komunikace IP vyžaduje další konfiguraci v clusteru Kafka.

Další informace o používání HDInsightu ve virtuální síti najdete v [tématu Plánování virtuální sítě pro clustery Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a>Připojení k Apache Kafka z místní sítě

Pokud chcete vytvořit cluster Kafka, který komunikuje s vaší místní sítí, postupujte podle pokynů v [části Připojení HDInsightu k místnímu síťovému](./../connect-on-premises-network.md) dokumentu.

> [!IMPORTANT]  
> Při vytváření clusteru HDInsight vyberte typ clusteru __Kafka.__

Tímto postupem vytvořte následující konfiguraci:

* Azure Virtual Network
* Brána VPN mezi lokalitami
* Účet Azure Storage (používaný HDInsight)
* Kafka v HDInsightu

Chcete-li ověřit, že klient Kafka můžete připojit ke clusteru z místního, použijte kroky v [příkladu: Klient Python](#python-client) části.

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a>Připojení k Apache Kafka s klientem VPN

Pomocí kroků v této části vytvořte následující konfiguraci:

* Azure Virtual Network
* Brána VPN point-to-site
* Účet úložiště Azure (používaný HDInsight)
* Kafka v HDInsightu

1. Postupujte podle pokynů v dokumentu [Práce s certifikáty podepsanými svým držitelem pro připojení typu Point-to-site.](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) Tento dokument vytvoří certifikáty potřebné pro bránu.

2. Otevřete výzvu PowerShellu a přihlaste se k předplatnému Azure pomocí následujícího kódu:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Následující kód slouží k vytvoření proměnných, které obsahují informace o konfiguraci:

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

4. K vytvoření skupiny prostředků Azure a virtuální sítě použijte následující kód:

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

5. K vytvoření účtu úložiště Azure a kontejneru objektů blob použijte následující kód:

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

6. K vytvoření clusteru HDInsight použijte následující kód:

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
   > Tento proces trvá přibližně 15 minut.

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurace Kafky pro ip reklamu

Ve výchozím nastavení Apache Zookeeper vrátí klientům název domény zprostředkovatelů Kafka. Tato konfigurace nefunguje s klientem softwaru VPN, protože nemůže používat překlad názvů pro entity ve virtuální síti. Pro tuto konfiguraci nakonfigurujte kafku takto následující kroky k inzerci ADRES IP namísto názvů domén:

1. Pomocí webového prohlížeče přejděte na `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte `CLUSTERNAME` se názvem clusteru Kafka v hdinsightu.

    Po zobrazení výzvy použijte uživatelské jméno a heslo HTTPS pro cluster. The Ambari Web UI for the cluster is displayed.

2. Chcete-li zobrazit informace o Kafce, vyberte __kafku__ ze seznamu vlevo.

    ![Seznam služeb se zvýrazněnou položkou Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Chcete-li zobrazit konfiguraci Kafka, vyberte __konfigurace__ z horního středu.

    ![Konfigurace služeb Apache Ambari](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Chcete-li najít konfiguraci __kafka-env,__ zadejte `kafka-env` __pole Filtr__ v pravém horním.

    ![Konfigurace Kafka, pro kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Chcete-li nakonfigurovat Kafku tak, aby inzerovala IP adresy, přidejte na konec pole __kafka-env-template__ následující text:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Chcete-li nakonfigurovat rozhraní, na které `listeners` Kafka naslouchá, zadejte pole __Filtr__ v pravém horním horním.

7. Chcete-li nakonfigurovat Kafku tak, aby naslouchala ve všech `PLAINTEXT://0.0.0.0:9092`síťových rozhraních, změňte hodnotu v poli __posluchačů__ na .

8. Chcete-li uložit změny konfigurace, použijte tlačítko __Uložit.__ Zadejte textovou zprávu popisující změny. Po uložení změn vyberte __ok.__

    ![Apache Ambari uložit konfiguraci](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Chcete-li zabránit chybám při restartování kafky, použijte tlačítko __Servisní akce__ a vyberte __Možnost Zapnout režim údržby__. Chcete-li dokončit tuto operaci, vyberte možnost OK.

    ![Servisní akce se zvýrazněnou zapnutou údržbou](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Chcete-li restartovat aplikaci Kafka, použijte tlačítko __Restartovat__ a vyberte __možnost Restartovat všechny ovlivněné položky__. Potvrďte restartování a po dokončení operace použijte tlačítko __OK.__

    ![Tlačítko Restartovat se zvýrazněným restartováním](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Chcete-li režim údržby zakázat, použijte tlačítko __Akce servisu__ a vyberte __vypnout režim údržby__. Chcete-li dokončit tuto operaci, vyberte **možnost OK.**

### <a name="connect-to-the-vpn-gateway"></a>Připojení k bráně VPN

Chcete-li se připojit k bráně VPN, použijte část __Připojit k Azure__ v dokumentu Konfigurace připojení bodu na [místo.](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)

## <a name="example-python-client"></a><a id="python-client"></a>Příklad: Klient Pythonu

Chcete-li ověřit připojení k Kafka, použijte následující kroky k vytvoření a spuštění pythonvýrobce a spotřebitele:

1. K načtení plně kvalifikovaného názvu domény (Plně kvalifikovaný název) a IP adres uzlů v clusteru Kafka použijte jednu z následujících metod:

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

    Uložte vrácené informace pro použití v dalších krocích.

2. K instalaci [klienta kafka-python](https://kafka-python.readthedocs.io/) použijte následující:

    ```bash
    pip install kafka-python
    ```

3. Chcete-li odeslat data do Kafky, použijte následující kód Pythonu:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Nahraďte `'kafka_broker'` položky adresami vrácenými z kroku 1 v této části:

   * Pokud používáte __klienta SOFTWARE__VPN `kafka_broker` , nahraďte položky IP adresou pracovních uzlů.

   * Pokud jste __povolili překlad názvů prostřednictvím vlastního serveru DNS__, nahraďte `kafka_broker` položky programem ReQDN pracovních uzlů.

     > [!NOTE]
     > Tento kód odešle `test message` řetězec `testtopic`na téma . Výchozí konfigurace Kafka na HDInsight je vytvořit téma, pokud neexistuje.

4. Chcete-li načíst zprávy z Kafky, použijte následující kód Pythonu:

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

    * Pokud používáte __klienta SOFTWARE__VPN `kafka_broker` , nahraďte položky IP adresou pracovních uzlů.

    * Pokud jste __povolili překlad názvů prostřednictvím vlastního serveru DNS__, nahraďte `kafka_broker` položky programem ReQDN pracovních uzlů.

## <a name="next-steps"></a>Další kroky

Další informace o používání HDInsightu s virtuální sítí najdete v dokumentu [Plánování nasazení virtuální sítě pro clustery Azure HDInsight.](../hdinsight-plan-virtual-network-deployment.md)

Další informace o vytvoření virtuální sítě Azure s bránou VPN z bodu na web najdete v následujících dokumentech:

* [Konfigurace připojení point-to-site pomocí portálu Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Konfigurace připojení point-to-site pomocí Azure PowerShellu](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Další informace o práci s Apache Kafka na HDInsight, naleznete v následujících dokumentech:

* [Začínáme s Apache Kafka na HDInsightu](apache-kafka-get-started.md)
* [Použití zrcadlení s Apache Kafka na HDInsight](apache-kafka-mirroring.md)
