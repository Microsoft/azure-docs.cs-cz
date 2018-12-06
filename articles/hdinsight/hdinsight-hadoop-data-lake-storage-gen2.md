---
title: Použití Azure Data Lake Storage (ADLS) Gen2 s Apache Hadoop v Azure HDInsight
description: Poskytuje přehled o Azure Data Lake Storage Gen2 a jak funguje s Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 2ae11afe1ecbe500a4851aab6d56e612fbe79ee6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975127"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Použití Azure Data Lake Storage Gen2 s Apache Hadoop v Azure HDInsight

Azure Data Lake Storage (ADLS) Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 například kompatibilní souborový systém Hadoop, Azure Active Directory, a na základě seznamů ACL POSIX a integruje do služby Azure Blob Storage. Tato kombinace umožňuje využít výkon Azure Data Lake Storage Gen1 a zároveň využívat vrstvení úložiště objektů Blob a správa životního cyklu data.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

- Hadoop kompatibilní přístup: Azure Data Lake Storage Gen2 umožňuje spravovat a přistupovat k datům, stejně jako byste to udělali s souboru systému HDFS (Hadoop Distributed). Je k dispozici ve všech prostředích Apache Hadoop, včetně Azure HDInsight a Azure Databricks pro přístup k datům uloženým v Data Lake Storage Gen2 ABFS ovladač.

- Nadmnožina POSIX oprávnění: model zabezpečení pro Data Lake Gen2 podporuje oprávnění řízení přístupu a POSIX spolu s nějaké další specifické pro Data Lake Storage Gen2 členitosti. Nastavení lze nakonfigurovat prostřednictvím správce nástroje nebo architektury, jako je Apache Hivu a Apache Sparku.

- Nákladově efektivní: nabízí kapacitu úložiště s nízkými náklady a transakce s Data Lake Storage Gen2. Funkce, jako je životní cyklus úložiště objektů Blob v Azure pomůže snížit náklady na úpravou fakturační sazby podle dat prochází přes životního cyklu.

- Funguje s Blob storage nástroje, platformy a aplikace: Data Lake Storage Gen2 nadále využívat širokou škálu nástroje, platformy a aplikace, které existují ještě dnes pro úložiště objektů Blob.

- Optimalizované ovladač: The ABFS ovladač je speciálně pro analýzy velkých objemů dat optimalizovaná. Zobrazují se prostřednictvím koncového bodu systému souborů dfs, odpovídající rozhraní REST API dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Co je nového v Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro přístup k souborům zabezpečení

Azure HDInsight pomocí spravované identity zabezpečení clusteru přístup k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkcí služby Azure Active Directory, která poskytuje služby Azure se sadou automaticky spravované přihlašovací údaje. Tyto přihlašovací údaje slouží k ověření na libovolnou službu, která podporuje ověřování AD. Použití spravované identity nevyžaduje k uložení přihlašovacích údajů v kódu nebo konfiguračního souboru.

Další informace najdete v tématu [co je spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Ovladač systému souborů (ABFS) služby Azure Blob

Apache Hadoop aplikace nativně očekávají, že ke čtení a zápisu dat z úložiště na místním disku. Ovladač systému souborů Hadoop jako ABFS umožňuje aplikacím Hadoop pro práci s cloudovým úložištěm emulací standardních operací systému souborů Hadoop pro aplikaci. Tyto operace ovladač potom převede na operace, které rozumí skutečnou cloudovou platformu úložiště.

Ovladač systému souborů Hadoop by dříve, převést všechny operace systému souborů na volání REST API služby Azure Storage na straně klienta a pak vyvoláte rozhraní REST API. Tento na straně klienta převod, ale výsledkem více rozhraní REST API je volání pro jeden vykonání operace systému souborů jako soubor přejmenovat. ABFS přesunul některé logiky systému souborů Hadoop ze strany klienta na straně serveru a rozhraní API Gen2 ADLS teď běží paralelně s rozhraním API pro objekt Blob. Tato migrace zlepší výkon, protože nyní mohou být provedeny běžných operací systému souborů Hadoop pomocí jednoho volání rozhraní REST API.

Další informace najdete v tématu [ovladačů systém souborů Azure Blob (ABFS): vyhrazené ovladač Azure Storage pro Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>Schéma identifikátoru URI ADLS Gen 2

ADLS Gen2 používá nové schéma identifikátoru URI pro přístup k souborům ve službě Azure storage v HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma identifikátoru URI poskytuje přístup šifrovanou protokolem SSL (`abfss://` předpony) a nešifrované přístup (`abfs://` předponu). Doporučujeme používat `abfss` kdykoli je to možné, i v případě, že přístup k datům, umístěným uvnitř stejné oblasti v Azure.

`<FILE_SYSTEM_NAME>` Určuje cestu systému souborů Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Určuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>` Je název cesty HDFS souboru nebo adresáře.

Pokud hodnoty `<FILE_SYSTEM_NAME>` a `<ACCOUNT_NAME>` nejsou zadané, se používá výchozí systém souborů. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například `hadoop-mapreduce-examples.jar` soubor, který se dodává s clustery HDInsight lze odkazovat pomocí jedné z následujících cest:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2.1 a 1.6. Při práci se soubory mimo HDInsight Většina nástrojů ABFS formátování a místo toho očekávají základní formát cesty, jako například nebyla rozpoznána `example/jars/hadoop-mapreduce-examples.jar`.

Další informace najdete v tématu [použít identifikátor URI služby Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>Další postup
- [Úvod do služby Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md)
- [Použití Azure Data Lake Storage Gen2 Preview s využitím clusterů Azure HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)