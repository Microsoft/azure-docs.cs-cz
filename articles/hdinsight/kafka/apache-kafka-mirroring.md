---
title: Zrcadlení Apache Kafka témata - Azure HDInsight | Microsoft Docs
description: Další informace o použití funkce zrcadlení Apache Kafka udržovat zrcadlením témata do clusteru s podporou sekundární repliku Kafka na clusteru HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/31/2018
ms.author: larryfr
ms.openlocfilehash: 39d7eeaf565a6c58bac162d110e6fd1cd96ad1f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Použití MirrorMaker k replikaci Apache Kafka témata s Kafka v HDInsight

Zjistěte, jak použít funkci zrcadlení Apache Kafka k replikaci témata do clusteru s podporou sekundární. Zrcadlení lze byla spuštěna jako nepřetržitý proces, nebo použít občas jako metodu migrace dat z jednoho clusteru do druhého.

V tomto příkladu je zrcadlení používanou k replikaci témata mezi dvěma clustery HDInsight. Oba clustery jsou ve virtuální síti Azure ve stejné oblasti.

> [!WARNING]
> Zrcadlení by se neměla považovat jako prostředky k zajištění odolnosti proti chybám. Posun na položky v rámci téma jsou rozdíly mezi zdrojovým a cílovým clustery, takže klienti nemohou použít dva zcela zaměnitelným významem.
>
> Pokud máte obavy o odolnost proti chybám, byste měli nastavit replikace pro témata v rámci clusteru. Další informace najdete v tématu [začít pracovat s Kafka v HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Jak funguje Kafka zrcadlení

Zrcadlení funguje pomocí nástroje MirrorMaker (součást Apache Kafka) na využívat záznamy ze témata ve zdrojovém clusteru a pak vytvořit místní kopii v cílovém clusteru. MirrorMaker používá (nejméně jeden) *příjemci* který číst ze zdrojového clusteru a *producent* , zapíše do místní (cíl) clusteru.

Následující diagram znázorňuje proces zrcadlení:

![Diagram procesu zrcadlení](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka v HDInsight neposkytuje přístup ke službě Kafka prostřednictvím veřejného Internetu. Producenti Kafka nebo příjemci musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu jsou obě Kafka zdrojové a cílové clusterů umístěné v virtuální sítě Azure. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram zdrojové a cílové Kafka clusterů v virtuální sítě Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Zdrojové a cílové clustery se může lišit v počtu uzlů a oddíly a odsazení v rámci témata se také liší. Hodnota klíče, který se používá pro vytváření oddílů, zrcadlení udržuje, takže pořadí záznamů se zachová, i na základě na klíč.

### <a name="mirroring-across-network-boundaries"></a>Zrcadlení napříč síťovými hranicemi

Pokud potřebujete zrcadlení mezi clustery Kafka v jiných sítích, existují následující další aspekty:

* **Brány**: sítě musí být schopný komunikovat na úrovni protokolu TCPIP.

* **Překlad názvů**: The Kafka clustery v každé sítě musí být schopný se připojit k sobě navzájem pomocí názvy hostitelů. Může to vyžadovat systému DNS (Domain Name) server v každé sítě, který je nakonfigurovaný pro směrování požadavků k jiným sítím.

    Při vytváření virtuální síť Azure, místo použití automatické DNS součástí sítě, je nutné zadat vlastního serveru DNS a IP adresu serveru. Po vytvoření virtuální sítě, můžete musí poté vytvořte virtuální počítač Azure, který používá IP adresu, pak nainstalujte a nakonfigurujte DNS software na něm.

    > [!WARNING]
    > Vytvoření a konfigurace vlastního serveru DNS před instalací HDInsight do virtuální sítě. Neexistuje žádná další konfigurace požadované pro HDInsight použít server DNS nakonfigurovaný pro virtuální síť.

Další informace o připojení dvou virtuálních sítí Azure najdete v tématu [konfigurace připojení typu VNet-to-VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Vytvoření Kafka clusterů

Když vytvoříte virtuální síť Azure a Kafka clusterů ručně, je jednodušší použít šablonu Azure Resource Manager. Použijte následující kroky k nasazení virtuální sítě Azure a dva clustery Kafka k předplatnému Azure.

1. Na následující tlačítko použijte pro přihlášení do Azure a otevřete šablonu na portálu Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Šablona Azure Resource Manager je umístěna ve **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka, který obsahuje tři uzly pracovního procesu.

2. Následující informace slouží k naplnění položek na **vlastní nasazení** okno:
    
    ![HDInsight vlastní nasazení](./media/apache-kafka-mirroring/parameters.png)
    
    * **Skupina prostředků**: vytvoření skupiny nebo vyberte nějaký existující. Tato skupina obsahuje clusteru HDInsight.

    * **Umístění**: Vyberte umístění geograficky blízko vás.
     
    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro clustery Kafka. Například zadáním **hdi** vytvoří clustery s názvem **zdroj hdi** a **cíle hdi**.

    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce pro zdrojové a cílové Kafka clusterů.

    * **Heslo pro přihlášení clusteru**: uživatelské heslo správce pro zdrojové a cílové Kafka clusterů.

    * **Uživatelské jméno SSH**: SSH, aby uživatel vytvořil pro zdrojové a cílové Kafka clustery.

    * **Heslo SSH**: heslo pro uživatele SSH pro zdrojové a cílové Kafka clusterů.

3. Pro čtení **podmínky a ujednání**a potom vyberte **souhlasím s podmínkami a ujednáními výše uvedených**.

4. Nakonec zkontrolujte **připnout na řídicí panel** a pak vyberte **nákupu**. Chcete-li vytvořit clustery trvá asi 20 minut.

> [!IMPORTANT]
> Názvy clusterů HDInsight se **zdroj BASENAME** a **cíle BASENAME**, kde BASENAME je jméno, které jste zadali v šabloně. Názvy těchto používat v dalších krocích při připojování k clustery.

## <a name="create-topics"></a>Vytvoření témata

1. Připojení k **zdroj** clusteru pomocí protokolu SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** s uživatelským jménem SSH použít při vytváření clusteru. Nahraďte **BASENAME** s základní název použít při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pokud chcete najít hostitele Zookeeper pro zdrojový cluster, použijte následující příkazy:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Nahraďte `$CLUSTERNAME` s názvem zdrojového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

3. Chcete-li vytvořit téma s názvem `testtopic`, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Chcete-li ověřit, zda byl vytvořen v tématu použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Odpověď obsahuje `testtopic`.

4. Použijte následující zobrazíte informace o hostiteli Zookeeper pro tento ( **zdroj**) clusteru:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Vrátí informace podobná následující text:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Tyto informace uložte. Používá se v další části.

## <a name="configure-mirroring"></a>Konfigurace zrcadlení

1. Připojení k **cílové** clusteru pomocí jiné relace SSH:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** s uživatelským jménem SSH použít při vytváření clusteru. Nahraďte **BASENAME** s základní název použít při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` soubor slouží ke konfiguraci komunikace s **zdroj** clusteru. Pokud chcete vytvořit soubor, použijte následující příkaz:

    ```bash
    nano consumer.properties
    ```

    Použít následující text jako obsah `consumer.properties` souboru:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Nahraďte **SOURCE_ZKHOSTS** informacemi Zookeeper hostitele z **zdroj** clusteru.

    Tento soubor popisuje příjemce informace používat při čtení ze zdroje Kafka clusteru. Další informace o uživatelských nastavení, naleznete v tématu [příjemce konfigurací](https://kafka.apache.org/documentation#consumerconfigs) v kafka.apache.org.

    Chcete-li uložit soubor, použijte **kombinaci kláves Ctrl + X**, **Y**a potom **Enter**.

3. Před konfigurací producent, který komunikuje s cílový cluster, musíte vyhledat zprostředkovatele hostitelů **cílové** clusteru. K načtení těchto informací použijte následující příkazy:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Nahraďte `$CLUSTERNAME` s názvem cílového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

    `echo` Příkaz vrátí informace podobná následující text:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. A `producer.properties` souboru se používá pro komunikaci __cílové__ clusteru. Pokud chcete vytvořit soubor, použijte následující příkaz:

    ```bash
    nano producer.properties
    ```

    Použít následující text jako obsah `producer.properties` souboru:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Nahraďte **DEST_BROKERS** s informacemi o zprostředkovatele z předchozího kroku.

    Další informace o producent konfigurace, najdete v části [producent konfigurací](https://kafka.apache.org/documentation#producerconfigs) v kafka.apache.org.

5. Pokud chcete najít hostitele Zookeeper pro cílový cluster, použijte následující příkazy:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Nahraďte `$CLUSTERNAME` s názvem cílového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

7. Výchozí konfiguraci pro Kafka v HDInsight nepovoluje automatické vytváření témat. Před zahájením procesu zrcadlení musí používat jednu z následujících možností:

    * **Vytvořte v tématech v cílovém clusteru**: tuto možnost můžete také nastavit počet oddílů a faktor replikace.

        Témata týkající se předem, můžete vytvořit pomocí následujícího příkazu:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Nahraďte `testtopic` s názvem tématu, které chcete vytvořit.

    * **Konfigurace clusteru pro vytvoření automatických tématu**: Tato možnost umožňuje MirrorMaker pro automatické vytvoření témata, ale ji může vytvořit s jiný počet oddílů nebo replikace Multi-Factor než tématu zdroje.

        Pokud chcete konfigurovat cílový cluster pro automatické vytvoření témata, proveďte tyto kroky:

        1. Z [portál Azure](https://portal.azure.com), vyberte cíl Kafka clusteru.
        2. Přehled cluster, vyberte __řídicí panel clusteru__. Potom vyberte __řídicí panel clusteru HDInsight__. Po zobrazení výzvy, proveďte ověření pomocí přihlašovacích (správce) pro cluster.
        3. Vyberte __Kafka__ služby ze seznamu na levé straně stránky.
        4. Vyberte __konfigurací__ uprostřed stránky.
        5. V __filtru__ pole, zadejte hodnotu `auto.create`. Tím se odfiltrují seznam vlastností a zobrazí `auto.create.topics.enable` nastavení.
        6. Změňte hodnotu `auto.create.topics.enable` na hodnotu true a potom vyberte __Uložit__. Přidat poznámku a potom vyberte __Uložit__ znovu.
        7. Vyberte __Kafka__ služby, vyberte __restartujte__a potom vyberte __restartujte všechny zasažené__. Po zobrazení výzvy vyberte __potvrdit restartujte__.

## <a name="start-mirrormaker"></a>Spustit MirrorMaker

1. Připojení SSH ke **cílové** clusteru, použijte následující příkaz ke spuštění procesu MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry použité v tomto příkladu jsou:

    * **--consumer.config**: Určuje soubor, který obsahuje příjemce vlastnosti. Tyto vlastnosti se používají k vytvoření příjemce, který čte z *zdroj* Kafka clusteru.

    * **--producer.config**: Určuje soubor, který obsahuje producent vlastnosti. Tyto vlastnosti se používají k vytvoření producent, který zapíše do *cílové* Kafka clusteru.

    * **seznam povolených adres –**: seznam témat, která replikuje MirrorMaker ze zdrojového clusteru do cílového umístění.

    * **--num.streams**: počet vláken příjemce k vytvoření.

 Při spuštění MirrorMaker vrátí informace podobná následující text:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Připojení SSH ke **zdroj** clusteru, použijte následující příkaz ke spuštění producent a odesílání zpráv do tématu:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Nahraďte `$CLUSTERNAME` s názvem zdrojového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

     Až přijedete do prázdný řádek s kurzoru, zadejte několik textové zprávy. Zprávy jsou odeslány do tématu **zdroj** clusteru. Až budete hotoví, použijte **kombinaci kláves Ctrl + C** ukončit proces producent.

3. Připojení SSH ke **cílové** clusteru, použijte **kombinaci kláves Ctrl + C** ukončit proces MirrorMaker. Může trvat několik sekund ukončit proces. Pokud chcete ověřit, že zprávy replikovaly do cílového umístění, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Nahraďte `$CLUSTERNAME` s názvem cílového clusteru. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

    Seznam témat nyní zahrnuje `testtopic`, který se vytvoří při MirrorMaster zrcadlí tématu ze zdrojového clusteru do cílového umístění. Zprávy přijaté z tématu jsou stejné, jako je zadaný ve zdrojovém clusteru.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvořit oba clustery ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků na portálu Azure. Odstraňuje se skupina prostředků odebere všechny prostředky, které jsou vytvořené pomocí následujících tento dokument, Azure Virtual Network a účet úložiště, které jsou používané clustery.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat MirrorMaker k vytvoření repliky Kafka clusteru. Chcete-li zjistit další způsoby, jak pracovat s Kafka pomocí následujících odkazů:

* [Dokumentaci Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) v cwiki.apache.org.
* [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md)
* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
