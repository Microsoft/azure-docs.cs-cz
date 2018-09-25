---
title: Vysoká dostupnost pro Hadoop – Azure HDInsight
description: Zjistěte, jak clustery HDInsight zvýšit spolehlivost a dostupnost pomocí dalšího hlavního uzlu. Zjistěte, jak to má vliv služby Hadoop jako je například Ambari a Hive, a jak se připojit jednotlivě každému hlavnímu uzlu pomocí SSH.
services: hdinsight
ms.reviewer: jasonh
author: jasonwhowell
keywords: Vysoká dostupnost systému hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.openlocfilehash: 33458794ad74b367f1278364d7b4ace30f7d13a8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953579"
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Dostupnost a spolehlivost clusterů Hadoop ve službě HDInsight

Clustery HDInsight poskytují dva hlavní uzly zvýšit dostupnost a spolehlivost služby Hadoop a spouštění úloh.

Hadoop dosahuje vysoké dostupnosti a spolehlivosti replikuje data a služby napříč několika uzly v clusteru. Ale standardní distribucích systému Hadoop obvykle mívají pouze jeden hlavní uzel. Jakémkoli výpadku jeden hlavní uzel může způsobit, že cluster přestane fungovat. HDInsight poskytuje dva hlavní uzly zlepšit dostupnost a spolehlivost Hadoop.

> [!IMPORTANT]
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="availability-and-reliability-of-nodes"></a>Dostupnost a spolehlivost uzlů

Uzly v clusteru HDInsight jsou implementovány pomocí Azure Virtual Machines. Následující části popisují jednotlivých uzlů typy použité spolu s HDInsight. 

> [!NOTE]
> Ne všechny typy uzlů se používají pro typ clusteru. Například typ clusteru Hadoop nemá žádné uzly Nimbus. Další informace o uzlech používá typy clusterů HDInsight, najdete v oddílu typy clusteru [založených na Linuxu vytvoření Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) dokumentu.

### <a name="head-nodes"></a>Hlavní uzly

K zajištění vysoké dostupnosti služby Hadoop HDInsight poskytuje dva hlavní uzly. Oba hlavní uzly jsou současně aktivní a v chodu v rámci clusteru HDInsight. Některé služby, například HDFS nebo YARN, jsou aktivní na jeden hlavní uzel pouze v daném okamžiku. Jiné služby, jako je například HiveServer2 nebo Hive MetaStore jsou aktivní na oba hlavní uzly ve stejnou dobu.

Hlavní uzly (a dalších uzlů v HDInsight) mají číselnou hodnotu jako součást názvu hostitele uzlu. Například `hn0-CLUSTERNAME` nebo `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> S tím, zda je uzel primární nebo sekundární nepřidružujte číselnou hodnotu. Číselná hodnota se nachází pouze jako jedinečný název pro každý uzel.

### <a name="nimbus-nodes"></a>Uzly Nimbus

Uzly nimbus jsou k dispozici u clusterů Storm. Uzly Nimbus poskytují podobné funkce jako Hadoop jobtracker distribuci a monitorování zpracování v rámci pracovních uzlů. HDInsight poskytuje dva uzly Nimbus pro clustery Storm

### <a name="zookeeper-nodes"></a>Uzly Zookeeper

[ZooKeeper](http://zookeeper.apache.org/) uzlů se používají pro vedoucího hlavní služeb na hlavní uzly. Používají se také zajistit, že služby, datových (pracovní) uzlů a brány vědět, které hlavního uzlu je hlavní služba aktivní na. Ve výchozím nastavení HDInsight poskytuje tři uzly ZooKeeper.

### <a name="worker-nodes"></a>Pracovní uzly

Pracovní uzly provádět analýzu skutečná data, když je úloha odeslána do clusteru. Pokud selže jeden uzel pracovního procesu, úkol, který byl výkon se odešle do jiného uzlu pracovního procesu. Ve výchozím nastavení vytvoří čtyři pracovní uzly HDInsight. Toto číslo tak, aby odpovídala vašim potřebám, během a po vytvoření clusteru můžete změnit.

### <a name="edge-node"></a>Hraniční uzel

Hraniční uzel není součástí aktivně analýzy dat v rámci clusteru. Používá se vývojáři nebo datovým vědcům při práci se systémem Hadoop. Na hraničním uzlu se nachází ve stejné virtuální síti Azure jako ostatní uzly v clusteru a přímý přístup k jiné uzly. Na hraničním uzlu je možné bez nutnosti převádět prostředky mimo kritické služby Hadoop nebo analytických úloh.

V současné době služby ML na HDInsight je jediný typ clusteru, který obsahuje hraniční uzel ve výchozím nastavení. Pro služby ML na HDInsight, se používá k hraničnímu uzlu R testovací kód místně v uzlu před jeho odesláním do clusteru pro distribuované zpracování.

Informace o použití hraniční uzel s jinými typy clusteru, najdete v článku [použití hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md) dokumentu.

## <a name="accessing-the-nodes"></a>Přístup k uzlům

Poskytuje přístup ke clusteru přes internet prostřednictvím veřejné brány. Přístup je omezený na připojení k hlavním uzlům a (pokud existuje) na hraničním uzlu. Přístup ke službám, které běží na hlavní uzly. to neovlivní tím, že více hlavních uzlů. Veřejnou brány směruje žádosti do hlavního uzlu, který je hostitelem požadovanou službu. Například pokud Ambari je aktuálně hostitelem sekundárnímu hlavnímu uzlu, brány směruje příchozí žádosti pro Ambari do tohoto uzlu.

Přístup přes veřejnou brány je omezen na portu 443 (HTTPS), 22 a 23.

* Port __443__ slouží k přístupu k Ambari a další webové uživatelské rozhraní nebo rozhraní REST API hostované na hlavní uzly.

* Port __22__ slouží k přístupu k primárnímu hlavnímu uzlu nebo hraničnímu uzlu pomocí SSH.

* Port __23__ slouží k přístupu k sekundárnímu hlavnímu uzlu pomocí SSH. Například `ssh username@mycluster-ssh.azurehdinsight.net` připojí k primárnímu hlavnímu uzlu clusteru s názvem **mycluster**.

Další informace o používání SSH najdete v tématu [použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentu.

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Interní plně kvalifikované názvy domény (FQDN)

Uzly v clusteru služby HDInsight mají interní IP adresy a plně kvalifikovaný název domény, který je přístupný pouze z clusteru. Při přístupu ke službám v clusteru pomocí interní plně kvalifikovaný název domény nebo IP adresa, měli byste použít Ambari ověření IP adresy nebo plně kvalifikovaný název domény pro použití při přístupu ke službě.

Například služba Oozie dají spustit jenom na jeden hlavní uzel a použití `oozie` příkaz z relace SSH vyžaduje adresu URL služby. Tuto adresu URL můžete získat z Ambari pomocí následujícího příkazu:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Tento příkaz vrátí hodnotu, podobně jako následující příkaz, který obsahuje interní adresa URL pro použití s `oozie` příkaz:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Další informace o práci se rozhraní Ambari REST API najdete v tématu [monitorování a správa HDInsight pomocí rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Přístup k jiné typy uzlů

Můžete připojit k uzlům, které nejsou přímo přístupné přes internet pomocí následujících metod:

* **SSH**: Po připojení k hlavnímu uzlu pomocí SSH, pak vám pomůže SSH z hlavního uzlu připojení k ostatním uzlům v clusteru. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Tunel SSH**: Pokud potřebujete přístup k webové službě hostované na jednom z uzlů, které není přístupný z Internetu, je nutné použít tunelu SSH. Další informace najdete v tématu [použití tunelu SSH s HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

* **Azure Virtual Network**: Pokud váš cluster HDInsight je součástí služby Azure Virtual Network, prostředek ve stejné virtuální síti přímo přístupné všechny uzly v clusteru. Další informace najdete v tématu [rozšířit HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="how-to-check-on-a-service-status"></a>Jak zkontrolovat stav služby

Pokud chcete zkontrolovat stav služby, které běží na hlavní uzly, pomocí uživatelského rozhraní Ambari Web nebo Ambari REST API.

### <a name="ambari-web-ui"></a>Ambari Web UI

Je viditelný na webové uživatelské rozhraní Ambari https://CLUSTERNAME.azurehdinsight.net. Nahraďte **CLUSTERNAME** názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte přihlašovací údaje uživatele protokolu HTTP pro váš cluster. Je výchozí uživatelské jméno HTTP **správce** a heslo je heslo, které jste zadali při vytváření clusteru.

Při doručení na stránce Ambari, nainstalované služby jsou uvedené na levé straně stránky.

![Nainstalované služby](./media/hdinsight-high-availability-linux/services.png)

Existuje řada ikony, které se mohou objevit vedle služby k označení stavu. Žádné výstrahy týkající se služby lze zobrazit pomocí **výstrahy** odkazu v horní části stránky. Každá služba zobrazíte další informace v něm můžete vybrat.

Když na stránku služby obsahuje informace o stavu a konfiguraci každé služby, neposkytuje informace, na které hlavního uzlu je služba spuštěna na. Chcete-li zobrazit tyto informace, použijte **hostitele** odkazu v horní části stránky. Tato stránka zobrazuje hostitele v clusteru, a to včetně hlavní uzly.

![seznam hostitelů](./media/hdinsight-high-availability-linux/hosts.png)

Vyberte odkaz pro jeden z hlavních uzlů se zobrazí služeb a součástí, které běží na tomto uzlu.

![Stav součásti](./media/hdinsight-high-availability-linux/nodeservices.png)

Další informace o použití Ambari, naleznete v tématu [monitorování a správa HDInsight pomocí webového uživatelského rozhraní Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>Rozhraní Ambari REST API

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

Další informace o práci se rozhraní Ambari REST API najdete v tématu [monitorování a správa HDInsight pomocí rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

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
> Existují také grafické rozhraní, které umožňují vizualizovat systému souborů při připojení pomocí protokolu SFTP. Například [MobaXTerm](http://mobaxterm.mobatek.net/) umožňuje procházet pomocí rozhraní Windows Explorer podobně jako systém souborů.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Pro přístup k souborům protokolu pomocí nástroje Ambari, je nutné použít tunelu SSH. Webové rozhraní pro jednotlivé služby nejsou zveřejněné veřejně na Internetu. Informace o používání tunelu SSH najdete v tématu [používání tunelového propojení SSH](hdinsight-linux-ambari-ssh-tunnel.md) dokumentu.

Webové uživatelské rozhraní Ambari vyberte službu, kterou chcete zobrazit protokoly pro (například YARN). Pak pomocí **rychlé odkazy** vyberte které hlavního uzlu, chcete-li zobrazit v protokolech.

![Chcete-li zobrazit protokoly pomocí rychlé odkazy](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Jak konfigurovat velikost uzlu

Velikost uzlu lze vybrat pouze při vytváření clusteru. Pro HDInsight na najdete seznam různých velikostí virtuálních počítačů dostupných [HDInsight stránce s cenami](https://azure.microsoft.com/pricing/details/hdinsight/).

Při vytváření clusteru, můžete zadat velikost uzlů. Následující informace poskytují pokyny o tom, jak zadat velikost pomocí [webu Azure portal][preview-portal], [prostředí Azure PowerShell][azure-powershell]a [Klasické rozhraní příkazového řádku azure][azure-cli]:

* **Azure portal**: při vytváření clusteru, můžete nastavit velikost uzlů cluster používat:

    ![Obrázek průvodce vytvořením clusteru s výběr velikost uzlu](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure Classic CLI**: při použití `azure hdinsight cluster create` příkazu, můžete nastavit velikost head, pracovních procesů a uzly ZooKeeper s použitím `--headNodeSize`, `--workerNodeSize`, a `--zookeeperNodeSize` parametry.

* **Prostředí Azure PowerShell**: při použití `New-AzureRmHDInsightCluster` rutiny, můžete nastavit velikost head, pracovních procesů a uzly ZooKeeper s použitím `-HeadNodeVMSize`, `-WorkerNodeSize`, a `-ZookeeperNodeSize` parametry.

## <a name="next-steps"></a>Další postup

Další informace o možnosti uvedené v tomto dokumentu pomocí následujících odkazů.

* [Reference k rozhraní Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalace a konfigurace rozhraní příkazového řádku Azure Classic](../cli-install-nodejs.md)
* [Nainstalujte a nakonfigurujte Azure PowerShell.](/powershell/azure/overview)
* [Správa HDInsight pomocí Ambari](hdinsight-hadoop-manage-ambari.md)
* [Zřizování clusterů HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
