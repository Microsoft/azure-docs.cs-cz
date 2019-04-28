---
title: Vysoká dostupnost pro Hadoop – Azure HDInsight
description: Zjistěte, jak clustery HDInsight zvýšit spolehlivost a dostupnost pomocí dalšího hlavního uzlu. Zjistěte, jak to má vliv služby Hadoop jako je například Ambari a Hive, a jak se připojit jednotlivě každému hlavnímu uzlu pomocí SSH.
ms.reviewer: jasonh
author: hrasheed-msft
keywords: Vysoká dostupnost systému hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 596b53d468a7dfc719c16dc6e6339492381d7f41
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763811"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Dostupnost a spolehlivost clusterů systému Apache Hadoop v HDInsight

Clustery HDInsight poskytují dva hlavní uzly pro zvýšení dostupnosti a spolehlivosti služeb Apache Hadoop a spouštění úloh.

Hadoop dosahuje vysoké dostupnosti a spolehlivosti replikuje data a služby napříč několika uzly v clusteru. Ale standardní distribucích systému Hadoop obvykle mívají pouze jeden hlavní uzel. Jakémkoli výpadku jeden hlavní uzel může způsobit, že cluster přestane fungovat. HDInsight poskytuje dva hlavní uzly zlepšit dostupnost a spolehlivost Hadoop.

[!INCLUDE [windows-retirement-notice](../../includes/windows-retirement-notice.md)]

## <a name="availability-and-reliability-of-nodes"></a>Dostupnost a spolehlivost uzlů

Uzly v clusteru HDInsight jsou implementovány pomocí Azure Virtual Machines. Následující části popisují jednotlivých uzlů typy použité spolu s HDInsight. 

> [!NOTE]  
> Ne všechny typy uzlů se používají pro typ clusteru. Například typ clusteru Hadoop nemá žádné uzly Nimbus. Další informace o uzlech používá typy clusterů HDInsight, najdete v oddílu typy clusteru [založených na Linuxu vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentu.

### <a name="head-nodes"></a>Hlavní uzly

K zajištění vysoké dostupnosti služby Hadoop HDInsight poskytuje dva hlavní uzly. Oba hlavní uzly jsou současně aktivní a v chodu v rámci clusteru HDInsight. Některé služby, jako je například Apache HDFS nebo Apache Hadoop YARN, jsou aktivní na jeden hlavní uzel pouze v daném okamžiku. Jiné služby, jako je například HiveServer2 nebo Hive MetaStore jsou aktivní na oba hlavní uzly ve stejnou dobu.

Hlavní uzly (a dalších uzlů v HDInsight) mají číselnou hodnotu jako součást názvu hostitele uzlu. Například `hn0-CLUSTERNAME` nebo `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> S tím, zda je uzel primární nebo sekundární nepřidružujte číselnou hodnotu. Číselná hodnota se nachází pouze jako jedinečný název pro každý uzel.

### <a name="nimbus-nodes"></a>Uzly Nimbus

Uzly nimbus jsou k dispozici s clustery Apache Storm. Uzly Nimbus poskytují podobné funkce jako Hadoop jobtracker distribuci a monitorování zpracování v rámci pracovních uzlů. HDInsight poskytuje dva uzly Nimbus pro clustery Storm

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper uzly

[ZooKeeper](https://zookeeper.apache.org/) uzlů se používají pro vedoucího hlavní služeb na hlavní uzly. Používají se také zajistit, že služby, datových (pracovní) uzlů a brány vědět, které hlavního uzlu je hlavní služba aktivní na. Ve výchozím nastavení HDInsight poskytuje tři uzly ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

Pracovní uzly provádět analýzu skutečná data, když je úloha odeslána do clusteru. Pokud selže jeden uzel pracovního procesu, úkol, který byl výkon se odešle do jiného uzlu pracovního procesu. Ve výchozím nastavení vytvoří čtyři pracovní uzly HDInsight. Toto číslo tak, aby odpovídala vašim potřebám, během a po vytvoření clusteru můžete změnit.

### <a name="edge-node"></a>Hraniční uzel

Hraniční uzel není součástí aktivně analýzy dat v rámci clusteru. Používá se vývojáři nebo datovým vědcům při práci se systémem Hadoop. Na hraničním uzlu se nachází ve stejné virtuální síti Azure jako ostatní uzly v clusteru a přímý přístup k jiné uzly. Na hraničním uzlu je možné bez nutnosti převádět prostředky mimo kritické služby Hadoop nebo analytických úloh.

V současné době služby ML na HDInsight je jediný typ clusteru, který obsahuje hraniční uzel ve výchozím nastavení. Pro služby ML na HDInsight, se používá k hraničnímu uzlu R testovací kód místně v uzlu před jeho odesláním do clusteru pro distribuované zpracování.

Informace o použití hraniční uzel s jinými typy clusteru, najdete v článku [použití hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md) dokumentu.

## <a name="accessing-the-nodes"></a>Přístup k uzlům

Poskytuje přístup ke clusteru přes internet prostřednictvím veřejné brány. Přístup je omezený na připojení k hlavním uzlům a (pokud existuje) na hraničním uzlu. Přístup ke službám, které běží na hlavní uzly. to neovlivní tím, že více hlavních uzlů. Veřejnou brány směruje žádosti do hlavního uzlu, který je hostitelem požadovanou službu. Například pokud Apache Ambari je aktuálně hostitelem sekundárnímu hlavnímu uzlu, brány směruje příchozí žádosti pro Ambari do tohoto uzlu.

Přístup přes veřejnou brány je omezen na portu 443 (HTTPS), 22 a 23.

* Port __443__ slouží k přístupu k Ambari a další webové uživatelské rozhraní nebo rozhraní REST API hostované na hlavní uzly.

* Port __22__ slouží k přístupu k primárnímu hlavnímu uzlu nebo hraničnímu uzlu pomocí SSH.

* Port __23__ slouží k přístupu k sekundárnímu hlavnímu uzlu pomocí SSH. Například `ssh username@mycluster-ssh.azurehdinsight.net` připojí k primárnímu hlavnímu uzlu clusteru s názvem **mycluster**.

Další informace o používání SSH najdete v tématu [použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentu.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interní plně kvalifikované názvy domény (FQDN)

Uzly v clusteru služby HDInsight mají interní IP adresy a plně kvalifikovaný název domény, který je přístupný pouze z clusteru. Při přístupu ke službám v clusteru pomocí interní plně kvalifikovaný název domény nebo IP adresa, měli byste použít Ambari ověření IP adresy nebo plně kvalifikovaný název domény pro použití při přístupu ke službě.

Například služba Apache Oozie dají spustit jenom na jeden hlavní uzel a použití `oozie` příkaz z relace SSH vyžaduje adresu URL služby. Tuto adresu URL můžete získat z Ambari pomocí následujícího příkazu:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Tento příkaz vrátí hodnotu, podobně jako následující příkaz, který obsahuje interní adresa URL pro použití s `oozie` příkaz:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Další informace o práci se rozhraní Ambari REST API najdete v tématu [monitorování a správa HDInsight pomocí rozhraní REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Přístup k jiné typy uzlů

Můžete připojit k uzlům, které nejsou přímo přístupné přes internet pomocí následujících metod:

* **SSH**: Po připojení k hlavnímu uzlu pomocí SSH, pak vám pomůže SSH z hlavního uzlu připojení k ostatním uzlům v clusteru. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **SSH tunelového propojení**: Pokud potřebujete přístup k webové službě hostované na jednom z uzlů, které není přístupný z Internetu, musíte použít tunelu SSH. Další informace najdete v tématu [použití tunelu SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

* **Azure Virtual Network**: Pokud váš cluster HDInsight je součástí služby Azure Virtual Network, prostředek ve stejné virtuální síti přímo přístupné všechny uzly v clusteru. Další informace najdete v tématu [rozšířit HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="how-to-check-on-a-service-status"></a>Jak zkontrolovat stav služby

Pokud chcete zkontrolovat stav služby, které běží na hlavní uzly, pomocí uživatelského rozhraní Ambari Web nebo Ambari REST API.

### <a name="ambari-web-ui"></a>Ambari Web UI

Je viditelný na webové uživatelské rozhraní Ambari https://CLUSTERNAME.azurehdinsight.net. Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte přihlašovací údaje uživatele protokolu HTTP pro váš cluster. Je výchozí uživatelské jméno HTTP **správce** a heslo je heslo, které jste zadali při vytváření clusteru.

Při doručení na stránce Ambari, nainstalované služby jsou uvedené na levé straně stránky.

![Nainstalované služby](./media/hdinsight-high-availability-linux/services.png)

Existuje řada ikony, které se mohou objevit vedle služby k označení stavu. Žádné výstrahy týkající se služby lze zobrazit pomocí **výstrahy** odkazu v horní části stránky.  Ambari nabízí několik předdefinovaných výstrahy.

Tyto výstrahy pomáhají monitorovat dostupnost clusteru:

| Název výstrahy                               | Popis                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Metriky monitorování stavu                    | Tato výstraha indikuje stav metriky monitorování procesu podle monitorování stavu skriptu.                                                                                   |
| Ambari Agent Heartbeat                   | Tato výstraha se aktivuje, pokud server ztratí kontaktu s vloženým agentem.                                                                                                                        |
| ZooKeeper Server Process                 | Tato výstraha úrovni hostitele se aktivuje, pokud proces ZooKeeper server nemůže být určené dá zvládnout a naslouchá na síti.                                                               |
| IOCache Metadata Server Status           | Tato výstraha úrovni hostitele se aktivuje, pokud IOCache Metadata Server nemůže být určené dá zvládnout a reagovat na žádosti klientů                                                            |
| JournalNode Web UI                       | Pokud JournalNode webového uživatelského rozhraní nedostupné, se aktivuje upozornění na této úrovni hostitele.                                                                                                                 |
| Spark2 Thrift Server                     | Pokud Spark2 Thrift Server nelze určit dá zvládnout, se aktivuje upozornění na této úrovni hostitele.                                                                                                |
| Proces serveru historie                   | Tato výstraha úrovni hostitele se aktivuje, pokud proces historie serveru nemůže být zavedený dá zvládnout a naslouchá na síti.                                                                |
| Historie serveru webového uživatelského rozhraní                    | Pokud webové uživatelské rozhraní historie Server nedostupný, se aktivuje upozornění na této úrovni hostitele.                                                                                                              |
| Webové uživatelské rozhraní správce prostředků                   | Tato výstraha úrovni hostitele se aktivuje, pokud nedostupný webového uživatelského rozhraní správce prostředků.                                                                                                             |
| Souhrn stavu NodeManager               | Tato úroveň služby výstraha se aktivuje, pokud není v pořádku NodeManagers                                                                                                                    |
| Časová osa webové aplikace uživatelského rozhraní                      | Pokud webového uživatelského rozhraní aplikace časová osa Server nedostupný, se aktivuje upozornění na této úrovni hostitele.                                                                                                         |
| Souhrn stavu DataNode                  | Tato úroveň služby výstraha se aktivuje, pokud není v pořádku DataNodes                                                                                                                       |
| NameNode webového uživatelského rozhraní                          | Pokud NameNode webového uživatelského rozhraní nedostupné, se aktivuje upozornění na této úrovni hostitele.                                                                                                                    |
| Proces převzetí služeb při selhání Kontroleru zooKeeper    | Tato výstraha úrovni hostitele se aktivuje, pokud proces převzetí služeb při selhání Kontroleru ZooKeeper nemůže být potvrzen dá zvládnout a naslouchá na síti.                                                   |
| Oozie Server webového uživatelského rozhraní                      | Pokud server Oozie webového uživatelského rozhraní nedostupný, se aktivuje upozornění na této úrovni hostitele.                                                                                                                |
| Stav serveru Oozie                      | Pokud Oozie server nemůže být určené dá zvládnout a reagovat na požadavky klientů, se aktivuje upozornění na této úrovni hostitele.                                                                      |
| Proces Metastoru Hive                   | Tato výstraha úrovni hostitele se aktivuje, pokud proces Hive Metastore nemůže být určené dá zvládnout a naslouchá na síti.                                                                 |
| Proces serveru HiveServer2                      | Pokud HiveServer nemůže být určené dá zvládnout a reagovat na požadavky klientů, se aktivuje upozornění na této úrovni hostitele.                                                                        |
| WebHCat Server Status                    | Pokud server templeton stav není v pořádku, se aktivuje upozornění na této úrovni hostitele.                                                                                                            |
| Procento ZooKeeper servery, které jsou k dispozici      | Tato výstraha se aktivuje, pokud počet dolů ZooKeeper servery v clusteru je větší než nastavená prahová hodnota důležité. Agreguje výsledky kontrol procesu ZooKeeper.     |
| Spark2 Livy Server                       | Pokud Livy2 Server nelze určit dá zvládnout, se aktivuje upozornění na této úrovni hostitele.                                                                                                        |
| Server historie Spark2                    | Pokud Server historie Spark2 nelze určit dá zvládnout, se aktivuje upozornění na této úrovni hostitele.                                                                                               |
| Proces shromažďování metrik                | Tato výstraha se aktivuje, pokud kolekce metriky nemůže být potvrzen dá zvládnout a naslouchá na konfigurovaném portu pro počet sekund rovna prahové hodnoty.                                 |
| Metriky kolekcí – proces hlavní server HBase | Tato výstraha se aktivuje, pokud hlavní procesy shromažďování metrik HBase nemůže být potvrzen dá zvládnout a naslouchání v síti pro nakonfigurovanou prahovou hodnotu kritického, zadaný v řádu sekund. |
| Monitorování procenta metriky k dispozici       | Tato výstraha se aktivuje, pokud procento metriky monitorování procesů nejsou nahoru a naslouchá na síti nakonfigurované upozornění a kritické prahové hodnoty.                             |
| Procento NodeManagers k dispozici           | Tato výstraha se aktivuje, pokud počet dolů NodeManagers v clusteru je větší než nastavená prahová hodnota důležité. Agreguje výsledky kontrol NodeManager procesu.        |
| NodeManager Health                       | Tato výstraha úrovni hostitele kontroly stavu vlastnost uzlu, které jsou k dispozici z komponenty NodeManager.                                                                                              |
| NodeManager Web UI                       | Pokud NodeManager webového uživatelského rozhraní nedostupné, se aktivuje upozornění na této úrovni hostitele.                                                                                                                 |
| Stav NameNode vysoké dostupnosti        | Tato úroveň služby výstraha se aktivuje, pokud nejsou spuštěné na aktivní NameNode nebo NameNode pohotovostní režim.                                                                                     |
| DataNode procesu                         | Pokud jednotlivé procesy DataNode nemůže být zavedený dá zvládnout a naslouchá na síti, se aktivuje upozornění na této úrovni hostitele.                                                         |
| DataNode webového uživatelského rozhraní                          | Pokud DataNode webového uživatelského rozhraní nedostupné, se aktivuje upozornění na této úrovni hostitele.                                                                                                                    |
| Procento JournalNodes k dispozici           | Tato výstraha se aktivuje, pokud počet dolů JournalNodes v clusteru je větší než nastavená prahová hodnota důležité. Agreguje výsledky kontrol JournalNode procesu.        |
| Procento DataNodes k dispozici              | Tato výstraha se aktivuje, pokud počet dolů DataNodes v clusteru je větší než nastavená prahová hodnota důležité. Agreguje výsledky kontrol DataNode procesu.              |
| Stav aplikace Zeppelinu serveru                   | Pokud Zeppelin server nemůže být určené dá zvládnout a reagovat na požadavky klientů, se aktivuje upozornění na této úrovni hostitele.                                                                   |
| Interaktivní proces serveru HiveServer2          | Pokud HiveServerInteractive nemůže být určené dá zvládnout a reagovat na požadavky klientů, se aktivuje upozornění na této úrovni hostitele.                                                             |
| Aplikace funkcí LLAP                         | Tato výstraha se aktivuje, pokud aplikace LLAP nemůže být určené dá zvládnout a reagovat na požadavky.                                                                                    |

Každá služba zobrazíte další informace v něm můžete vybrat.

Když na stránku služby obsahuje informace o stavu a konfiguraci každé služby, neposkytuje informace, na které hlavního uzlu je služba spuštěna na. Chcete-li zobrazit tyto informace, použijte **hostitele** odkazu v horní části stránky. Tato stránka zobrazuje hostitele v clusteru, a to včetně hlavní uzly.

![seznam hostitelů](./media/hdinsight-high-availability-linux/hosts.png)

Vyberte odkaz pro jeden z hlavních uzlů se zobrazí služeb a součástí, které běží na tomto uzlu.

![Stav součásti](./media/hdinsight-high-availability-linux/nodeservices.png)

Další informace o použití Ambari, naleznete v tématu [monitorování a správa HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Rozhraní Ambari REST API je k dispozici přes internet. HDInsight bránu veřejné zpracovává směrování žádostí na hlavní uzel, který je aktuálně hostitelem rozhraní REST API.

Pokusila zkontrolovat stav služby pomocí rozhraní Ambari REST API můžete použít následující příkaz:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Nahraďte **heslo** heslem účtu uživatele (správce) protokolu HTTP.
* Místo **CLUSTERNAME** zadejte název vašeho clusteru.
* Nahraďte **SERVICENAME** s názvem služby, které chcete zkontrolovat stav.

Například, chcete-li zkontrolovat stav **HDFS** služby v clusteru s názvem **mycluster**, s použitím hesla **heslo**, použijte následující příkaz:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Odpověď se podobá následující JSON:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

Adresa URL nám říká, že služba běží na hlavního uzlu s názvem **hn0 CLUSTERNAME**.

Stav nám říká, že je služba aktuálně spuštěna, nebo **spuštěno**.

Pokud si nejste jisti, jaké služby jsou nainstalované v clusteru, můžete použít následující příkaz pro načtení seznamu:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Další informace o práci se rozhraní Ambari REST API najdete v tématu [monitorování a správa HDInsight pomocí rozhraní REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Součásti služby

Služby mohou obsahovat komponenty, které chcete zkontrolovat stav jednotlivě. Například HDFS obsahuje komponentu NameNode. Chcete-li zobrazit informace na komponentu, by příkaz vypadal takto:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Pokud si nejste jisti, jaké součásti jsou k dispozici ve službě, můžete použít následující příkaz k načtení seznamu:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak získat přístup k protokolu souborů na hlavní uzly.

### <a name="ssh"></a>SSH

Při připojení k hlavnímu uzlu přes SSH, souborů protokolu najdete v části **/var/log**. Například **/var/log/hadoop-yarn/yarn** obsahovat protokoly YARN.

Každý hlavního uzlu může mít položky jedinečný protokolu, měli byste zkontrolovat protokoly na obojí.

### <a name="sftp"></a>SFTP

Můžete také připojit k hlavnímu uzlu pomocí SSH File Transfer Protocol nebo rozhraní Secure File Transfer Protocol (SFTP) a přímo stahovat soubory protokolu.

Podobně jako pomocí klienta SSH, při připojování ke clusteru musíte zadat název uživatelského účtu SSH a adresa SSH clusteru. Například, `sftp username@mycluster-ssh.azurehdinsight.net`. Zadejte heslo pro účet po zobrazení výzvy, nebo zadat veřejný klíč pomocí `-i` parametru.

Jakmile budete připojeni, zobrazí se `sftp>` řádku. Z příkazového řádku, můžete změnit adresáře, nahrávání a stahování souborů. Například následující příkazy přejděte do adresáře **/var/log/hadoop/hdfs** adresář a potom stažení všechny soubory v adresáři.

    cd /var/log/hadoop/hdfs
    get *

Seznam dostupných příkazů zadejte `help` na `sftp>` řádku.

> [!NOTE]  
> Existují také grafické rozhraní, které umožňují vizualizovat systému souborů při připojení pomocí protokolu SFTP. Například [MobaXTerm](https://mobaxterm.mobatek.net/) umožňuje procházet pomocí rozhraní Windows Explorer podobně jako systém souborů.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Pro přístup k souborům protokolu pomocí nástroje Ambari, je nutné použít tunelu SSH. Webové rozhraní pro jednotlivé služby nejsou zveřejněné veřejně na Internetu. Informace o používání tunelu SSH najdete v tématu [používání tunelového propojení SSH](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Webové uživatelské rozhraní Ambari vyberte službu, kterou chcete zobrazit protokoly pro (například YARN). Pak pomocí **rychlé odkazy** vyberte které hlavního uzlu, chcete-li zobrazit v protokolech.

![Chcete-li zobrazit protokoly pomocí rychlé odkazy](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Jak konfigurovat velikost uzlu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Velikost uzlu lze vybrat pouze při vytváření clusteru. Pro HDInsight na najdete seznam různých velikostí virtuálních počítačů dostupných [HDInsight stránce s cenami](https://azure.microsoft.com/pricing/details/hdinsight/).

Při vytváření clusteru, můžete zadat velikost uzlů. Následující informace poskytují pokyny o tom, jak zadat velikost pomocí [webu Azure portal][preview-portal], [prostředí Azure PowerShell][azure-powershell]a [Klasické rozhraní příkazového řádku azure][azure-cli]:

* **Azure portal**: Při vytváření clusteru, můžete nastavit velikost uzlů cluster používat:

    ![Obrázek průvodce vytvořením clusteru s výběr velikost uzlu](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure Classic CLI**: Při použití `azure hdinsight cluster create` příkazu, můžete nastavit velikost head, pracovních procesů a uzly ZooKeeper s použitím `--headNodeSize`, `--workerNodeSize`, a `--zookeeperNodeSize` parametry.

* **Azure PowerShell**: Při použití `New-AzHDInsightCluster` rutiny, můžete nastavit velikost head, pracovních procesů a uzly ZooKeeper s použitím `-HeadNodeVMSize`, `-WorkerNodeSize`, a `-ZookeeperNodeSize` parametry.

## <a name="next-steps"></a>Další postup

Další informace o možnosti uvedené v tomto dokumentu pomocí následujících odkazů.

* [Reference k rozhraní Apache Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalace a konfigurace rozhraní příkazového řádku Azure Classic](../cli-install-nodejs.md)
* [Nainstalujte a nakonfigurujte Azure PowerShell](/powershell/azure/overview)
* [Správa HDInsight pomocí nástroje Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zřizování clusterů HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
