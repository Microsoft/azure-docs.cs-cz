---
title: Používání služby Azure Kubernetes service s Kafkou na HDInsightu
description: Naučte se používat Kafka na HDInsight z kontejneru iimages hostovaných ve službě Azure Kubernetes Service (AKS).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 6abb4f632535f1bda7e9f337f111ba372a624f2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239616"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Používání služby Azure Kubernetes se službou Apache Kafka na WEBU HDInsight

Přečtěte si, jak používat službu Azure Kubernetes Service (AKS) s [Apache Kafka](https://kafka.apache.org/) v clusteru HDInsight. Kroky v tomto dokumentu používají aplikaci Node.js hostovoje v AKS k ověření připojení k Kafka. Tato aplikace používá balíček [kafka-node](https://www.npmjs.com/package/kafka-node) ke komunikaci s Kafkou. Používá [Socket.io](https://socket.io/) pro zasílání zpráv řízených událostmi mezi klientem prohlížeče a back-endem hostovaným v AKS.

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Služba Azure Kubernetes spravuje vaše hostované prostředí Kubernetes a umožňuje rychlé a snadné nasazení kontejnerizovaných aplikací. Pomocí virtuální sítě Azure můžete tyto dvě služby propojit.

> [!NOTE]  
> Tento dokument se zaměřuje na kroky potřebné k povolení azure kubernetes service ke komunikaci s Kafka na HDInsight. Samotný příklad je pouze základní klient Kafka prokázat, že konfigurace funguje.

## <a name="prerequisites"></a>Požadavky

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Předplatné Azure

Tento dokument předpokládá, že jste obeznámeni s vytvářením a používáním následujících služeb Azure:

* Kafka v HDInsightu
* Azure Kubernetes Service
* Virtuální sítě Azure

Tento dokument také předpokládá, že jste prošli [kurz služby Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). Tento článek vytvoří kontejnerovou službu, vytvoří cluster Kubernetes, `kubectl` registr kontejnerů a nakonfiguruje nástroj.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Síťová topologie

HDInsight i AKS používají virtuální síť Azure jako kontejner pro výpočetní prostředky. Chcete-li povolit komunikaci mezi hdinsight a AKS, musíte povolit komunikaci mezi jejich sítěmi. Kroky v tomto dokumentu používají partnerský vztah virtuální sítě k sítím. Ostatní připojení, jako je například VPN, by měla také fungovat. Další informace o partnerské síti najdete v dokumentu [partnerského vztahu virtuální sítě.](../../virtual-network/virtual-network-peering-overview.md)

Následující diagram znázorňuje topologii sítě použitou v tomto dokumentu:

![HDInsight v jedné virtuální síti, AKS v jiné, pomocí partnerského vztahu](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Překlad názvů není mezi partnerskými sítěmi povolen, takže se používá adresování IP. Ve výchozím nastavení je Kafka na HDInsight nakonfigurován tak, aby vracel názvy hostitelů namísto IP adres, když se klienti připojují. Kroky v tomto dokumentu upravit Kafka použít IP reklamy místo.

## <a name="create-an-azure-kubernetes-service-aks"></a>Vytvoření služby Azure Kubernetes (AKS)

Pokud ještě nemáte cluster AKS, použijte jeden z následujících dokumentů, abyste se dozvěděli, jak ho vytvořit:

* [Nasazení clusteru služby Azure Kubernetes Service (AKS) – portál](../../aks/kubernetes-walkthrough-portal.md)
* [Nasazení clusteru služby Azure Kubernetes (AKS) – cli](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS vytvoří virtuální síť během instalace v **další** skupině prostředků. Další skupina prostředků se řídí konvencí pojmenování **MC_resourceGroup_AKSclusterName_location**.  
> Tato síť je peered na ten, vytvořený pro HDInsight v další části.

## <a name="configure-virtual-network-peering"></a>Konfigurace partnerského vztahu virtuální sítě

### <a name="identify-preliminary-information"></a>Identifikovat předběžné informace

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte další **skupinu prostředků,** která obsahuje virtuální síť pro váš cluster AKS.

2. Ze skupiny prostředků vyberte prostředek __virtuální sítě.__ Tento název si poznamenejte pro pozdější použití.

3. V části **Nastavení**vyberte __Možnost Adresní prostor__. Poznamenejte si uvedený adresní prostor.

### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

1. Chcete-li vytvořit virtuální síť pro hdinsight, přejděte na __+ Vytvořit virtuální__ > __Networking__ > __síť__prostředků .

1. Vytvořte síť pomocí následujících pokynů pro určité vlastnosti:

    |Vlastnost | Hodnota |
    |---|---|
    |Adresní prostor|Je nutné použít adresní prostor, který nepřekrývá prostor používaný sítí clusterů AKS.|
    |Umístění|Pro virtuální síť, kterou jste použili pro cluster AKS, použijte stejné __umístění.__|

1. Počkejte, až virtuální síť byla vytvořena před přejde k dalšímu kroku.

### <a name="configure-peering"></a>Konfigurace peeringu

1. Chcete-li nakonfigurovat partnerský vztah mezi sítí HDInsight a sítí clusterů AKS, vyberte virtuální síť a pak vyberte __Partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské partnerské společnosti__.

1. Vyberte __+ Přidat__ a použijte následující hodnoty k naplnění formuláře:

    |Vlastnost |Hodnota |
    |---|---|
    |Název partnerského vztahu \<z tohoto> VN do vzdálené virtuální sítě|Zadejte jedinečný název pro tuto konfiguraci partnerského vztahu.|
    |Virtuální síť|vyberte virtuální síť **pro cluster AKS**.|
    |Název partnerského vztahu \<z> AKS VN do \<tohoto> VN|Zadejte jedinečný název.|

    Všechna ostatní pole ponechejte ve výchozí hodnotě a pak vyberte __OK__ a nakonfigurujte partnerský vztah.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Vytvoření clusteru Apache Kafka na HDInsightu

Při vytváření clusteru Kafka v HDInsight se musíte připojit k virtuální síti vytvořené dříve pro HDInsight. Další informace o vytvoření clusteru Kafka najdete v dokumentu [vytvoření clusteru Apache Kafka.](apache-kafka-get-started.md)

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurace Apache Kafka IP Reklama

Pomocí následujících kroků nakonfigurujte kafku tak, aby inzerovala IP adresy místo názvů domén:

1. Pomocí webového prohlížeče přejděte na `https://CLUSTERNAME.azurehdinsight.net`. Nahraďte cluster u clusteru Kafka v clusteru HDInsight.

    Po zobrazení výzvy použijte uživatelské jméno a heslo HTTPS pro cluster. The Ambari Web UI for the cluster is displayed.

2. Chcete-li zobrazit informace o Kafce, vyberte __kafku__ ze seznamu vlevo.

    ![Seznam služeb se zvýrazněnou položkou Kafka](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Chcete-li zobrazit konfiguraci Kafka, vyberte __konfigurace__ z horního středu.

    ![Konfigurace služeb Apache Ambari](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Chcete-li najít konfiguraci __kafka-env,__ zadejte `kafka-env` __pole Filtr__ v pravém horním.

    ![Konfigurace Kafka, pro kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Chcete-li nakonfigurovat Kafku tak, aby inzerovala IP adresy, přidejte na konec pole __kafka-env-template__ následující text:

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Chcete-li nakonfigurovat rozhraní, na které `listeners` Kafka naslouchá, zadejte pole __Filtr__ v pravém horním horním.

7. Chcete-li nakonfigurovat Kafku tak, aby naslouchala ve všech `PLAINTEXT://0.0.0.0:9092`síťových rozhraních, změňte hodnotu v poli __posluchačů__ na .

8. Chcete-li uložit změny konfigurace, použijte tlačítko __Uložit.__ Zadejte textovou zprávu popisující změny. Po uložení změn vyberte __ok.__

    ![Apache Ambari uložit konfiguraci](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Chcete-li zabránit chybám při restartování kafky, použijte tlačítko __Servisní akce__ a vyberte __Možnost Zapnout režim údržby__. Chcete-li dokončit tuto operaci, vyberte možnost OK.

    ![Servisní akce se zvýrazněnou zapnutou údržbou](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Chcete-li restartovat aplikaci Kafka, použijte tlačítko __Restartovat__ a vyberte __možnost Restartovat všechny ovlivněné položky__. Potvrďte restartování a po dokončení operace použijte tlačítko __OK.__

    ![Tlačítko Restartovat se zvýrazněným restartováním](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Chcete-li režim údržby zakázat, použijte tlačítko __Akce servisu__ a vyberte __vypnout režim údržby__. Chcete-li dokončit tuto operaci, vyberte **možnost OK.**

## <a name="test-the-configuration"></a>Otestujte konfiguraci

V tomto okamžiku Kafka a Azure Kubernetes Service jsou v komunikaci prostřednictvím partnerských virtuálních sítí. Chcete-li toto připojení otestovat, postupujte takto:

1. Vytvořte téma Kafka, které používá testovací aplikace. Informace o vytváření témat Kafka najdete v dokumentu [clusteru Vytvoření Apache Kafka.](apache-kafka-get-started.md)

2. Stáhněte si [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)ukázkovou aplikaci z aplikace .

3. Upravte `index.js` soubor a změňte následující řádky:

    * `var topic = 'mytopic'`: `mytopic` Nahraďte se názvem tématu Kafka, které tato aplikace používá.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Nahraďte interní IP adresou jednoho z hostitelů brokera pro váš cluster.

        Chcete-li najít interní IP adresu hostitelů brokerů (workernodes) v clusteru, najdete v dokumentu [Apache Ambari REST API.](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) Vyberte IP adresu jedné z položek, kde `wn`název domény začíná na .

4. Z příkazového řádku `src` v adresáři nainstalujte závislosti a pomocí Dockeru vytvořte bitovou kopii pro nasazení:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Balíčky vyžadované touto aplikací jsou kontrolovány do `npm` úložiště, takže není nutné použít nástroj k jejich instalaci.

5. Přihlaste se k registru kontejnerů Azure (ACR) a najděte název loginServer:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Pokud neznáte název registru kontejnerů Azure nebo neznáte použití příkazového uživatelského příkazu Azure ke službě Azure Kubernetes, přečtěte si [kurzy AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Označte `kafka-aks-test` místní obrázek pomocí loginServer vašeho ACR. Také `:v1` přidejte na konec k označení verze obrázku:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Zasuňte bitovou kopii do registru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Tato operace trvá několik minut.

8. Upravte soubor manifestu Kubernetes (`kafka-aks-test.yaml`) a nahraďte `microsoft` názvem ACR loginServer načteným v kroku 4.

9. K nasazení nastavení aplikace z manifestu použijte následující příkaz:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Pomocí následujícího příkazu `EXTERNAL-IP` sledovat aplikace:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Jakmile je přiřazena externí IP adresa, ukončete hodinky pomocí __kombinace kláves CTRL + C__

11. Otevřete webový prohlížeč a zadejte externí IP adresu služby. Dostanete se na stránku podobnou následujícímu obrázku:

    ![Obrázek webové stránky testu Apache Kafka](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Zadejte text do pole a pak vyberte tlačítko __Odeslat.__ Data jsou odeslána do Kafky. Potom kafka příjemce v aplikaci přečte zprávu a přidá ji do __zprávy z Kafka__ části.

    > [!WARNING]  
    > Může se zobrazit více kopií zprávy. K tomuto problému obvykle dochází při aktualizaci prohlížeče po připojení nebo otevření více připojení prohlížeče k aplikaci.

## <a name="next-steps"></a>Další kroky

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Začínáme s Apache Kafka na HDInsightu](apache-kafka-get-started.md)

* [Pomocí MirrorMakeru vytvořte repliku Apache Kafka na HDInsightu](apache-kafka-mirroring.md)

* [Použijte Apache Storm s Apache Kafka na HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Používejte Apache Spark s Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Připojení k Apache Kafka prostřednictvím virtuální sítě Azure](apache-kafka-connect-vpn-gateway.md)
