---
title: Vysoká dostupnost pro Hadoop - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak clustery HDInsight zvyšování spolehlivosti a dostupnosti pomocí dalšího hlavního uzlu. Zjistěte, jak to ovlivní služby Hadoop například Ambari a Hive, jak dobře jednotlivě připojit ke každému hlavního uzlu pomocí protokolu SSH.
services: hdinsight
editor: cgronlun
manager: cgronlun
author: Blackmist
documentationcenter: ''
tags: azure-portal
keywords: hadoop vysokou dostupnost
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: larryfr
ms.openlocfilehash: 974ed70fbda88dfcb775e021474583f7afb0576b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Dostupnost a spolehlivost clusterů Hadoop ve službě HDInsight

Clustery HDInsight poskytují dva uzly head zvýšit dostupnost a spolehlivost Hadoop služeb a spuštěné úlohy.

Hadoop dosahuje vysoké dostupnosti a spolehlivosti replikace služeb a dat mezi několika uzly v clusteru. Ale standardní distribuce systému Hadoop obvykle mít pouze jeden hlavní uzel. Všechny výpadku jeden hlavní uzel může způsobit clusteru přestane fungovat. HDInsight nabízí dva headnodes zvýšit dostupnost a spolehlivost na Hadoop.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="availability-and-reliability-of-nodes"></a>Dostupnost a spolehlivost uzlů

Uzly v clusteru HDInsight se implementují pomocí virtuálních počítačů Azure. Následující části popisují typy jednotlivých uzlů použít s HDInsight. 

> [!NOTE]
> Ne všechny typy uzlů se používají pro typ clusteru. Typ clusteru Hadoop například nemá žádné uzly Nimbus. Další informace o uzlech používá typy clusterů HDInsight, najdete v části typy clusteru [vytvořit systémem Linux Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentu.

### <a name="head-nodes"></a>hlavních uzlech

Zajistit vysokou dostupnost služeb Hadoop HDInsight nabízí dva uzly head. Obě hlavních uzlech současně jsou aktivní a spuštěné v rámci clusteru HDInsight. Některé služby, jako je například HDFS nebo YARN, jsou pouze aktivní jeden hlavního uzlu v daném okamžiku. Dalšími službami, například HiveServer2 nebo Metaúložiště Hive jsou aktivní v obou uzlech head ve stejnou dobu.

Uzly HEAD (a jiné uzly v HDInsight) mít číselnou hodnotu v rámci uzlu název hostitele. Například `hn0-CLUSTERNAME` nebo `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Nepřidružujte číselná hodnota k tom, zda je uzel primární nebo sekundární. Číselná hodnota nachází pouze zadejte jedinečný název pro každý uzel.

### <a name="nimbus-nodes"></a>Uzly Nimbus

Jsou k dispozici u clusterů Storm uzly nimbus. Uzly Nimbus poskytují podobné funkce jako Hadoop jobtracker distribuci a monitorování zpracování napříč uzly pracovního procesu. HDInsight nabízí dva uzly Nimbus pro clustery Storm

### <a name="zookeeper-nodes"></a>Uzly zookeeper

[ZooKeeper](http://zookeeper.apache.org/) uzlech se používají pro vedoucí volba hlavní služeb v hlavních uzlech. Používají se také zajistit, aby služby, datové (pracovník) uzly a brány věděli, jaké hlavního uzlu hlavní služby na aktivní. Ve výchozím nastavení HDInsight poskytuje tři uzly ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

Pracovní uzly provádět analýzy skutečná data, když je úloha odeslána do clusteru. V případě selhání pracovního uzlu je úloha, která byla provádění odeslán do jiného uzlu pracovníka. Ve výchozím nastavení vytvoří HDInsight čtyři uzly pracovního procesu. Toto číslo během a po vytvoření clusteru podle potřeby můžete změnit.

### <a name="edge-node"></a>Hraniční uzel

Hraniční uzel není součástí aktivně analýzu dat v rámci clusteru. Používá se vývojáři nebo datových vědců při práci s Hadoop. Hraničního uzlu je umístěn ve stejné virtuální síti Azure jako ostatní uzly v clusteru a přímý přístup k jiné uzly. Hraničního uzlu lze použít bez nutnosti převádět prostředky z úlohy analýzy nebo důležité služby Hadoop.

V současné době R serverem v HDInsight je pouze typ clusteru, který poskytuje hraniční uzel ve výchozím nastavení. Pro R serverem v HDInsight, se používá hraničního uzlu testovací R kód místně na uzlu před odesláním do clusteru pro distribuované zpracování.

Informace o používání hraniční uzel s typy clusteru než R Server najdete v tématu [používají uzly okraj v HDInsight](hdinsight-apps-use-edge-node.md) dokumentu.

## <a name="accessing-the-nodes"></a>Přístup k uzlu

Přístup ke clusteru přes internet je zajišťováno prostřednictvím veřejného brány. Přístup je omezen na připojení k hlavnímu uzlu a (pokud existuje) hraničního uzlu. Přístup ke službám systémem o hlavních uzlech není provedena tak, že více hlavních uzlech. Veřejné brány směruje požadavky k hlavnímu uzlu, který je hostitelem požadované služby. Například pokud Ambari je aktuálně hostitelem sekundárního hlavního uzlu, bránu směruje příchozí požadavky pro Ambari k tomuto uzlu.

Přístup prostřednictvím veřejného brány je omezený na portu 443 (HTTPS), 22 a 23.

* Port __443__ se používá pro přístup k Ambari a dalších webových uživatelského rozhraní nebo rozhraní REST API hostované o hlavních uzlech.

* Port __22__ se používá pro přístup k primární hlavní uzel nebo okraj uzel s SSH.

* Port __23__ se používá pro přístup sekundárního hlavního uzlu pomocí protokolu SSH. Například `ssh username@mycluster-ssh.azurehdinsight.net` připojí k primární hlavního uzlu v clusteru s názvem **mycluster**.

Další informace o používání SSH najdete v tématu [použití SSH s HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentu.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interní plně kvalifikované názvy domény (FQDN)

Uzly v clusteru služby HDInsight mít interní IP adresu a plně kvalifikovaný název domény, který lze přistupovat pouze z clusteru. Při přístupu k služby v clusteru pomocí interní plně kvalifikovaný název domény nebo IP adresa, měli byste použít Ambari ověření IP adresu nebo plně kvalifikovaný název domény, který má použít při přístupu ke službě.

Například službu Oozie lze spustit jen u jednoho hlavního uzlu a pomocí `oozie` příkaz z relace SSH vyžaduje adresu URL pro službu. Tato adresa URL může načíst z Ambari pomocí následujícího příkazu:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Tento příkaz vrátí hodnotu podobná následující příkaz, který obsahuje interní adresa URL pro použití s `oozie` příkaz:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Další informace o práci s Ambari REST API najdete v tématu [monitorování a správě HDInsight pomocí Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Přístup k jiné typy uzlů

Můžete připojit k uzlů, které nejsou přímo přístupné přes internet pomocí následujících metod:

* **SSH**: Po připojení k hlavnímu uzlu pomocí protokolu SSH, pak můžete SSH z hlavního uzlu se připojit k jiné uzly v clusteru. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Tunelové propojení SSH**: Pokud potřebujete pro přístup k webové službě hostované na jednom z uzlů, které není přístup k Internetu, musíte použít tunelového propojení SSH. Další informace najdete v tématu [použijte tunelového propojení SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

* **Virtuální síť Azure**: Pokud váš cluster HDInsight je součástí virtuální síť Azure, jakýkoli prostředek na stejné virtuální síti přímo přistupovat k všechny uzly v clusteru. Další informace najdete v tématu [rozšířit HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="how-to-check-on-a-service-status"></a>Tom, jak zkontrolovat stav služby

Chcete-li zkontrolovat stav služby, které běží o hlavních uzlech, použijte rozhraní Ambari Web nebo Ambari REST API.

### <a name="ambari-web-ui"></a>Ambari Web UI

Webové uživatelské rozhraní Ambari jsou viditelná na https://CLUSTERNAME.azurehdinsight.net. Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte přihlašovací údaje HTTP pro váš cluster. Výchozí uživatelské jméno protokolu HTTP je **správce** a heslo je heslo, které jste zadali při vytvoření clusteru.

Až přijedete na stránce Ambari, nainstalovaná services jsou uvedeny na levé straně stránky.

![Nainstalovaná services](./media/hdinsight-high-availability-linux/services.png)

Existuje řada ikony, které mohou být zobrazeny vedle služby, který indikuje stav. Žádné výstrahy týkající se služby lze zobrazit pomocí **výstrahy** odkaz v horní části stránky. Můžete vybrat každé služby můžete na něm zobrazit další informace.

Když stránku služby obsahuje informace o stavu a konfiguraci každé služby, neposkytuje informace, které hlavního uzlu je služba spuštěná na. Chcete-li tyto informace zobrazit, použijte **hostitele** odkaz v horní části stránky. Tato stránka zobrazuje hostitelích v clusteru, včetně hlavních uzlech.

![seznam hostitelů](./media/hdinsight-high-availability-linux/hosts.png)

Výběr odkaz pro jeden z hlavních uzlech zobrazí služeb a komponent spuštěných na tomto uzlu.

![Stav součásti](./media/hdinsight-high-availability-linux/nodeservices.png)

Další informace o používání Ambari najdete v tématu [monitorování a správě HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Ambari REST API

Ambari REST API je dostupná přes internet. Veřejné brány HDInsight zpracovává požadavky na směrování k hlavnímu uzlu, který je aktuálně hostitelem rozhraní REST API.

Chcete-li zkontrolovat stav služby pomocí rozhraní Ambari REST API můžete následující příkaz:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Nahraďte **heslo** pomocí protokolu HTTP (správce) hesla.
* Místo **CLUSTERNAME** zadejte název vašeho clusteru.
* Nahraďte **SERVICENAME** s názvem služby, které chcete zkontrolovat stav.

Například zkontrolovat stav **HDFS** služby v clusteru s názvem **mycluster**, s použitím hesla **heslo**, použijte následující příkaz:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Odpověď je podobná následujícím kódu JSON:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

Adresa URL víme, že služba běží aktuálně hlavního uzlu s názvem **hn0 CLUSTERNAME**.

Stav víme, že služba je aktuálně spuštěna, nebo **ZAČÍNÁME**.

Pokud si nejste jisti, jaké služby jsou nainstalovány v clusteru, můžete použít následující příkaz pro načtení seznamu:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Další informace o práci s Ambari REST API najdete v tématu [monitorování a správě HDInsight pomocí Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Součásti služby

Služby mohou obsahovat součásti, které chcete zkontrolovat stav jednotlivě. Například HDFS obsahuje součást, NameNode. Chcete-li zobrazit informace o součást, bude příkaz vypadat:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Pokud si nejste jisti, jaké součásti jsou poskytovány služby, můžete pro načtení seznamu následující příkaz:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Jak získat přístup k souborům protokolu o hlavních uzlech

### <a name="ssh"></a>SSH

Při připojení k hlavnímu uzlu prostřednictvím SSH, souborů protokolu najdete v části **/var/log**. Například **/var/log/hadoop-yarn/yarn** neobsahuje protokoly YARN.

Každý hlavního uzlu mohou obsahovat položky jedinečný protokolu, takže byste měli zkontrolovat protokoly na obě.

### <a name="sftp"></a>SFTP

Také můžete připojit k hlavnímu uzlu pomocí SSH protokol FTP nebo rozhraní zabezpečení souboru Transfer Protocol (SFTP) a přímo stáhnout soubory protokolu.

Podobně jako pomocí klienta SSH, při připojování ke clusteru musíte zadat název účtu uživatele SSH a adresa SSH clusteru. Například, `sftp username@mycluster-ssh.azurehdinsight.net`. Zadejte heslo pro účet po zobrazení výzvy, nebo zadejte veřejný klíč pomocí `-i` parametr.

Po připojení se zobrazí `sftp>` řádku. Z příkazového řádku, můžete změnit adresáře, nahrávání a stahování souborů. Například následující příkazy přejděte do adresáře **/var/log/hadoop/hdfs** adresář a pak stáhnout všechny soubory v adresáři.

    cd /var/log/hadoop/hdfs
    get *

Seznam dostupné příkazy, zadejte `help` na `sftp>` řádku.

> [!NOTE]
> Existují také grafické rozhraní, které vám umožní vizualizovat systému souborů při připojení pomocí protokolu SFTP. Například [MobaXTerm](http://mobaxterm.mobatek.net/) umožňuje procházet přes rozhraní podobně jako Průzkumník systému souborů.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Pro přístup k souborům protokolu pomocí nástroje Ambari, je nutné použít tunelového propojení SSH. Webové rozhraní pro jednotlivé služby nejsou veřejně přístupné v síti Internet. Informace o používání tunelového propojení SSH naleznete v tématu [používání tunelového propojení SSH](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Webové uživatelské rozhraní Ambari vyberte službu, kterou chcete zobrazit protokoly pro (například YARN). Potom pomocí **rychlé odkazy** vyberte head uzel k zobrazení protokolů pro.

![Pokud chcete zobrazit protokoly pomocí rychlé odkazy](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Postup konfigurace velikost uzlu

Velikost uzlu lze vybrat pouze při vytváření clusteru. Můžete najít seznam různých velikosti virtuálních počítačů, která je k dispozici pro HDInsight na [HDInsight stránce s cenami](https://azure.microsoft.com/pricing/details/hdinsight/).

Při vytváření clusteru, můžete zadat velikost uzlů. Následující informace poskytují pokyny o tom, jak určit velikost pomocí [portál Azure][preview-portal], [prostředí Azure PowerShell][azure-powershell]a [rozhraní příkazového řádku Azure][azure-cli]:

* **Portál Azure**: při vytváření clusteru, můžete nastavit velikost uzlů používaný v clusteru:

    ![Obrázek průvodce vytvořením clusteru s výběrem velikost uzlu](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Rozhraní příkazového řádku Azure**: při použití `azure hdinsight cluster create` příkaz, velikost head, pracovního procesu a uzly ZooKeeper lze nastavit pomocí `--headNodeSize`, `--workerNodeSize`, a `--zookeeperNodeSize` parametry.

* **Prostředí Azure PowerShell**: při použití `New-AzureRmHDInsightCluster` rutiny, můžete nastavit velikost head, pracovního procesu a uzly ZooKeeper pomocí `-HeadNodeVMSize`, `-WorkerNodeSize`, a `-ZookeeperNodeSize` parametry.

## <a name="next-steps"></a>Další postup

Pomocí následujících odkazů na další informace o aspektech, které jsou uvedené v tomto dokumentu.

* [Ambari REST odkaz](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalace a konfigurace rozhraní příkazového řádku Azure](../cli-install-nodejs.md)
* [Nainstalujte a nakonfigurujte Azure PowerShell.](/powershell/azure/overview)
* [Spravovat HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zřizování clusterů HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
