---
title: Dotazování dat z HDFS kompatibilního úložiště Azure – Azure HDInsight
description: Naučte se, jak zadávat dotazy na data z Azure Storage a Azure Data Lake Storage ukládat výsledky analýzy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936861"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Použití Azure Storage s clustery Azure HDInsight

Chcete-li analyzovat data v clusteru HDInsight, můžete ukládat data buď v [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage @no__t 1. generace](../data-lake-store/data-lake-store-overview.md)[2 Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)nebo kombinaci. Tyto možnosti úložiště umožňují bezpečně odstraňovat clustery HDInsight, které se používají pro výpočty, aniž by došlo ke ztrátě uživatelských dat.

Apache Hadoop podporuje pojem výchozího systému souborů. Výchozí systém souborů implikuje výchozí schéma a autoritu. Dá se použít i k překladu relativních cest. Během procesu vytváření clusteru HDInsight můžete jako výchozí systém souborů zadat kontejner objektů BLOB ve Azure Storage, nebo pomocí HDInsight 3,6, můžete jako výchozí soubory vybrat buď Azure Storage, nebo Azure Data Lake Storage obecné 1/Azure Data Lake Storage Gen 2. systém s několika výjimkami. Informace o podpoře použití Data Lake Storage Gen 1 jako výchozího i propojeného úložiště najdete v tématu [dostupnost pro cluster HDInsight](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

V tomto článku se dozvíte, jak Azure Storage pracuje s clustery HDInsight. Informace o tom, jak Data Lake Storage Gen 1 pracuje s clustery HDInsight, najdete v tématu [použití Azure Data Lake Storage s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md). Další informace o vytvoření clusteru HDInsight najdete v tématu věnovaném [vytváření Apache Hadoop clusterů ve službě HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage je robustní řešení úložiště pro obecné účely, které se bezproblémově integruje se službou HDInsight. HDInsight může použít kontejner objektů BLOB v Azure Storage jako výchozí systém souborů pro cluster. Prostřednictvím rozhraní systému Hadoop Distributed File System (HDFS) může celá sada komponent ve službě HDInsight pracovat přímo se strukturovanými nebo nestrukturovanými daty uloženými jako objekty blob.

> [!IMPORTANT]  
> Druh účtu úložiště **BlobStorage** se dá použít jenom jako sekundární úložiště pro clustery HDInsight.

| Druh účtu úložiště | Podporované služby | Podporované úrovně výkonu | Podporované úrovně přístupu |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (pro obecné účely v2)  | Příznaky     | Standardní                    | Horká, studená, Archivovaná @ no__t-0   |
| Storage (pro obecné účely V1)   | Příznaky     | Standardní                    | Není k dispozici                    |
| BlobStorage                    | Příznaky     | Standardní                    | Horká, studená, Archivovaná @ no__t-0   |

Pro ukládání obchodních dat nedoporučujeme používat výchozí kontejner objektů BLOB. Odstranění výchozího kontejneru objektů BLOB po každém použití za účelem snížení nákladů na úložiště je dobrým zvykem. Výchozí kontejner obsahuje protokoly aplikací a systému. Před odstraněním kontejneru nezapomeňte protokoly načíst.

Sdílení jednoho kontejneru objektů BLOB jako výchozího systému souborů pro více clusterů se nepodporuje.

> [!NOTE]  
> Úroveň přístupu archivu je offline vrstva s odezvou na několik hodin a nedoporučuje se pro použití se službou HDInsight. Další informace najdete v tématu [archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Pokud se rozhodnete zabezpečit svůj účet úložiště s omezeními **bran firewall a virtuální sítě** u **vybraných sítí**, ujistěte se, že je **povolená výjimka Povolit důvěryhodné služby Microsoftu...** , aby HDInsight mohla získat přístup k vašemu úložišti. zohledňují.

## <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram představuje abstraktní zobrazení architektury úložiště HDInsight, které používá Azure Storage:

![Clustery Hadoop používají HDFS API pro přístup k datům a jejich ukládání do služby BLOB storage](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "HDInsight Storage architektury") .

HDInsight poskytuje přístup k systému souborů DFS, který je místně připojený k výpočetním uzlům. K tomuto systému souborů lze přistup pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Kromě toho HDInsight umožňuje přístup k datům, která jsou uložená v Azure Storage. Syntaxe je následující:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Tady jsou některé předpoklady při použití Azure Storage účtu s clustery HDInsight.

* **Kontejnery v účtech úložiště, které jsou připojené ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou během vytváření spojeny s clusterem, máte plný přístup k objektům blob v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které nejsou připojené ke clusteru:** K objektům blob v kontejnerech máte oprávnění jen pro čtení.
  
> [!NOTE]  
> Veřejné kontejnery umožňují získat seznam všech objektů blob, které jsou v tomto kontejneru k dispozici, a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob pouze v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [Správa přístupu ke kontejnerům a](../storage/blobs/storage-manage-access-to-resources.md)objektům blob.

* **Privátní kontejnery v účtech úložiště, které nejsou připojené ke clusteru:** Nemůžete získat přístup k objektům blob v kontejnerech, pokud nedefinujete účet úložiště při odesílání úloh WebHCat. To je vysvětleno dále v tomto článku.

Účty úložiště, které jsou definovány v procesu vytváření a jejich klíče, jsou uloženy v `%HADOOP_HOME%/conf/core-site.xml` na uzlech clusteru. Výchozím chováním služby HDInsight je použití účtů úložiště, které jsou definovány v souboru Core-site. XML. Toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Několik úloh WebHCat, včetně Apache Hive, MapReduce, Apache Hadoop streaming a Apache prasete, může obsahovat popis účtů úložiště a metadat. (Tato funkce aktuálně funguje pro prase s účty úložiště, ale ne pro metadata.) Další informace najdete v tématu [použití clusteru HDInsight s alternativním účtem úložiště a metaúložiště](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná a nestrukturovaná data. Kontejnery objektů BLOB ukládají data jako páry klíč/hodnota a neexistuje žádná hierarchie adresářů. Znak lomítka (/) se však dá použít v názvu klíče, aby se zobrazil jako soubor, který je uložený v rámci adresářové struktury. Klíč objektu BLOB může být například *input/log1. txt*. Neexistuje žádný skutečný *vstupní* adresář, ale v důsledku přítomnosti znaku lomítka v názvu klíče má vzhled cesty k souboru.

## <a id="benefits"></a>Výhody Azure Storage

Předpokládané náklady na výkon při neumísťování výpočetních clusterů a prostředků úložiště jsou zmírněny tím, že výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure, kde vysokorychlostní síť je efektivní pro výpočetní uzly pro přístup k datům ve službě Azure Storage.

K ukládání dat do služby Azure Storage místo HDFS je potřeba několik výhod:

* **Opakované použití a sdílení dat:** Data v HDFS se nachází ve výpočetním clusteru. Data můžou používat jenom aplikace, které mají přístup k výpočetnímu clusteru, pomocí rozhraní API HDFS. Data ve službě Azure Storage jsou dostupná prostřednictvím rozhraní API HDFS nebo prostřednictvím [rozhraní API REST BLOB Storage](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Proto můžete k vytváření a využívání dat použít větší sadu aplikací (včetně dalších clusterů HDInsight) a nástroje.

* **Archivace dat:** Ukládání dat v Azure Storage umožňuje bezpečně odstranit clustery HDInsight používané pro výpočty, aniž by došlo ke ztrátě uživatelských dat.

* **Náklady na úložiště dat:** Ukládání dat v systému souborů DFS je z dlouhodobého hlediska dražší než ukládání dat do služby Azure Storage, protože náklady na výpočetní cluster jsou vyšší než náklady na službu Azure Storage. Vzhledem k tomu, že data není nutné znovu načíst pro každou generaci výpočetních clusterů, jsou také uloženy náklady na načítání dat.

* **Elastické škálování na** více instancí: I když HDFS poskytuje systém souborů se škálováním na více systémů, měřítko se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna měřítka se může stát složitějším procesem, než se spoléhá na možnosti elastického škálování, které automaticky získáte v Azure Storage.

* **Geografická replikace:** Vaše úložiště Azure může být geograficky replikované. I když vám poskytuje geografické obnovení a redundanci dat, převzetí služeb při selhání geograficky replikovaným umístěním vážně ovlivní váš výkon a může to mít za následek další náklady. Proto doporučujeme zvolit geografickou replikaci, a to i v případě, že hodnota dat stojí za další náklady.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které ve službě Azure Storage opravdu nechcete ukládat. V takovém případě se můžete rozhodnout ukládat data do místního HDFS. Ve skutečnosti používá HDInsight DFS pro několik těchto mezilehlých výsledků v úlohách podregistru a dalších procesech.

> [!NOTE]  
> Většina příkazů HDFS (například `ls`, `copyFromLocal` a `mkdir`) stále pracuje podle očekávání. V Azure Storage se zobrazí různé vlastnosti, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), například `fschk` a `dfsadmin`.

## <a name="address-files-in-azure-storage"></a>Adresování souborů ve službě Azure Storage

Schéma identifikátoru URI pro přístup k souborům ve službě Azure Storage ze služby HDInsight je:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Schéma identifikátoru URI poskytuje nešifrovaný přístup (s předponou *wasb:* prefix) a ŠIFROVANÝ přístup SSL (s *wasbs*). Pokud je to možné, doporučujeme používat *wasbs* , a to i v případě, že přistupujete k datům umístěným uvnitř stejné oblasti v Azure.

@No__t-0 identifikuje název kontejneru objektů BLOB ve službě Azure Storage.
@No__t-0 identifikuje název účtu Azure Storage. Vyžaduje se plně kvalifikovaný název domény (FQDN).

Pokud není zadána žádná z `<BlobStorageContainerName>` ani `<StorageAccountName>`, použije se výchozí systém souborů. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například soubor *Hadoop-MapReduce-Examples. jar* , který je součástí clusterů HDInsight, může být odkazován pomocí jednoho z následujících postupů:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2,1 a 1,6.

Cesta je název cesty HDFS souboru nebo adresáře. Vzhledem k tomu, že kontejnery ve službě Azure Storage jsou úložištěm klíč-hodnota, neexistuje žádný skutečný hierarchický systém souborů. Znak lomítka (/) uvnitř klíče objektu BLOB je interpretován jako oddělovač adresáře. Název objektu BLOB pro *Hadoop-MapReduce-Examples. jar* je například:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Při práci s objekty BLOB mimo HDInsight Většina nástrojů nerozpozná formát WASB a místo toho očekává základní formát cesty, například `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>Kontejnery objektů BLOB

Pokud chcete použít bloby, musíte nejdřív vytvořit [účet Azure Storage](../storage/common/storage-create-storage-account.md). V rámci tohoto nastavení určíte oblast Azure, ve které se účet úložiště vytvoří. Cluster a účet úložiště musí být hostované ve stejné oblasti. Databáze metastore Hive SQL Server a databáze Apache Oozie SQL Server metastore musí být také umístěny ve stejné oblasti.

Všechny objekty blob, které vytvoříte, patří do kontejneru ve vašem účtu Azure Storage. Tento kontejner může být existující objekt blob, který se vytvořil mimo HDInsight, nebo se může jednat o kontejner, který se vytvoří pro cluster HDInsight.

Výchozí kontejner objektu BLOB ukládá informace specifické pro cluster, jako je historie a protokoly úloh. Nesdílejte výchozí kontejner objektů BLOB s více clustery HDInsight. To může poškodit historii úlohy. Doporučuje se použít pro každý cluster jiný kontejner a umístit sdílená data na propojený účet úložiště zadaný v nasazení všech relevantních clusterů, a ne jako výchozí účet úložiště. Další informace o konfiguraci propojených účtů úložiště najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Po odstranění původního clusteru HDInsight ale můžete znovu použít výchozí kontejner úložiště. U clusterů HBA můžete ve skutečnosti udržovat schéma a data v tabulce HBA vytvořením nového clusteru HBA pomocí výchozího kontejneru objektů blob, který se používá v clusteru HBA, který byl odstraněn.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interakce s Azure Storage

Společnost Microsoft poskytuje následující nástroje pro práci s Azure Storage:

| Nástroj | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Rozhraní příkazového řádku Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Použití dalších účtů úložiště

Při vytváření clusteru HDInsight zadáváte účet Azure Storage, který se k němu chcete přidružit. Kromě tohoto účtu úložiště můžete přidat další účty úložiště ze stejného předplatného Azure nebo různých předplatných Azure během procesu vytváření nebo po vytvoření clusteru. Pokyny k přidání dalších účtů úložiště najdete v tématu [Vytvoření clusterů HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat HDFS kompatibilní úložiště Azure se službou HDInsight. To umožňuje vytvářet škálovatelná a dlouhodobá řešení pro získávání archivovaných dat a používat HDInsight k odemknutí informací uvnitř uložených strukturovaných a nestrukturovaných dat.

Další informace naleznete v tématu:

* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Nahrání dat do HDInsight](hdinsight-upload-data.md)
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Omezení přístupu k datům pomocí HDInsight pomocí Azure Storage sdílených přístupových podpisů](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
