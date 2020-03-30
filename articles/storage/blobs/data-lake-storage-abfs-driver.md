---
title: Ovladač souborového systému objektů blob Azure pro Azure Data Lake Storage Gen2
description: Ovladač souborového systému ABFS Hadoop
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75970278"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Ovladač souborového systému objektů blob Azure (ABFS): Vyhrazený ovladač úložiště Azure pro Hadoop

Jednou z primárních metod přístupu k datům v Azure Data Lake Storage Gen2 je [hadoopfilesystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Storage Data Lake Storage Gen2 umožňuje uživatelům azure blob storage přístup k `ABFS`novému ovladači, ovladači systému souborů Azure Blob nebo . ABFS je součástí Apache Hadoop a je součástí mnoha komerčních distribucí Hadoopu. Pomocí tohoto ovladače mnoho aplikací a architektur y přístup k datům v Azure Blob Storage bez jakéhokoli kódu explicitně odkazující na Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Předchozí možnosti: Ovladač objektu blob úložiště Windows Azure

Ovladač objektu blob úložiště Windows Azure nebo [ovladač WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) poskytl původní podporu pro azure blob storage. Tento ovladač provedl složitou úlohu mapování sémantiky systému souborů (podle požadavků rozhraní Hadoop FileSystem) na rozhraní stylu úložiště objektů vystavené službou Azure Blob Storage. Tento ovladač nadále podporuje tento model a poskytuje vysoce výkonný přístup k datům uloženým v objektech BLOB, ale obsahuje značné množství kódu provádějících toto mapování, což ztěžuje údržbu. Kromě toho některé operace, jako je [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) a [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) při použití na adresáře vyžadují ovladač provádět obrovské množství operací (z důvodu objektu ukládá nedostatek podpory pro adresáře), což často vede ke snížení výkonu. Ovladač ABFS byl navržen tak, aby překonal inherentní nedostatky WASB.

## <a name="the-azure-blob-file-system-driver"></a>Ovladač systému souborů objektů blob Azure

Rozhraní [Azure Data Lake Storage REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) je navržené tak, aby podporovalo sémantiku systému souborů přes Azure Blob Storage. Vzhledem k tomu, že souborový systém Hadoop je také navržen tak, aby podporoval stejnou sémantiku, neexistuje žádný požadavek na komplexní mapování v ovladači. Ovladač systému souborů Azure Blob (nebo ABFS) je tedy pouze překrytí klienta pro rozhraní REST API.

Existují však některé funkce, které musí ovladač stále provádět:

### <a name="uri-scheme-to-reference-data"></a>Schéma URI pro referenční data

V souladu s ostatními implementacemi systému souborů v rámci Hadoop, ovladač ABFS definuje své vlastní schéma URI tak, aby prostředky (adresáře a soubory) mohou být zřetelně řešeny. Schéma URI je zdokumentováno v [použití identifikátoru Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md). Struktura identifikátoru URI je:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Pomocí výše uvedeného formátu URI lze k odkazování na tyto prostředky použít standardní nástroje hadoopa a architektury:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Interně ovladač ABFS překládá prostředky zadané v URI na soubory a adresáře a provádí volání rozhraní API REST úložiště datového jezera Azure s těmito odkazy.

### <a name="authentication"></a>Ověřování

Ovladač ABFS podporuje dvě formy ověřování tak, aby aplikace Hadoop může bezpečně přistupovat k prostředkům obsaženým v účtu s podporou úložiště datového jezera Gen2. Podrobné informace o dostupných schématech ověřování jsou k dispozici v [průvodci zabezpečením úložiště Azure](security-recommendations.md). Jsou to tyto:

- **Sdílený klíč:** To umožňuje uživatelům přístup ke všem prostředkům v účtu. Klíč je zašifrován a uložen v konfiguraci Hadoop.

- **Token nosiče služby Azure Active Directory OAuth:** Tokeny nosiče Azure AD jsou získány a aktualizovány ovladačem pomocí identity koncového uživatele nebo nakonfigurovaného instančního objektu. Pomocí tohoto ověřovacího modelu je veškerý přístup autorizován na základě volání pomocí identity přidružené k zadanému tokenu a vyhodnocen proti přiřazenému seznamu řízení přístupu POSIX (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 podporuje jenom koncové body Azure AD v1.0.

### <a name="configuration"></a>Konfigurace

Veškerá konfigurace ovladače ABFS je <code>core-site.xml</code> uložena v konfiguračním souboru. Na hadoopských distribucích s [Ambari](https://ambari.apache.org/)může být konfigurace spravována také pomocí webového portálu nebo rozhraní Ambari REST API.

Podrobnosti o všech podporovaných položkách konfigurace jsou uvedeny v [dokumentaci Oficiální Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Dokumentace hadoopu

Ovladač ABFS je plně zdokumentován v [oficiální dokumentaci Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Další kroky

- [Vytvoření clusteru Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)
- [Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)
