---
title: Přehled Azure Data Lake Storage Gen1 | Microsoft Docs
description: Pochopit, jaké Gen1 úložiště Data Lake je (dříve označované jako Azure Data Lake Store) a hodnota, kterou poskytuje oproti jiným úložištím dat
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 4dff8f4ff9fc324d48391c0399677b64824493c6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035806"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Přehled Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Store je celopodnikové, flexibilně škálovatelné úložiště pro analytické úlohy s velkými objemy dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu do jediného místa pro účely provozní a zjišťovací analýzy.

> [!TIP]
> Použijte [výukový postup Data Lake Store](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) a začněte se seznamovat se službou Azure Data Lake Store.
> 
> 

Služba Azure Data Lake Store je přístupná z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Je speciálně navržená pro analýzy uložených dat a je optimalizovaná tak, aby zajistila maximální výkon pro scénáře analýzy dat. Ihned poskytuje veškeré funkce na podnikové úrovni – zabezpečení, správu, škálovatelnost, spolehlivost a dostupnost – které jsou v podmínkách reálného podnikového použití nezbytné.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Mezi klíčové funkce Azure Data Lake patří následující.

### <a name="built-for-hadoop"></a>Sestaveno pro Hadoop
Úložiště Azure Data Lake Store je systém souborů Apache Hadoop, který je kompatibilní se systémem Hadoop Distributed File System (HDFS) a pracuje s ekosystémem Hadoop.  Stávající aplikace a služby HDInsight, které používají rozhraní API WebHDFS, se můžou snadno integrovat s Data Lake Store. Data Lake Store taky aplikacím zpřístupňuje rozhraní REST kompatibilní s WebHDFS.

Data uložená v Data Lake Store se dají snadno analyzovat pomocí analytických rámců Hadoop, jako je například MapReduce nebo Hive. Clustery Microsoft Azure HDInsight se dají zřídit a nakonfigurovat tak, aby měly přímý přístup k datům uloženým v Data Lake Store.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory
Služba Azure Data Lake Store poskytuje neomezené úložiště a je vhodná pro ukládání nejrůznějších dat k analýze. Neuplatňuje žádná omezení, pokud jde o velikosti účtů, velikosti souborů nebo množství dat, které se dá uložit do úložiště Data Lake. Velikost jednotlivých souborů se může pohybovat od kilobajtů až po petabajty, a díky tomu je služba skvělou volbou pro ukládání libovolného typu dat. Data se ukládají spolehlivě, protože se vytváří víc kopií a není omezená doba, po kterou můžou být data uložená v úložišti Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat
Služba Azure Data Lake Store je sestavena pro spouštění rozsáhlých analytických systémů, které při dotazování a analýze velkých objemů dat vyžadují obrovskou propustnost. Úložiště Data Lake rozděluje části souborů do několika jednotlivých serverů úložiště. Tím se zvyšuje propustnost čtení při paralelním čtení souboru pro provádění analýz dat.

### <a name="enterprise-ready-highly-available-and-secure"></a>Připraveno pro podniky: Vysoká dostupnost a zabezpečení
Azure Data Lake Store poskytuje dostupnost a spolehlivost odpovídající standardům odvětví. Vaše datové prostředky se ukládají odolným způsobem díky vytváření redundantních kopií, které chrání před neočekávaným selháním. Podniky můžou v rámci svých řešení využít Azure Data Lake jako důležitou součást svojí stávající datové platformy.

Data Lake Store taky zajišťuje zabezpečení uložených dat na podnikové úrovni. Další informace najdete v tématu [Zabezpečení dat v Azure Data Lake Store](#DataLakeStoreSecurity).

### <a name="all-data"></a>Všechna data
Služba Azure Data Lake Store dokáže ukládat libovolná data v nativním formátu tak, jak jsou, bez nutnosti předchozí transformace. Data Lake Store nevyžaduje, aby bylo před nahráním dat definované schéma, ale ponechává na konkrétním analytickém rámci, aby při analýze interpretoval data a definoval rámec. Díky schopnosti ukládat soubory libovolných velikostí a formátů dokáže služba Data Lake Store zpracovávat strukturovaná, částečně strukturovaná i nestrukturovaná data.

Kontejnery na data Azure Data Lake Store jsou v podstatě složky a soubory. S uloženými daty pracujete pomocí sady SDK, webu Azure Portal a prostředí Azure Powershell. Pokud ukládáte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete ukládat jakýkoli typ dat. Služba Data Lake Store nezpracovává uložená data žádným zvláštním způsobem, který by závisel na jejich typu.

## <a name="DataLakeStoreSecurity"></a>Zabezpečení dat v Azure Data Lake Storu
Azure Data Lake Store využívá k ověřování službu Azure Active Directory a spravuje přístup k datům pomocí seznamů řízení přístupu (ACL).

| Funkce | Popis |
| --- | --- |
| Authentication |Služba Azure Data Lake Store se integruje se službou Azure Active Directory (AAD) v oblasti správy identit a přístupu veškerých dat uložených v Azure Data Lake Store. Díky této integraci získává služba Azure Data Lake všechny funkce AAD, včetně vícefaktorového ověřování, podmíněného přístupu, řízení přístupu na základě role, sledování využití aplikací, sledování a výstrah zabezpečení atd. Azure Data Lake Store podporuje protokol OAuth 2.0 pro ověřování v rozhraní REST. Viz [Ověřování pomocí služby Data Lake Store](data-lakes-store-authentication-using-azure-active-directory.md).|
| Řízení přístupu |Azure Data Lake Store zajišťuje řízení přístupu tím, že podporuje oprávnění ve stylu POSIX zpřístupněná protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů řízení přístupu v souvislosti s Data Lake Storem najdete v tématu [Řízení přístupu v Data Lake Storu](data-lake-store-access-control.md). |
| Šifrování |Data Lake Store také zajišťuje šifrování dat, která jsou uložená v účtu. Nastavení šifrování se zadává při vytváření účtu Data Lake Storu. Můžete zvolit, aby se vaše data šifrovala, nebo zvolit možnost bez šifrování. Další informace najdete v tématu [Šifrování ve službě Data Lake Store](data-lake-store-encryption.md). Pokyny k provedení konfigurace související se šifrováním najdete v tématu [Začínáme s Azure Data Lake Store pomocí webu Azure Portal](data-lake-store-get-started-portal.md). |

Chcete se dozvědět víc o zabezpečení dat v Data Lake Store? Použijte následující odkazy.

* Pokyny týkající se zabezpečení dat v Data Lake Store najdete v tématu [Zabezpečení dat v Azure Data Lake Store](data-lake-store-secure-data.md).
* Dáváte přednost videu? [Podívejte se na toto video](https://mix.office.com/watch/1q2mgzh9nn5lx), které se týká postupu zabezpečení dat uložených v Data Lake Store.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Aplikace kompatibilní s Azure Data Lake Store
Azure Data Lake Store je kompatibilní s většinou součástí typu Open Source v ekosystému Hadoop. Výborně se taky integruje s jinými službami Azure. Díky tomu je služba Data Lake Store ideální volbou pro ukládání vašich dat. Další informace o tom, jak používat službu Data Lake Store se součástmi typu Open Source a jinými službami Azure, najdete na následujících odkazech.

* Seznam aplikací typu Open Source, které spolupracují se službou Azure Data Lake Store, najdete v tématu [Aplikace a služby kompatibilní s Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md).
* Pokud se chcete seznámit s tím, jak používat Data Lake Store s jinými službami Azure a rozšířit škálu možných scénářů, přejděte k tématu [Integrace s jinými službami Azure](data-lake-store-integrate-with-other-services.md).
* Pokud se chcete naučit službu Data Lake Store používat ve scénářích, jako je příjem dat, zpracování dat, stahování dat nebo vizualizace dat, přejděte k tématu [Scénáře použití Data Lake Store](data-lake-store-data-scenarios.md).

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Co je systém souborů Azure Data Lake Store (adl://)?
Služba Data Lake Store umožňuje přístup prostřednictvím nového systému souborů, AzureDataLakeFilesystem (adl://), a to v prostředích Hadoop (dostupné s clusterem HDInsight). Aplikace a služby, které používají adl://, můžou těžit z další optimalizace výkonu, která není ve WebHDFS aktuálně dostupná. Služba Data Lake Store vám díky tomu dává možnost buď využít nejlepší výkon, pokud se rozhodnete používat doporučený systém adl://, nebo zachovat stávající kód a dál přímo používat rozhraní API WebHDFS. Azure HDInsight plně využívá systém AzureDataLakeFilesystem k zajištění maximálního výkonu služby Data Lake Store.

K datům v Data Lake Store můžete přistupovat pomocí `adl://<data_lake_store_name>.azuredatalakestore.net`. Další informace o přístupu k datům v Data Lake Store najdete v tématu [Zobrazení vlastností uložených dat](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Další postup

* [Začínáme s Data Lake Store pomocí portálu Azure](data-lake-store-get-started-portal.md)
* [Začínáme s Azure Data Lake Storem pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)