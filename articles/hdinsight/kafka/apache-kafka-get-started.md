---
title: 'Úvodní příručka: Nastavení Apache Kafka na HDInsight pomocí portálu Azure'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka v Azure HDInsight pomocí portálu Azure Portal. Dozvíte se také o tématech, předplatitelích a konzumentech platformy Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130282"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Úvodní příručka: Vytvoření clusteru Apache Kafka v Azure HDInsight pomocí portálu Azure

[Apache Kafka](./apache-kafka-introduction.md) je open-source, distribuovaná streamovací platforma. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv.

V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka pomocí webu Azure Portal. Dozvíte se také, jak používat obsažené nástroje k odesílání a příjmu zpráv pomocí platformy Apache Kafka. Podrobné vysvětlení dostupných konfigurací naleznete v tématu [Nastavení clusterů v hdinsightu](../hdinsight-hadoop-provision-linux-clusters.md). Další informace týkající se použití portálu k vytvoření clusterů naleznete [v tématu Vytváření clusterů na portálu](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Rozhraní Apache Kafka API je přístupné jenom pro prostředky ve stejné virtuální síti. V tomto rychlém startu budete ke clusteru přistupovat přímo pomocí SSH. Pokud chcete k platformě Apache Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete v dokumentu [Připojení k platformě Apache Kafka pomocí virtuální sítě](apache-kafka-connect-vpn-gateway.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

Chcete-li vytvořit cluster Apache Kafka na HDInsight, použijte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V horní nabídce vyberte **+ Vytvořit prostředek**.

    ![Azure Portal vytvořit zdroj HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Vyberte **Analytics** > **Azure HDInsight** a přejděte na stránku **vytvořit cluster HDInsight.**

1. Na **kartě Základy** zadejte následující informace:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné    |  V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster. |
    |Skupina prostředků     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |Název clusteru   | Zadejte globálně jedinečný název. Název se může skládat až z 59 znaků včetně písmen, čísel a spojovníků. První a poslední znak názvu nemůže být pomlčka. |
    |Region (Oblast)    | V rozevíracím seznamu vyberte oblast, ve které je cluster vytvořen.  Zvolte oblast, která je vám blíže, pro lepší výkon. |
    |Typ clusteru| Chcete-li otevřít **seznam,** vyberte vybrat typ clusteru. V seznamu vyberte jako typ clusteru **Kafka.**|
    |Version|Bude určena výchozí verze pro typ clusteru. Chcete-li zadat jinou verzi, vyberte je z rozevíracího seznamu.|
    |Přihlašovací uživatelské jméno a heslo clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít délku alespoň 10 znaků a musí obsahovat alespoň jednu číslici, jedno velké a jedno malé \)písmeno, jeden nealfanumerický znak (kromě znaků " " . Ujistěte se, že **nezadáváte** běžné heslo, jako je například Pass@word1.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser**.  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použití přihlašovacího hesla clusteru pro SSH| Zaškrtnutím tohoto políčka použijete stejné heslo pro uživatele SSH jako to, které jste zadali pro uživatele přihlášení k clusteru.|

   ![Azure Portal vytváří základy clusteru](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Každá oblast Azure (umístění) poskytuje _domény selhání_. Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

    Pro zajištění vysoké dostupnosti dat vyberte oblast (umístění), které obsahuje __tři domény selhání__. Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

    Chcete-li přestoupit do nastavení úložiště, vyberte kartu **Další: >>úložiště.**

1. Na kartě **Úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **Vybrat ze seznamu**.|
    |Účet primárního úložiště|V rozevíracím seznamu vyberte existující účet úložiště nebo vyberte **Vytvořit nový**. Pokud vytvoříte nový účet, musí být název o délce 3 až 24 znaků a může obsahovat pouze čísla a malá písmena.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![HDInsight Linux začíná poskytovat hodnoty úložiště clusteru](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Poskytnutí hodnot úložiště pro vytvoření clusteru HDInsight")

    Vyberte kartu **Zabezpečení + síť.**

1. Pro účely tohoto rychlého startu ponechte výchozí nastavení zabezpečení. Další informace o Balíčku zabezpečení podniku najdete v tématu [Konfigurace clusteru HDInsight s Balíčkem zabezpečení podniku pomocí služby Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Chcete-li se dozvědět, jak používat vlastní klíč pro šifrování disku Apache Kafka, navštivte [šifrování disku klíče spravované zákazníkem](../disk-encryption.md)

   Pokud chcete svůj cluster připojit k virtuální síti, vyberte virtuální síť v rozevíracím seznamu **Virtuální síť**.

   ![Přidání clusteru do virtuální sítě](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Vyberte kartu **Konfigurace + ceny.**

1. Chcete-li zaručit dostupnost Apache Kafka na HDInsight, __počet uzlů__ vstupu pro **uzel pracovníka** musí být nastavena na 3 nebo vyšší. Výchozí hodnota je 4.

    **Standardní disky na položku pracovního uzlu** konfiguruje škálovatelnost Apache Kafka na HDInsight. Apache Kafka ve službě HDInsight používá k ukládání dat místní disky virtuálních počítačů v clusteru. Platforma Apache Kafka je náročná na vstupně-výstupní operace, proto se k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel využívají [Spravované disky Azure](../../virtual-machines/windows/managed-disks-overview.md). Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Typ disku závisí na velikosti virtuálního počítače používaného pracovními uzly (zprostředkovateli Apache Kafka). U virtuálních počítačů řady DS a GS se automaticky používají disky Premium. Všechny ostatní typy virtuálních počítačů používají standardní disky.

   ![Nastavení velikosti clusteru Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Vyberte kartu **Revize + vytvoření.**

1. Zkontrolujte konfiguraci clusteru. Změňte všechna nesprávná nastavení. Nakonec vyberte **Vytvořit,** chcete-li vytvořit cluster.

    ![Souhrn konfigurace clusteru kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Vytvoření clusteru trvá přibližně 20 minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po zobrazení výzvy zadejte heslo uživatele SSH.

    Po připojení se zobrazí informace podobné tomuto textu:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Získejte informace o hostiteli Apache Zookeeper a Broker

Při práci s Kafkou musíte znát hostitele *Apache Zookeeper* a *Broker.* Tito hostitelé se používají s rozhraním Apache Kafka API a mnohými z nástrojů, které se se systémem Kafka dodávají.

V této části získáte informace o hostiteli z apache ambari rest api v clusteru.

1. Nainstalujte [jq](https://stedolan.github.io/jq/), procesor JSON příkazového řádku. Tento nástroj se používá k analýzě dokumentů JSON a je užitečné při analýzě informací o hostiteli. Z otevřeného připojení SSH zadejte `jq`následující příkaz k instalaci :

    ```bash
    sudo apt -y install jq
    ```

1. Nastavte proměnnou hesla. Nahraďte `PASSWORD` přihlašovacím heslem clusteru a zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahujte název clusteru s správně rozložených písmen. Skutečné velikosti písmen názvu clusteru se mohou lišit od očekávání v závislosti na způsobu vytvoření clusteru. Tento příkaz získá skutečné velikosti písmen a uloží jej do proměnné. Zadejte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Pokud provádíte tento proces mimo cluster, existuje jiný postup pro ukládání názvu clusteru. Získejte název clusteru v malá písmena z portálu Azure. Potom nahraďte název `<clustername>` clusteru v následujícím `export clusterName='<clustername>'`příkazu a spusťte jej: .


1. Chcete-li nastavit proměnnou prostředí s informacemi o hostiteli Zookeeper, použijte následující příkaz. Příkaz načte všechny hostitele Zookeeper a pak vrátí pouze první dvě položky. Je to proto, že chcete určitou redundanci pro případ, že jeden hostitel bude nedosažitelný.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Tento příkaz vyžaduje přístup Ambari. Pokud je váš cluster za skupinou sítě, spusťte tento příkaz z počítače, který má přístup k Ambari. 

1. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. K nastavení proměnné prostředí s použitím informací o hostiteli zprostředkovatele Apache Kafka použijte následující příkaz:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Tento příkaz vyžaduje přístup Ambari. Pokud je váš cluster za skupinou sítě, spusťte tento příkaz z počítače, který má přístup k Ambari. 

1. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
    echo $KAFKABROKERS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Správa témat Apache Kafka

Kafka ukládá datové proudy do *témat*. Témata můžete spravovat pomocí nástroje `kafka-topics.sh`.

* **K vytvoření tématu** použijte tento příkaz v připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz se připojí k hostiteli Zookeeper s použitím informací uložených v proměnné `$KAFKAZKHOSTS`. Pak vytvoří téma Apache Kafka s názvem **test**.

    * Data uložená v tomto tématu jsou rozdělená mezi osm oddílů.

    * Každý oddíl se replikuje mezi tři pracovní uzly v clusteru.

        Pokud jste vytvořili cluster v oblasti Azure, která poskytuje tři domény selhání, použijte faktor replikace 3. Jinak použijte faktor replikace 4.
        
        V oblastech se třemi doménami selhání faktor replikace 3 umožní rozložení replik mezi domény selhání. V oblastech se dvěma doménami selhání faktor replikace 4 rozloží repliky rovnoměrně mezi domény selhání.
        
        Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        Apache Kafka nemá o doménách selhání Azure žádné informace. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti.

        Chcete-li zajistit vysokou dostupnost, použijte [nástroj pro vyvážení oddílů Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z připojení SSH k hlavnímu uzlu clusteru Apache Kafka.

        K zajištění nejvyšší dostupnosti dat Apache Kafka byste měli obnovit rovnováhu replik oddílů pro vaše téma v těchto situacích:

        * Při vytvoření nového tématu nebo oddílu

        * Při vertikálním navýšení kapacity clusteru

* **K zobrazení seznamu témat** použijte tento příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz vypíše seznam témat dostupných v clusteru Apache Kafka.

* **K odstranění tématu** použijte tento příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz odstraní téma s názvem `topicname`.

    > [!WARNING]  
    > Pokud odstraníte dříve vytvořené téma `test`, pak ho musíte vytvořit znovu. Používá se v dalších krocích tohoto dokumentu.

Další informace o příkazech, které jsou k dispozici v nástroji `kafka-topics.sh`, získáte pomocí tohoto příkazu:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produkce a konzumace záznamů

Kafka ukládá *záznamy* v tématech. Záznamy jsou vytvářeny *producenty* a spotřebovávány *konzumenty*. Producenti a konzumenti komunikují se službou *zprostředkovatele Kafka*. Každý pracovní uzel v clusteru HDInsight je hostitelem zprostředkovatele Apache Kafka.

Pokud chcete uložit záznamy do dříve vytvořeného tématu test a pak je načíst pomocí konzumenta, použijte následující postup:

1. Pokud chcete zapsat záznamy do tématu, použijte nástroj `kafka-console-producer.sh` z připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Po tomto příkazu přejdete na prázdný řádek.

2. Na prázdný řádek zadejte textovou zprávu a stiskněte Enter. Tímto způsobem zadejte několik zpráv a pak se pomocí klávesové zkratky **Ctrl + C** vraťte na normální příkazový řádek. Každý řádek se odešle do tématu Apache Kafka jako samostatný záznam.

3. Pokud chcete číst záznamy z tématu, použijte nástroj `kafka-console-consumer.sh` z připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Tento příkaz načte záznamy z tématu a zobrazí je. Parametr `--from-beginning` způsobí, že konzument začne načítat od začátku datového proudu a zpracuje tak všechny záznamy.

    Pokud používáte starší verzi Kafka, nahraďte `--bootstrap-server $KAFKABROKERS` za `--zookeeper $KAFKAZKHOSTS`.

4. Konzumenta zastavíte stisknutím __Ctrl+C__.

Můžete také programově vytvořit producenty a spotřebitele. Příklad použití tohoto rozhraní API najdete v dokumentu [Apache Kafka Producer and Consumer API s HDInsight.](apache-kafka-producer-consumer-api.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto rychlém startu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]  
> Odstraněním clusteru Apache Kafka na HDInsight odstraníte všechna data uložená v Kafce.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
