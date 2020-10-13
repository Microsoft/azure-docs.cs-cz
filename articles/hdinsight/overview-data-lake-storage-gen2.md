---
title: Přehled Azure Data Lake Storage Gen2 v HDInsight
description: Přehled Data Lake Storage Gen2 v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: a7ae1a0f77d0d19fc48695ddc5cc3d3a14d65ab9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82195124"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Přehled Azure Data Lake Storage Gen2 v HDInsight

Azure Data Lake Storage Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 a integruje je do úložiště objektů BLOB v Azure. Mezi tyto funkce patří systém souborů, který je kompatibilní se systémy Hadoop, Azure Active Directory (Azure AD) a seznamy řízení přístupu (ACL) založenými na POSIX. Tato kombinace vám umožní využít výkon Azure Data Lake Storage Gen1. I když se používá Správa životního cyklu služby Blob Storage pomocí vrstev a dat.

Další informace o Azure Data Lake Storage Gen2 najdete v tématu [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

* **Přístup kompatibilní se systémem Hadoop:** V Azure Data Lake Storage Gen2 můžete spravovat data a přistupovat k nim stejným způsobem jako v systém souborů DFS (Distributed File System) Hadoop (HDFS). Ovladač systému souborů objektů BLOB v Azure (ABFS) je k dispozici ve všech Apache Hadoop prostředích, včetně Azure HDInsight a Azure Databricks. Pro přístup k datům uloženým v Data Lake Storage Gen2 použijte ABFS.

* **Nadmnožina oprávnění POSIX:** Model zabezpečení pro Data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro Data Lake Storage Gen2. Nastavení lze nakonfigurovat prostřednictvím nástrojů pro správu nebo architektury, jako jsou Apache Hive a Apache Spark.

* **Efektivita nákladů:** Data Lake Storage Gen2 nabízí s nízkými náklady kapacitu a transakce úložiště. Životní cykly Azure Blob Storage vám pomůžou snížit náklady nastavením fakturačních sazeb, protože data se pohybují během jejího životního cyklu.

* **Kompatibilita s nástroji, rozhraními a aplikacemi služby Blob Storage:** Data Lake Storage Gen2 i nadále pracují se škálou nástrojů, platforem a aplikací pro úložiště objektů BLOB.

* **Optimalizovaný ovladač:** Ovladač ABFS se optimalizuje speciálně pro analýzy velkých objemů dat. Odpovídající rozhraní REST API se procházejí prostřednictvím koncového bodu systému souborů DFS (Distributed File System), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co je nového pro Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro zabezpečený přístup k souborům

Azure HDInsight používá spravované identity k zabezpečení přístupu clusteru k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkcí Azure Active Directory, která poskytuje služby Azure se sadou automaticky spravovaných přihlašovacích údajů. Pomocí těchto přihlašovacích údajů se můžete ověřit u jakékoli služby, která podporuje ověřování pomocí služby Active Directory. Použití spravovaných identit nevyžaduje ukládání přihlašovacích údajů do kódu nebo konfiguračních souborů.

Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-file-system-driver"></a>Ovladač systému souborů Azure Blob

Apache Hadoop aplikace nativně očekávají čtení a zápis dat z diskového úložiště na místním disku. Ovladač systému souborů Hadoop, jako je ABFS, umožňuje aplikacím Hadoop pracovat s cloudovým úložištěm. Funguje tak, že emuluje běžné operace systému souborů Hadoop. Ovladač převede tyto příkazy přijaté z aplikace na operace, které rozumí skutečná platforma cloudového úložiště.

Dříve ovladač systému souborů Hadoop převedl všechny operace systému souborů na Azure Storage REST API volání na straně klienta. A potom vyvolá REST API. Tento převod na straně klienta však má za následek více REST API volání pro jednu operaci systému souborů, jako je přejmenování souboru. ABFS přesunula logiku systému souborů Hadoop ze strany klienta na stranu serveru. Rozhraní Azure Data Lake Storage Gen2 API teď běží paralelně s rozhraním API objektů BLOB. Tato migrace zvyšuje výkon, protože teď můžete provádět běžné operace se systémem souborů Hadoop pomocí jednoho REST API volání.

Další informace najdete v tématu [ovladač systému souborů objektů BLOB v Azure (ABFS): vyhrazený ovladač Azure Storage pro Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schéma identifikátoru URI pro Azure Data Lake Storage Gen 2

Azure Data Lake Storage Gen2 používá nové schéma identifikátoru URI pro přístup k souborům v Azure Storage ze služby HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma identifikátoru URI poskytuje přístup šifrovaný protokolem SSL.

`<FILE_SYSTEM_NAME>` Určuje cestu Data Lake Storage Gen2 systému souborů.

`<ACCOUNT_NAME>` Určuje název Azure Storage účtu. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>` je název cesty HDFS souboru nebo adresáře.

Pokud `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` nejsou zadány hodnoty pro a, použije se výchozí systém souborů. Pro soubory ve výchozím systému souborů použijte relativní cestu nebo absolutní cestu. Například `hadoop-mapreduce-examples.jar` soubor, který je součástí clusterů HDInsight, lze odkazovat pomocí jedné z následujících cest:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2,1 a 1,6. Když pracujete se soubory mimo HDInsight, zjistíte, že většina nástrojů nerozpoznala formát ABFS, ale očekává jako základní formát cesty, například `example/jars/hadoop-mapreduce-examples.jar` .

Další informace najdete v tématu [použití identifikátoru URI Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
* [Přehled služby Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)