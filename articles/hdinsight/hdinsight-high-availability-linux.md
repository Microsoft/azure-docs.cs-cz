---
title: Vysoká dostupnost pro Hadoop – Azure HDInsight
description: Přečtěte si, jak clustery HDInsight zlepšují spolehlivost a dostupnost pomocí dalšího hlavního uzlu. Přečtěte si, jak se to týká služeb Hadoop, jako je Ambari a podregistr, a také jak se jednotlivě připojit ke každému hlavnímu uzlu pomocí SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: vysoká dostupnost Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 767d87efcf94d720159dcf3b9dc42981ec957ef0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381394"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsight

Clustery HDInsight poskytují dva hlavní uzly, které zvyšují dostupnost a spolehlivost Apache Hadoopch služeb a úloh, které běží.

Hadoop dosahuje vysoké dostupnosti a spolehlivosti tím, že replikuje služby a data napříč několika uzly v clusteru. Standardní distribuce Hadoop mají obvykle pouze jeden hlavní uzel. Případný výpadek jednoho hlavního uzlu může způsobit, že cluster přestane fungovat. HDInsight poskytuje dvě hlavníchy, které zlepšují dostupnost a spolehlivost Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Dostupnost a spolehlivost uzlů

Uzly v clusteru HDInsight se implementují pomocí Azure Virtual Machines. Následující části popisují jednotlivé typy uzlů používané v HDInsight.

> [!NOTE]  
> Ne všechny typy uzlů se používají pro typ clusteru. Například typ clusteru Hadoop nemá žádné uzly Nimbus. Další informace o uzlech, které používají typy clusterů HDInsight, najdete v části typy clusterů v tématu [Vytvoření clusterů Hadoop se systémem Linux v dokumentu HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type) .

### <a name="head-nodes"></a>Hlavní uzly

Pro zajištění vysoké dostupnosti služeb Hadoop poskytuje HDInsight dva hlavní uzly. Oba hlavní uzly jsou aktivní a běží v clusteru HDInsight současně. Některé služby, jako je Apache HDFS nebo Apache Hadoop nitě, jsou v jednom hlavním uzlu v každém okamžiku aktivní pouze pro jeden hlavní uzel. Další služby, jako je HiveServer2 nebo podregistr MetaStore, jsou v obou hlavních uzlech aktivní.

Pokud chcete získat názvy hostitelů pro různé typy uzlů v clusteru, použijte prosím [REST API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Nepřidružte číselnou hodnotu k tomu, zda je uzel primární nebo sekundární. Číselná hodnota je přítomna pouze pro zadání jedinečného názvu pro každý uzel.

### <a name="nimbus-nodes"></a>Uzly Nimbus

Uzly Nimbus jsou k dispozici v clusterech Apache Storm. Uzly Nimbus poskytují podobné funkce pro Hadoop JobTracker, a to distribucí a monitorováním zpracování napříč pracovními uzly. HDInsight poskytuje dva uzly Nimbus pro clustery s více prostředími.

### <a name="apache-zookeeper-nodes"></a>Uzly Apache Zookeeper

Uzly [Zookeeper](https://zookeeper.apache.org/) slouží k volbě vedoucích služeb Master Services u hlavních uzlů. Používají se také k zajištění toho, aby služby, uzly dat (Worker) a brány věděly, ve kterých hlavních uzlech je hlavní služba aktivní. Ve výchozím nastavení služba HDInsight poskytuje tři uzly ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

Pracovní uzly provádějí vlastní analýzu dat při odeslání úlohy do clusteru. Pokud se pracovní uzel nezdařil, úloha, kterou prováděl, je odeslána do jiného pracovního uzlu. Ve výchozím nastavení vytvoří HDInsight čtyři pracovní uzly. Toto číslo můžete změnit tak, aby vyhovovalo vašim potřebám během i po vytvoření clusteru.

### <a name="edge-node"></a>hraniční uzel

Hraniční uzel se aktivně neúčastní analýzy dat v rámci clusteru. Používá ho vývojáři nebo odborníci přes data při práci se systémem Hadoop. Hraniční uzel žije ve stejné službě Azure Virtual Network jako ostatní uzly v clusteru a má přímý přístup ke všem ostatním uzlům. Hraniční uzel se dá použít, aniž byste museli přebírat prostředky z důležitých služeb Hadoop nebo úloh analýzy.

V současné době jsou služby ML v HDInsight jediným typem clusteru, který ve výchozím nastavení poskytuje hraniční uzel. Pro služby ML Services v HDInsight se hraniční uzel před odesláním do clusteru pro distribuované zpracování používá v místním prostředí k testování kódu R.

Informace o používání hraničního uzlu s jinými typy clusterů najdete v tématu [použití hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md) .

## <a name="accessing-the-nodes"></a>Přístup k uzlům

Přístup ke clusteru přes Internet se poskytuje prostřednictvím veřejné brány. Přístup je omezený na připojení k hlavním uzlům a pokud jeden existuje, hraniční uzel. Přístup ke službám, které běží na hlavních uzlech, nemá vliv na více hlavních uzlů. Veřejná brána směruje požadavky na hlavní uzel, který hostuje požadovanou službu. Pokud je například služba Apache Ambari aktuálně hostovaná v sekundárním hlavním uzlu, brána směruje příchozí požadavky na Ambari do tohoto uzlu.

Přístup přes veřejnou bránu je omezený na porty 443 (HTTPS), 22 a 23.

|Port |Popis |
|---|---|
|443|Slouží k přístupu k Ambari a dalším webovým uživatelským rozhraním nebo rozhraním API REST hostovaným na hlavních uzlech.|
|22|Slouží k přístupu k primárnímu hlavnímu uzlu nebo hraničnímu uzlu pomocí SSH.|
|23|Používá se pro přístup k sekundárnímu hlavnímu uzlu pomocí SSH. Například se `ssh username@mycluster-ssh.azurehdinsight.net` připojí k primárnímu hlavnímu uzlu clusteru s názvem **mycluster**.|

Další informace o používání SSH najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) .

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interní plně kvalifikované názvy domény (FQDN)

Uzly v clusteru HDInsight mají interní IP adresu a plně kvalifikovaný název domény, ke kterým se dá dostat jenom z clusteru. Při přístupu ke službám na clusteru pomocí interního plně kvalifikovaného názvu domény nebo IP adresy byste měli použít Ambari k ověření IP adresy nebo plně kvalifikovaného názvu domény pro použití při přístupu ke službě.

Například služba Apache Oozie se dá spustit jenom na jednom hlavním uzlu a použití `oozie` příkazu z relace SSH vyžaduje adresu URL služby. Tuto adresu URL můžete načíst z Ambari pomocí následujícího příkazu:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Tento příkaz vrátí hodnotu podobnou následující, která obsahuje interní adresu URL pro použití s `oozie` příkazem:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Další informace o práci s Ambari REST API najdete v tématu [monitorování a Správa HDInsight pomocí REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Přístup k jiným typům uzlů

K uzlům, které nejsou přímo přístupné přes Internet, se můžete připojit pomocí následujících metod:

|Metoda |Popis |
|---|---|
|SSH|Po připojení k hlavnímu uzlu pomocí SSH můžete pomocí SSH z hlavního uzlu se připojit k ostatním uzlům v clusteru. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Tunel SSH|Pokud potřebujete přístup k webové službě hostované na jednom z uzlů, které nejsou přístupné pro Internet, musíte použít tunel SSH. Další informace najdete v tématu [použití tunelu SSH](hdinsight-linux-ambari-ssh-tunnel.md) v dokumentu HDInsight.|
|Azure Virtual Network|Pokud je cluster HDInsight součástí Virtual Network Azure, může libovolný prostředek na stejném Virtual Network mít přímý přístup ke všem uzlům v clusteru. Další informace najdete v tématu [Naplánování virtuálního síťového dokumentu HDInsight](hdinsight-plan-virtual-network-deployment.md) .|

## <a name="how-to-check-on-a-service-status"></a>Postup kontroly stavu služby

Chcete-li zjistit stav služeb, které jsou spuštěny na hlavních uzlech, použijte webové uživatelské rozhraní Ambari nebo Ambari REST API.

### <a name="ambari-web-ui"></a>Webové uživatelské rozhraní Ambari

Webové uživatelské rozhraní Ambari je možné zobrazit `https://CLUSTERNAME.azurehdinsight.net`na adrese. Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte přihlašovací údaje uživatele HTTP pro váš cluster. Výchozí uživatelské jméno HTTP je **admin** a heslo je heslo, které jste zadali při vytváření clusteru.

Po přijetí na stránku Ambari jsou nainstalované služby uvedeny na levé straně stránky.

![Instalované služby Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Pro indikaci stavu se může zobrazit řada ikon, které se mohou objevit vedle služby. Jakékoli výstrahy týkající se služby lze zobrazit pomocí odkazu **výstrahy** v horní části stránky.  Ambari nabízí několik předdefinovaných výstrah.

Následující výstrahy vám pomůžou monitorovat dostupnost clusteru:

| Název výstrahy                               | Popis                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stav monitorování metriky                    | Tato výstraha indikuje stav procesu monitorování metrik, jak je určený skriptem stavu monitorování.                                                                                   |
| Prezenční signál agenta Ambari                   | Tato výstraha se aktivuje, pokud server ztratil kontakt s agentem.                                                                                                                        |
| Proces serveru ZooKeeper                 | Tato výstraha na úrovni hostitele se aktivuje, pokud se proces serveru ZooKeeper nedá určit a naslouchat v síti.                                                               |
| Stav serveru metadat IOCache           | Tato výstraha na úrovni hostitele se aktivuje, pokud se server metadat IOCache nedá určit a reagovat na požadavky klientů.                                                            |
| Webové uživatelské rozhraní JournalNode                       | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní JournalNode nedosažitelné.                                                                                                                 |
| Server Spark2 Thrift                     | Tato výstraha na úrovni hostitele se aktivuje, pokud server Thrift Spark2 nejde určit jako.                                                                                                |
| Proces serveru historie                   | Tato výstraha na úrovni hostitele se aktivuje, pokud se proces serveru historie nedá navázat a naslouchat v síti.                                                                |
| Webové uživatelské rozhraní serveru historie                    | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní serveru historie nedosažitelné.                                                                                                              |
| `ResourceManager`Webové uživatelské rozhraní                   | Tato výstraha na `ResourceManager` úrovni hostitele se aktivuje, pokud webové uživatelské rozhraní není dostupné.                                                                                                             |
| Shrnutí stavu NodeManager               | Tato výstraha na úrovni služby se aktivuje, pokud není v pořádku NodeManagers                                                                                                                    |
| Webové uživatelské rozhraní časové osy aplikace                      | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní serveru časové osy aplikace nedosažitelné.                                                                                                         |
| Shrnutí stavu datauzel                  | Tato výstraha na úrovni služby se aktivuje, pokud existují chybné datauzly.                                                                                                                       |
| Webové uživatelské rozhraní NameNode                          | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní NameNode nedosažitelné.                                                                                                                    |
| Proces kontroleru převzetí služeb při selhání ZooKeeper    | Tato výstraha na úrovni hostitele se aktivuje, pokud se nepodaří potvrdit proces ZooKeeper převzetí služeb při selhání v síti.                                                   |
| Webové uživatelské rozhraní serveru Oozie                      | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní Oozie serveru nedostupné.                                                                                                                |
| Stav serveru Oozie                      | Tato výstraha na úrovni hostitele se aktivuje, pokud se server Oozie nedá zjistit a reagovat na požadavky klientů.                                                                      |
| Proces metastore podregistru                   | Tato výstraha na úrovni hostitele se aktivuje, pokud se proces metastore podregistru nedá určit a naslouchat v síti.                                                                 |
| HiveServer2 proces                      | Tato výstraha na úrovni hostitele se aktivuje, pokud HiveServer nejde zjistit a reagovat na požadavky klientů.                                                                        |
| Stav serveru WebHCat                    | Tato výstraha na `templeton` úrovni hostitele se aktivuje, pokud stav serveru není v pořádku.                                                                                                            |
| Procento dostupných serverů ZooKeeper      | Tato výstraha se aktivuje, pokud je počet ZooKeeper serverů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu ZooKeeper.     |
| Server Spark2 Livy                       | Tato výstraha na úrovni hostitele se aktivuje, pokud se server Livy2 nedá zjistit.                                                                                                        |
| Server historie Spark2                    | Tato výstraha na úrovni hostitele se aktivuje, pokud se server historie Spark2 nedá zjistit.                                                                                               |
| Proces kolektoru metrik                | Tato výstraha se aktivuje, pokud se kolekce metriky nedá potvrdit a naslouchá na konfigurovaném portu po dobu v sekundách, která se rovná prahové hodnotě.                                 |
| Kolektor metrik – proces HBase Master | Tato výstraha se aktivuje, pokud se hlavní procesy v kolekci metriky (HBA) v síti nedají potvrzovat a naslouchat v síti pro nakonfigurovanou kritickou prahovou hodnotu zadanou v sekundách. |
| Dostupné monitory metriky v procentech       | Tato výstraha se aktivuje, pokud procento procesů monitorování metrik nefunguje a naslouchá v síti pro nakonfigurovaná upozornění a kritické prahové hodnoty.                             |
| Procento dostupných NodeManagers           | Tato výstraha se aktivuje, pokud je počet vypnutých NodeManagers v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu NodeManager.        |
| Stav NodeManager                       | Tato výstraha na úrovni hostitele kontroluje vlastnost stavu uzlu dostupnou ze součásti NodeManager.                                                                                              |
| Webové uživatelské rozhraní NodeManager                       | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní NodeManager nedosažitelné.                                                                                                                 |
| Stav vysoké dostupnosti NameNode        | Tato výstraha na úrovni služby se aktivuje v případě, že aktivní NameNode nebo pohotovostní NameNode nejsou spuštěné.                                                                                     |
| Proces datanode                         | Tato výstraha na úrovni hostitele se aktivuje, pokud se jednotlivé procesy datanode nedají navázat a naslouchat v síti.                                                         |
| Webové uživatelské rozhraní pro datauzel                          | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské rozhraní pro datauzel nedosažitelné.                                                                                                                    |
| Procento dostupných dostupných deníkových uzlů           | Tato výstraha se aktivuje, pokud je počet vypnutých dostupných deníkových uzlů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu JournalNode.        |
| Procento dostupných datanode              | Tato výstraha se aktivuje, pokud je počet nefunkčních uzlů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů datanode.              |
| Stav serveru Zeppelin                   | Tato výstraha na úrovni hostitele se aktivuje, pokud se server Zeppelin nedá zjistit a reagovat na požadavky klientů.                                                                   |
| Interaktivní proces HiveServer2          | Tato výstraha na úrovni hostitele se aktivuje, pokud HiveServerInteractive nejde zjistit a reagovat na požadavky klientů.                                                             |
| Aplikace LLAP                         | Tato výstraha se aktivuje, pokud se aplikace LLAP nedá zjistit a reagovat na požadavky.                                                                                    |

Můžete vybrat každou službu a zobrazit další informace.

I když stránka služby poskytuje informace o stavu a konfiguraci každé služby, neposkytuje informace o tom, na kterém hlavním uzlu služba běží. Chcete-li zobrazit tyto informace, použijte odkaz **hostitelé** v horní části stránky. Tato stránka zobrazuje hostitele v rámci clusteru, včetně hlavních uzlů.

![Seznam hostitelů Apache Ambari hlavnímu uzlu](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Výběr odkazu pro jeden z hlavních uzlů zobrazí služby a komponenty běžící v tomto uzlu.

![Stav součásti Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Další informace o použití Ambari najdete v tématu [monitorování a Správa HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

REST API Ambari je k dispozici prostřednictvím Internetu. Veřejná brána HDInsight zpracovává požadavky na směrování do hlavního uzlu, který je aktuálně hostitelem REST API.

Pomocí následujícího příkazu můžete kontrolovat stav služby prostřednictvím REST API Ambari:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Heslo účtu uživatele protokolu HTTP (admin **) nahraďte heslem.**
* Místo **CLUSTERNAME** zadejte název vašeho clusteru.
* Místo **ServiceName** nahraďte názvem služby, jejíž stav chcete zjistit.

Pokud například chcete ověřit stav služby **HDFS** v clusteru s názvem **mycluster** **s heslem hesla, použijte**následující příkaz:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Odpověď je podobná následujícímu kódu JSON:

```json
{
    "href" : "http://mycluster.wutj3h4ic1zejluqhxzvckxq0g.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

Adresa URL oznamuje, že služba je aktuálně spuštěná na hlavním uzlu s názvem **mycluster. wutj3h4ic1zejluqhxzvckxq0g**.

Stav oznamuje, že je služba aktuálně spuštěná nebo **spuštěná**.

Pokud si nejste jisti, jaké služby jsou v clusteru nainstalované, můžete k získání seznamu použít následující příkaz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Další informace o práci s Ambari REST API najdete v tématu [monitorování a Správa HDInsight pomocí REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Součásti služby

Služby mohou obsahovat komponenty, u kterých chcete zkontrolovat stav jednotlivě. Například HDFS obsahuje komponentu NameNode. Chcete-li zobrazit informace o komponentě, příkaz by byl:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Pokud si nejste jisti, jaké součásti služba poskytuje, můžete k načtení seznamu použít následující příkaz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Přístup k souborům protokolu na hlavních uzlech

### <a name="ssh"></a>SSH

V případě připojení k hlavnímu uzlu přes SSH se soubory protokolu nacházejí v **/var/log**. Například **/var/log/Hadoop-YARN/YARN** obsahuje protokoly pro příze.

Každý hlavní uzel může mít jedinečné položky protokolu, takže byste měli kontrolovat protokoly v obou.

### <a name="sftp"></a>SFTP

Můžete se také připojit k hlavnímu uzlu pomocí protokolu SSH protokol FTP (File Transfer Protocol) nebo Secure protokol FTP (File Transfer Protocol) (SFTP) a stáhnout soubory protokolu přímo.

Podobně jako při připojování ke clusteru použijte klienta SSH, musíte zadat název uživatelského účtu SSH a adresu SSH clusteru. Například, `sftp username@mycluster-ssh.azurehdinsight.net`. Po zobrazení výzvy zadejte heslo k účtu nebo zadejte veřejný klíč pomocí `-i` parametru.

Po připojení se zobrazí `sftp>` výzva. Z této výzvy můžete měnit adresáře, nahrávat a stahovat soubory. Například následující příkazy mění adresáře do adresáře **/var/log/Hadoop/HDFS** a pak stáhnou všechny soubory v adresáři.

    cd /var/log/hadoop/hdfs
    get *

Seznam dostupných příkazů zobrazíte zadáním `help` na `sftp>` příkazovém řádku.

> [!NOTE]  
> K dispozici jsou také grafická rozhraní, která umožňují vizualizovat systém souborů při připojení pomocí protokolu SFTP. [MobaXTerm](https://mobaxterm.mobatek.net/) například umožňuje procházet systém souborů pomocí rozhraní podobného Průzkumníku Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Pro přístup k souborům protokolu pomocí Ambari je nutné použít tunel SSH. Webová rozhraní pro jednotlivé služby nejsou veřejně vystavená na internetu. Informace o použití tunelu SSH najdete v tématu [použití tunelového dokumentu SSH](hdinsight-linux-ambari-ssh-tunnel.md) .

Z webového uživatelského rozhraní Ambari vyberte službu, pro kterou chcete zobrazit protokoly (například PŘÍZe). Pak použijte **Rychlé odkazy** k výběru hlavního uzlu, pro který chcete zobrazit protokoly.

![Použití rychlých odkazů k zobrazení protokolů](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Konfigurace velikosti uzlu

Velikost uzlu lze vybrat pouze při vytváření clusteru. Seznam různých velikostí virtuálních počítačů dostupných pro HDInsight najdete na stránce s cenami za službu [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Při vytváření clusteru můžete určit velikost uzlů. Následující informace obsahují pokyny k určení velikosti pomocí [Azure Portal](https://portal.azure.com/), [Azure PowerShell modulu AZ](/powershell/azureps-cmdlets-docs)a [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

* **Azure Portal**: při vytváření clusteru můžete nastavit velikost uzlů používaných clusterem:

    ![Obrázek Průvodce vytvořením clusteru s výběrem velikosti uzlu](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) při použití příkazu můžete nastavit velikost hlav, pracovních procesů a Zookeeper uzlů pomocí `--headnode-size`parametrů, `--workernode-size`a. `--zookeepernode-size`

* **Azure PowerShell**: při použití rutiny [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) můžete nastavit velikost hlav, pracovních procesů a Zookeeper uzlů pomocí parametrů `-HeadNodeSize`, `-WorkerNodeSize`a. `-ZookeeperNodeSize`

## <a name="next-steps"></a>Další kroky

Další informace o položkách, které jsou popsány v tomto článku, najdete v těchto tématech:

* [Referenční informace k Apache Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalace a konfigurace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Instalace a konfigurace Azure PowerShell modul AZ](/powershell/azure/overview)
* [Správa HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zřizování clusterů HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)
