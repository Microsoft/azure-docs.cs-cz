---
title: Zrcadlová témata apache kafka - Azure HDInsight
description: Naučte se používat funkci zrcadlení Apache Kafka k udržování repliky clusteru Kafka v clusteru HDInsight zrcadlením témat do sekundárního clusteru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425880"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Použití nástroje MirrorMaker k replikaci témat Apache Kafka s využitím platformy Kafka ve službě HDInsight

Přečtěte si, jak pomocí funkce zrcadlení Apache Kafka replikovat témata do sekundárního clusteru. Zrcadlení lze schvalovat jako nepřetržitý proces nebo používat přerušovaně jako metodu migrace dat z jednoho clusteru do druhého.

V tomto příkladu zrcadlení se používá k replikaci témat mezi dvěma clustery HDInsight. Oba clustery jsou v různých virtuálních sítích v různých datových centrech.

> [!WARNING]  
> Zrcadlení by nemělo být považováno za prostředek k dosažení odolnosti proti chybám. Posun k položkám v rámci tématu se liší mezi primárním a sekundárním clusterem, takže klienti nemohou tyto dvě položky zaměnitelně používat.
>
> Pokud máte obavy z odolnosti proti chybám, měli byste nastavit replikaci pro témata v rámci clusteru. Další informace najdete [v tématu Začínáme s Apache Kafka na HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak funguje zrcadlení Apache Kafka

Zrcadlení funguje pomocí nástroje [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (součást Apache Kafka) ke spotřebování záznamů z témat v primárním clusteru a potom k vytvoření místní kopie v sekundárním clusteru. MirrorMaker používá jeden (nebo více) *spotřebitelů,* kteří čtou z primárního clusteru a *výrobce,* který zapisuje do místního (sekundárního) clusteru.

Nejužitečnější zrcadlení nastavení pro zotavení po havárii využívá Kafka clustery v různých oblastech Azure. K dosažení tohoto cíle jsou virtuální sítě, kde clustery umístěny společně.

Následující diagram znázorňuje proces zrcadlení a způsob, jakým komunikace probíhá mezi clustery:

![Diagram procesu zrcadlení](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Primární a sekundární clustery se mohou lišit v počtu uzlů a oddílů a posuny v rámci témat se také liší. Zrcadlení udržuje hodnotu klíče, která se používá pro dělení, takže pořadí záznamů je zachována na základě klíče.

### <a name="mirroring-across-network-boundaries"></a>Zrcadlení přes hranice sítě

Pokud potřebujete zrcadlit mezi clustery Kafka v různých sítích, existují následující další aspekty:

* **Brány**: Sítě musí být schopny komunikovat na úrovni protokolu TCP/IP.

* **Adresování serveru**: Můžete se rozhodnout adresovat uzly clusteru pomocí jejich IP adres nebo plně kvalifikovaných názvů domén.

    * **IP adresy**: Pokud nakonfigurujete clustery Kafka tak, aby používaly inzerci IP adres, můžete pokračovat v nastavení zrcadlení pomocí IP adres uzlů brokerů a uzlů zookeeperu.
    
    * **Názvy domén**: Pokud clustery Kafka nenakonfigurujete pro inzerci ip adres, musí se k sobě clustery připojit pomocí plně kvalifikovaných názvů domén (FQDN). To vyžaduje server DNS (Domain Name System) v každé síti, která je nakonfigurována tak, aby přesměrovala požadavky do jiných sítí. Při vytváření virtuální sítě Azure je místo použití automatického DNS dodanýho se sítí nutné zadat vlastní server DNS a adresu IP serveru. Po vytvoření virtuální sítě je nutné vytvořit virtuální počítač Azure, který používá tuto IP adresu, a pak na něm nainstalovat a nakonfigurovat software DNS.

    > [!WARNING]  
    > Před instalací služby HDInsight do virtuální sítě vytvořte a nakonfigurujte vlastní server DNS. Pro hdinsight není vyžadována žádná další konfigurace, která by používala server DNS nakonfigurovaný pro virtuální síť.

Další informace o připojení dvou virtuálních sítí Azure najdete [v tématu Konfigurace připojení virtuální sítě k virtuální síti](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Zrcadlení architektury

Tato architektura obsahuje dva clustery v různých skupinách prostředků a virtuálních sítích: **primární** a **sekundární**.

### <a name="creation-steps"></a>Kroky vytváření

1. Vytvořte dvě nové skupiny prostředků:

    |Skupina prostředků | Umístění |
    |---|---|
    | kafka-primární-rg | USA – střed |
    | kafka-sekundární-rg | USA – středosever |

1. Vytvořte novou virtuální síť **kafka-primary-vnet** v **kafka-primary-rg**. Ponechte výchozí nastavení.
1. Vytvořte novou virtuální síť **kafka-secondary-vnet** v **kafka-secondary-rg**, také s výchozím nastavením.

1. Vytvořte dva nové klastry Kafka:

    | Název clusteru | Skupina prostředků | Virtual Network | Účet úložiště |
    |---|---|---|---|
    | kafka-primární cluster | kafka-primární-rg | kafka-primární-vnet | kafkaprimárnískladování |
    | kafka-sekundární cluster | kafka-sekundární-rg | kafka-sekundární-vnet | kafkasekundární skladování |

1. Vytvořte partnerské partnerské servery virtuální sítě. Tento krok vytvoří dva partnerských stran: jeden z **kafka-primary-vnet** na **kafka sekundární-virtuální síť** a jeden zpět z **kafka sekundární-virtuální síť** **kafka-primární-vnet**.
    1. Vyberte virtuální síť **kafka-primary-vnet.**
    1. V části **Nastavení** **vyberte Peerings** .
    1. Vyberte **Přidat**.
    1. Na obrazovce **Přidat partnerský vztah** zadejte podrobnosti, jak je znázorněno na následujícím snímku obrazovky.

        ![HDInsight Kafka přidat partnerský vztah virtuální sítě](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Konfigurace inzerce IP

Nakonfigurujte inzerci IP tak, aby se klient mohl připojit pomocí ip adres brokera namísto názvů domén.

1. Přejděte na řídicí panel Ambari `https://PRIMARYCLUSTERNAME.azurehdinsight.net`pro primární cluster: .
1. Vyberte **možnost Služby** > **Kafka**. CliSelectck karta **Configs.**
1. Přidejte následující konfigurační řádky do spodní části **šablony kafka-env.** Vyberte **Uložit**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Na obrazovce **Uložit konfiguraci** zadejte poznámku a klepněte na **tlačítko Uložit**.
1. Pokud se zobrazí výzva s upozorněním na konfiguraci, klepněte na tlačítko **Pokračovat v každém případě**.
1. V nabídce **Uložit změny konfigurace**vyberte možnost **Ok** .
1. V oznámení **O restartování požadované restartování** vyberte možnost **Restartovat** > **všechny ovlivněné.** Vyberte **možnost Potvrdit restartovat vše**.

    ![Apache Ambari restartovat všechny ovlivněny](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Konfigurace Kafky pro naslouchání ve všech síťových rozhraních.
    
1. Zůstaňte na kartě **Configs** v části **Services** > **Kafka**. V části **Kafka Broker** nastavte vlastnost `PLAINTEXT://0.0.0.0:9092` **posluchače** na .
1. Vyberte **Uložit**.
1. Vyberte **restartovat**a **potvrďte restartovat vše**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Záznam Broker IP adresy a Zookeeper adresy pro primární cluster.

1. Na řídicím panelu Ambari vyberte **Hostitelé.**
1. Poznamenejte si IP adresy pro makléře a zookeepery. Uzly zprostředkovatele mají **wn** jako první dvě písmena názvu hostitele a uzly zookeeper mají **zk** jako první dvě písmena názvu hostitele.

    ![Apache Ambari zobrazit ip adresy uzlu](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Opakujte předchozí tři kroky pro druhý cluster **kafka-sekundární cluster**: konfigurace IP reklamy, nastavit naslouchací procesy a poznamenejte si IP adresy Broker a Zookeeper.

## <a name="create-topics"></a>Vytváření témat

1. Připojte se k **primárnímu** clusteru pomocí SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** uživatelským jménem SSH, které se používá při vytváření clusteru. Nahraďte **PRIMARYCLUSTER** základním názvem používaným při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Pomocí následujícího příkazu vytvořte proměnnou s hostiteli Apache Zookeeper pro primární cluster. Řetězce jako `ZOOKEEPER_IP_ADDRESS1` musí být nahrazeny skutečné IP adresy zaznamenané `10.23.0.11` dříve, například a `10.23.0.7`. Pokud používáte rezoluci ve službě ReQDN s vlastním serverem DNS, postupujte [takto,](apache-kafka-get-started.md#getkafkainfo) abyste získali názvy brokerů a zookeeperů.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Chcete-li vytvořit `testtopic`téma s názvem , použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Pomocí následujícího příkazu ověřte, zda bylo téma vytvořeno:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Odpověď obsahuje `testtopic`.

1. K zobrazení informací o hostiteli zookeeperu pro tento **(primární)** cluster použijte následující:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    To vrátí informace podobné následujícímu textu:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Uložte tyto informace. Používá se v další části.

## <a name="configure-mirroring"></a>Konfigurace zrcadlení

1. Připojte se k **sekundárnímu** clusteru pomocí jiné relace SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** uživatelským jménem SSH, které se používá při vytváření clusteru. Nahraďte **SEKUNDÁRNÍ CLUSTER** názvem použitým při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Soubor `consumer.properties` slouží ke konfiguraci komunikace s **primárním** clusterem. Chcete-li soubor vytvořit, použijte následující příkaz:

    ```bash
    nano consumer.properties
    ```

    Jako obsah `consumer.properties` souboru použijte následující text:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Nahraďte **PRIMARY_ZKHOSTS** adresami IP zookeeperu z **primárního** clusteru.

    Tento soubor popisuje informace pro spotřebitele, které mají být používány při čtení z primárního clusteru Kafka. Další informace o konfiguraci spotřebitele naleznete v [tématu Konfigurace spotřebitelů](https://kafka.apache.org/documentation#consumerconfigs) na kafka.apache.org.

    Chcete-li soubor uložit, použijte **kombinaci kláves Ctrl + X**, **Y**a potom **klávesu Enter**.

1. Před konfigurací výrobce, který komunikuje se sekundárním clusterem, nastavte proměnnou pro IP adresy zprostředkovatele **sekundárního** clusteru. K vytvoření této proměnné použijte následující příkazy:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Příkaz `echo $SECONDARY_BROKERHOSTS` by měl vrátit informace podobné následujícímu textu:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Soubor `producer.properties` se používá ke komunikaci **sekundárního** clusteru. Chcete-li soubor vytvořit, použijte následující příkaz:

    ```bash
    nano producer.properties
    ```

    Jako obsah `producer.properties` souboru použijte následující text:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Nahraďte **SECONDARY_BROKERHOSTS** IP adresami zprostředkovatele použitými v předchozím kroku.

    Další informace o konfiguraci výrobce naleznete v [tématu Konfigurace výrobců](https://kafka.apache.org/documentation#producerconfigs) v kafka.apache.org.

1. Pomocí následujících příkazů vytvořte proměnnou prostředí s IP adresami hostitelů Zookeeper pro sekundární cluster:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Výchozí konfigurace pro Kafka na HDInsight neumožňuje automatické vytváření témat. Před spuštěním procesu zrcadlení je nutné použít jednu z následujících možností:

    * **Vytvořit témata v sekundárním clusteru**: Tato možnost také umožňuje nastavit počet oddílů a faktor replikace.

        Témata můžete vytvořit předem pomocí následujícího příkazu:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Nahraďte `testtopic` název tématu, které chcete vytvořit.

    * **Konfigurace clusteru pro automatické vytváření témat**: Tato možnost umožňuje mirrormakeru automaticky vytvářet témata, ale může je vytvářet s jiným počtem oddílů nebo faktorem replikace než primární téma.

        Chcete-li nakonfigurovat sekundární cluster tak, aby automaticky vytvářel témata, proveďte následující kroky:

        1. Přejděte na řídicí panel Ambari `https://SECONDARYCLUSTERNAME.azurehdinsight.net`pro sekundární cluster: .
        1. Klepněte na **položku Services** > **Kafka**. Klepněte na kartu **Configs.**
        1. Do pole __Filtr__ zadejte `auto.create`hodnotu . Tím se vyfiltruje `auto.create.topics.enable` seznam vlastností a zobrazí se nastavení.
        1. Změňte hodnotu na hodnotu `auto.create.topics.enable` true a pak vyberte __Uložit__. Přidejte poznámku a pak znovu vyberte __Uložit.__
        1. Vyberte službu __Kafka,__ vyberte __Restartovat__a pak vyberte __Restartovat všechny ovlivněné__. Po zobrazení výzvy vyberte __Potvrdit restart all__.

        ![kafka povolit automatické vytváření témat](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Spustit MirrorMaker

1. Z připojení SSH k **sekundárnímu** clusteru spusťte proces MirrorMaker pomocí následujícího příkazu:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry použité v tomto příkladu jsou:

    |Parametr |Popis |
    |---|---|
    |--consumer.config|Určuje soubor, který obsahuje vlastnosti příjemce. Tyto vlastnosti se používají k vytvoření příjemce, který čte z *primárního* clusteru Kafka.|
    |--producer.config|Určuje soubor, který obsahuje vlastnosti výrobce. Tyto vlastnosti se používají k vytvoření výrobce, který zapisuje do *sekundárního* clusteru Kafka.|
    |--whitelist|Seznam témat, která MirrorMaker replikuje z primárního clusteru do sekundární.|
    |--num.streamy|Počet spotřebitelských podprocesů, které chcete vytvořit.|

    Příjemce na sekundární uzel nyní čeká na příjem zpráv.

2. Z připojení SSH k **primárnímu** clusteru použijte následující příkaz ke spuštění výrobce a odesílání zpráv k tématu:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Když se dostanete na prázdný řádek s kurzorem, zadejte několik textových zpráv. Zprávy jsou odesílány na téma v **primárním** clusteru. Po dokončení ukončujete proces výrobce pomocí **kombinace kláves Ctrl + C.**

3. Z připojení SSH k **sekundárnícluster,** použijte **Ctrl + C** ukončit proces MirrorMaker. Proces může trvat několik sekund. Chcete-li ověřit, zda byly zprávy replikovány do sekundárního, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Seznam témat nyní `testtopic`obsahuje , který je vytvořen při MirrorMaster zrcadlí téma z primárního clusteru na sekundární. Zprávy načtené z tématu jsou stejné jako zprávy, které jste zadali v primárním clusteru.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kroky v tomto dokumentu vytvořily clustery v různých skupinách prostředků Azure. Chcete-li odstranit všechny vytvořené prostředky, můžete odstranit dvě vytvořené skupiny prostředků: **kafka-primary-rg** a **kafka-secondary_rg**. Odstraněním skupin prostředků odeberete všechny prostředky vytvořené pomocí tohoto dokumentu, včetně clusterů, virtuálních sítí a účtů úložiště.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili používat [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) k vytvoření repliky clusteru [Apache Kafka.](https://kafka.apache.org/) Pomocí následujících odkazů můžete zjistit další způsoby práce s Kafkou:

* [Apache Kafka MirrorMaker dokumentace](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [Osvědčené postupy pro výrobce zrcadla Kafka](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Začínáme s Apache Kafka na HDInsightu](apache-kafka-get-started.md)
* [Používejte Apache Spark s Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Připojení k Apache Kafka prostřednictvím virtuální sítě Azure](apache-kafka-connect-vpn-gateway.md)
