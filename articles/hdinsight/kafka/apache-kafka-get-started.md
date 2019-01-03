---
title: Začínáme s Apache Kafka – Rychlý start pro Azure HDInsight
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka v Azure HDInsight pomocí portálu Azure Portal. Také se seznámíte s tématy, předplatiteli a konzumenty Kafka.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: mvc,hdinsightactive
ms.topic: quickstart
ms.date: 10/12/2018
ms.openlocfilehash: 76f09af66e362fb6b03346b43a6be1a3ec7cf681
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976762"
---
# <a name="quickstart-create-an-apache-kafka-on-hdinsight-cluster"></a>Rychlý start: Vytvoření Apache Kafka v clusteru HDInsight

Apache Kafka je open source distribuovaná streamovací platforma. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv. 

V tomto rychlém startu se dozvíte, jak vytvořit cluster [Apache Kafka](https://kafka.apache.org) pomocí webu Azure Portal. Dozvíte se také, jak používat obsažené nástroje k odesílání a příjmu zpráv pomocí platformy Apache Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]  
> Rozhraní Apache Kafka API je přístupné jenom pro prostředky ve stejné virtuální síti. V tomto rychlém startu budete ke clusteru přistupovat přímo pomocí SSH. Pokud chcete k platformě Apache Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky.
>
> Další informace najdete v dokumentu [Připojení k platformě Apache Kafka pomocí virtuální sítě](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Klient SSH. V krocích v tomto dokumentu se pro připojení ke clusteru používá SSH.

    Příkaz `ssh` je ve výchozím nastavení k dispozici v systémech Linux, Unix a macOS. Ve Windows 10 nainstalujte příkaz `ssh` některým z těchto způsobů:

    * Použijte [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). Cloud Shell poskytuje příkaz `ssh` a dá se nakonfigurovat tak, aby jako prostředí používal Bash nebo PowerShell.

    * [Nainstalujte podsystém Windows pro Linux](https://docs.microsoft.com/windows/wsl/install-win10). Distribuce Linuxu dostupné v Microsoft Storu poskytují příkaz `ssh`.

    > [!IMPORTANT]  
    > Kroky v tomto dokumentu předpokládají, že používáte některého z klientů SSH uvedených výše. Pokud používáte jiného klienta SSH a narazíte na potíže, zkuste najít řešení v dokumentaci pro vašeho klienta SSH.
    >
    > Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

K vytvoření clusteru Apache Kafka ve službě HDInsight použijte následující postup:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **+ vytvořit prostředek**.

1. V části **Azure marketplace**vyberte **Analytics**.

1. V části **doporučené**vyberte **HDInsight**.
   
    ![Vytvoření clusteru HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. V části **Základy** zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | --- | --- |
    | Název clusteru | Jedinečný název clusteru HDInsight. |
    | Předplatné | Vyberte své předplatné. |
    
   Vyberte __Typ clusteru__ a zobrazte **Konfiguraci clusteru**.
   
   ![Základní konfigurace clusteru Apache Kafka ve službě HDInsight](./media/apache-kafka-get-started/hdinsight-basic-configuration-1.png)

3. V části __Konfigurace clusteru__ vyberte následující hodnoty:

    | Nastavení | Hodnota |
    | --- | --- |
    | Typ clusteru | Kafka |
    | Verze | Kafka 1.1.0 (HDI 3.6) |

    Pomocí tlačítka **Vybrat** uložte nastavení typu clusteru a vraťte se do části __Základy__.

    ![Výběr typu clusteru](./media/apache-kafka-get-started/kafka-cluster-type.png)

4. V části __Základy__ zadejte nebo vyberte následující informace:

    | Nastavení | Hodnota |
    | --- | --- |
    | Uživatelské jméno přihlášení clusteru | Přihlašovací jméno pro přístup k webovým službám nebo rozhraním REST API hostovaným v clusteru. Ponechte výchozí hodnotu (admin). |
    | Heslo přihlášení clusteru | Přihlašovací heslo pro přístup k webovým službám nebo rozhraním REST API hostovaným v clusteru. |
    | Uživatelské jméno Secure Shell (SSH) | Přihlašovací údaje používané pro přístup ke clusteru přes SSH. Ve výchozím nastavení je heslo stejné jako pro přihlášení ke clusteru. |
    | Skupina prostředků | Skupina prostředků, ve které se cluster vytváří. |
    | Umístění | Oblast Azure, ve které se cluster vytváří. |

    > [!TIP]  
    > Každá oblast Azure (umístění) poskytuje _domény selhání_. Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Virtuální počítače a spravované disky, které implementují uzly v clusteru služby HDInsight, jsou distribuované napříč těmito doménami selhání. Tato architektura omezuje potenciální dopad selhání fyzického hardwaru.
    >
    > Pro zajištění vysoké dostupnosti dat vyberte oblast (umístění), které obsahuje __tři domény selhání__. Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

   ![Výběr předplatného](./media/apache-kafka-get-started/hdinsight-basic-configuration-2.png)

    Pomocí tlačítka __Další__ dokončete základní konfiguraci.

5. Pro účely tohoto rychlého startu ponechte výchozí nastavení zabezpečení. Další informace o Balíčku zabezpečení podniku najdete v tématu [Konfigurace clusteru HDInsight s Balíčkem zabezpečení podniku pomocí služby Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Informace o tom, jak k šifrování disku Apache Kafka použít vlastní klíč, najdete v tématu [Používání vlastního klíče pro Apache Kafka ve službě Azure HDInsight](apache-kafka-byok.md).

   Pokud chcete svůj cluster připojit k virtuální síti, vyberte virtuální síť v rozevíracím seznamu **Virtuální síť**.

   ![Přidání clusteru do virtuální sítě](./media/apache-kafka-get-started/kafka-security-config.png)

6. V části **Úložiště** vyberte nebo vytvořte účet úložiště. Pro ukázkový postup v tomto dokumentu ponechte ve všech ostatních polích výchozí hodnoty. Stisknutím tlačítka __Další__ uložte konfiguraci úložiště. Další informace o použití Data Lake Storage Gen2 najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

   ![Nastavení účtu úložiště pro HDInsight](./media/apache-kafka-get-started/storage-configuration.png)

7. Pokud chcete pokračovat s výchozím nastavením, v části __Aplikace (volitelné)__ vyberte __Další__.

8. Pokud chcete pokračovat s výchozím nastavením, v části __Velikost clusteru__ vyberte __Další__.

    > [!IMPORTANT]  
    > K zajištění dostupnosti systému Apache Kafka ve službě HDInsight musí být položka __Počet pracovních uzlů__ nastavená na hodnotu 3 nebo vyšší. Výchozí hodnota je 4.
    
    > [!TIP]  
    > Položka **Počet disků na pracovní uzel** konfiguruje škálovatelnost systému Apache Kafka ve službě HDInsight. Apache Kafka ve službě HDInsight používá k ukládání dat místní disky virtuálních počítačů v clusteru. Platforma Apache Kafka je náročná na vstupně-výstupní operace, proto se k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel využívá služba [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Typ disku závisí na velikosti virtuálního počítače používaného pracovními uzly (zprostředkovateli Apache Kafka). U virtuálních počítačů řady DS a GS se automaticky používají disky Premium. Všechny ostatní typy virtuálních počítačů používají standardní disky.

   ![Nastavení velikosti clusteru Apache Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

9. Pokud chcete pokračovat s výchozím nastavením, v části __Upřesňující nastavení__ vyberte __Další__.

10. V části **Souhrn** zkontrolujte konfiguraci clusteru. Pomocí odkazů __Upravit__ opravte případná chybná nastavení. Nakonec stisknutím tlačítka Vytvořit cluster vytvořte.
   
    ![Souhrn konfigurace clusteru](./media/apache-kafka-get-started/kafka-configuration-summary.png)
   
    > [!NOTE]
    > Vytvoření clusteru trvá přibližně 20 minut.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

1. Pro připojení k primárnímu hlavnímu uzlu clusteru Apache Kafka, použijte následující příkaz. Nahraďte `sshuser` uživatelským jménem SSH. Nahraďte `mykafka` s názvem vaší Kafkacluster Apache.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Když se ke clusteru poprvé připojíte, ve vašem klientovi SSH se může zobrazit upozornění na nemožnost potvrzení pravosti hostitele. Po zobrazení výzvy zadejte __yes__ (ano) a pak stisknutím klávesy __Enter__ přidejte hostitele na seznam důvěryhodných serverů vašeho klienta SSH.

3. Po zobrazení výzvy zadejte heslo uživatele SSH.

Po připojení se zobrazí informace podobné tomuto textu:

```text
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
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Získání informací o Apache Zookeeper a Broker hostiteli

Při práci s platformou Kafka musíte znát *Apache Zookeeper* a *zprostředkovatele* hostitele. Tito hostitelé se používají s rozhraním Apache Kafka API a mnohými z nástrojů, které se se systémem Kafka dodávají.

V této části můžete získat informace o hostiteli od Apache Ambari REST API v clusteru.

1. Připojte se ke clusteru přes SSH a následujícím příkazem nainstalujte nástroj `jq`. Tento nástroj slouží k parsování dokumentů JSON a je užitečný při načítání informací o hostiteli:
   
    ```bash
    sudo apt -y install jq
    ```

2. K nastavení proměnné prostředí na název clusteru použijte následující příkaz:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Po zobrazení výzvy zadejte název clusteru Apache Kafka.

3. K nastavení proměnné prostředí s použitím informací o hostiteli Zookeeper použijte následující příkaz:
    
    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    > [!TIP]
    > Tento příkaz se přímo dotazuje služby Ambari na hlavním uzlu clusteru. K Ambari můžete také přistupovat prostřednictvím veřejné adresy `https://$CLUSTERNAME.azurehdinsight.net:80/`. Některé síťové konfigurace mohou přístupu k veřejné adrese bránit. Použití skupin zabezpečení sítě například omezuje přístup k HDInsight ve virtuální síti.

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (ne účtu SSH).

    > [!NOTE]
    > Tento příkaz načte všechny hostitele Zookeeper a pak vrátí jenom první dva záznamy. Je to proto, že chcete určitou redundanci pro případ, že jeden hostitel bude nedosažitelný.

4. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. K nastavení proměnné prostředí s použitím informací o hostiteli zprostředkovatele Apache Kafka použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (ne účtu SSH).

6. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
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

        > [!IMPORTANT]
        > Pokud jste vytvořili cluster v oblasti Azure, která poskytuje tři domény selhání, použijte faktor replikace 3. Jinak použijte faktor replikace 4.
        
        V oblastech se třemi doménami selhání faktor replikace 3 umožní rozložení replik mezi domény selhání. V oblastech se dvěma doménami selhání faktor replikace 4 rozloží repliky rovnoměrně mezi domény selhání.
        
        Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        > [!IMPORTANT] 
        > Apache Kafka nemá o doménách selhání Azure žádné informace. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti.

        Pokud chcete zajistit vysokou dostupnost, použijte [nástroj pro obnovení rovnováhy oddílů Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z připojení SSH k hlavnímu uzlu clusteru Apache Kafka.

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

    > [!NOTE]
    > Pokud používáte starší verzi Kafka, nahraďte `--bootstrap-server $KAFKABROKERS` za `--zookeeper $KAFKAZKHOSTS`.

4. Konzumenta zastavíte stisknutím __Ctrl+C__.

Můžete také programově vytvořit producenty a spotřebitele. Příklad použití tohoto rozhraní API, najdete v článku [Apache Kafka Producer and Consumer API ve službě HDInsight](apache-kafka-producer-consumer-api.md) dokumentu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto rychlém startu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá.
> 
> Odstraněním clusteru Apache Kafka ve službě HDInsight odstraníte také všechna data uložená v systému Kafka.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití Apache Sparku s využitím Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)

