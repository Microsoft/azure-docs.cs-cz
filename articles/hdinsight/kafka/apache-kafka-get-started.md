---
title: 'Rychlý Start: nastavení Apache Kafka ve službě HDInsight pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka v Azure HDInsight pomocí portálu Azure Portal. Dozvíte se také o tématech, předplatitelích a konzumentech platformy Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 6198475025ff5222edeeb14cf25634ad2d916a1f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88651433"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí Azure Portal

[Apache Kafka](./apache-kafka-introduction.md) je open source platforma pro distribuované streamování. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv.

V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka pomocí Azure Portal. Dozvíte se také, jak používat obsažené nástroje k odesílání a příjmu zpráv pomocí platformy Apache Kafka. Podrobné vysvětlení dostupných konfigurací najdete v tématu [Nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Další informace týkající se používání portálu při vytváření clusterů najdete v tématu [Vytvoření clusterů na portálu](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Rozhraní Apache Kafka API je přístupné jenom pro prostředky ve stejné virtuální síti. V tomto rychlém startu získáte přímý přístup ke clusteru pomocí SSH. Pokud chcete k platformě Apache Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete v dokumentu [Připojení k platformě Apache Kafka pomocí virtuální sítě](apache-kafka-connect-vpn-gateway.md). Obecnější informace o plánování virtuálních sítí pro HDInsight najdete v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

Pokud chcete vytvořit cluster Apache Kafka v HDInsight, použijte následující postup:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V horní nabídce vyberte **+ vytvořit prostředek**.

    ![Azure Portal vytvoření prostředku HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Vyberte **Analytics**  >  **Azure HDInsight** a přejdete na stránku **vytvořit cluster HDInsight** .

1. Na kartě **základy** zadejte následující informace:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné    |  V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster. |
    |Skupina prostředků     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |Název clusteru   | Zadejte globálně jedinečný název. Název může obsahovat až 59 znaků včetně písmen, číslic a spojovníků. První a poslední znak názvu nemůže být pomlčka. |
    |Oblast    | V rozevíracím seznamu vyberte oblast, ve které se cluster vytvoří.  Vyberte oblast blíže pro lepší výkon. |
    |Typ clusteru| Vyberte **Vybrat typ clusteru** a otevřete seznam. V seznamu vyberte jako typ clusteru **Kafka** .|
    |Verze|Bude zadána výchozí verze typu clusteru. V rozevíracím seznamu vyberte, pokud chcete zadat jinou verzi.|
    |Přihlašovací uživatelské jméno a heslo clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden jiný než alfanumerický znak (kromě znaků) \) . Ujistěte se, že **nezadáváte** běžné heslo, jako je například Pass@word1.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser**.  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použít heslo přihlášení clusteru pro SSH| Toto políčko zaškrtněte, pokud chcete pro uživatele SSH použít stejné heslo jako ten, který jste zadali pro uživatele přihlášení clusteru.|

   ![Základy vytváření clusterů Azure Portal](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Každá oblast Azure (umístění) poskytuje _domény selhání_. Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

    Pro zajištění vysoké dostupnosti dat vyberte oblast (umístění), které obsahuje __tři domény selhání__. Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

    Vyberte kartu **Další: úložiště >>** , abyste mohli přejít k nastavení úložiště.

1. Na kartě **úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Účet primárního úložiště|Pomocí rozevíracího seznamu vyberte existující účet úložiště, nebo vyberte **vytvořit novou**. Pokud vytvoříte nový účet, musí mít název délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![Začínáme s HDInsight Linux poskytují hodnoty úložiště clusteru](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Zadejte hodnoty úložiště pro vytvoření clusteru HDInsight.")

    Vyberte kartu **zabezpečení + sítě** .

1. Pro tento rychlý Start ponechte výchozí nastavení zabezpečení. Další informace o Balíčku zabezpečení podniku najdete v tématu [Konfigurace clusteru HDInsight s Balíčkem zabezpečení podniku pomocí služby Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Informace o tom, jak používat vlastní klíč k šifrování Apache Kafka disku, najdete v tématu [šifrování klíčového disku spravovaného zákazníky](../disk-encryption.md) .

   Pokud chcete svůj cluster připojit k virtuální síti, vyberte virtuální síť v rozevíracím seznamu **Virtuální síť**.

   ![Přidání clusteru do virtuální sítě](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Vyberte kartu **Konfigurace + ceny** .

1. Aby byla zajištěna dostupnost Apache Kafka ve službě HDInsight, musí být položka __počet uzlů__ pro **uzel pracovního procesu** nastavena na 3 nebo vyšší. Výchozí hodnota je 4.

    Položka **standardní disky na pracovní uzel** nakonfiguruje škálovatelnost Apache Kafka v HDInsight. Apache Kafka ve službě HDInsight používá k ukládání dat místní disky virtuálních počítačů v clusteru. Platforma Apache Kafka je náročná na vstupně-výstupní operace, proto se k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel využívají [Spravované disky Azure](../../virtual-machines/managed-disks-overview.md). Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Typ disku závisí na velikosti virtuálního počítače používaného pracovními uzly (zprostředkovateli Apache Kafka). U virtuálních počítačů řady DS a GS se automaticky používají disky Premium. Všechny ostatní typy virtuálních počítačů používají standardní disky.

   ![Nastavení velikosti clusteru Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Vyberte kartu **Revize + vytvořit** .

1. Zkontrolujte konfiguraci clusteru. Změňte všechna nastavení, která jsou nesprávná. Nakonec vyberte **vytvořit** a vytvořte cluster.

    ![Souhrn konfigurace clusteru Kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Vytvoření clusteru trvá přibližně 20 minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

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

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Získání informací o hostiteli Apache Zookeeper a Broker

Při práci s Kafka je nutné znát hostitele *Apache Zookeeper* a *Broker* . Tito hostitelé se používají s rozhraním Apache Kafka API a mnohými z nástrojů, které se se systémem Kafka dodávají.

V této části získáte informace o hostiteli z REST API Apache Ambari v clusteru.

1. Nainstalujte [JQ](https://stedolan.github.io/jq/)procesor JSON pro příkazový řádek. Tento nástroj slouží k analýze dokumentů JSON a je užitečný při analýze informací o hostiteli. V otevřeném připojení SSH zadejte následující příkaz, který chcete nainstalovat `jq` :

    ```bash
    sudo apt -y install jq
    ```

1. Nastavte proměnnou hesla. Nahraďte `PASSWORD` heslem přihlášení clusteru a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahujte správně název clusteru použita. V závislosti na tom, jak byl cluster vytvořen, může být skutečná velikost názvu clusteru odlišná, než očekáváte. Tento příkaz získá skutečnou velikost písmen a uloží ji do proměnné. Zadejte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Pokud provádíte tento proces mimo cluster, existuje jiný postup pro uložení názvu clusteru. Získá název clusteru z Azure Portal malými písmeny. Pak v následujícím příkazu nahraďte název clusteru `<clustername>` a spusťte ho: `export clusterName='<clustername>'` .


1. K nastavení proměnné prostředí s informacemi o hostiteli Zookeeper použijte následující příkaz. Příkaz načte všechny hostitele Zookeeper a potom vrátí pouze první dvě položky. Je to proto, že chcete určitou redundanci pro případ, že jeden hostitel bude nedosažitelný.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Tento příkaz vyžaduje přístup Ambari. Pokud je váš cluster za NSG, spusťte tento příkaz z počítače, který má přístup k Ambari.

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
    > Tento příkaz vyžaduje přístup Ambari. Pokud je váš cluster za NSG, spusťte tento příkaz z počítače, který má přístup k Ambari.

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

        * Pokud jste vytvořili cluster v oblasti Azure, která poskytuje tři domény selhání, použijte faktor replikace 3. Jinak použijte faktor replikace 4.
        
        * V oblastech se třemi doménami selhání faktor replikace 3 umožní rozložení replik mezi domény selhání. V oblastech se dvěma doménami selhání faktor replikace 4 rozloží repliky rovnoměrně mezi domény selhání.
        
        * Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        * Apache Kafka nemá o doménách selhání Azure žádné informace. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti.

        * Pokud chcete zajistit vysokou dostupnost, použijte [Nástroj pro obnovení rovnováhy oddílu Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z připojení SSH k hlavnímu uzlu clusteru Apache Kafka.

        * K zajištění nejvyšší dostupnosti dat Apache Kafka byste měli obnovit rovnováhu replik oddílů pro vaše téma v těchto situacích:

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

Můžete také programově vytvořit producenty a spotřebitele. Příklad použití tohoto rozhraní API najdete v dokumentu [rozhraní API pro Apache Kafka výrobce a příjemce s](apache-kafka-producer-consumer-api.md) využitím služby HDInsight.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto rychlém startu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]  
> Odstranění clusteru Apache Kafka v HDInsight odstraní všechna data uložená v Kafka.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
