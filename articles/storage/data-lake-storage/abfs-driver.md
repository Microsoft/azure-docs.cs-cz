---
title: Ovladač systému Azure Blob souborů pro Azure Data Lake Storage Gen2 Preview
description: Ovladač systému souborů Hadoop ABFS
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e92c4efba29f1c40f6d4cb155974ca3a896796e5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114329"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure Blob souborů ovladačů (ABFS): vyhrazené ovladač Azure Storage pro Hadoop

Jedna z metod primární přístupu pro data v Azure Data Lake Storage Gen2 Preview je prostřednictvím [systému souborů Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Azure Data Lake Storage Gen2 funkce přidružené ovladače, systém souborů Azure Blob ovladače nebo `ABFS`. ABFS je součástí Apache Hadoop a je součástí mnoha komerční distribucích systému Hadoop. Pomocí tento ovladač, mnoho aplikací a architektury získat přístup k datům v Data Lake Storage Gen2 bez jakékoli kódu explicitně odkazy na službu Gen2 úložiště Data Lake.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Předchozí schopností: ovladače systému Windows Azure Blob Storage

Ovladače systému Windows Azure Blob Storage nebo [WASB ovladač](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) zadat původní podpora pro objekty BLOB úložiště Azure. Tento ovladač provést složité úlohy systému souborů, mapování sémantiku (podle potřeby pomocí rozhraní Hadoop systému souborů) u objektu uložení rozhraní ve stylu vystavené Azure Blob Storage. Tento ovladač nadále podporuje tento model poskytuje vysoký výkon přístupu k datům ukládají do objektů BLOB, ale obsahuje významné množství kód, který provádí toto mapování, takže je obtížné. Kromě toho některé operace, jako [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) a [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) při použití adresáře vyžaduje ovladač k provedení velká počet operací (z důvodu nedostatku objektu úložiště Podpora pro adresáře) která často vede ke snížení výkonu.

K překonání nedostatky vyplývajících návrhu z WASB, byl proto nové služby Azure Data Lake Storage a s podporou nový ovladač ABFS implementován.

## <a name="the-azure-blob-file-system-driver"></a>Ovladač systému souborů objektů Blob Azure

[Rozhraní Azure Data Lake Storage REST](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2) je navržen pro podporu sémantiku systému souborů přes Azure Blob Storage. Vzhledem k tomu, že systému souborů Hadoop je navržen pro podporu stejnou sémantiku se nevyžaduje pro komplexní mapování v ovladači. Ovladač systému souborů objektů Blob Azure (nebo ABFS) tedy shim pouhé klienta pro REST API.

Existují však některé funkce, které ovladač musí i nadále provádět:

### <a name="uri-scheme-to-reference-data"></a>Schéma URI, které referenční data

Konzistentní s jiným implementacím systému souborů v systému Hadoop, ovladač ABFS definuje vlastní schéma identifikátoru URI takže prostředky (adresářů a souborů) může být odlišné řešit. Schéma identifikátoru URI je popsána v [použít identifikátor URI úložiště Azure Data Lake Gen2](./introduction-abfs-uri.md). Struktura identifikátoru URI je: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Pomocí výše uvedených formát identifikátoru URI, standardní nástroje Hadoop a rozhraní lze odkazovat na tyto prostředky:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Interně ABFS ovladač překládá prostředky zadaný v identifikátoru URI pro soubory a adresáře a provede volání REST API pro Azure Data Lake Storage s tyto odkazy.

### <a name="authentication"></a>Authentication

Ovladač ABFS aktuálně podporuje ověření sdíleným klíčem tak, aby aplikace Hadoop může bezpečný přístup k prostředkům, které jsou obsažené v Gen2 úložiště Data Lake. Klíč je šifrovaný a uložené v konfiguraci Hadoop.

### <a name="configuration"></a>Konfigurace

Všechny konfigurace pro ovladač ABFS je uložená v <code>core-site.xml</code> konfigurační soubor. Na Hadoop distribuce s funkcí [Ambari](http://ambari.apache.org/), konfigurace může spravovat také pomocí portálu pro web nebo Ambari REST API.

Podrobnosti o všech položek podporovanou konfiguraci jsou určené v [Hadoop oficiální dokumentaci](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Dokumentace systému Hadoop

Ovladač ABFS je plně dokumentovány v článku [Hadoop oficiální dokumentaci](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Další postup

- [Instalační program clustery HDInsight](./quickstart-create-connect-hdi-cluster.md)
- [Vytvoření clusteru Databricks služby Azure](./quickstart-create-databricks-account.md)
- [Pomocí Azure Data Lake Storage Gen2 identifikátor URI](./introduction-abfs-uri.md)
