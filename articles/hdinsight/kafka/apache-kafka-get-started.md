---
title: Nastavení Apache Kafka ve službě HDInsight pomocí Azure Portal – rychlý Start
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka v Azure HDInsight pomocí Azure Portal. Naučíte se také Kafka témata, předplatitelé a spotřebitelé.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: f11cbdab59548906f751116a2ca7b9c545b25d91
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677889"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí Azure Portal

Apache Kafka je open source platforma pro distribuované streamování. Často se používá jako zprostředkovatel zpráv, protože poskytuje funkce podobné frontě pro publikování a odběr zpráv.

V tomto rychlém startu se dozvíte, jak vytvořit cluster [Apache Kafka](https://kafka.apache.org) pomocí Azure Portal. Naučíte se také, jak používat zahrnuté nástroje k posílání a přijímání zpráv pomocí Apache Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

K rozhraní Apache Kafka API se dá dostat jenom prostředky v rámci stejné virtuální sítě. V tomto rychlém startu získáte přímý přístup ke clusteru pomocí SSH. Pokud chcete k Apache Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejdřív vytvořit virtuální síť a pak vytvořit prostředky v síti. Další informace najdete v tématu [připojení k Apache Kafka pomocí virtuálního síťového](apache-kafka-connect-vpn-gateway.md) dokumentu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

Pokud chcete vytvořit Apache Kafka v clusteru HDInsight, použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo přejděte na **+ vytvořit prostředek** > **Analýza** > **HDInsight**.

    ![Azure Portal vytvoření prostředku HDInsight](./media/apache-kafka-get-started/create-hdinsight-cluster.png)

1. V části **základy**zadejte nebo vyberte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |formě    |  Vyberte své předplatné Azure. |
    |Skupina prostředků     | Vytvořte skupinu prostředků nebo vyberte existující skupinu prostředků.  Skupina prostředků je kontejner součástí Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a účet závislého Azure Storage. |
    |Název clusteru   | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery v HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může obsahovat až 59 znaků včetně písmen, číslic a spojovníků. První a poslední znak jména nesmí být spojovníky. |
    |Umístění    | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro lepší výkon vyberte umístění blíž. |
    |Typ clusteru| Vyberte **možnost vybrat typ clusteru**. Pak jako typ clusteru vyberte **Kafka** .|
    |Version|Bude zadána výchozí verze typu clusteru. V rozevíracím seznamu vyberte, pokud chcete zadat jinou verzi.|
    |Uživatelské jméno a heslo přihlášení clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků ' "' ' \). Ujistěte se, že **neposkytujete** společná hesla, jako je například "Pass@word1".|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser**.  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použít heslo přihlášení clusteru pro SSH| Toto políčko zaškrtněte, pokud chcete pro uživatele SSH použít stejné heslo jako ten, který jste zadali pro uživatele přihlášení clusteru.|

   ![Základy vytváření clusterů Azure Portal](./media/apache-kafka-get-started/azure-portal-cluster-basics-blank.png)

    Jednotlivé oblasti Azure (umístění) poskytují _domény selhání_. Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Každá doména selhání sdílí společný zdroj napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v rámci clusteru HDInsight, se distribuují napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.

    Pro zajištění vysoké dostupnosti dat vyberte oblast (umístění), která obsahuje __tři domény selhání__. Informace o počtu domén selhání v oblasti najdete v dokumentu věnovaném [dostupnosti virtuálních počítačů se systémem Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) .

    Vyberte kartu **Další: > úložiště >** přejděte k nastavení úložiště.

1. Na kartě **úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Primární účet úložiště|Pomocí rozevíracího seznamu vyberte existující účet úložiště, nebo vyberte **vytvořit novou**. Pokud vytvoříte nový účet, musí mít název délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|
    |Vnitřního|Použijte automaticky vyplněnou hodnotu.|

    ![Začínáme s HDInsight Linux poskytují hodnoty úložiště clusteru], které(./media/apache-kafka-get-started/azure-portal-cluster-storage-blank.png "poskytují hodnoty úložiště pro vytvoření clusteru HDInsight") .

    Vyberte kartu **zabezpečení + sítě** .

1. Pro tento rychlý Start ponechte výchozí nastavení zabezpečení. Pokud se chcete dozvědět víc o balíčku podnikového zabezpečení, navštivte téma [Konfigurace clusteru HDInsight s balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Informace o tom, jak používat vlastní klíč k šifrování Apache Kafka disku, najdete [v tématu Získání vlastního klíče pro Apache Kafka v Azure HDInsight](apache-kafka-byok.md) .

   Pokud chcete připojit cluster k virtuální síti, vyberte z rozevíracího seznamu **virtuální síť** virtuální síť.

   ![Přidat cluster do virtuální sítě](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vn.png)

    Vyberte kartu **Konfigurace + ceny** .

1. Aby byla zajištěna dostupnost Apache Kafka ve službě HDInsight, musí být položka __počet uzlů__ pro **uzel pracovního procesu** nastavena na 3 nebo vyšší. Výchozí hodnota je 4.

    Položka **standardní disky na pracovní uzel** nakonfiguruje škálovatelnost Apache Kafka v HDInsight. Apache Kafka v HDInsight používá k ukládání dat místní disk virtuálních počítačů v clusteru. Apache Kafka je v/v těžký, takže [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md) slouží k zajištění vysoké propustnosti a většího počtu úložiště na jeden uzel. Typ spravovaného disku může být buď __Standard__ (HDD), nebo __Premium__ (SSD). Typ disku závisí na velikosti virtuálního počítače, kterou používají pracovní uzly (Apache Kafka Brokers). Prémiové disky se používají automaticky s virtuálními počítači řady DS a GS. Všechny ostatní typy virtuálních počítačů používají standard.

   ![Nastavení velikosti clusteru Apache Kafka](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Vyberte kartu **Revize + vytvořit** .

1. Zkontrolujte konfiguraci clusteru. Změňte všechna nastavení, která jsou nesprávná. Nakonec vyberte **vytvořit** a vytvořte cluster.

    ![Souhrn konfigurace clusteru Kafka](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Vytvoření clusteru může trvat až 20 minut.

## <a name="connect-to-the-cluster"></a>Připojit ke clusteru

1. Pokud se chcete připojit k primárnímu hlavnímu uzlu Apache Kafka clusteru, použijte následující příkaz. Nahraďte `sshuser` uživatelským jménem SSH. Nahraďte `mykafka` názvem vaší aplikace Apache Kafkacluster.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Když se poprvé připojíte ke clusteru, váš klient SSH může zobrazit upozornění, že pravost hostitele není možné navázat. Po zobrazení výzvy zadejte __Ano__a potom stiskněte klávesu __ENTER__ , aby se hostitel přidal do seznamu důvěryhodných serverů klienta ssh.

3. Po zobrazení výzvy zadejte heslo uživatele SSH.

    Po připojení se zobrazí informace podobné následujícímu textu:
    
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

## <a id="getkafkainfo"></a>Získání informací o hostiteli Apache Zookeeper a Broker

Při práci s Kafka je nutné znát hostitele *Apache Zookeeper* a *Broker* . Tito hostitelé se používají s rozhraním Apache Kafka API a mnoho nástrojů, které se dodávají s Kafka.

V této části získáte informace o hostiteli z REST API Apache Ambari v clusteru.

1. Nainstalujte [JQ](https://stedolan.github.io/jq/)procesor JSON pro příkazový řádek. Tento nástroj slouží k analýze dokumentů JSON a je užitečný při analýze informací o hostiteli. V otevřeném připojení SSH zadejte následující příkaz pro instalaci `jq`:

    ```bash
    sudo apt -y install jq
    ```

2. Nastavte proměnné prostředí. Pomocí hesla pro přihlášení ke clusteru a názvu clusteru nahraďte `PASSWORD` a `CLUSTERNAME` a potom zadejte příkaz:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

3. Extrahuje správně použita název clusteru. V závislosti na tom, jak byl cluster vytvořen, může být skutečná velikost názvu clusteru odlišná, než očekáváte. Tento příkaz získá skutečná velká a malá písmena, uloží je do proměnné a pak zobrazí správně použita jméno a název, který jste zadali dříve. Zadejte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

4. K nastavení proměnné prostředí s informacemi o hostiteli Zookeeper použijte následující příkaz. Příkaz načte všechny hostitele Zookeeper a potom vrátí pouze první dvě položky. Důvodem je to, že chcete určitou redundanci v případě, že je jeden hostitel nedosažitelný.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Tento příkaz přímo dotazuje službu Ambari na hlavní uzel clusteru. K Ambari můžete také přistupovat pomocí veřejné adresy `https://$CLUSTERNAME.azurehdinsight.net:80/`. Některé konfigurace sítě můžou zabránit přístupu k veřejné adrese. Například použití skupin zabezpečení sítě (NSG) k omezení přístupu ke službě HDInsight ve virtuální síti.

5. Chcete-li ověřit, zda je proměnná prostředí nastavena správně, použijte následující příkaz:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Tento příkaz vrátí informace podobné následujícímu textu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

6. Chcete-li nastavit proměnnou prostředí pomocí informací o hostiteli zprostředkovatele Apache Kafka, použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

7. Chcete-li ověřit, zda je proměnná prostředí nastavena správně, použijte následující příkaz:

    ```bash   
    echo $KAFKABROKERS
    ```

    Tento příkaz vrátí informace podobné následujícímu textu:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Správa témat Apache Kafka

Kafka ukládá proudy dat v *tématech*. Pomocí nástroje `kafka-topics.sh` můžete spravovat témata.

* **Chcete-li vytvořit téma**, použijte následující příkaz v připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz se připojí k Zookeeper pomocí informací o hostiteli uložených v `$KAFKAZKHOSTS`. Pak vytvoří téma Apache Kafka s názvem **test**.

    * Data uložená v tomto tématu jsou rozdělená na více než osm oddílů.

    * Každý oddíl se replikuje mezi tři pracovní uzly v clusteru.

        Pokud jste cluster vytvořili v oblasti Azure, která obsahuje tři domény selhání, použijte faktor replikace 3. V opačném případě použijte faktor replikace 4.
        
        V oblastech se třemi doménami selhání umožňuje faktor replikace 3 repliky rozprostřít napříč doménami selhání. V oblastech se dvěma doménami selhání představuje faktor replikace čtyři dvojstránky rovnoměrně napříč doménami.
        
        Informace o počtu domén selhání v oblasti najdete v dokumentu věnovaném [dostupnosti virtuálních počítačů se systémem Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) .

        Apache Kafka nevědí o doménách selhání Azure. Při vytváření replik oddílů pro témata nemusí repliky správně distribuovat pro zajištění vysoké dostupnosti.

        Pokud chcete zajistit vysokou dostupnost, použijte [Nástroj pro obnovení rovnováhy oddílu Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj musí být spuštěn z připojení SSH k hlavnímu uzlu vašeho clusteru Apache Kafka.

        Pro nejvyšší dostupnost vašich Apache Kafka dat byste měli znovu vyrovnávat repliky oddílů pro vaše téma v těchto případech:

        * Vytvoříte nové téma nebo oddíl.

        * Horizontální navýšení kapacity clusteru

* **Chcete-li zobrazit seznam témat**, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz vypíše seznam témat, která jsou k dispozici v clusteru Apache Kafka.

* **Chcete-li odstranit téma**, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz odstraní téma s názvem `topicname`.

    > [!WARNING]  
    > Pokud odstraníte dříve vytvořené téma `test`, je nutné ho znovu vytvořit. Používá ho postup dále v tomto dokumentu.

Další informace o příkazech, které jsou k dispozici s nástrojem `kafka-topics.sh`, získáte pomocí následujícího příkazu:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Vytváření záznamů a jejich využívání

Kafka ukládá *záznamy* v tématech. Záznamy jsou vytvářeny *producenty*a využívány *spotřebiteli*. Producenti a spotřebitelé komunikují se službou *Kafka Broker* . Každý pracovní uzel v clusteru HDInsight je hostitel služby Apache Kafka Broker.

Chcete-li uložit záznamy do tématu test, které jste vytvořili dříve, a pak je přečíst pomocí příjemce, použijte následující postup:

1. Chcete-li zapsat záznamy do tématu, použijte nástroj `kafka-console-producer.sh` z připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Po provedení tohoto příkazu přijdete o prázdný řádek.

2. Do prázdného řádku zadejte textovou zprávu a stiskněte ENTER. Tímto způsobem zadejte několik zpráv a potom se pomocí **kombinace kláves CTRL + C** vraťte do normálního zobrazení výzvy. Každý řádek se odešle jako samostatný záznam Apache Kafka tématu.

3. Chcete-li číst záznamy z tématu, použijte nástroj `kafka-console-consumer.sh` z připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Tento příkaz načte záznamy z tématu a zobrazí je. Použití `--from-beginning` oznamuje spotřebiteli, aby začal od začátku streamu, takže se načtou všechny záznamy.

    Pokud používáte starší verzi Kafka, nahraďte `--bootstrap-server $KAFKABROKERS` pomocí `--zookeeper $KAFKAZKHOSTS`.

4. Pro zastavení příjemce použijte __kombinaci kláves CTRL + C__ .

Můžete také programově vytvářet výrobce a uživatele. Příklad použití tohoto rozhraní API najdete v dokumentu [rozhraní API pro Apache Kafka výrobce a příjemce s](apache-kafka-producer-consumer-api.md) využitím služby HDInsight.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto rychlého startu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků se odstraní také přidružený cluster HDInsight a všechny další prostředky přidružené ke skupině prostředků.

Odebrání skupiny prostředků pomocí Azure Portal:

1. V Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a potom zvolte __skupiny prostředků__ . zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a potom klikněte pravým tlačítkem myši na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__a pak potvrďte.

> [!WARNING]  
> Fakturace clusteru HDInsight začíná, jakmile se cluster vytvoří a zastaví se, když se cluster odstraní. Fakturuje se poměrnou částí po minutách, takže byste cluster měli vždycky odstranit, když už se nepoužívá.
>
> Odstranění Apache Kafka v clusteru HDInsight odstraní všechna data uložená v Kafka.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
