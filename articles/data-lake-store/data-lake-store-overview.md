---
title: Co je Azure Data Lake Storage Gen1? | Dokumentace Microsoftu
description: Přehled Data Lake Storage Gen1 (dříve označovaných jako Azure Data Lake Store) a hodnota, kterou poskytuje v jiných úložištích dat
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 2ca6ceb326a1fd6f7aaf5a9871d043377e60de83
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85508550"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Co je Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 je vysoce škálovatelné úložiště pro analýzy velkých objemů dat v podnikové síti. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu do jediného místa pro účely provozní a zjišťovací analýzy.

K Data Lake Storage Gen1 lze získat přístup z Hadoop (k dispozici s clusterem HDInsight) pomocí rozhraní REST API kompatibilního s WebHDFS. Je navržená tak, aby umožňovala analýzy uložených dat a vyladěná na výkon pro scénáře analýzy dat. Data Lake Storage Gen1 zahrnují všechny funkce na podnikové úrovni: zabezpečení, možnosti správy, škálovatelnost, spolehlivost a dostupnost.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Klíčové funkce

Mezi klíčové funkce Data Lake Storage Gen1 patří následující:

### <a name="built-for-hadoop"></a>Sestaveno pro Hadoop

Data Lake Storage Gen1 je Apache Hadoop systém souborů, který je kompatibilní se systémem Hadoop systém souborů DFS (Distributed File System) (HDFS) a pracuje s ekosystémem Hadoop. Stávající aplikace nebo služby HDInsight, které používají rozhraní WebHDFS API, se můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také zpřístupňuje rozhraní REST kompatibilní s WebHDFS pro aplikace.

Data uložená v Data Lake Storage Gen1 můžete snadno analyzovat pomocí analytických rozhraní Hadoop, jako je MapReduce nebo podregistr. Clustery Azure HDInsight můžete zřídit a nakonfigurovat pro přímý přístup k datům uloženým v Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a může ukládat nejrůznější data pro analýzy. Neobsahují omezení velikosti účtů, velikosti souborů ani množství dat, která se dají ukládat do data Lake. Velikost jednotlivých souborů může být v rozsahu od kilobajtů do petabajty. Data se ukládají trvale pomocí několika kopií. Doba, po kterou je možné ukládat data do data Lake, není nijak omezený.

### <a name="performance-tuned-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je sestavená pro spouštění rozsáhlých analytických systémů, které vyžadují velkou propustnost pro dotazování a analýzu velkých objemů dat. Úložiště Data Lake rozděluje části souborů do několika jednotlivých serverů úložiště. Tím se zvyšuje propustnost čtení při paralelním čtení souboru pro provádění analýz dat.

### <a name="enterprise-ready-highly-available-and-secure"></a>Připravené pro podniky: vysoce dostupné a zabezpečené

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost v oboru. Vaše datové prostředky se ukládají odolným způsobem díky vytváření redundantních kopií, které chrání před neočekávaným selháním.

Data Lake Storage Gen1 taky poskytuje zabezpečení uložených dat na podnikové úrovni. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Všechna data

Data Lake Storage Gen1 může ukládat jakákoli data v nativním formátu, aniž by to vyžadovalo předchozí transformaci. Data Lake Storage Gen1 nevyžaduje, aby bylo definováno schéma před načtením dat, takže zůstane v rámci jednotlivého analytického rozhraní pro interpretaci dat a definování schématu v době analýzy. Schopnost ukládat soubory libovolných velikostí a formátů umožňuje Data Lake Storage Gen1 zpracovávat strukturovaná, částečně strukturovaná a nestrukturovaná data.

Data Lake Storage Gen1 kontejnery pro data jsou v podstatě složky a soubory. Pracujete s uloženými daty pomocí sad SDK, Azure Portal a Azure PowerShellu. Pokud zadáte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete uložit jakýkoli typ dat. Data Lake Storage Gen1 neprovádí žádné speciální zpracování dat na základě typu uložených dat.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Zabezpečení dat

Data Lake Storage Gen1 používá k ověřování Azure Active Directory (Azure AD) a seznamy řízení přístupu (ACL) ke správě přístupu k vašim datům.

| Funkce | Popis |
| --- | --- |
| Authentication |Data Lake Storage Gen1 se integruje s Azure AD pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Kvůli integraci Data Lake Storage Gen1 výhody ze všech funkcí služby Azure AD, jako je Multi-Factor Authentication, podmíněný přístup, řízení přístupu na základě role, monitorování využití aplikací, monitorování zabezpečení a upozorňování atd. Data Lake Storage Gen1 podporuje protokol OAuth 2,0 pro ověřování v rámci rozhraní REST. Viz [Data Lake Storage Gen1 ověřování](data-lakes-store-authentication-using-azure-active-directory.md).|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu tím, že podporuje oprávnění ve stylu POSIX vystavené protokolem WebHDFS. Můžete povolit seznamy řízení přístupu (ACL) pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů ACL v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 taky poskytuje šifrování pro data, která jsou uložená v účtu. Nastavení šifrování určíte při vytváření účtu Data Lake Storage Gen1. Můžete se rozhodnout, že vaše data budou zašifrovaná nebo se můžou rozhodnout bez šifrování. Další informace najdete v tématu [šifrování v Data Lake Storage Gen1](data-lake-store-encryption.md). Pokyny, jak poskytnout konfiguraci související s šifrováním, najdete v tématu Začínáme [s Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md). |

Pokyny k zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Kompatibilita aplikací

Data Lake Storage Gen1 je kompatibilní s většinou open source komponent v ekosystému Hadoop. Také se integruje s dalšími službami Azure. Další informace o tom, jak můžete použít Data Lake Storage Gen1 s open source komponentami a dalšími službami Azure, získáte pomocí následujících odkazů:

- Seznam open source aplikací, které spolupracují s Data Lake Storage Gen1, najdete v tématu [aplikace a služby kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) .
- V tématu věnovaném [integraci s dalšími službami Azure](data-lake-store-integrate-with-other-services.md) můžete pochopit, jak používat data Lake Storage Gen1 s dalšími službami Azure k zajištění širší škály scénářů.
- V tématu [scénáře použití Data Lake Storage Gen1](data-lake-store-data-scenarios.md) se naučíte, jak používat data Lake Storage Gen1 ve scénářích, jako je ingestování dat, zpracování dat, stahování dat a vizualizaci dat.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 systému souborů

K Data Lake Storage Gen1 lze získat přístup prostřednictvím AzureDataLakeFilesystem systému souborů (adl://) v prostředích Hadoop (k dispozici v clusteru HDInsight). Aplikace a služby, které používají adl://, mohou využívat další optimalizace výkonu, které nejsou aktuálně k dispozici v WebHDFS. V důsledku toho vám Data Lake Storage Gen1 poskytuje flexibilitu při použití nejlepšího výkonu s doporučenou možností použití adl://nebo udržování stávajícího kódu tím, že budete pokračovat v používání rozhraní WebHDFS API přímo. Azure HDInsight plně využívá AzureDataLakeFilesystem k zajištění nejlepšího výkonu Data Lake Storage Gen1.

K datům v Data Lake Storage Gen1 můžete přistupovat pomocí `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` . Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1, najdete v tématu [zobrazení vlastností uložených dat](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Další kroky

- [Začínáme s Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md)
- [Začínáme s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)