---
title: Vysoká dostupnost pro Hadoop – Azure HDInsight
description: Zjistěte, jak clustery HDInsight zlepšují spolehlivost a dostupnost pomocí dalšího hlavního uzlu. Zjistěte, jak to ovlivňuje služby Hadoop, jako jsou Ambari a Hive, a jak se individuálně připojit ke každému hlavnímu uzlu pomocí SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: vysoká dostupnost hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 085933f9a74ee37779ce63ce499d89ea53a9f7d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198935"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Dostupnost a spolehlivost clusterů Apache Hadoop v HDInsightu

Clustery HDInsight poskytují dva hlavní uzly pro zvýšení dostupnosti a spolehlivosti spuštěných služeb Apache Hadoop a úloh.

Hadoop dosahuje vysoké dostupnosti a spolehlivosti replikací služeb a dat napříč více uzly v clusteru. Standardní distribuce Hadoopu však mají obvykle pouze jeden uzel. Jakýkoli výpadek jednoho hlavního uzlu může způsobit, že cluster přestane fungovat. HDInsight poskytuje dva headnody pro zlepšení dostupnosti a spolehlivosti Hadoopu.

## <a name="availability-and-reliability-of-nodes"></a>Dostupnost a spolehlivost uzlů

Uzly v clusteru HDInsight se implementují pomocí virtuálních počítačů Azure. V následujících částech se popisují jednotlivé typy uzlů používané s HDInsight.

> [!NOTE]  
> Pro typ clusteru se nepoužívají všechny typy uzlů. Například typ clusteru Hadoop nemá žádné uzly Nimbus. Další informace o uzlech používaných typy clusterů HDInsight najdete v části Typy clusterů v dokumentu [Create Linux Hadoop v dokumentu HDInsight.](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)

### <a name="head-nodes"></a>Hlavní uzly

Pro zajištění vysoké dostupnosti služeb Hadoop, HDInsight poskytuje dva hlavní uzly. Oba hlavní uzly jsou aktivní a běží v clusteru HDInsight současně. Některé služby, například Apache HDFS nebo Apache Hadoop YARN, jsou v daném okamžiku aktivní pouze na jednom hlavním uzlu. Ostatní služby, jako je HiveServer2 nebo Hive MetaStore jsou aktivní na obou hlavní uzly ve stejnou dobu.

Chcete-li získat názvy hostitelů pro různé typy uzlů v clusteru, použijte [rozhraní REST API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

> [!IMPORTANT]  
> Nepřidružte číselnou hodnotu k tomu, zda je uzel primární nebo sekundární. Číselná hodnota je k dispozici pouze poskytnout jedinečný název pro každý uzel.

### <a name="nimbus-nodes"></a>Nimbus uzly

Nimbus uzly jsou k dispozici s Clustery Apache Storm. Uzly Nimbus poskytují podobné funkce hadoopjobtrackeru distribucí a sledováním zpracování napříč pracovními uzly. HDInsight poskytuje dva uzly Nimbus pro clustery Storm

### <a name="apache-zookeeper-nodes"></a>Apache Zookeeper uzly

[ZooKeeper](https://zookeeper.apache.org/) uzly se používají pro vedoucí volby master služby na hlavní uzly. Používají se také k pojištění, že služby, datové (pracovní) uzly a brány vědí, na kterém hlavním uzlu je hlavní služba aktivní. Ve výchozím nastavení HDInsight poskytuje tři uzly ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

Pracovní uzly provádějí analýzu skutečných dat při odeslání úlohy do clusteru. Pokud pracovní uzel selže, úkol, který prováděl, je odeslán do jiného pracovního uzlu. Ve výchozím nastavení HDInsight vytvoří čtyři pracovní uzly. Toto číslo můžete změnit tak, aby vyhovovalo vašim potřebám během vytváření clusteru i po něm.

### <a name="edge-node"></a>hraniční uzel

Hraniční uzel se aktivně neúčastní analýzy dat v rámci clusteru. Používá se vývojáři nebo datoví vědci při práci s Hadoopem. Hraniční uzel žije ve stejné virtuální síti Azure jako ostatní uzly v clusteru a může přímo přistupovat ke všem ostatním uzlům. Hraniční uzel lze použít bez převzetí prostředků z kritických služeb Hadoop nebo úloh analýzy.

Služby ML Services na hdinsight je v současné době jediný typ clusteru, který poskytuje hraniční uzel ve výchozím nastavení. Pro ml služby na HDInsight uzel okraje se používá test R kód místně na uzlu před odesláním do clusteru pro distribuované zpracování.

Informace o použití hraničního uzlu s jinými typy clusterů najdete v dokumentu [Use edge nodes v HDInsight.](hdinsight-apps-use-edge-node.md)

## <a name="accessing-the-nodes"></a>Přístup k uzlům

Přístup ke clusteru přes internet je poskytován prostřednictvím veřejné brány. Přístup je omezen na připojení k hlavním uzlům a pokud existuje, hraniční uzel. Přístup ke službám spuštěným na hlavní uzlech není ovlivněn tím, že má více uzlů hlavy. Veřejná brána směruje požadavky na hlavní uzel, který hostuje požadovanou službu. Například pokud Apache Ambari je aktuálně hostována na sekundární hlavní uzel, brána směruje příchozí požadavky pro Ambari do tohoto uzlu.

Přístup přes veřejnou bránu je omezen na porty 443 (HTTPS), 22 a 23.

|Port |Popis |
|---|---|
|443|Používá se pro přístup k Ambari a další webové uživatelské prostředí nebo REST API hostované na hlavní uzly.|
|22|Slouží k přístupu k primárnímu hlavnímu uzlu nebo hraničnímu uzlu s SSH.|
|23|Slouží k přístupu k sekundárnímu hlavnímu uzlu s SSH. Například `ssh username@mycluster-ssh.azurehdinsight.net` se připojí k primárnímu hlavnímu uzlu clusteru s názvem **mycluster**.|

Další informace o používání SSH najdete v [dokumentu Použití SSH s HDInsight.](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interní plně kvalifikované názvy domén (FQDN)

Uzly v clusteru HDInsight mají interní IP adresu a hlavní název sítě, ke kterým lze přistupovat pouze z clusteru. Při přístupu ke službám v clusteru pomocí interního vícenežového fondu nebo adresy IP byste měli pomocí ambari ověřit, zda se ip nebo vícednázevn IP používá při přístupu ke službě.

Například služba Apache Oozie může běžet pouze na jednom `oozie` hlavním uzlu a použití příkazu z relace SSH vyžaduje adresu URL služby. Tuto adresu URL lze z Ambari načíst pomocí následujícího příkazu:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Tento příkaz vrátí hodnotu podobnou následující, která obsahuje `oozie` interní adresu URL pro použití s příkazem:

```output
"oozie.base.url": "http://<ACTIVE-HEADNODE-NAME>cx.internal.cloudapp.net:11000/oozie"
```

Další informace o práci s rozhraním REST API Ambari najdete v [tématu Sledování a správa HDInsightu pomocí rozhraní APACHE Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Přístup k jiným typům uzlů

K uzlům, které nejsou přímo přístupné přes Internet, se můžete připojit pomocí následujících metod:

|Metoda |Popis |
|---|---|
|SSH|Po připojení k hlavnímu uzlu pomocí SSH, pak můžete použít SSH z hlavního uzlu pro připojení k jiným uzlům v clusteru. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Tunel SSH|Pokud potřebujete získat přístup k webové službě hostované na jednom z uzlů, které nejsou vystaveny internetu, musíte použít tunel SSH. Další informace naleznete [v dokumentu Použití tunelového propojení SSH s HDInsight.](hdinsight-linux-ambari-ssh-tunnel.md)|
|Azure Virtual Network|Pokud je váš cluster HDInsight součástí virtuální sítě Azure, může každý prostředek ve stejné virtuální síti přímo přistupovat ke všem uzlům v clusteru. Další informace najdete v [tématu Plánování virtuální sítě pro HDInsight](hdinsight-plan-virtual-network-deployment.md) dokumentu.|

## <a name="how-to-check-on-a-service-status"></a>Jak zkontrolovat stav služby

Chcete-li zkontrolovat stav služeb spuštěných na hlavní uzly, použijte webové uživatelské rozhraní Ambari nebo rozhraní API Ambari REST.

### <a name="ambari-web-ui"></a>Webové uživatelské uzlina Ambari

Webové uživatelské uživatelské pole Ambari `https://CLUSTERNAME.azurehdinsight.net`lze zobrazit na adrese . Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte pověření uživatele HTTP pro váš cluster. Výchozí uživatelské jméno HTTP je **admin** a heslo je heslo, které jste zadali při vytváření clusteru.

Po příjezdu na stránku Ambari jsou nainstalované služby uvedeny v levé části stránky.

![Apache Ambari nainstalované služby](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Vedle služby se může zobrazit řada ikon, které označují stav. Všechna upozornění související se službou lze zobrazit pomocí odkazu **Výstrahy** v horní části stránky.  Ambari nabízí několik předdefinovaných výstrah.

Následující výstrahy pomáhají sledovat dostupnost clusteru:

| Název výstrahy                               | Popis                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stav monitoru metriky                    | Tato výstraha označuje stav procesu sledování metrik, jak je určen skriptu stavu monitorování.                                                                                   |
| Ambari Agent Heartbeat                   | Tato výstraha se aktivuje, pokud server ztratil kontakt s agentem.                                                                                                                        |
| Proces serveru ZooKeeper                 | Tato výstraha na úrovni hostitele se aktivuje, pokud proces serveru ZooKeeper nelze určit, že je v síti spuštěn a naslouchá.                                                               |
| Stav serveru metadat iOCache           | Tato výstraha na úrovni hostitele se aktivuje, pokud nelze určit server metadat IOCache, který je spuštěn a reaguje na požadavky klientů.                                                            |
| JournalNode Web UI                       | This host-level alert is triggered if the JournalNode Web UI is unreachable.                                                                                                                 |
| Spark2 Šetrnost Server                     | Tato výstraha na úrovni hostitele se aktivuje, pokud se server Sparku 2 nemůže určit, že je spuštěn.                                                                                                |
| Proces serveru historie                   | Tato výstraha na úrovni hostitele se aktivuje, pokud nelze vytvořit proces history server, aby byl spuštěn a naslouchá v síti.                                                                |
| Webové uživatelské uuživatelské uživatelské nastavení serveru historie                    | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské uživatelské nastavení serveru History Server nedostupné.                                                                                                              |
| `ResourceManager`Webové uživatelské uuživatelské hod                   | This host-level alert is triggered if the `ResourceManager` Web UI is unreachable.                                                                                                             |
| Souhrn stavu nodemanageru               | Tato výstraha na úrovni služby se aktivuje, pokud existují nejsou v pořádku NodeManagers                                                                                                                    |
| App Timeline Web UI                      | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské uživatelské nastavení serveru Časové osy aplikace nedostupné.                                                                                                         |
| Souhrn stavu datanode                  | Tato výstraha na úrovni služby se aktivuje, pokud existují nefunkční datanodes                                                                                                                       |
| Uživatelské uzlina NameNode                          | This host-level alert is triggered if the NameNode Web UI is unreachable.                                                                                                                    |
| Proces řadiče převzetí služeb při selhání ZooKeeper    | Tato výstraha na úrovni hostitele se aktivuje, pokud proces kontroler převzetí služeb při selhání ZooKeeper nelze potvrdit, že je v síti spuštěn a naslouchá.                                                   |
| Webové uživatelské nastavení serveru Oozie                      | Tato výstraha na úrovni hostitele se aktivuje, pokud je webové uživatelské uživatelské nastavení serveru Oozie nedostupné.                                                                                                                |
| Stav serveru Oozie                      | Tato výstraha na úrovni hostitele se aktivuje, pokud server Oozie nelze určit, že je nahoru a reagovat na požadavky klientů.                                                                      |
| Proces metastore úlu                   | Tato výstraha na úrovni hostitele se aktivuje, pokud proces Hive Metastore nelze určit, že je nahoru a naslouchání v síti.                                                                 |
| Proces HiveServer2                      | Tato výstraha na úrovni hostitele se aktivuje, pokud HiveServer nelze určit, že je nahoru a reagovat na požadavky klientů.                                                                        |
| Stav serveru WebHCat                    | Tato výstraha na úrovni `templeton` hostitele se aktivuje, pokud stav serveru není v pořádku.                                                                                                            |
| Procento ZooKeeper servery k dispozici      | Tato výstraha se aktivuje, pokud je počet serverů zookeeperu v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů ZooKeeper.     |
| Spark2 Livy Server                       | Tato výstraha na úrovni hostitele se aktivuje, pokud nelze zjistit, že je server Livy2.                                                                                                        |
| Spark2 History Server                    | Tato výstraha na úrovni hostitele se aktivuje, pokud server historie Spark2 nelze určit, že je nahoru.                                                                                               |
| Proces kolektoru metrik                | Tato výstraha se aktivuje, pokud kolektor metrik nelze potvrdit, že je nahoru a naslouchání na nakonfigurovaném portu po dobu několika sekund rovnající se prahové hodnotě.                                 |
| Kolektor metrik – hlavní proces HBase | Tato výstraha se aktivuje, pokud hlavní procesy HBase sběratele metrik nelze potvrdit, že jsou v síti spuštěny a naslouchají v síti pro nakonfigurovanou kritickou prahovou hodnotu uvedenou během několika sekund. |
| Dostupné monitorování procent metrik       | Tato výstraha se aktivuje, pokud procento procesů sledování metrik není nahoru a naslouchání v síti pro nakonfigurované upozornění a kritické prahové hodnoty.                             |
| Procento nodemanagers k dispozici           | Tato výstraha se aktivuje, pokud je počet nenodů manažerů v clusteru větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů NodeManager.        |
| Stav nodemanageru                       | Tato výstraha na úrovni hostitele kontroluje vlastnost stavu uzlu, která je k dispozici z komponenty NodeManager.                                                                                              |
| NodeManager Web UI                       | This host-level alert is triggered if the NodeManager Web UI is unreachable.                                                                                                                 |
| NázevNode Stav vysoké dostupnosti        | Tato výstraha na úrovni služby se aktivuje, pokud není spuštěna aktivní název namenode nebo pohotovostní název NameNode.                                                                                     |
| Proces datanode                         | Tato výstraha na úrovni hostitele se aktivuje, pokud jednotlivé procesy DataNode nelze vytvořit tak, aby byly v síti spuštěny a naslouchány.                                                         |
| DataNode Web UI                          | This host-level alert is triggered if the DataNode Web UI is unreachable.                                                                                                                    |
| Procento deníkůJsou k dispozici           | Tato výstraha se aktivuje, pokud je počet dolů Deníknodes v clusteru je větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesu JournalNode.        |
| Procento datových nodů k dispozici              | Tato výstraha se aktivuje, pokud je počet dolů DataNodes v clusteru je větší než nakonfigurovaná kritická prahová hodnota. Agreguje výsledky kontrol procesů DataNode.              |
| Stav serveru Zeppelin                   | Tato výstraha na úrovni hostitele se aktivuje, pokud server Zeppelin nelze určit, že je nahoru a reagovat na požadavky klientů.                                                                   |
| Interaktivní proces HiveServer2          | Tato výstraha na úrovni hostitele se aktivuje, pokud HiveServerInteractive nelze určit, že je nahoru a reagovat na požadavky klientů.                                                             |
| Aplikace LLAP                         | Tato výstraha se aktivuje, pokud nelze určit, že aplikace LLAP je spuštěna a reaguje na požadavky.                                                                                    |

Můžete vybrat každou službu a zobrazit o ní další informace.

Zatímco stránka služby poskytuje informace o stavu a konfiguraci každé služby, neposkytuje informace o tom, na kterém hlavním uzlu je služba spuštěna. Chcete-li tyto informace zobrazit, použijte odkaz **Hosts** v horní části stránky. Tato stránka zobrazuje hostitele v rámci clusteru, včetně předních uzlů.

![Apache Ambari headnode hostitelé seznam](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Výběrem odkazu pro jeden z hlavních uzlů se zobrazí služby a součásti spuštěné v tomto uzlu.

![Stav součásti Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Další informace o používání Ambari najdete [v tématu Sledování a správa HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Rozhraní Ambari REST API je k dispozici přes internet. Veřejná brána HDInsight zpracovává požadavky na směrování do hlavního uzlu, který je aktuálně hostitelem rozhraní REST API.

Pomocí následujícího příkazu můžete zkontrolovat stav služby prostřednictvím rozhraní Ambari REST API:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Nahraďte **heslo uživatelem** HTTP (správcem) heslem účtu.
* Místo **CLUSTERNAME** zadejte název vašeho clusteru.
* Nahraďte **SERVICENAME** názvem služby, kterou chcete zkontrolovat stav.

Chcete-li například zkontrolovat stav služby **HDFS** v clusteru s názvem **mycluster**, pomocí hesla **byste**použili následující příkaz:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

Odpověď je podobná následující JSON:

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

Adresa URL nám říká, že služba je v současné době spuštěna na hlavním uzlu s názvem **mycluster.wutj3h4ic1zejluqhxzvckxq0g**.

Stav nám říká, že služba je aktuálně spuštěna, nebo **STARTED**.

Pokud nevíte, jaké služby jsou v clusteru nainstalovány, můžete k načtení seznamu použít následující příkaz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Další informace o práci s rozhraním REST API Ambari najdete v [tématu Sledování a správa HDInsightu pomocí rozhraní APACHE Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Součásti servisu

Služby mohou obsahovat součásti, které chcete zkontrolovat stav jednotlivě. Například HDFS obsahuje komponentu NameNode. Chcete-li zobrazit informace o součásti, příkaz by byl:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Pokud nevíte, jaké součásti poskytuje služba, můžete k načtení seznamu použít následující příkaz:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak získat přístup k souborům protokolu na hlavní uzly

### <a name="ssh"></a>SSH

Při připojení k hlavnímu uzlu prostřednictvím SSH lze soubory protokolu nalézt pod **/var/log**. Například **/var/log/hadoop-yarn/yarn** obsahují protokoly pro YARN.

Každý hlavní uzel může mít jedinečné položky protokolu, takže byste měli zkontrolovat protokoly na obou.

### <a name="sftp"></a>SFTP

Můžete se také připojit k hlavnímu uzlu pomocí protokolu SSH File Transfer Protocol nebo Protokolu s bezpečným přenosem souborů (SFTP) a stáhnout soubory protokolu přímo.

Podobně jako při použití klienta SSH, při připojování ke clusteru je nutné zadat název uživatelského účtu SSH a adresu SSH clusteru. Například, `sftp username@mycluster-ssh.azurehdinsight.net`. Po zobrazení výzvy zadejte heslo pro účet nebo `-i` zadejte veřejný klíč pomocí parametru.

Po připojení se zobrazí `sftp>` výzva. Na této výzvu můžete změnit adresáře, nahrávat a stahovat soubory. Následující příkazy například změní adresáře do adresáře **/var/log/hadoop/hdfs** a poté stáhnou všechny soubory v adresáři.

    cd /var/log/hadoop/hdfs
    get *

Seznam dostupných příkazů naleznete `help` v `sftp>` řádku.

> [!NOTE]  
> Existují také grafická rozhraní, která umožňují vizualizovat systém souborů při připojení pomocí SFTP. Například [MobaXTerm](https://mobaxterm.mobatek.net/) umožňuje procházet systém souborů pomocí rozhraní podobného Průzkumníkovi Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Chcete-li získat přístup k souborům protokolu pomocí ambari, musíte použít tunelové propojení SSH. Webová rozhraní pro jednotlivé služby nejsou veřejně vystavena na internetu. Informace o použití tunelového propojení SSH naleznete v [dokumentu Použití tunelového propojení SSH.](hdinsight-linux-ambari-ssh-tunnel.md)

V uživatelském uživatelském panelu Ambari vyberte službu, pro kterou chcete zobrazit protokoly (například YARN). Potom pomocí **rychlých odkazů** vyberte, pro který hlavní uzel chcete protokoly zobrazit.

![Použití rychlých odkazů k zobrazení protokolů](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Jak nakonfigurovat velikost uzlu

Velikost uzlu lze vybrat pouze během vytváření clusteru. Seznam různých velikostí virtuálních počítačů dostupných pro HDInsight najdete na [stránce s cenami HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Při vytváření clusteru můžete určit velikost uzlů. Následující informace obsahují pokyny, jak určit velikost pomocí [portálu Azure Portal](https://portal.azure.com/), [modulu Azure PowerShell Az](/powershell/azureps-cmdlets-docs)a [rozhraní příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

* **Portál Azure**: Při vytváření clusteru můžete nastavit velikost uzlů používaných clusterem:

    ![Obrázek průvodce vytvořením clusteru s výběrem velikosti uzlu](./media/hdinsight-high-availability-linux/azure-portal-cluster-configuration-pricing-hadoop.png)

* **Azure CLI**: [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) Při použití příkazu můžete nastavit velikost uzly hlavy, pracovníka `--workernode-size`a `--zookeepernode-size` ZooKeeper pomocí `--headnode-size`, a parametry.

* **Azure PowerShell**: Při použití rutiny [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) můžete nastavit velikost uzly hlavy, pracovníka `-HeadNodeSize` `-WorkerNodeSize`a `-ZookeeperNodeSize` ZooKeeper pomocí , a parametry.

## <a name="next-steps"></a>Další kroky

Další informace o položkách popsaných v tomto článku najdete v následujících tématech:

* [Apache Ambari REST Odkaz](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalace a konfigurace rozhraní příkazového příkazu Azure](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Instalace a konfigurace modulu Azure PowerShell Az](/powershell/azure/overview)
* [Správa HDInsight pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zřízení clusterů HDInsight založených na Linuxu](hdinsight-hadoop-provision-linux-clusters.md)
