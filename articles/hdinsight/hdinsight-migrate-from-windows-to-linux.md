---
title: Migrace z HDInsight se systémem Windows do HDInsight se systémem Linux - Azure | Microsoft Docs
description: Informace o migraci z clusteru HDInsight se systémem Windows do clusteru HDInsight se systémem Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 6d4b537834b8a70f0015fc9c6316dae20af5dd5a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrace z clusteru HDInsight se systémem Windows do clusteru se systémem Linux

Tento dokument poskytuje podrobné informace o rozdílech mezi HDInsight v systému Windows a Linux. Obsahuje taky pokyny o tom, jak migrovat existující úlohy do clusteru se systémem Linux.

Zatímco HDInsight se systémem Windows poskytuje snadný způsob, jak používat Hadoop v cloudu, musíte při migraci do clusteru se systémem Linux. Chcete-li například využít výhod systémem Linux nástroje a technologie, které jsou požadovány pro vaše řešení. Celou řadu věcí v ekosystému Hadoop jsou vyvinuté na systémy Linux a nemusí být k dispozici pro použití s HDInsight se systémem Windows. Mnoho knihy, videa a další materiály školení předpokládá, že používáte systém Linux, při práci s Hadoop.

> [!NOTE]
> Clustery HDInsight dlouhodobé podporu Ubuntu (LTS) použít jako operační systém pro uzly v clusteru. Informace o verzi Ubuntu s HDInsight, společně s další informace o správě verzí součásti, najdete v části [HDInsight verze součástí](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Úlohy migrace

Tady je obecný postup pro migraci.

![Diagram pracovního postupu migrace](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Přečtěte si každá část tohoto dokumentu pochopit změny, které může být potřeba při migraci.

2. Vytvoření clusteru se systémem Linux jako prostředí testovací a kvalita záruky. Další informace týkající se vytvoření clusteru se systémem Linux najdete v tématu [vytvořit systémem Linux clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Zkopírujte existující úlohy, zdroje dat a jímky do nového prostředí.

4. Provádění testů pro ověření a ujistěte se, že vaše úlohy fungují v novém clusteru podle očekávání.

Jakmile si ověříte, že vše funguje podle očekávání, naplánujte dobu odstávky migrace. Během této výpadky proveďte následující akce:

1. Zálohujte všechna přechodný data uložená místně na uzlech clusteru. Například pokud máte data uložená přímo na hlavního uzlu.

2. Odstranění clusteru systému Windows.

3. Vytvořte cluster se systémem Linux pomocí stejné výchozí úložiště dat, které používá clusteru systému Windows. Do clusteru se systémem Linux můžete pokračovat v práci s existující provozní data.

4. Importujte přechodný data, která jste zálohovali.

5. Spuštění úlohy nebo pokračovat ve zpracovávání pomocí nového clusteru.

### <a name="copy-data-to-the-test-environment"></a>Zkopírujte data do testovacího prostředí

Existuje mnoho způsobů zkopírovat data a úlohy, ale dva popsaných v této části jsou nejjednodušší metody, které se přímo přesunout soubory do clusteru s podporou testovací.

#### <a name="hdfs-copy"></a>HDFS kopie

Použijte následující postup ke zkopírování dat z clusteru výroby pro testovací cluster. Tyto kroky používají `hdfs dfs` nástroj, který se dodává s HDInsight.

1. Najít úložiště informace o účtu a výchozí kontejner pro existující cluster. Následující příklad používá prostředí PowerShell pro načtení těchto informací:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Pokud chcete vytvořit testovací prostředí, postupujte podle kroků v vytvořit systémem Linux clusterů v HDInsight dokumentu. Zastavit před vytvořením clusteru a místo toho vyberte **volitelné konfiguraci**.

3. Z části volitelné konfiguraci vyberte **propojených účtech Storage**.

4. Vyberte **přidejte klíč k úložišti**a po zobrazení výzvy vyberte účet úložiště, které vrátil skript prostředí PowerShell v kroku 1. Klikněte na tlačítko **vyberte** v každé části. Nakonec vytvořte cluster.

5. Po vytvoření clusteru připojit se pomocí **SSH.** Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Z relace SSH použijte následující příkaz pro kopírování souborů z propojený účet úložiště pro nový výchozí účet úložiště. Nahraďte KONTEJNERU informace o kontejneru vrácený prostředí PowerShell. Nahraďte __účet__ s názvem účtu. Cesta k datům nahraďte cestu k souboru data.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Pokud strukturu adresáře, který obsahuje data neexistuje v testovacím prostředí, můžete vytvořit pomocí následujícího příkazu:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` Přepínač umožňuje vytvoření všechny adresáře v cestě.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Přímé kopírování mezi objekty BLOB ve službě Azure Storage

Alternativně můžete chtít použít `Start-AzureStorageBlobCopy` rutiny Azure Powershellu zkopírovat objekty BLOB mezi účty úložiště mimo HDInsight. Další informace najdete v tématu jak spravovat části objektů BLOB služby Azure pomocí Azure powershellu s Azure Storage.

## <a name="client-side-technologies"></a>Technologie na straně klienta

Klientské technologie, jako [rutin prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs), [rozhraní příkazového řádku Azure](../cli-install-nodejs.md), nebo [.NET SDK pro Hadoop](https://hadoopsdk.codeplex.com/) pokračovat v práci clusterech se systémem Linux. Tyto technologie spoléhají na REST API, která jsou stejné napříč oba typy clusteru operačního systému.

## <a name="server-side-technologies"></a>Technologie na straně serveru

Následující tabulka obsahuje pokyny k migraci serverové komponenty, které jsou specifické pro systém Windows.

| Pokud použijete tuto technologii... | Tato akce trvat... |
| --- | --- |
| **Prostředí PowerShell** (skripty na straně serveru, včetně akcí skriptů používají při vytváření clusteru) |Přepisování jako skripty Bash. Pro skript akce, najdete v části [HDInsight se systémem Linux přizpůsobit pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md) a [vývoj akcí pro HDInsight se systémem Linux skriptů](hdinsight-hadoop-script-actions-linux.md). |
| **Rozhraní příkazového řádku Azure** (skripty na straně serveru) |Zatímco Azure CLI je k dispozici v systému Linux, nepochází předem nainstalovaná o hlavních uzlech clusteru HDInsight. Další informace o instalaci rozhraní příkazového řádku Azure najdete v tématu [Začínáme s Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Součásti rozhraní .NET** |Rozhraní .NET je podporována v HDInsight se systémem Linux prostřednictvím [Mono](https://mono-project.com). Další informace najdete v tématu [řešení migrovat .NET HDInsight se systémem Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Součásti Win32 nebo jiné technologie pouze pro systém Windows** |Pokyny, závisí na součásti nebo technologii. Bude pravděpodobně možné najít na verzi, která je kompatibilní s Linuxem. Pokud ne, musíte najít alternativní řešení nebo přepsání této součásti. |

> [!IMPORTANT]
> Správa HDInsight SDK není plně kompatibilní se Mono. Nepoužívejte ho jako součást řešení, které jsou nasazené na clusteru HDInsight.

## <a name="cluster-creation"></a>Vytvoření clusteru

Tato část obsahuje informace o rozdíly ve vytváření clusteru.

### <a name="ssh-user"></a>SSH uživatele

Clustery HDInsight se systémem Linux pomocí **Secure Shell (SSH)** protokol poskytnout vzdálený přístup k uzlům clusteru. Na rozdíl od clustery založené na vzdálené plochy pro Windows neposkytují většině klientů SSH v grafickém uživatelském rozhraní. Místo toho klientů SSH zadejte příkazový řádek, který slouží ke spuštění příkazů v clusteru. Někteří klienti (například [MobaXterm](http://mobaxterm.mobatek.net/)) zadejte Prohlížeč systému grafické souboru kromě vzdáleného příkazového řádku.

Při vytváření clusteru, je třeba zadat uživatel SSH a buď **heslo** nebo **certifikátu veřejného klíče** pro ověřování.

Doporučujeme používat certifikát veřejného klíče, jako je bezpečnější než použití hesla. Ověřování pomocí certifikátu funguje tak, že generování podepsaný pár veřejného a privátního klíče a pak poskytuje veřejný klíč při vytvoření clusteru. Při připojování k serveru pomocí protokolu SSH, poskytuje privátní klíč na straně klienta pro připojení.

Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Přizpůsobení clusteru

**Skript akce** použít s clustery se systémem Linux musí být napsaný ve skriptu Bash. Clustery se systémem Linux pomocí akcí skriptů během nebo po vytvoření clusteru. Další informace najdete v tématu [HDInsight se systémem Linux přizpůsobit pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md) a [vývoj akcí pro HDInsight se systémem Linux skriptů](hdinsight-hadoop-script-actions-linux.md).

Další přizpůsobení funkcí je **bootstrap**. Pro clustery systému Windows tato funkce umožňuje zadat umístění další knihovny pro použití s Hive. Po vytvoření clusteru, jsou automaticky dostupné pro použití s dotazy Hive, aniž by bylo nutné použít tyto knihovny `ADD JAR`.

Spuštění funkce pro clustery se systémem Linux neposkytuje tuto funkci. Místo toho použijte akce skriptu, které jsou dokumentovány v článku [přidat Hive knihovny při vytváření clusteru](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtuální sítě

HDInsight se systémem Windows clusterů funguje jenom s klasické virtuální sítě, zatímco clustery HDInsight se systémem Linux vyžadují virtuální sítě Resource Manager. Pokud máte v klasické virtuální síti, která clusteru Linux HDInsight musí připojit k prostředkům, najdete v části [připojení klasickou virtuální síť k virtuální síti Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Další informace o požadavky na konfiguraci najdete v tématu [HDInsight rozšířit možnosti pomocí virtuální sítě](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="management-and-monitoring"></a>Správa a monitorování

Mnoho webových uživatelská možná zneužil s HDInsight se systémem Windows, jako je historie úlohy nebo uživatelského rozhraní Yarn, jsou k dispozici prostřednictvím Ambari. Kromě toho zobrazení Ambari Hive poskytuje způsob, jak spouštět dotazy Hive pomocí webového prohlížeče. Webové uživatelské rozhraní Ambari je k dispozici v clusterech se systémem Linux v https://CLUSTERNAME.azurehdinsight.net.

Další informace o práci s Ambari najdete v následujících dokumentech:

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari výstrahy

Ambari má upozornění systému, který zjistí potenciální problémy s clusterem. Výstrahy se zobrazují jako red nebo žlutou položek ve webovém uživatelském rozhraní Ambari, ale můžete také načíst přes rozhraní REST API.

> [!IMPORTANT]
> Výstrahy Ambari označuje, zda *může* se jednat o problém není že v něm *je* problém. Například můžete obdržet oznámení, že nejsou k dispozici HiveServer2, i když je k němu přístup normálně.
>
> Mnoho výstrah jsou implementované jako dotazy na bázi intervalů pro služby a očekávat odpovědi v určitém časovém rámci. Takže upozornění nemusí nutně znamenat, že služba je mimo provoz, stejně to nevrátila výsledky v očekávané časovém rámci.

## <a name="file-system-locations"></a>Umístění systému souborů

Systém souborů clusteru Linux rozložená jinak než clustery HDInsight se systémem Windows. Následující tabulku použijte k vyhledání běžně používané soubory.

| Je nutné nalézt... | Je umístěný... |
| --- | --- |
| Konfigurace |`/etc`. Například `/etc/hadoop/conf/core-site.xml`. |
| Soubory protokolu |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Existují dva adresáře tady, ten, který je aktuální verze softwaru HDP a `current`. `current` Adresář obsahuje symbolické odkazy na soubory a adresáře, které jsou umístěné v adresáři číslo verze. `current` Adresář je zadaný jako pohodlný způsob přístupu k souborům HDP od změní se číslo verze jako softwaru HDP za verzi aktualizovat. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Obecně platí Pokud znáte název souboru, můžete použít následující příkaz z relace SSH najít cestu k souboru:

    find / -name FILENAME 2>/dev/null

Můžete také použít zástupné znaky s názvem souboru. Například `find / -name *streaming*.jar 2>/dev/null` vrací cestu k soubory jar obsahující slovo "streamování jako součást názvu souboru.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig a MapReduce

Pig a MapReduce úlohy jsou podobné v clusterech se systémem Linux. Ale clustery HDInsight se systémem Linux můžete vytvořit pomocí novější verze systému Hadoop, Hive a Pig. Tyto rozdíly verze může znamenat změny v jak vaše stávající řešení funkce. Další informace o verzích součásti, které jsou zahrnuté do HDInsight naleznete v tématu [Správa verzí komponenty HDInsight](hdinsight-component-versioning.md).

HDInsight se systémem Linux neposkytuje funkce vzdálené plochy. Místo toho můžete SSH vzdálené připojení k hlavnímu uzlu clusteru. Další informace najdete v následujících dokumentech:

* [Použijte Hive pomocí protokolu SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Použijte Pig s SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Používání nástroje MapReduce pomocí protokolu SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Pokud chcete použít externí metaúložiště Hive, byste měli zálohovat metaúložiště před jeho použitím s HDInsight se systémem Linux. HDInsight se systémem Linux je k dispozici novější verze Hive, který může mít nekompatibilitu s metaúložiště vytvořené pomocí dřívějších verzí.

Následující graf obsahuje pokyny k migraci vašich úloh Hive.

| V systému Windows použít... | Na základě Linux... |
| --- | --- |
| **Hive Editor** |[Zobrazení Ambari Hive](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` Chcete-li povolit Tez |Tez je výchozí modul provádění pro clustery se systémem Linux, takže příkaz set již není potřeba. |
| Uživatelem definované funkce jazyka C# | Informace o ověřování součásti C# s HDInsight se systémem Linux najdete v tématu [řešení migrovat .NET HDInsight se systémem Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD soubory nebo skripty na serveru vyvolána jako součást úlohy Hive |pomocí skriptů Bash |
| `hive` příkaz z vzdálené plochy |Použití [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) nebo [Hive z relace SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| V systému Windows použít... | Na základě Linux... |
| --- | --- |
| Uživatelem definované funkce jazyka C# | Informace o ověřování součásti C# s HDInsight se systémem Linux najdete v tématu [řešení migrovat .NET HDInsight se systémem Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD soubory nebo skripty na serveru vyvolána jako součást úlohy Pig |pomocí skriptů Bash |

### <a name="mapreduce"></a>MapReduce

| V systému Windows použít... | Na základě Linux... |
| --- | --- |
| C# mapper a reduktorem součásti | Informace o ověřování součásti C# s HDInsight se systémem Linux najdete v tématu [řešení migrovat .NET HDInsight se systémem Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| CMD soubory nebo skripty na serveru vyvolána jako součást úlohy Hive |pomocí skriptů Bash |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Pokud chcete použít externí metaúložiště Oozie, byste měli zálohovat metaúložiště před jeho použitím s HDInsight se systémem Linux. HDInsight se systémem Linux je k dispozici novější verze Oozie, který může mít nekompatibilitu s metaúložiště vytvořené pomocí dřívějších verzí.

Pracovní postupy Oozie prostředí akce povolit. Spustit příkazy příkazového řádku pomocí prostředí akce výchozí prostředí pro operační systém. Pokud máte Oozie pracovní postupy, které jsou závislé na prostředí systému Windows, musí přepsat s pracovními postupy k závisí na prostředí shell Linux (Bash). Další informace o používání prostředí akce s Oozie najdete v tématu [rozšíření akce prostředí Oozie](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Pokud máte pracovní postup, který používá aplikace v jazyce C#, ověřte tyto aplikace v prostředí Linux. Další informace najdete v tématu [řešení migrovat .NET HDInsight se systémem Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| V systému Windows použít... | Na základě Linux... |
| --- | --- |
| Řídicí panel Storm |Řídicí panel Storm není k dispozici. V tématu [topologií nasazení a správa Storm v HDInsight se systémem Linux](storm/apache-storm-deploy-monitor-topology-linux.md) pro způsoby, jak odeslat topologie |
| Storm uživatelského rozhraní |Uživatelské rozhraní Storm je k dispozici na https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio k vytvoření, nasazení a správě jazyka C# nebo hybridní topologie |Visual Studio můžete použít k vytvoření, nasazení a správě jazyka C# (SCP.NET) nebo hybridní topologie na Storm v HDInsight se systémem Linux. Je možné použít jenom s clustery vytvořené po 10/28/2016. |

## <a name="hbase"></a>HBase

V clusterech se systémem Linux, znode nadřazeného prvku pro HBase je `/hbase-unsecure`. Tuto hodnotu lze nastavte v konfiguraci pro jakéhokoli Java klienta aplikace, které používají nativní API HBase Java.

V tématu [sestavení aplikace založené na jazyce Java HBase](hdinsight-hbase-build-java-maven.md) pro příklad klienta, který nastavuje tuto hodnotu.

## <a name="spark"></a>Spark

Clustery Spark nebyly k dispozici v clusterech Windows verzi Preview. Spark GA je dostupná jenom s clustery se systémem Linux. Neexistuje cesta migrace z clusteru se systémem Windows Spark preview do clusteru se systémem Linux Spark verze.

## <a name="known-issues"></a>Známé problémy

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory vlastních aktivit rozhraní .NET

Azure Data Factory vlastních aktivit .NET nejsou aktuálně podporovány na clusterech HDInsight se systémem Linux. Místo toho používejte jednu z následujících metod k implementaci vlastních aktivit v rámci vašeho kanálu ADF.

* Spuštění aktivity rozhraní .NET ve fondu Azure Batch. Použití Azure Batch propojené služby části najdete [použít vlastní aktivity v kanálu Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementujte aktivitu jako činnost MapReduce. Další informace najdete v tématu [vyvolání MapReduce programy z datové továrny](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Konce řádků

Konce řádků na systémy Windows se obvykle používá Line FEED, při systémy Linux používat LF. Můžete upravit existující dat producenti a spotřebitelé pro práci s LF.

Například použití Azure PowerShell k dotazu HDInsight v clusteru se systémem Windows vrací data s Line FEED. Stejný dotaz s clusterem se systémem Linux vrátí LF. Vyzkoušejte, pokud ukončuje řádku způsobí, že problém s vaším řešením před migrací do clusteru se systémem Linux.

Vždy používejte LF jako řádku ukončení pro skripty, které v uzlech clusteru spustit. Pokud používáte Line FEED, může se zobrazí chyby při spuštění skriptů v clusteru se systémem Linux.

Pokud skripty neobsahují řetězce s embedded znaky CR, můžete změnit hromadné konce řádků pomocí jedné z následujících metod:

* **Před nahráním do clusteru**: použijte následující příkazy prostředí PowerShell a změňte konce řádků z Line FEED na LF před nahráním skript do clusteru.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Po nahrání do clusteru**: použijte následující příkaz z relace SSH do clusteru se systémem Linux a upravte skript.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Další kroky

* [Naučte se vytvářet clustery HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Použití SSH se připojit k HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Spravovat cluster se systémem Linux pomocí nástroje Ambari](hdinsight-hadoop-manage-ambari.md)
