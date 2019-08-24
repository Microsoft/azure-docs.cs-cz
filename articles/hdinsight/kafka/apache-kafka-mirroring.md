---
title: Zrcadlení témat Apache Kafka – Azure HDInsight
description: Další informace o použití zrcadlení funkce platformy Apache Kafka udržovat zrcadlením témata, které cluster sekundární repliky Kafka v clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657153"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Replikace témat Apache Kafka s využitím Kafka v HDInsight pomocí Mirrormakeru

Další informace o použití zrcadlení funkce platformy Apache Kafka pro replikaci do sekundární clusteru témata. Zrcadlení lze spustili jako nepřetržitý proces, nebo použít přerušovaně jako metoda migrace dat z jednoho clusteru do jiného.

V tomto příkladu je zrcadlení používanou k replikaci témata mezi dvěma clustery HDInsight. Oba clustery jsou v různých virtuálních sítí v různých datových centrech.

> [!WARNING]  
> Zrcadlení by neměly být zahrnuté jako prostředek k dosažení odolnost proti chybám. Posun na položky v rámci tématu se liší mezi primárním a sekundárním clustery, takže klienti nemohou použít dva Zaměnitelně.
>
> Pokud máte obavy o odolnosti proti chybám, byste měli nastavit replikaci pro témata v rámci vašeho clusteru. Další informace najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Jak funguje zrcadlení Apache Kafka

Zrcadlení s využitím funguje [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) nástroj (součást platformy Apache Kafka) konzumovat záznamy z témat ve primárního clusteru a pak vytvořit místní kopii na sekundární clusteru. Nástroje MirrorMaker používá (nejméně jeden) *příjemci* , čtení z primárního clusteru a *producent* , která zapisuje do místního clusteru (sekundární).

Nejužitečnější zrcadlení nastavení pro zotavení po havárii využívá clustery Kafka v různých oblastech Azure. Za tím účelem virtuálních sítí, kde jsou umístěny clustery vytvoření partnerského vztahu společně.

Následující diagram znázorňuje proces zrcadlení a tok komunikace mezi clustery:

![Diagram procesu zrcadlení](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Primární a sekundární clustery se může lišit v počtu používaných uzlů a oddíly a posun v rámci témata se také liší. Zrcadlení uchovává hodnotu klíče, který se používá pro dělení, tak zachování pořadí záznamů na základě-key.

### <a name="mirroring-across-network-boundaries"></a>Zrcadlení napříč síťovými hranicemi

Pokud potřebujete pro zrcadlení mezi clustery Kafka v různých sítích, existují následující další aspekty:

* **Brány**: Sítě musí být schopné komunikovat na úrovni protokolu TCP/IP.

* **Server adresování**: Můžete řešit pomocí jejich IP adresy nebo plně kvalifikované názvy domény uzlů clusteru.

    * **IP adresy**: Při konfiguraci vašich clusterů Kafka použít IP adresu reklamy, vám pokračujte zrcadlení instalaci pomocí IP adresy zprostředkovatelské uzly a uzly zookeeper.
    
    * **Názvy domén**: Pokud nenakonfigurujete vašich clusterů Kafka pro reklamní účely IP adresu, clustery musí být schopný se připojit k sobě navzájem pomocí plně kvalifikované názvy domény (FQDN). To vyžaduje server systému DNS (Domain Name) v každé sítě nakonfigurovaný tak, aby směrovala požadavky k jiným sítím. Při vytváření služby Azure Virtual Network, místo použití automatické DNS, opatřeného sítě, je nutné zadat vlastní server DNS a IP adresu serveru. Po vytvoření virtuální sítě můžete musí pak vytvořte virtuální počítač Azure, která používá tuto IP adresu, pak instalace a konfigurace DNS softwaru na něj.

    > [!WARNING]  
    > Vytvoření a konfigurace vlastního serveru DNS. před instalací HDInsight do virtuální sítě. Neexistuje žádná další konfigurace požadované pro HDInsight použít server DNS nakonfigurovaný pro virtuální síť.

Další informace o propojení dvou virtuálních sítích Azure najdete v tématu [konfigurace připojení typu VNet-to-VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Zrcadlení architektury

Tato architektura obsahuje dva clustery v různých skupinách prostředků a virtuální sítě: **primární** a **sekundární**.

### <a name="creation-steps"></a>Postup vytvoření

1. Vytvořte dvě nové skupiny prostředků:

    |Skupina prostředků | Location |
    |---|---|
    | kafka-primary-rg | Střední USA |
    | kafka-secondary-rg | Středoseverní USA |

1. Vytvořit novou virtuální síť **kafka-primary-vnet** v **kafka-primary-rg**. Ponechejte výchozí nastavení.
1. Vytvořit novou virtuální síť **kafka sekundární vnet** v **kafka – sekundární rg**, také s výchozím nastavením.

1. Vytvoření dvou nových clusterů Kafka:

    | Název clusteru | Skupina prostředků | Virtuální sítě | Účet úložiště |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Vytvořte partnerské vztahy virtuálních sítí. Tento krok vytvoří dvě partnerské vztahy: jeden z **kafka-primary-vnet** k **kafka sekundární vnet** a jeden zpět z **kafka sekundární vnet** k  **kafka-primary-vnet**.
    1. Vyberte **kafka-primary-vnet** virtuální sítě.
    1. Klikněte na tlačítko **partnerské vztahy** pod **nastavení**.
    1. Klikněte na **Přidat**.
    1. Na **přidat partnerský vztah** obrazovky, zadejte podrobnosti, jak je znázorněno v následujícím snímku obrazovky.

        ![Přidat partnerský vztah virtuální sítě](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Nakonfigurujte inzerování protokolu IP:
    1. Přejděte na řídicí panel Ambari pro primární cluster: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Klikněte na tlačítko **služby** > **Kafka**. Klikněte na tlačítko **Configs** kartu.
    1. Přidejte následující řádky config do dolní části **kafka env šablony** oddílu. Klikněte na **Uložit**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Zadání poznámky **uložit konfiguraci** obrazovku a klikněte na tlačítko **Uložit**.
    1. Pokud se zobrazí výzva s upozorněním konfigurace, klikněte na tlačítko **i přesto pokračovat**.
    1. Klikněte na tlačítko **Ok** na **uložit změny konfigurace**.
    1. Klikněte na tlačítko **restartovat** > **restartujte všechny ovlivněné** v **vyžaduje restartování** oznámení. Klikněte na tlačítko **potvrďte restartování všech**.

        ![Restartujte uzly kafka](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Nakonfigurujte Kafka tak, aby naslouchala na všech síťových rozhraních.
    1. Zůstaňte na **Configs** kartu **služby** > **Kafka**. V **zprostředkovatelem Kafka** části sada **naslouchacích procesů** vlastnost `PLAINTEXT://0.0.0.0:9092`.
    1. Klikněte na **Uložit**.
    1. Klikněte na tlačítko **restartovat**, a **potvrďte restartování všech**.

1. Zprostředkovatel IP adresy a adresy primárního clusteru Zookeeper si poznamenejte.
    1. Klikněte na tlačítko **hostitele** na řídicím panelu Ambari.
    1. Poznamenejte si IP adresy můžou být zprostředkovatelé a Zookeeper. Zprostředkovatelské uzly mají **wn** jako první dvě písmena názvu hostitele a zookeeper uzly měly **zk** jako první dvě písmena názvu hostitele.

        ![zobrazení ip adres](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Opakujte předchozí tři kroky pro druhý cluster **clusteru kafka sekundární**: Konfigurace IP reklamy, nastavte naslouchacích procesů a poznamenejte si zprostředkovatele a Zookeeper IP adres.

## <a name="create-topics"></a>Vytvářejte témata

1. Připojte se k **primární** clusteru pomocí SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** s uživatelským jménem SSH při vytváření clusteru. Nahraďte **BASENAME** se základním názvem používá při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Následujícím příkazem Vytvořte proměnnou s Apache Zookeeper hostitelů pro primární cluster. Řetězce, jako jsou `ZOOKEEPER_IP_ADDRESS1` musí být nahrazen skutečné IP adresy poznamenali dříve, jako například `10.23.0.11` a `10.23.0.7`. Pokud používáte překlad plně kvalifikovaného názvu domény pomocí vlastního serveru DNS, postupujte podle [tyto kroky](apache-kafka-get-started.md#getkafkainfo) získat zprostředkovatele a zookeeper názvy.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Chcete vytvořit téma s názvem `testtopic`, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Chcete-li ověřit, zda byl vytvořen v tématu použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Odpověď obsahuje `testtopic`.

4. Použijte následující postup k zobrazení informací o hostiteli Zookeeper to ( **primární**) clusteru:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    To vrátit informace podobné následujícímu textu:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Tyto informace uložte. Používá se v další části.

## <a name="configure-mirroring"></a>Konfigurace zrcadlení

1. Připojte se k **sekundární** clusteru s použitím jiné relaci SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Nahraďte **sshuser** s uživatelským jménem SSH při vytváření clusteru. Nahraďte **SECONDARYCLUSTER** s názvem používá při vytváření clusteru.

    Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A `consumer.properties` soubor se používá ke komunikaci s konfiguraci **primární** clusteru. K vytvoření souboru, použijte následující příkaz:

    ```bash
    nano consumer.properties
    ```

    Použijte následující text jako obsah `consumer.properties` souboru:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Nahraďte **PRIMARY_ZKHOSTS** s IP adresami Zookeeper z **primární** clusteru.

    Tento soubor popisuje příjemce informace používat při čtení z primárního clusteru Kafka. Další informace o příjemce konfigurace, najdete v článku [příjemce Configs](https://kafka.apache.org/documentation#consumerconfigs) na webu kafka.apache.org.

    Chcete-li uložit soubor, použijte **Ctrl + X**, **Y**a potom **Enter**.

3. Před konfigurací výrobce, který komunikuje s sekundární clusteru, nastavte proměnnou pro IP adresy zprostředkovatele **sekundární** clusteru. Chcete-li vytvořit tuto proměnnou použijte následující příkazy:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Příkaz `echo $SECONDARY_BROKERHOSTS` by měl vrátit informace podobné následujícímu textu:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. A `producer.properties` soubor se používá ke komunikaci **sekundární** clusteru. K vytvoření souboru, použijte následující příkaz:

    ```bash
    nano producer.properties
    ```

    Použijte následující text jako obsah `producer.properties` souboru:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Nahraďte **SECONDARY_BROKERHOSTS** pomocí zprostředkovatele IP adresy použité v předchozím kroku.

    Konfigurace výrobce další informace najdete v části [producent Configs](https://kafka.apache.org/documentation#producerconfigs) na webu kafka.apache.org.

5. Vytvořte proměnnou prostředí s IP adresami pro sekundární cluster hostitelů Zookeeper použijte následující příkazy:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. Výchozí konfigurace pro systém Kafka na HDInsight automatického vytváření témat týkajících se nepovoluje. Před zahájením procesu zrcadlení musíte použít některý z následujících možností:

    * **Vytvoření témata na sekundární clusteru**: Tato možnost umožňuje nastavit počet oddílech a faktor replikace.

        Témata předem můžete vytvořit pomocí následujícího příkazu:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Nahraďte `testtopic` s názvem tématu, které chcete vytvořit.

    * **Konfigurace clusteru pro vytvoření tématu Automatické**: Tato možnost umožňuje nástroje MirrorMaker pro automatické vytvoření témata, ale může ho vytvořit s různým počtem oddílů nebo faktor replikace než primární tématu.

        Konfigurace sekundární clusteru mohla automaticky vytvářet témata, proveďte tyto kroky:

        1. Přejděte na řídicí panel Ambari pro sekundární clusteru: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Klikněte na tlačítko **služby** > **Kafka**. Klikněte na tlačítko **Configs** kartu.
        5. V __filtr__ pole, zadejte hodnotu `auto.create`. Tím vyfiltrujete seznam vlastností a zobrazí `auto.create.topics.enable` nastavení.
        6. Změňte hodnotu vlastnosti `auto.create.topics.enable` na hodnotu true a pak vyberte __Uložit__. Přidat poznámku a potom vyberte __Uložit__ znovu.
        7. Vyberte __Kafka__ služby, vyberte __restartovat__a pak vyberte __restartování všech ovlivněných__. Po zobrazení výzvy vyberte __potvrdit restartujte všechny__.

        ![Konfigurace automatického vytvoření tématu](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Spuštění nástroje MirrorMaker

1. V rámci připojení SSH k **sekundární** clusteru, použijte následující příkaz pro spuštění procesu nástroje MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Parametry použité v tomto příkladu jsou:

    * **--consumer.config**: Určuje soubor, který obsahuje vlastnosti příjemce. Tyto vlastnosti se používají k vytváření, která čte z příjemce *primární* clusteru Kafka.

    * **--producer.config**: Určuje soubor, který obsahuje vlastnosti výrobce. Tyto vlastnosti se používají k vytváření producenta, který zapisuje do *sekundární* clusteru Kafka.

    * **--whitelist**: Seznam témat, která replikuje nástroje MirrorMaker z clusteru primární do sekundární.

    * **--num.streams**: Počet vláken příjemce pro vytvoření.

    Příjemce na sekundárním uzlu je nyní čekání na příjem zprávy.

2. V rámci připojení SSH k **primární** clusteru, použijte následující příkaz ke spuštění výrobce a odesílání zpráv do tématu:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Až přijedete prázdný řádek s kurzorem, zadejte několik textových zpráv. Zprávy jsou odeslány do tématu **primární** clusteru. Až budete hotovi, použijte **Ctrl + C** ukončit proces výrobce.

3. V rámci připojení SSH k **sekundární** clusteru, použijte **Ctrl + C** ukončit proces nástroje MirrorMaker. Může trvat několik sekund se má ukončit proces. Pokud chcete ověřit, že zprávy se replikují do sekundární, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Teď obsahuje seznam témat `testtopic`, který je vytvořen při MirrorMaster zrcadlí téma z clusteru primární na sekundární. Zprávy načtené z tématu jsou stejné jako ty, které jste zadali na primární clusteru.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Kroky v tomto dokumentu Vytvoření clusterů ve skupinách různých prostředků Azure. Pokud chcete odstranit všechny prostředky vytvořené, můžete odstranit vytvořené skupiny prostředků dva: **kafka-primary-rg** a **kafka secondary_rg**. Odstranění skupiny prostředků odstraní všechny prostředky vytvořené podle tohoto dokumentu, včetně clusterů, virtuální sítě a účty úložiště.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat [nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) k vytvoření repliky [Apache Kafka](https://kafka.apache.org/) clusteru. Zjistit další způsoby, jak pracovat s využitím Kafka pomocí následujících odkazů:

* [Dokumentace Apache Kafka nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) na cwiki.apache.org.
* [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md)
* [Použití Apache Sparku s využitím Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Stormu s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k Apache Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
