---
title: Migrace z HDInsight se systémem Windows k Linuxovým systémem HDInsight – Azure
description: Zjistěte, jak provést migraci z clusteru HDInsight se systémem Windows do clusteru se systémem Linux HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: jasonh
ms.openlocfilehash: cb2ca9ac3be0034f5a90add58249a2c2043975d0
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094080"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrace z clusteru HDInsight se systémem Windows do clusteru se systémem Linux

Tento dokument obsahuje podrobné informace o rozdílech mezi HDInsight ve Windows a Linux. Obsahuje také pokyny o tom, jak migrovat existující úlohy do clusteru se systémem Linux.

HDInsight se systémem Windows poskytuje snadný způsob, jak používat Hadoop v cloudu, budete muset migrovat do clusteru se systémem Linux. Chcete-li například využít založených na Linuxu nástrojů a technologií, které jsou požadovány pro vaše řešení. Mnoho věcí v ekosystému Hadoop jsou vyvíjeny v systémech založených na Linuxu a možná není k dispozici pro použití se službou HDInsight se systémem Windows. Mnoho knih, videa a další školicí materiály se předpokládá, že používáte systém Linux, při práci se systémem Hadoop.

> [!NOTE]
> Clustery HDInsight se systémem Ubuntu dlouhodobé podporu (LTS) použít jako operační systém pro uzly v clusteru. Informace o verzi Ubuntu s HDInsight, spolu s dalšími informacemi součásti správy verzí, naleznete v tématu [verzí komponenty HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Úlohy migrace

Obecný pracovní postup migrace je následující.

![Diagram pracovního postupu migrace](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Přečtěte si každá část tohoto dokumentu o změny, které mohou být vyžadovány při migraci.

2. Vytvoření clusteru se systémem Linux jako assurance prostředí test nebo kvalita. Další informace týkající se vytvoření clusteru se systémem Linux najdete v tématu [založených na Linuxu Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Zkopírujte do nové prostředí stávající úlohy, zdroje a jímky.

4. Proveďte ověřovací testování, abyste měli jistotu, že vaše úlohy fungovat podle očekávání v novém clusteru.

Jakmile si ověříte, že vše funguje podle očekávání, naplánujte výpadky na migraci. Během výpadků udělejte toto:

1. Zazálohujte si všechna přechodné data uložená místně na uzlech clusteru. Například pokud máte data přímo ze hlavního uzlu.

2. Odstranění clusteru založené na Windows.

3. Vytvoření clusteru založených na Linuxu pomocí stejného úložiště dat výchozí, který používá clusteru se systémem Windows. Clusteru se systémem Linux můžete pokračovat v práci s daty existujícího produkčního prostředí.

4. Importujte všechny přechodné dat, který jste zazálohovali.

5. Spuštění úlohy nebo pokračovat ve zpracování pomocí nového clusteru.

### <a name="copy-data-to-the-test-environment"></a>Kopírování dat do testovacího prostředí

Kopírování dat a úlohy mnoha způsoby, dva popsaných v této části jsou ale nejjednodušší metody, které se přímo přesunout soubory do testovací cluster.

#### <a name="hdfs-copy"></a>Kopírování HDFS

Pomocí následujících kroků ke zkopírování dat z produkčního prostředí clusteru do clusteru testu. Tyto kroky používají `hdfs dfs` nástroj, který se dodává s HDInsight.

1. Najdete úložiště informace o účtu a výchozí kontejner pro vaše existující cluster. Prostředí PowerShell pro načtení těchto informací v následujícím příkladu:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. K vytvoření testovacího prostředí, postupujte podle kroků v vytvoření linuxových clusterech HDInsight dokumentu. Zastavit před vytvořením clusteru a místo toho vyberte **volitelná konfigurace**.

3. V části volitelná konfigurace vyberte **propojených účtech Storage**.

4. Vyberte **přidat klíč úložiště**a po zobrazení výzvy vyberte účet úložiště, které vrátil skript prostředí PowerShell v kroku 1. Klikněte na tlačítko **vyberte** v každé části. Nakonec vytvořte cluster.

5. Po vytvoření clusteru, připojení k němu pomocí **SSH.** Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Z relace SSH použijte následující příkaz pro kopírování souborů z propojeném účtu úložiště na nové výchozí účet úložiště. Nahraďte KONTEJNERU kontejneru informace vrácené prostředí PowerShell. Nahraďte __účet__ názvem účtu. Cesta k datům nahraďte cestou do datového souboru.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Pokud v testovacím prostředí neexistuje adresářovou strukturu, která obsahuje data, můžete vytvořit pomocí následujícího příkazu:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` Přepínač umožňuje vytvářet všechny adresáře v cestě.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Přímé kopírování mezi objekty BLOB ve službě Azure Storage

Alternativně můžete chtít použít `Start-AzureStorageBlobCopy` rutiny Azure Powershellu pro kopírování objektů BLOB mezi účty úložiště mimo HDInsight. Další informace najdete v tématu jak spravovat části objektů BLOB systému Azure pomocí Azure powershellu s Azure Storage.

## <a name="client-side-technologies"></a>Technologie na straně klienta

Klientské technologie, jako [rutin prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs), [rozhraní příkazového řádku Azure](../cli-install-nodejs.md), nebo [sady .NET SDK pro Hadoop](https://hadoopsdk.codeplex.com/) pokračovat v práci clusterů se systémem Linux. Tyto technologie závisí na rozhraní REST API, která jsou stejné ve oba typy clusterů operačního systému.

## <a name="server-side-technologies"></a>Technologie na straně serveru

Následující tabulka obsahuje pokyny k migraci komponenty na straně serveru, které jsou specifické pro Windows.

| Pokud použijete tuto technologii... | Tuto akci neprovede... |
| --- | --- |
| **Prostředí PowerShell** (skripty na straně serveru, včetně akce se skripty používají při vytváření clusteru) |Přepsat jako skripty Bash. Akce skriptů, najdete v části [přizpůsobit Linuxovým systémem HDInsight pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md) a [vývoj akcí pro HDInsight se systémem Linux skriptů](hdinsight-hadoop-script-actions-linux.md). |
| **Azure CLI** (skripty na straně serveru) |I když rozhraní příkazového řádku Azure v Linuxu k dispozici, nepochází předem nainstalované na hlavním uzlům clusteru HDInsight. Další informace o instalaci rozhraní příkazového řádku Azure najdete v tématu [Začínáme s Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Součásti rozhraní .NET** |.NET je podporována v systémem Linux HDInsight prostřednictvím [Mono](https://mono-project.com). Další informace najdete v tématu [řešení migrace .NET k Linuxovým systémem HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Součásti systému Win32 nebo jiné technologie jen pro Windows** |Pokyny k závisí na součásti nebo technologii. Je možné najít verzi, která je kompatibilní s Linuxem. Pokud ne, musíte najít alternativní řešení nebo přepsání této součásti. |

> [!IMPORTANT]
> Správa HDInsight SDK není plně kompatibilní s Mono. Nepoužívejte ho jako součást řešení, které jsou nasazené na clusteru HDInsight.

## <a name="cluster-creation"></a>Vytvoření clusteru

Tato část obsahuje informace o rozdílech při vytváření clusteru.

### <a name="ssh-user"></a>SSH uživatele

Použití clusterů HDInsight se systémem Linux **Secure Shell (SSH)** protokol moct poskytovat vzdálený přístup k uzlům clusteru. Na rozdíl od clusterů se systémem Vzdálená plocha pro Windows většina klientů SSH se neposkytuje grafické uživatelské prostředí. Místo toho klienty SSH zadejte příkazový řádek, který umožňuje spouštění příkazů v clusteru. Někteří klienti (například [MobaXterm](http://mobaxterm.mobatek.net/)) zadejte Prohlížeč systému souborů grafické kromě vzdáleného příkazového řádku.

Při vytváření clusteru, je nutné zadat uživatele SSH a buď **heslo** nebo **certifikátu veřejného klíče** pro ověřování.

Doporučujeme používat certifikát veřejného klíče, jako je bezpečnější než použití hesla. Ověřování pomocí certifikátu funguje tak, že generování podepsaný dvojice veřejného/soukromého klíče a poskytnutí veřejného klíče při vytváření clusteru. Při připojování k serveru pomocí protokolu SSH, obsahuje privátní klíč na klientovi ověřování pro připojení.

Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Přizpůsobení clusteru

**Akcí skriptů** použít s clustery se systémem Linux musí být napsaný ve skriptu Bash. Clustery se systémem Linux pomocí akcí skriptů během nebo po vytvoření clusteru. Další informace najdete v tématu [přizpůsobit Linuxovým systémem HDInsight pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md) a [vývoj akcí pro HDInsight se systémem Linux skriptů](hdinsight-hadoop-script-actions-linux.md).

Další přizpůsobení funkcí je **bootstrap**. V případě clusterů Windows tuto funkci můžete zadat umístění další knihovny pro použití pomocí Hive. Po vytvoření clusteru, jsou automaticky dostupné pro použití s dotazy Hive, aniž byste museli používat tyto knihovny `ADD JAR`.

Spuštění funkce pro clustery založené na Linuxu nenabízí tuto funkci. Místo toho použijte akci skriptu dokumentovány v článku [knihovny Hive přidat při vytváření clusteru](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtuální sítě

HDInsight se systémem Windows clusterů fungují jenom u klasických virtuálních sítí, zatímco clustery HDInsight založené na Linuxu vyžadují virtuální sítě Resource Manageru. Pokud máte prostředky v klasickou virtuální síť, ke kterému musí připojit ke clusteru HDInsight Linux, přečtěte si téma [připojit klasickou virtuální síť k virtuální síti správce prostředků](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Další informace o požadavcích na konfiguraci, najdete v článku [HDInsight rozšířit možnosti pomocí virtuální sítě](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="management-and-monitoring"></a>Správa a monitorování

Mnoho webových uživatelských rozhraní, které jste mohli použít s HDInsight založené na Windows, jako je historie úlohy nebo uživatelském rozhraní Yarn, jsou k dispozici prostřednictvím Ambari. Kromě toho zobrazení Ambari Hive poskytuje způsob, jak spouštět dotazy Hive pomocí webového prohlížeče. Webové uživatelské rozhraní Ambari je k dispozici v clusterech založených na Linuxu v https://CLUSTERNAME.azurehdinsight.net.

Další informace o práci s Ambari najdete v následujících dokumentech:

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Rozhraní Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari výstrahy

Ambari obsahuje upozornění systému, který může identifikovat potenciální problémy s clusterem. Výstrahy se zobrazují jako červené a žluté položky ve webové uživatelské rozhraní Ambari, ale můžete také načíst přes rozhraní REST API.

> [!IMPORTANT]
> Ambari upozornění označuje, zda *může* se jednat o problém není že v něm *je* problém. Například můžete obdržet upozornění, že nelze získat přístup k serveru HiveServer2, i když k němu máte přístup normálně.
>
> Mnoho výstrah jsou implementovány jako bázi intervalů dotazy na službu a očekávat odpověď v určitém časovém limitu. Takže upozornění nemusí nutně znamenat, že služba je mimo provoz, to nevrátil výsledky v očekávané časovém rámci.

## <a name="file-system-locations"></a>Umístění systému souborů

Systém souborů clusteru Linux rozložená jinak než clustery HDInsight se systémem Windows. V následující tabulce můžete najít běžně používané soubory.

| Je potřeba najít... | Je umístěn... |
| --- | --- |
| Konfigurace |`/etc`. Například `/etc/hadoop/conf/core-site.xml`. |
| Soubory protokolu |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Existují dva adresáře umístěný tady ten, který je aktuální verze HDP a `current`. `current` Adresář obsahuje symbolické odkazy na soubory a adresáře v adresáři číslo verze. `current` Adresář je k dispozici jako pohodlný způsob přístupu k souborům HDP od změní se číslo verze jako HDP verze aktualizovat. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Obecně platí Pokud znáte název souboru, můžete použít následující příkaz z relace SSH se najít cestu k souboru:

    find / -name FILENAME 2>/dev/null

Můžete také použít zástupné znaky s názvem souboru. Například `find / -name *streaming*.jar 2>/dev/null` vrátí cestu pro soubory jar, které obsahují slovo "streaming" jako součást názvu souboru.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig a MapReduce

Úlohy pig a MapReduce se podobně jako na linuxových clusterech. Však clustery HDInsight se systémem Linux můžete vytvořit pomocí novější verze systému Hadoop, Hive a Pig. Tyto rozdíly mezi verzemi může zavádět změny v tom funkce vaše existující řešení. Další informace o verzích komponenty, které jsou zahrnuté v HDInsight najdete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md).

Linuxovým systémem HDInsight neposkytuje funkce vzdálené plochy. Místo toho můžete použít SSH ke vzdálenému připojení k hlavním uzlům clusteru. Další informace najdete v tématu v následujících dokumentech:

* [Použití Hivu pomocí SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Použití Pigu se službou SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Použití MapReduce se přes SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Pokud používáte externí metaúložiště Hive, byste měli zálohovat metastore před použitím s Linuxovým systémem HDInsight. HDInsight se systémem Linux je k dispozici s novějšími verzemi Hive, který může mít nekompatibility s metaúložiště vytvořené ve starších verzích.

Následující diagram obsahuje pokyny k migraci vašich úloh Hive.

| Na základě Windows používám... | Na základě Linux... |
| --- | --- |
| **Hive Editor** |[Zobrazení Ambari Hive](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` povolení Tez |Tez je výchozí prováděcí modul pro clustery založené na Linuxu, takže příkaz set je už je nepotřebujete. |
| Uživatelem definované funkce jazyka C# | Informace o ověřování komponenty jazyka C# s Linuxovým systémem HDInsight najdete v tématu [řešení migrace .NET k Linuxovým systémem HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD soubory nebo skripty na serveru vyvolat jako součást úlohy Hive |použití skriptů prostředí Bash |
| `hive` příkaz pro vzdálené plochy |Použití [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) nebo [Hive z relace SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Na základě Windows používám... | Na základě Linux... |
| --- | --- |
| Uživatelem definované funkce jazyka C# | Informace o ověřování komponenty jazyka C# s Linuxovým systémem HDInsight najdete v tématu [řešení migrace .NET k Linuxovým systémem HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD soubory nebo skripty na serveru pro volaných v rámci úlohy Pig |použití skriptů prostředí Bash |

### <a name="mapreduce"></a>MapReduce

| Na základě Windows používám... | Na základě Linux... |
| --- | --- |
| Komponenty jazyka C# mapovací a redukční funkci | Informace o ověřování komponenty jazyka C# s Linuxovým systémem HDInsight najdete v tématu [řešení migrace .NET k Linuxovým systémem HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD soubory nebo skripty na serveru vyvolat jako součást úlohy Hive |použití skriptů prostředí Bash |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Pokud používáte externí metaúložiště Oozie, byste měli zálohovat metastore před použitím s Linuxovým systémem HDInsight. HDInsight se systémem Linux je k dispozici s novějšími verzemi Oozie, který může mít nekompatibility s metaúložiště vytvořené ve starších verzích.

Oozie pracovních postupů prostředí akce povolit. Akce prostředí pomocí výchozí prostředí pro operační systém můžete spouštět příkazy příkazového řádku. Pokud máte pracovní postupy s Oozie, které závisí na prostředí Windows, je třeba přepsat pracovní postupy závisí na prostředí v Linuxu (Bash). Další informace o používání prostředí akce s Oozie najdete v tématu [rozšíření akce prostředí Oozie](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Pokud máte pracovní postup, který používá aplikace v jazyce C#, ověření těchto aplikací v prostředí Linux. Další informace najdete v tématu [řešení migrace .NET k Linuxovým systémem HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Na základě Windows používám... | Na základě Linux... |
| --- | --- |
| Řídicí panel Storm |Řídicí panel Storm není k dispozici. Zobrazit [topologií nasazení a správa Storm v HDInsight se systémem Linux](storm/apache-storm-deploy-monitor-topology-linux.md) způsoby, jak odeslat topologie |
| Storm UI |Uživatelské rozhraní Storm je k dispozici na https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio k vytvoření, nasazení a správa topologií C# nebo hybridní |Visual Studio umožňuje vytvářet, nasazovat a spravovat jazyka C# (SCP.NET) nebo hybridní topologie na linuxovým Storm v HDInsight. To jde použít jenom s clustery vytvořené po 10/28/2016. |

## <a name="hbase"></a>HBase

Na clusterech založených na Linuxu je nadřazenou znode HBase `/hbase-unsecure`. Nastavení této hodnoty v konfiguraci pro jakéhokoli Java klienta aplikace, které používají nativní rozhraní API Java HBase.

Zobrazit [sestavení aplikace založené na jazyce Java HBase](hdinsight-hbase-build-java-maven.md) příklad klienta, který nastaví tuto hodnotu.

## <a name="spark"></a>Spark

Clustery Spark byly k dispozici v clusterech Windows ve verzi preview. Verze GA Spark je dostupný jenom s clustery se systémem Linux. Neexistuje žádná cesta migrace z clusteru Spart založený na Windows ve verzi preview do clusteru Spart založený na systému Linux verze.

## <a name="known-issues"></a>Známé problémy

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory vlastní aktivity .NET

Azure Data Factory vlastní aktivity .NET nejsou aktuálně podporované v clusterech HDInsight založených na Linuxu. Místo toho používejte jeden z následujících metod k implementaci vlastních aktivit v rámci vašeho kanálu ADF.

* Spuštění aktivit rozhraní .NET ve fondu Azure Batch. Naleznete v části služby propojené služby Azure Batch pomocí [použití vlastních aktivit v kanálu Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementujte aktivitu jako aktivita MapReduce. Další informace najdete v tématu [volání programů MapReduce z Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Konce řádků

Obecně platí konce řádků v systémech Windows používat CRLF, zatímco systémů založených na Linuxu používat LF. Budete muset upravit existující producenty a spotřebiteli dat pro práci s LF.

Například pomocí prostředí Azure PowerShell k dotazu HDInsight v clusteru se systémem Windows vrací data s CRLF. Stejný dotaz s clusterem se systémem Linux vrátí LF. Otestujte, pokud ukončení řádku způsobí, že potíže s řešením před migrací do clusteru se systémem Linux.

Vždy používejte LF jako konců řádků pro skripty, které běží na uzlech clusteru. Pokud používáte CRLF, může se zobrazit chyby při spouštění skriptů v clusteru se systémem Linux.

Pokud tyto skripty neobsahují řetězce s vložené znaky CR, můžete provést hromadnou změnu konce řádků pomocí jedné z následujících metod:

* **Před nahráním do clusteru**: ke změně konce řádků z CRLF na LF před odesláním skriptu na clusteru použijte následující příkazy prostředí PowerShell.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Po nahrání do clusteru**: použijte následující příkaz z relace SSH do clusteru založených na Linuxu a upravte skript.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak vytvářet clustery HDInsight založené na Linuxu](hdinsight-hadoop-provision-linux-clusters.md)
* [Použití SSH pro připojení k HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Správa clusteru s linuxem pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md)
