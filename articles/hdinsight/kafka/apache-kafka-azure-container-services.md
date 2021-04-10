---
title: Použití služby Azure Kubernetes s Kafka ve službě HDInsight
description: Naučte se používat Kafka v HDInsight z imagí kontejnerů hostovaných ve službě Azure Kubernetes Service (AKS).
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869646"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Použití služby Azure Kubernetes s Apache Kafka v HDInsight

Naučte se používat službu Azure Kubernetes Service (AKS) s [Apache Kafka](https://kafka.apache.org/) v clusteru HDInsight. Kroky v tomto dokumentu používají Node.js aplikaci hostovanou v AKS k ověření připojení pomocí Kafka. Tato aplikace používá balíček [Kafka-Node](https://www.npmjs.com/package/kafka-node) ke komunikaci s Kafka. Používá [Socket.IO](https://socket.io/) pro zasílání zpráv řízených událostmi mezi klientem prohlížeče a back-end hostovaným v AKS.

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Služba Azure Kubernetes spravuje vaše hostované prostředí Kubernetes a umožňuje rychlé a snadné nasazení kontejnerových aplikací. Pomocí Virtual Network Azure můžete tyto dvě služby propojit.

> [!NOTE]  
> Tento dokument se zaměřuje na kroky potřebné k tomu, aby služba Azure Kubernetes mohla komunikovat s Kafka v HDInsight. Samotným příkladem je pouze základní klient Kafka, který předvádí, že konfigurace funguje.

## <a name="prerequisites"></a>Předpoklady

* [Azure CLI](/cli/azure/install-azure-cli)
* Předplatné Azure

Tento dokument předpokládá, že máte zkušenosti s vytvářením a používáním následujících služeb Azure:

* Kafka v HDInsightu
* Azure Kubernetes Service
* Virtuální sítě Azure

Tento dokument také předpokládá, že jste vás provedl prostřednictvím [kurzu služby Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). Tento článek vytvoří službu kontejneru, vytvoří cluster Kubernetes, registr kontejnerů a nakonfiguruje `kubectl` nástroj.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Síťová topologie

HDInsight i AKS používají jako kontejner pro výpočetní prostředky Azure Virtual Network. Pokud chcete povolit komunikaci mezi HDInsight a AKS, musíte povolit komunikaci mezi jejich sítěmi. Kroky v tomto dokumentu slouží Virtual Network partnerských vztahů k sítím. Další připojení, jako je třeba VPN, by měla také fungovat. Další informace o partnerském vztahu najdete v dokumentu [partnerského vztahu virtuální sítě](../../virtual-network/virtual-network-peering-overview.md) .

Následující diagram znázorňuje topologii sítě použitou v tomto dokumentu:

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="HDInsight v jedné virtuální síti, AKS v jiné, s použitím partnerského vztahu" border="false":::

> [!IMPORTANT]  
> Překlad názvů není mezi partnerskými sítěmi povolený, takže se používá IP adresování. Ve výchozím nastavení je Kafka v HDInsight nakonfigurovaná tak, aby vracela názvy hostitelů, a ne IP adresy, když se klienti připojují. Kroky v tomto dokumentu upravují Kafka místo toho použití reklamy protokolu IP.

## <a name="create-an-azure-kubernetes-service-aks"></a>Vytvoření služby Azure Kubernetes (AKS)

Pokud ještě nemáte cluster AKS, použijte jeden z následujících dokumentů, abyste se dozvěděli, jak ho vytvořit:

* [Nasazení clusteru Azure Kubernetes Service (AKS) – portál](../../aks/kubernetes-walkthrough-portal.md)
* [Nasazení clusteru Azure Kubernetes Service (AKS) – CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS vytvoří během instalace virtuální síť v **Další** skupině prostředků. Další skupina prostředků sleduje konvence pojmenování **MC_resourceGroup_AKSclusterName_location**.  
> Tato síť má partnerský vztah k tomu, který vytvořil pro HDInsight, v další části.

## <a name="configure-virtual-network-peering"></a>Konfigurace partnerského vztahu virtuálních sítí

### <a name="identify-preliminary-information"></a>Identifikace předběžných informací

1. Z [Azure Portal](https://portal.azure.com)vyhledejte další **skupinu prostředků** , která obsahuje virtuální síť pro cluster AKS.

2. Ze skupiny prostředků vyberte prostředek __virtuální sítě__ . Tento název si poznamenejte pro pozdější použití.

3. V části **Nastavení** vyberte __Adresní prostor__. Všimněte si adresního prostoru uvedeného v seznamu.

### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

1. Pokud chcete vytvořit virtuální síť pro HDInsight, přejděte na __+ vytvořit prostředek__  >  __sítě__  >  __virtuální síť__.

1. Vytvořte síť pomocí následujících pokynů pro určité vlastnosti:

    |Vlastnost | Hodnota |
    |---|---|
    |Adresní prostor|Je nutné použít adresní prostor, který se nepřekrývá s tím, který používá síť s clustery AKS.|
    |Umístění|Pro virtuální síť, kterou jste použili pro cluster AKS, použijte stejné __umístění__ .|

1. Než přejdete k dalšímu kroku, počkejte, než se vytvoří virtuální síť.

### <a name="configure-peering"></a>Konfigurace peeringu

1. Pokud chcete nakonfigurovat partnerský vztah mezi sítí HDInsight a sítí s clustery AKS, vyberte virtuální síť a pak vyberte __partnerské vztahy__.

1. Vyberte __+ Přidat__ a pomocí následujících hodnot vyplňte formulář:

    |Vlastnost |Hodnota |
    |---|---|
    |Název partnerského vztahu z \<this VN> do vzdálené virtuální sítě|Zadejte jedinečný název pro tuto konfiguraci partnerského vztahu.|
    |Virtuální síť|Vyberte virtuální síť pro **cluster AKS**.|
    |Název partnerského vztahu od \<AKS VN> do \<this VN>|Zadejte jedinečný název.|

    Ponechte všechna ostatní pole na výchozí hodnotě a pak výběrem __OK__ nakonfigurujte partnerský vztah.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Vytvoření clusteru Apache Kafka v HDInsight

Při vytváření Kafka v clusteru HDInsight se musíte připojit k virtuální síti vytvořené dříve pro HDInsight. Další informace o vytvoření clusteru Kafka naleznete v dokumentu [Vytvoření clusteru Apache Kafka](apache-kafka-get-started.md) .

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurace Apache Kafka reklamy protokolu IP

Pomocí následujících kroků nakonfigurujte Kafka, aby inzerovala IP adresy místo názvů domén:

1. Pomocí webového prohlížeče přejděte na `https://CLUSTERNAME.azurehdinsight.net`. Položku název_clusteru nahraďte názvem Kafka v clusteru HDInsight.

    Po zobrazení výzvy použijte uživatelské jméno a heslo HTTPS pro daný cluster. Zobrazí se webové uživatelské rozhraní Ambari pro cluster.

2. Pokud chcete zobrazit informace o Kafka, vyberte v seznamu vlevo možnost __Kafka__ .

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Seznam služeb s zvýrazněným Kafka" border="true":::

3. Pokud chcete zobrazit konfiguraci Kafka, v horním __rohu vyberte konfigurace__ .

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Konfigurace služeb Apache Ambari" border="true":::

4. Pokud chcete najít konfiguraci __Kafka-ENV__ , zadejte `kafka-env` do pole __filtru__ v pravém horním rohu.

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="Konfigurace Kafka pro Kafka-ENV" border="true":::

5. Pokud chcete nakonfigurovat Kafka pro inzerování IP adres, přidejte následující text do dolní části pole __Kafka-ENV-Template__ :

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Chcete-li nakonfigurovat rozhraní, na kterém naslouchá Kafka, zadejte `listeners` do pole __filtru__ v pravém horním rohu.

7. Chcete-li nakonfigurovat Kafka pro naslouchání na všech síťových rozhraních, změňte hodnotu v poli __naslouchací procesy__ na `PLAINTEXT://0.0.0.0:9092` .

8. Chcete-li uložit změny konfigurace, použijte tlačítko __Uložit__ . Zadejte textovou zprávu popisující změny. Po uložení změn klikněte na __OK__ .

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Konfigurace pro uložení konfigurace Apache Ambari" border="true":::

9. Chcete-li zabránit chybám při restartování Kafka, použijte tlačítko __Akce služby__ a vyberte __zapnout režim údržby__. Kliknutím na tlačítko OK dokončete tuto operaci.

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="Akce služby s zvýrazněnou možností zapnout údržbu" border="true":::

10. Pokud chcete restartovat Kafka, použijte tlačítko __restartovat__ a vyberte __restartovat všechny ovlivněné__. Potvrďte restartování a potom po dokončení operace použijte tlačítko __OK__ .

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="Tlačítko restartovat s zvýrazněnou možností restartovat všechny ovlivněné" border="true":::

11. Chcete-li zakázat režim údržby, použijte tlačítko __Akce služby__ a vyberte možnost __vypnout režim údržby__. Kliknutím na **tlačítko OK** dokončete tuto operaci.

## <a name="test-the-configuration"></a>Otestujte konfiguraci.

V tomto okamžiku Kafka a služba Azure Kubernetes komunikují prostřednictvím partnerských virtuálních sítí. K otestování tohoto připojení použijte následující postup:

1. Vytvořte Kafka téma, které se používá v testovací aplikaci. Informace o vytváření Kafkach témat najdete v dokumentu o [Vytvoření clusteru Apache Kafka](apache-kafka-get-started.md) .

2. Stáhněte si ukázkovou aplikaci z [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Upravte `index.js` soubor a změňte následující řádky:

    * `var topic = 'mytopic'`: Nahraďte `mytopic` názvem tématu Kafka použitým touto aplikací.
    * `var brokerHost = '176.16.0.13:9092`: Nahraďte `176.16.0.13` interní IP adresou jednoho z hostitelů zprostředkovatele pro váš cluster.

        Interní IP adresu hostitelů zprostředkovatele (workernodes) v clusteru najdete v dokumentu [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) . Vyberte IP adresu jedné z položek, kde začíná název domény `wn` .

4. Z příkazového řádku v `src` adresáři nainstalujte závislosti a pomocí Docker Sestavte image pro nasazení:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Balíčky, které tato aplikace vyžaduje, se zaregistrují do úložiště, takže je nemusíte `npm` instalovat pomocí tohoto nástroje.

5. Přihlaste se ke svému Azure Container Registry (ACR) a vyhledejte loginServer název:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Pokud neznáte svůj Azure Container Registry název, nebo neznáte použití rozhraní příkazového řádku Azure pro práci se službou Azure Kubernetes, přečtěte si [kurz AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Označte místní `kafka-aks-test` obrázek pomocí loginServer vašeho ACR. Přidejte také `:v1` na konec k označení verze Image:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Odeslat image do registru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Dokončení této operace trvá několik minut.

8. Upravte soubor manifestu Kubernetes ( `kafka-aks-test.yaml` ) a nahraďte `microsoft` názvem ACR loginServer, který jste získali v kroku 4.

9. Pomocí následujícího příkazu nasaďte nastavení aplikace z manifestu:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Pro sledování aplikace použijte následující příkaz `EXTERNAL-IP` :

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Po přiřazení externí IP adresy můžete sledování ukončit __stisknutím kombinace kláves CTRL + C__ .

11. Otevřete webový prohlížeč a zadejte externí IP adresu pro službu. Přijdete na stránku, která bude vypadat jako na následujícím obrázku:

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Obrázek webové stránky Apache Kafka test" border="true":::

12. Zadejte text do pole a potom vyberte tlačítko __Odeslat__ . Data se odesílají do Kafka. Pak příjemce Kafka v aplikaci přečte zprávu a přidá ji do části __zprávy z Kafka__ .

    > [!WARNING]  
    > Můžete obdržet více kopií zprávy. K tomuto problému obvykle dochází, když aktualizujete prohlížeč po připojení nebo otevřete více připojení prohlížeče k aplikaci.

## <a name="next-steps"></a>Další kroky

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md)

* [Vytvoření repliky Apache Kafka v HDInsight pomocí nástroje MirrorMaker](apache-kafka-mirroring.md)

* [Použití Apache Storm s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Použití Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Připojení k Apache Kafka prostřednictvím Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
