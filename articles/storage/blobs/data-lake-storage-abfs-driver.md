---
title: Ovladač systému souborů objektů BLOB v Azure pro Azure Data Lake Storage Gen2
description: Přečtěte si o ovladači systému souborů objektů BLOB v Azure (ABFS), vyhrazeném ovladače Azure Storage pro Hadoop. Přístup k datům v Azure Data Lake Storage Gen2 pomocí tohoto ovladače.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 54c6245ea5290e2cab0efcd52f2208ba874c0848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720439"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Ovladač systému souborů objektů BLOB v Azure (ABFS): vyhrazený ovladač Azure Storage pro Hadoop

Jednou z primárních metod přístupu k datům v Azure Data Lake Storage Gen2 je prostřednictvím [systému Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Data Lake Storage Gen2 umožňuje uživatelům Azure Blob Storage přístup k novému ovladači, ovladači systému souborů blob Azure nebo `ABFS` . ABFS je součástí Apache Hadoop a je součástí mnoha komerčních distribucí systému Hadoop. Pomocí tohoto ovladače můžou spousta aplikací a platforem získat přístup k datům v Azure Blob Storage bez explicitního odkazování na Data Lake Storage Gen2 kódu.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Předchozí schopnost: ovladač Azure Storage Blob Windows

Ovladač Windows Azure Storage Blob nebo [ovladač WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) poskytl původní podporu pro Azure Blob Storage. Tento ovladač provedl komplexní úlohu mapování sémantiky systému souborů (jak je požadováno rozhraním systému Hadoop) na rozhraní stylu úložiště objektů vystavené službou Azure Blob Storage. Tento ovladač nadále podporuje tento model a poskytuje vysoký výkon při přístupu k datům uloženým v objektech blob, ale obsahuje značný objem kódu, který provádí toto mapování, což ztěžuje jeho údržbu. Kromě toho některé operace, jako je například [FileSystem. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) a [FileSystem. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) při použití u adresářů, vyžadují, aby ovladač prováděl velké množství operací (vzhledem k tomu, že úložiště neobsahují podporu pro adresáře), což často vede ke snížení výkonu. Ovladač ABFS byl navržen pro překonání nedostatků WASB.

## <a name="the-azure-blob-file-system-driver"></a>Ovladač systému souborů BLOB v Azure

[Rozhraní Azure Data Lake Storage REST](/rest/api/storageservices/data-lake-storage-gen2) je navrženo tak, aby podporovalo sémantiku systému souborů přes Azure Blob Storage. Vzhledem k tom, že systém Hadoop je navržený tak, aby podporoval stejnou sémantiku, neexistuje žádný požadavek na komplexní mapování v ovladači. Proto je ovladač systému souborů objektů BLOB v Azure (nebo ABFS) pouhým překrytím klienta pro REST API.

Existují však některé funkce, které musí ovladač stále provádět:

### <a name="uri-scheme-to-reference-data"></a>Schéma identifikátoru URI pro referenční data

Ovladač ABFS je v souladu s dalšími implementacemi systému souborů v rámci systému Hadoop a definuje vlastní schéma identifikátoru URI, aby bylo možné prostředky (adresáře a soubory) rozlišit. Schéma identifikátoru URI je popsáno v [části použití Azure Data Lake Storage Gen2 identifikátor URI](./data-lake-storage-introduction-abfs-uri.md). Struktura identifikátoru URI je: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Pomocí výše uvedeného formátu identifikátoru URI se k odkazování na tyto prostředky dají použít standardní nástroje a architektury Hadoop:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Ovladač ABFS interně překládá prostředky zadané v identifikátoru URI do souborů a adresářů a provádí volání REST API Azure Data Lake Storage s těmito odkazy.

### <a name="authentication"></a>Authentication

Ovladač ABFS podporuje dvě formy ověřování, aby mohla aplikace Hadoop bezpečně přistupovat k prostředkům, které jsou obsaženy v účtu s podporou Data Lake Storage Gen2. Úplné podrobnosti o dostupných schématech ověřování jsou k dispozici v [Azure Storage příručce zabezpečení](security-recommendations.md). Jsou to tyto:

- **Sdílený klíč:** To umožňuje uživatelům přístup ke všem prostředkům v účtu. Klíč je zašifrovaný a uložený v konfiguraci Hadoop.

- **Azure Active Directory nosný token OAuth:** Tokeny nosiče Azure AD se získávají a aktualizují pomocí ovladače buď prostřednictvím identity koncového uživatele, nebo nakonfigurovaného instančního objektu. Pomocí tohoto modelu ověřování je veškerý přístup autorizovaný pro jednotlivá volání pomocí identity přidružené k zadanému tokenu a vyhodnocený na základě přiřazeného seznamu Access Control POSIX (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 podporuje pouze koncové body Azure AD v 1.0.

### <a name="configuration"></a>Konfigurace

Všechny konfigurace pro ovladač ABFS jsou uložené v <code>core-site.xml</code> konfiguračním souboru. V distribucích Hadoop s [Ambari](https://ambari.apache.org/)může být konfigurace spravovaná taky pomocí webového portálu nebo Ambari REST API.

Podrobnosti o všech podporovaných položkách konfigurace jsou uvedeny v [oficiální dokumentaci k systému Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Dokumentace k Hadoop

Ovladač ABFS je plně popsán v [oficiální dokumentaci k Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) .

## <a name="next-steps"></a>Další kroky

- [Vytvoření clusteru Azure Databricks](./data-lake-storage-use-databricks-spark.md)
- [Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)