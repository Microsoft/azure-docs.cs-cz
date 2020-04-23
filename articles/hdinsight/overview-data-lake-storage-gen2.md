---
title: Přehled úložiště datového jezera Azure v HDInsightu
description: Přehled data lake storage gen2 v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873339"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Přehled úložiště datového jezera Azure v HDInsightu

Azure Data Lake Storage Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 a integruje je do úložiště objektů blob Azure. Mezi tyto funkce patří systém souborů, který je kompatibilní s Hadoop, Azure Active Directory (Azure AD) a SEZNAMY řízení přístupu na základě POSIX (ACL). Tato kombinace umožňuje využít výhod výkonu Azure Data Lake Storage Gen1. Při použití také vrstvení a správy životního cyklu dat úložiště objektů blob.

Další informace o Azure Data Lake Storage Gen2 najdete [v tématu Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

* **Přístup, který je kompatibilní s Hadoopem:** V Azure Data Lake Storage Gen2 můžete spravovat a přistupovat k datům stejně jako pomocí distribuovaného souborového systému Hadoop (HDFS). Ovladač Azure Blob File System (ABFS) je k dispozici ve všech prostředích Apache Hadoop, včetně Azure HDInsight a Azure Databricks. Pomocí ABFS pro přístup k datům uloženým v datovém úložišti data Č. 2.

* **Nadmnožina oprávnění POSIX:** Model zabezpečení pro Data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými extra rozlišovací schopnost specifické pro Data Lake Storage Gen2. Nastavení lze konfigurovat pomocí nástrojů pro správu nebo rámců, jako je Apache Hive a Apache Spark.

* **Efektivita nákladů:** Data Lake Storage Gen2 nabízí nízkonákladovou úložnou kapacitu a transakce. Životní cykly úložiště objektů blob Azure pomáhají snižovat náklady úpravou fakturačních sazeb při pohybu dat v průběhu životního cyklu.

* **Kompatibilita s nástroji, architekturami a aplikacemi pro ukládání objektů Blob:** Data Lake Storage Gen2 pokračuje v práci s širokou škálou nástrojů, architektur a aplikací pro úložiště objektů Blob.

* **Optimalizovaný ovladač:** Ovladač ABFS je optimalizován speciálně pro analýzu velkých objemů dat. Odpovídající rest API jsou konsurfaced prostřednictvím koncového bodu distribuovaného systému souborů (DFS), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co je nového pro Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro zabezpečený přístup k souborům

Azure HDInsight používá spravované identity k zabezpečení přístupu clusteru k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkce služby Azure Active Directory, která poskytuje službám Azure sadu automaticky spravovaných přihlašovacích údajů. Tato pověření lze použít k ověření jakékoli služby, která podporuje ověřování služby Active Directory. Použití spravovaných identit nevyžaduje ukládání přihlašovacích údajů do kódových nebo konfiguračních souborů.

Další informace najdete v tématu [Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Ovladač systému souborů Objektů blob Azure

Aplikace Apache Hadoop nativně očekávají, že budou číst a zapisovat data z místního úložiště disků. Ovladač systému souborů Hadoop, jako je ABFS, umožňuje aplikacím Hadoop pracovat s cloudovým úložištěm. Funguje tak, že emuluje běžné operace systému souborů Hadoop. Ovladač převede tyto příkazy přijaté z aplikace na operace, které rozumí skutečná platforma cloudového úložiště.

Dříve ovladač souborového systému Hadoop převedl všechny operace systému souborů na volání rozhraní REST azure na straně klienta. A pak vyvolal rozhraní REST API. Tento převod na straně klienta však za následek více volání rozhraní REST API pro jednu operaci systému souborů, jako je přejmenování souboru. ABFS přesunul logiku systému souborů Hadoop ze strany klienta na stranu serveru. Rozhraní API Azure Data Lake Storage Gen2 teď běží paralelně s rozhraním BLOB API. Tato migrace zlepšuje výkon, protože nyní běžné operace systému souborů Hadoop lze provést s jedním voláním rozhraní REST API.

Další informace najdete [v tématu ovladač souborového systému objektů blob Azure (ABFS): Vyhrazený ovladač úložiště Azure pro Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schéma URI pro úložiště datových jezer Azure Gen 2

Azure Data Lake Storage Gen2 používá nové schéma URI pro přístup k souborům v Azure Storage z HDInsightu:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma URI poskytuje přístup šifrovaný s ssl.

`<FILE_SYSTEM_NAME>`identifikuje cestu systému souborů Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identifikuje název účtu Úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>`je název cesty HDFS souboru nebo adresáře.

Pokud nejsou `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` zadány hodnoty pro a nejsou zadány, použije se výchozí systém souborů. Pro soubory ve výchozím systému souborů použijte relativní cestu nebo absolutní cestu. Například `hadoop-mapreduce-examples.jar` soubor, který je dodáván s clustery HDInsight, může být odkazován pomocí jedné z následujících cest:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Název souboru `hadoop-examples.jar` je v clusterech HDInsight verze 2.1 a 1.6. Při práci se soubory mimo HDInsight zjistíte, že většina nástrojů nerozpozná formát ABFS, ale místo toho `example/jars/hadoop-mapreduce-examples.jar`očekává základní formát cesty, například .

Další informace najdete [v tématu Použití identifikátoru URI úložiště datového jezera Azure.](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)

## <a name="next-steps"></a>Další kroky

* [Úvod do úložiště datových jezer Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
* [Přehled úložiště datového jezera Azure1 – přehled](./overview-data-lake-storage-gen1.md)