---
title: Zrcadlení témat Apache Kafka – Azure HDInsight
description: Další informace o použití zrcadlení funkce platformy Apache Kafka udržovat zrcadlením témata, které cluster sekundární repliky Kafka v clusteru HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: fd9094d646b917cf811c28c9770fc2427a404ab4
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309034"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Replikace témat Apache Kafka s využitím Kafka v HDInsight pomocí Mirrormakeru

Další informace o použití zrcadlení funkce platformy Apache Kafka pro replikaci do sekundární clusteru témata. Zrcadlení lze spustili jako nepřetržitý proces, nebo použít přerušovaně jako metoda migrace dat z jednoho clusteru do jiného.

V tomto příkladu je zrcadlení používanou k replikaci témata mezi dvěma clustery HDInsight. Oba clustery jsou ve službě Azure Virtual Network ve stejné oblasti.

> [!WARNING]
> Zrcadlení by neměly být zahrnuté jako prostředek k dosažení odolnost proti chybám. Posun na položky v rámci tématu se liší mezi zdrojovým a cílovým clustery, aby klienti nemohou použít dva Zaměnitelně.
>
> Pokud máte obavy o odolnosti proti chybám, byste měli nastavit replikaci pro témata v rámci vašeho clusteru. Další informace najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak funguje zrcadlení Apache Kafka

Zrcadlení s využitím funguje [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) nástroj (součást platformy Apache Kafka) konzumovat záznamy z témat ve zdrojovém clusteru a pak vytvořit místní kopii v cílovém clusteru. Nástroje MirrorMaker používá (nejméně jeden) *příjemci* , který číst ze zdrojového clusteru a *producent* , která zapisuje do clusteru místní (cíl).

Následující obrázek znázorňuje proces zrcadlení:

![Diagram procesu zrcadlení](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka v HDInsight neposkytuje přístup ke službě systému Kafka přes veřejný internet. Producenti Kafka nebo spotřebitele, musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu jsou umístěny i zdrojových a cílových clusterů Kafka ve službě Azure virtual network. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram zdrojových a cílových Kafka clusterů ve službě Azure virtual network](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Zdrojových a cílových clusterech se může lišit v počtu používaných uzlů a oddíly a posun v rámci témata se také liší. Zrcadlení uchovává hodnotu klíče, který se používá pro dělení, tak zachování pořadí záznamů na základě-key.

### <a name="mirroring-across-network-boundaries"></a>Zrcadlení napříč síťovými hranicemi

Pokud potřebujete pro zrcadlení mezi clustery Kafka v různých sítích, existují následující další aspekty:

* **Brány**: sítě musí být schopné komunikovat na úrovni protokolu TCPIP.

* **Překlad názvů**: clustery The Kafka v každé sítě musí být schopný se připojit k sobě navzájem pomocí názvy hostitelů. To může vyžadovat serveru systému DNS (Domain Name) v každé sítě nakonfigurovaný tak, aby směrovala požadavky k jiným sítím.

    Při vytváření služby Azure Virtual Network, místo použití automatické DNS, opatřeného sítě, je nutné zadat vlastní server DNS a IP adresu serveru. Po vytvoření virtuální sítě můžete musí pak vytvořte virtuální počítač Azure, která používá tuto IP adresu, pak instalace a konfigurace DNS softwaru na něj.

    > [!WARNING]
    > Vytvoření a konfigurace vlastního serveru DNS. před instalací HDInsight do virtuální sítě. Neexistuje žádná další konfigurace požadované pro HDInsight použít server DNS nakonfigurovaný pro virtuální síť.

Další informace o propojení dvou virtuálních sítích Azure najdete v tématu [konfigurace připojení typu VNet-to-VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-apache-kafka-clusters"></a>Vytvoření clusterů systému Apache Kafka

I když můžete vytvořit virtuální síť Azure a ručně clustery Kafka, je jednodušší použít šablonu Azure Resource Manageru. V následujícím návodu pro nasazení služby Azure virtual network a dva clustery Kafka se svým předplatným Azure.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Šablona Azure Resource Manageru se nachází na adrese **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka se třemi pracovními uzly.

2. Pomocí následujících informací vyplňte položky na **vlastní nasazení** okno:
    
    ![HDInsight vlastního nasazení](./media/apache-kafka-mirroring/parameters.png)
    
    * **Skupina prostředků**: Vytvořte skupinu nebo vyberte existující. Tato skupina obsahuje HDInsight cluster.

    * **Umístění**: Vyberte umístění geograficky blízko vás.
     
    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro clustery Kafka. Například zadání **hdi** vytváří clustery s názvem **zdroj hdi** a **dest-hdi**.

    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce pro zdroj a cíl clusterů Kafka.

    * **Heslo přihlášení clusteru**: clustery Kafka heslo správce pro zdroj a cíl.

    * **Uživatelské jméno SSH**: SSH uživatele k vytvoření clusterů pro zdroj a cíl Kafka.

    * **Heslo SSH**: heslo pro uživatele SSH pro zdroj a cíl clusterů Kafka.

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec zaškrtněte políčko **Připnout na řídicí panel** a vyberte **Koupit**. Vytvoření clusteru trvá přibližně 20 minut.

> [!IMPORTANT]
> Názvy clusterů HDInsight se **zdroj BASENAME** a **dest BASENAME**, kde BASENAME je název, který jste zadali v šabloně. Tyto názvy používat v dalších krocích při připojování ke clusteru.

## <a name="create-topics"></a>Vytvářejte témata

1. Připojte se k **zdroj** clusteru pomocí SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** s uživatelským jménem SSH při vytváření clusteru. Nahraďte **BASENAME** se základním názvem používá při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Najít pro zdrojový cluster hostitelů Apache Zookeeper použijte následující příkazy:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Nahraďte `$CLUSTERNAME` s názvem zdrojového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

3. Chcete vytvořit téma s názvem `testtopic`, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Chcete-li ověřit, zda byl vytvořen v tématu použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Odpověď obsahuje `testtopic`.

4. Použijte následující postup k zobrazení informací o hostiteli Zookeeper to ( **zdroj**) clusteru:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    To vrátit informace podobné následujícímu textu:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Tyto informace uložte. Používá se v další části.

## <a name="configure-mirroring"></a>Konfigurace zrcadlení

1. Připojte se k **cílové** clusteru s použitím jiné relaci SSH:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** s uživatelským jménem SSH při vytváření clusteru. Nahraďte **BASENAME** se základním názvem používá při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` soubor se používá ke komunikaci s konfiguraci **zdroj** clusteru. K vytvoření souboru, použijte následující příkaz:

    ```bash
    nano consumer.properties
    ```

    Použijte následující text jako obsah `consumer.properties` souboru:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Nahraďte **SOURCE_ZKHOSTS** s informacemi o hostitelích Zookeeper z **zdroj** clusteru.

    Tento soubor popisuje příjemce informace používat při čtení ze zdrojového clusteru Kafka. Další informace o příjemce konfigurace, najdete v článku [příjemce Configs](https://kafka.apache.org/documentation#consumerconfigs) na webu kafka.apache.org.

    Chcete-li uložit soubor, použijte **Ctrl + X**, **Y**a potom **Enter**.

3. Před konfigurací výrobce, který komunikuje s cílový cluster, musíte najít zprostředkovatele pro hostitele **cílové** clusteru. Použijte následující příkazy pro načtení těchto informací:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Nahraďte `$CLUSTERNAME` s názvem bude cílový cluster. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

    `echo` Příkaz vrátit informace podobné následujícímu textu:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` soubor se používá ke komunikaci __cílové__ clusteru. K vytvoření souboru, použijte následující příkaz:

    ```bash
    nano producer.properties
    ```

    Použijte následující text jako obsah `producer.properties` souboru:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Nahraďte **DEST_BROKERS** zprostředkovatele informace z předchozího kroku.

    Konfigurace výrobce další informace najdete v části [producent Configs](https://kafka.apache.org/documentation#producerconfigs) na webu kafka.apache.org.

5. Najít pro cílový cluster hostiteli Zookeeper použijte následující příkazy:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Nahraďte `$CLUSTERNAME` s názvem bude cílový cluster. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

7. Výchozí konfigurace pro systém Kafka na HDInsight automatického vytváření témat týkajících se nepovoluje. Před zahájením procesu zrcadlení musíte použít některý z následujících možností:

    * **Vytvoření témata v cílovém clusteru**: tuto možnost můžete také nastavit počet oddílech a faktor replikace.

        Témata předem můžete vytvořit pomocí následujícího příkazu:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Nahraďte `testtopic` s názvem tématu, které chcete vytvořit.

    * **Konfigurace clusteru pro vytvoření tématu Automatické**: Tato možnost umožňuje nástroje MirrorMaker pro automatické vytvoření témata, však může ji vytvořit s různým počtem oddílů nebo faktor replikace než tématu zdroje.

        Ke konfiguraci cílového clusteru mohla automaticky vytvářet témata, proveďte tyto kroky:

        1. Z [webu Azure portal](https://portal.azure.com), vyberte cíl clusteru Kafka.
        2. Přehled clusteru, vyberte __řídicí panel clusteru__. Potom vyberte __řídicí panel clusteru HDInsight__. Po zobrazení výzvy ověřování pomocí přihlašovacích údajů (správce) pro cluster.
        3. Vyberte __Kafka__ služby ze seznamu na levé straně stránky.
        4. Vyberte __Configs__ uprostřed stránky.
        5. V __filtr__ pole, zadejte hodnotu `auto.create`. Tím vyfiltrujete seznam vlastností a zobrazí `auto.create.topics.enable` nastavení.
        6. Změňte hodnotu vlastnosti `auto.create.topics.enable` na hodnotu true a pak vyberte __Uložit__. Přidat poznámku a potom vyberte __Uložit__ znovu.
        7. Vyberte __Kafka__ služby, vyberte __restartovat__a pak vyberte __restartování všech ovlivněných__. Po zobrazení výzvy vyberte __potvrdit restartujte všechny__.

## <a name="start-mirrormaker"></a>Spuštění nástroje MirrorMaker

1. V rámci připojení SSH k **cílové** clusteru, použijte následující příkaz pro spuštění procesu nástroje MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry použité v tomto příkladu jsou:

    * **--consumer.config**: Určuje soubor, který obsahuje vlastnosti příjemce. Tyto vlastnosti se používají k vytváření, která čte z příjemce *zdroj* clusteru Kafka.

    * **--producer.config**: Určuje soubor, který obsahuje vlastnosti výrobce. Tyto vlastnosti se používají k vytváření producenta, který zapisuje do *cílové* clusteru Kafka.

    * **seznam povolených adres –**: seznam témat, která replikuje nástroje MirrorMaker z clusteru zdroje do cíle.

    * **--num.streams**: počet vláken příjemce pro vytvoření.

 Při spuštění nástroje MirrorMaker vrátit informace podobné následujícímu textu:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. V rámci připojení SSH k **zdroj** clusteru, použijte následující příkaz ke spuštění výrobce a odesílání zpráv do tématu:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Nahraďte `$CLUSTERNAME` s názvem zdrojového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

     Až přijedete prázdný řádek s kurzorem, zadejte několik textových zpráv. Zprávy jsou odeslány do tématu **zdroj** clusteru. Až budete hotovi, použijte **Ctrl + C** ukončit proces výrobce.

3. V rámci připojení SSH k **cílové** clusteru, použijte **Ctrl + C** ukončit proces nástroje MirrorMaker. Může trvat několik sekund se má ukončit proces. Pokud chcete ověřit, že zprávy se replikovaly do cílového umístění, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Nahraďte `$CLUSTERNAME` s názvem bude cílový cluster. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

    Teď obsahuje seznam témat `testtopic`, který je vytvořen při MirrorMaster zrcadlí téma z clusteru zdroje do cíle. Zprávy načtené z tématu je stejný jako zadaný ve zdrojovém clusteru.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvořte obou clusterech ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků na webu Azure Portal. Odstraněním skupiny prostředků odstraní všechny prostředky vytvořené podle tohoto dokumentu, Azure Virtual Network a účet úložiště používané clustery.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) k vytvoření repliky [Apache Kafka](https://kafka.apache.org/) clusteru. Zjistit další způsoby, jak pracovat s využitím Kafka pomocí následujících odkazů:

* [Dokumentace Apache Kafka nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md)
* [Použití Apache Sparku s využitím Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Stormu s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k Apache Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
