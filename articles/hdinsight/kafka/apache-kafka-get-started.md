---
title: Začínáme s Apache Kafka – Azure HDInsight | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit cluster Apache Kafka v Azure HDInsight. Naučte se, jak vytvářet témata, odběratele a příjemce.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 27e6472480dac104de799ebf0e7579a7987f6c4c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Začínáme s Apache Kafka ve službě HDInsight

Zjistěte, jak vytvořit a používat cluster [Apache Kafka](https://kafka.apache.org) v Azure HDInsight. Kafka je opensourcová distribuovaná streamovací platforma, která je dostupná pro HDInsight. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv. Kafka se často používá ve spojení s Apache Spark a Apache Storm k zasílání zpráv, sledování aktivit, agregaci datových proudů nebo transformaci dat.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Vytvoření clusteru Kafka

K vytvoření platformy Kafka v clusteru HDInsight použijte následující postup:

1. Na webu [Azure Portal](https://portal.azure.com), vyberte **+ Vytvořit prostředek**, **Data a analýzy** a poté vyberte **HDInsight**.
   
    ![Vytvoření clusteru HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. V části **Základy** zadejte následující informace:

    * **Název clusteru:** Název clusteru HDInsight. Tento název musí být jedinečný.
    * **Předplatné:** Vyberte předplatné, které chcete použít.
    * **Typ clusteru:** Vyberte tuto položku a pak v části **Konfigurace clusteru** nastavte následující hodnoty:

        * **Typ clusteru:** Kafka
        * **Verze:** Kafka 0.10.0 (HDI 3.6)

        Pomocí tlačítka **Vybrat** uložte nastavení typu clusteru.

        ![Výběr typu clusteru](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

    * **Uživatelské jméno přihlášení clusteru** a **Heslo přihlášení clusteru**: Přihlašovací údaje pro přístup ke clusteru pomocí protokolu HTTPS. Tyto přihlašovací údaje se používají i pro přístup ke službám, jako jsou webové uživatelské rozhraní Ambari nebo REST API.
    * **Uživatelské jméno Secure Shell (SSH:)** Přihlašovací údaje používané pro přístup ke clusteru přes SSH. Ve výchozím nastavení je heslo stejné jako pro přihlášení ke clusteru.
    * **Skupina prostředků:** Skupina prostředků, ve které se cluster vytváří.
    * **Umístění:** Oblast Azure, ve které se cluster vytváří.

        > [!IMPORTANT]
        > K zajištění vysoké dostupnosti dat doporučujeme vybrat umístění (oblast), které obsahuje __tři domény selhání__. Další informace najdete v části [Vysoká dostupnost dat](#data-high-availability).
   
 ![Výběr předplatného](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Pomocí tlačítka __Další__ dokončete základní konfiguraci.

4. V části **Úložiště** vyberte nebo vytvořte účet úložiště. Pro ukázkový postup v tomto dokumentu ponechte ve všech ostatních polích výchozí hodnoty. Stisknutím tlačítka __Další__ uložte konfiguraci úložiště.

    ![Nastavení účtu úložiště pro HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

5. Pokud chcete pokračovat, v části __Aplikace (volitelné)__ vyberte __Další__. Pro tento příklad se nepožadují žádné aplikace.

6. Pokud chcete pokračovat, v části __Velikost clusteru__ vyberte __Další__.

    > [!WARNING]
    > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Další informace najdete v části [Vysoká dostupnost dat](#data-high-availability).

    ![Nastavení velikosti clusteru Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > Položka **počet disků na pracovní uzel** konfiguruje škálovatelnost Kafka ve službě HDInsight. Platforma Kafka ve službě HDInsight používá místní disky virtuálních počítačů v clusteru. Platforma Kafka je náročná na V/V prostředky, proto k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel se využívá služba [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Prémiové disky se používají u virtuálních počítačů řady DS a GS. Všechny ostatní typy virtuálních počítačů používají standardní disky.

7. Pokud chcete pokračovat, v části __Upřesňující nastavení__ vyberte __Další__.

8. V části **Souhrn** zkontrolujte konfiguraci clusteru. Pomocí odkazů __Upravit__ opravte případná chybná nastavení. Nakonec stisknutím tlačítka Vytvořit cluster vytvořte.
   
    ![Souhrn konfigurace clusteru](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Vytvoření clusteru trvá přibližně 20 minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

> [!IMPORTANT]
> Při provádění následujících kroků je potřeba použít klienta SSH. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Pokud se chcete ke clusteru připojit pomocí SSH, musíte zadat název uživatelského účtu SSH a název vašeho clusteru. V následujícím příkladu nahraďte `sshuser` a `clustername` názvem vašeho účtu a clusteru:

```ssh sshuser@clustername-ssh.azurehdinsight.net```

Po zobrazení výzvy zadejte heslo, které jste pro účet SSH nastavili.

Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Získání informací o hostiteli Zookeeper a Broker

Při práci s platformou Kafka musíte znát hostitele *Zookeeper* a hostitele *Broker*. Tito hostitelé se používají s rozhraním Kafka API s mnohými z nástrojů, které se s tímto systémem dodávají.

K vytvoření proměnných prostředí s informacemi o hostitelích použijte následující postup:

1. Připojte se ke clusteru přes SSH a následujícím příkazem nainstalujte nástroj `jq`. Tento nástroj slouží k analýze dokumentů JSON a je užitečný při načítání informací hostitele zprostředkovatele:
   
    ```bash
    sudo apt -y install jq
    ```

2. K nastavení proměnné prostředí na název clusteru použijte následující příkaz:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

3. K nastavení proměnné prostředí s použitím informací o hostiteli Zookeeper použijte následující příkaz:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (admin).

4. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. K nastavení proměnné prostředí s použitím informací o hostiteli zprostředkovatele Kafka použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (admin).

6. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
> [!WARNING]
> Nespoléhejte na to, že informace vrácené v této relaci budou vždy přesné. Při škálování clusteru můžou být zprostředkovatelé přidáni nebo odebráni. Pokud dojde k chybě a dojde k nahrazení uzlu, může se změnit název hostitele pro uzel.
>
> Pokud chcete mít jistotu, že jsou informace platné, načtěte informace o hostitelích Zookeeper a Broker až krátce před jejich použitím.

## <a name="create-a-topic"></a>Vytvoření tématu

Kafka ukládá datové proudy do kategorií označovaných jako *témata*. V okně SSH připojenému k hlavnímu uzlu clusteru připojení použijte skript pro vytvoření tématu, dodaný se systémem Kafka:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Tento příkaz se připojí k hostiteli Zookeeper s použitím informací uložených v proměnné `$KAFKAZKHOSTS`. Pak vytvoří téma Kafka s názvem **test**. Vytvoření tématu můžete ověřit použitím následujícího skriptu pro vypsání témat:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

Výstup tohoto příkazu vypíše seznam témat Kafka v clusteru.

## <a name="produce-and-consume-records"></a>Produkce a konzumace záznamů

Kafka ukládá *záznamy* v tématech. Záznamy jsou vytvářeny *producenty* a spotřebovávány *konzumenty*. Producenti vytvářejí záznamy pro *zprostředkovatele* systému Kafka. Každý pracovní uzel v clusteru HDInsight je zprostředkovatelem Kafka.

Pokud chcete uložit záznamy do dříve vytvořeného tématu test a pak je načíst pomocí konzumenta, použijte následující postup:

1. V relaci SSH použijte skript pro zápis záznamu do tématu, dodaný se systémem Kafka:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Po tomto příkazu přejdete na prázdný řádek.

2. Na prázdný řádek zadejte textovou zprávu a stiskněte Enter. Tímto způsobem zadejte několik zpráv a pak se pomocí klávesové zkratky **Ctrl + C** vraťte na normální příkazový řádek. Každý řádek se odešle do tématu Kafka jako samostatný záznam.

3. Ke čtení záznamů z tématu použijte skript dodaný se systémem Kafka:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Tento příkaz načte záznamy z tématu a zobrazí je. Parametr `--from-beginning` způsobí, že konzument začne načítat od začátku datového proudu a zpracuje tak všechny záznamy.

    > [!NOTE]
    > Pokud používáte starší verzi Kafka, nahraďte `--bootstrap-server $KAFKABROKERS` za `--zookeeper $KAFKAZKHOSTS`.

4. Konzumenta zastavíte stisknutím __Ctrl+C__.

Můžete také programově vytvořit producenty a spotřebitele. Příklad používání tohoto rozhraní API najdete v dokumentu [Rozhraní API pro producenta a konzumenta Kafka pomocí HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="data-high-availability"></a>Vysoká dostupnost dat

Každá oblast Azure (umístění) poskytuje _domény selhání_. Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> Pokud je to možné, použijte oblast Azure, která obsahuje tři domény selhání, a vytvářejte témata s faktorem replikace 3.

Pokud použijete oblast, která obsahuje jenom dvě domény selhání, použijte faktor replikace 4, abyste zajistili rovnoměrné rozložení replik napříč těmito dvěma doménami selhání.

### <a name="kafka-and-fault-domains"></a>Kafka a domény selhání

Kafka nemá o doménách selhání žádné informace. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti. K zajištění vysoké dostupnosti použijte [nástroj pro obnovení rovnováhy oddílů Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z relace SSH na hlavní uzel clusteru Kafka.

K zajištění nejvyšší dostupnost dat Kafka byste měli obnovit rovnováhu replik oddílů pro vaše téma v těchto situacích:

* Při vytvoření nového tématu nebo oddílu

* Při vertikálním navýšení kapacity clusteru

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili základy práce s platformou Apache Kafka v HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Kafka](apache-kafka-mirroring.md)
* [Rozhraní API pro producenta a konzumenta Kafka pomocí HDInsight](apache-kafka-producer-consumer-api.md)
* [Rozhraní API pro datové proudy Kafka pomocí HDInsight](apache-kafka-streams-api.md)
* [Použití streamování Apache Sparku (DStream) se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití strukturovaného streamování Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Požití strukturovaného streamu Apache Spark k přesunu dat z Kafka na HDInsight do Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
* [Použití systému Kafka s Azure Container Service](apache-kafka-azure-container-services.md)
* [Použití systému Kafka s aplikacemi Azure Function Apps](apache-kafka-azure-functions.md)
