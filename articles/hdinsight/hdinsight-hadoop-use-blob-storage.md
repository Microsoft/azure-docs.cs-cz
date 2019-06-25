---
title: Dotazy na data z úložiště Azure kompatibilního se systémem HDFS – Azure HDInsight
description: Zjistěte, jak zadávat dotazy na data z Azure storage a Azure Data Lake Storage k ukládání výsledků analýzy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6e0192029decef95dcaecc0c60dce5fd5b6f99ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479901"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Použití úložiště Azure s clustery Azure HDInsight

Pokud chcete analyzovat data v clusteru HDInsight, můžete můžete ukládat buď v [služby Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[2. generace úložiště Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md), nebo ke kombinaci. Tyto možnosti ukládání umožňují bezpečné odstranění clusterů HDInsight, které jsou používány pro výpočty, aniž by se ztratila uživatelská data.

Apache Hadoop podporuje hodnoty výchozího systému souborů. Výchozí systém souborů znamená výchozí schéma a autoritu. Lze ho také použít k vyřešení relativní cesty. Během procesu vytváření clusteru HDInsight, můžete zadat kontejner objektů blob ve službě Azure Storage jako výchozí systém souborů nebo u služby HDInsight 3.6, můžete vybrat služby Azure Storage nebo Azure Data Lake Storage Gen 1 / Azure Data Lake Storage Gen 2 jako výchozí soubory systém s několika výjimkami. Možnosti použití Data Lake Storage Gen 1 jako výchozí i propojené úložiště, najdete v části [dostupnost pro HDInsight cluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

V tomto článku se dozvíte, jak služba Azure Storage pracuje s clustery HDInsight. Informace o tom, jak služba Data Lake Storage Gen 1 pracuje s clustery HDInsight, naleznete v tématu [použití služby Azure Data Lake Storage s Azure HDInsight clustery](hdinsight-hadoop-use-data-lake-store.md). Další informace o vytvoření clusteru HDInsight najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage je robustní řešení úložiště pro obecné účely, které se jednoduše integruje se službou HDInsight. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní systému souborů Hadoop DFS (HDFS) může celá sada komponent ve službě HDInsight pracovat přímo se strukturovanými nebo nestrukturovanými daty uloženými jako objekty blob.

> [!WARNING]  
> Druh účtu úložiště **BlobStorage** jde použít jenom jako sekundární úložiště pro clustery HDInsight.

| Druh účtu úložiště | Podporované služby | Úrovně výkonu podporované | Podporované přístupu |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (obecné účely v2)  | Objekt blob     | Standard                    | Horká, studená, archivní\*   |
| Storage (pro obecné účely v1)   | Objekt blob     | Standard                    | neuvedeno                    |
| BlobStorage                    | Objekt blob     | Standard                    | Horká, studená, archivní\*   |

Nedoporučujeme používat výchozí kontejner objektů blob pro ukládání firemních dat. Ideální postup je výchozí kontejner objektů blob po každém použití odstranit a snížit tak náklady na úložiště. Výchozí kontejner obsahuje aplikaci a systémové protokoly. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Sdílení jednoho kontejneru objektů blob jako výchozího systému souborů pro několik clusterů se nepodporuje.

> [!NOTE]  
> Archivní úroveň přístupu je offline vrstvy, který má několik latence načtení hodinu a nedoporučuje se používat pro použití s HDInsight. Další informace najdete v tématu [archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Pokud se rozhodnete k zabezpečení vašeho účtu úložiště pomocí **virtuální sítí a bran firewall** omezení **vybrané sítě**, je potřeba povolit výjimku **Povolit důvěryhodné Microsoft služby...**  tak, aby HDInsight můžete přístup k vašemu účtu úložiště.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight
Následující diagram představuje abstraktní zobrazení architektury úložiště HDInsight, které používá službu Azure Storage:

![Clustery Hadoop používají rozhraní API HDFS pro přístup a ukládání strukturovaných i nestrukturovaných dat do služby Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "Architektura HDInsight Storage")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Služba HDInsight navíc umožňuje přístup k datům uloženým ve službě Azure Storage. Syntaxe je následující:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Při použití účtu Azure Storage s clustery HDInsight je potřeba zvážit tyto aspekty.

* **Kontejnery v účtech úložiště, které jsou připojené ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou přidružené ke clusteru během vytváření, máte plný přístup k objektům BLOB v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které nejsou připojené ke clusteru:** Máte oprávnění jen pro čtení k objektům BLOB v kontejnerech.
  
> [!NOTE]  
> Veřejné kontejnery umožňují získat seznam všech objektů blob, které jsou k dispozici v tomto kontejneru a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [Správa přístupu ke kontejnerům a objektům blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Privátní kontejnery v účtech úložiště, které nejsou připojené ke clusteru:** Objekty BLOB v kontejnerech nelze získat přístup, dokud nedefinujete účet úložiště při odesílání úlohy WebHCat. To se vysvětluje dále v tomhle článku.

Účty úložiště, které jsou definovány v procesu vytváření a jejich klíče jsou uložené v `%HADOOP_HOME%/conf/core-site.xml` na uzlech clusteru. Výchozím chováním služby HDInsight je používání účtů úložiště, které jsou definovány v souboru core-site.xml. Je toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Více úloh WebHCat, včetně Apache Hive, MapReduce, streamování Apache Hadoop a Apache Pig, může obsahovat popis účtů úložiště a spojených metadat. (To aktuálně funguje pro Pig s účty úložiště, ale ne pro metadata.) Více informací najdete v části [Použití clusteru HDInsight s alternativními účty úložiště a metaúložišti](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů blob ukládají data jako páry klíč/hodnota a neexistuje žádná hierarchie adresářů. V názvu klíče se dá použít lomítko (/), aby název klíče připomínal  cestu k souboru. Klíč k objektu blob může být například *input/log1.txt*. Žádný skutečný *vstupní* adresář neexistuje, ale vzhledem k lomítku v názvu klíče tento název připomíná zobrazení cesty k souboru.

## <a id="benefits"></a>Výhody služby Azure Storage
Předpokládaná náklady není společné umístění výpočetních clusterů a prostředků úložiště je zmírnit tak, jak výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure, kde vysokorychlostní síť umožňuje efektivní výpočetní uzly pro přístup k datům ve službě Azure storage.

S ukládáním dat ve službě Azure Storage namísto HDFS je spojeno několik výhod:

* **Opakované použití dat a sdílení:** Data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data ve službě Azure storage je přístupná prostřednictvím rozhraní API HDFS nebo prostřednictvím [rozhraní API REST služby Blob Storage](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Proto s větším počtem aplikací (včetně jiných clusterů HDInsight) a nástrojů  se dají vytvářet a využívat data.

* **Archivace dat:** Ukládání dat ve službě Azure storage umožňuje clusterů HDInsight, které jsou používány pro výpočty bezpečně odstranit, aniž by se ztratila uživatelská data.

* **Náklady na úložiště dat:** Pro dlouhodobé ukládání dat v systému souborů DFS je proto dražší než ukládání dat ve službě Azure storage, protože náklady na výpočetní cluster jsou vyšší než náklady na úložiště Azure. Navíc se data nemusí nahrávat znovu pro každou generaci výpočetních clusterů, náklady na nahrávání dat jsou tak nižší.

* **Elastické horizontální navýšení kapacity:** I když HDFS poskytuje systém horizontálním navýšením kapacity souborů, škála se určuje podle počtu uzlů, které vytvoříte pro váš cluster. Změna škálování může být složitější než využití elastického škálování, které je automaticky k dispozici ve službě Azure Storage.

* **Geografická replikace:** Úložiště Azure může být geograficky replikovaný. I když to přináší geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může vést k dalším nákladům. Doporučujeme proto geografickou replikaci dobře zvážit a zvolit jen v případě, že hodnota dat je vyšší než náklady na celou operaci.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které ve službě Azure Storage ve skutečnosti uložit nechcete. V takovém případě můžete zvolit k uložení dat do místní HDFS. Ve skutečnosti služba HDInsight používá DFS pro některé z těchto mezilehlých výsledků v úlohách Hive a jiných procesech.

> [!NOTE]  
> Většina příkazů HDFS (například `ls`, `copyFromLocal` a `mkdir`) i nadále fungovat podle očekávání. Jenom příkazy, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), jako je například `fschk` a `dfsadmin`, zobrazit různé chování ve službě Azure storage.

## <a name="address-files-in-azure-storage"></a>Adresování souborů ve službě Azure Storage
Schéma identifikátoru URI pro přístup k souborům ve službě Azure Storage ze služby HDInsight je:

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Schéma identifikátoru URI poskytuje nezašifrovaný přístup (s předponou *wasb:* ) a zašifrovaný přístup SSL (s *wasbs*). Doporučujeme používat *wasbs* kdykoli je to možné, i v případě přístupu k datům, umístěným uvnitř stejné oblasti v Azure.

`<BlobStorageContainerName>` Identifikuje název kontejneru objektů blob ve službě Azure storage.
`<StorageAccountName>` Identifikuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

Pokud ani `<BlobStorageContainerName>` ani `<StorageAccountName>` byl zadán, výchozí systém souborů se používá. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například soubor *hadoop-mapreduce-examples.jar*, který se dodává s clustery HDInsight, lze odkazovat pomocí jedné z následujících akcí:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2.1 a 1.6.

Cesta je název cesty HDFS souboru nebo adresáře. Vzhledem k tomu, že kontejnery ve službě Azure storage jsou párů klíč-hodnota, neexistuje žádný opravdový hierarchický systém souborů. Lomítko ( / ) uvnitř klíče objektu blob se považuje za oddělovač adresářů. Například název objektu blob pro *hadoop-mapreduce-examples.jar* je:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Při práci s objekty blob mimo HDInsight většina nástrojů nerozpozná formát WASB a místo toho očekávají základní formát cesty, jako je například `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Kontejnery objektů BLOB
K použití objektů blob je třeba nejprve vytvořit [účtu služby Azure Storage](../storage/common/storage-create-storage-account.md). V rámci tohoto procesu zadáte oblast Azure, ve které se účet úložiště vytvoří. Účet úložiště a clusteru musí být uloženy ve stejné oblasti. Databáze serveru SQL metaúložiště Hive a databáze serveru SQL metaúložiště Apache Oozie musí být také umístěny ve stejné oblasti.

Bez ohledu na svoje umístění patří každý objekt blob, který vytvoříte, do kontejneru v účtu úložiště Azure. Tento kontejner může být existující objekt blob, který se vytvořil mimo HDInsight, nebo to může být kontejner, který se vytvořil pro cluster služby HDInsight.

Výchozí kontejner objektu blob ukládá konkrétní informace, jako je historie úlohy a protokoly. Výchozí kontejner objektu Blob nesdílejte s více clustery služby HDInsight. Může dojít k poškození historie úlohy. Doporučujeme použít jiný kontejner pro každý cluster a umístit sdílená data na propojený účet úložiště, zadaný v nasazení všech příslušných clusterů, nikoli na výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [clusterů HDInsight vytvořit](hdinsight-hadoop-provision-linux-clusters.md). Nicméně, po odstranění původního clusteru HDInsight můžete znovu použít výchozí kontejner úložiště. Pro clustery HBase můžete zachovat schéma a data tabulky HBase vytvořením nového clusteru HBase pomocí výchozího kontejneru objektů blob, který je používán odstraněným clusterem HBase.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interakce s Azure storage

Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet služby Azure Storage, který k němu chcete přidružit. Kromě tohoto účtu úložiště můžete během procesu vytváření nebo až po vytvoření clusteru přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure. Pokyny pro přidání dalších účtů úložiště najdete v tématu [Vytváření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat službu HDInsight k odemčení informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Nahrání dat do služby HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [Omezení přístupu k datům s HDInsight pomocí Azure Storage sdílených přístupových podpisů](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)