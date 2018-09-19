---
title: Přehled služby Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Pochopit, jaké Gen1 úložiště Data Lake je (dříve označované jako Azure Data Lake Store) a hodnota, kterou nabízí oproti jiným úložištím dat
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: b733a0bec20c7abae5df41acd74284bdf75bca2c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124129"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Přehled služby Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 je podnikové úrovni hyperškálovatelné úložiště pro analytické úlohy s velkými objemy dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu do jediného místa pro účely provozní a zjišťovací analýzy.

> [!TIP]
> Použití [postup výuky pro Data Lake Storage Gen1](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) a začněte se seznamovat služby Data Lake Storage Gen1.
> 
> 

Data Lake Storage Gen1 je přístupná z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Je speciálně navržená pro analýzy uložených dat a je optimalizovaná tak, aby zajistila maximální výkon pro scénáře analýzy dat. Ihned poskytuje veškeré funkce na podnikové úrovni – zabezpečení, správu, škálovatelnost, spolehlivost a dostupnost – které jsou v podmínkách reálného podnikového použití nezbytné.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Mezi klíčové funkce Gen1 úložiště Data Lake patří následující.

### <a name="built-for-hadoop"></a>Sestaveno pro Hadoop
Gen1 úložiště data Lake je systém souborů Apache Hadoop kompatibilní s Hadoop Distributed File System (HDFS) a pracuje s ekosystémem Hadoop.  Vaše aplikace HDInsight nebo služby, které používají rozhraní API WebHDFS můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také poskytuje rozhraní kompatibilních s WebHDFS REST pro aplikace

Data uložená v Data Lake Storage Gen1 se dají snadno analyzovat pomocí analytických rámců Hadoop, jako je například MapReduce nebo Hive. Clustery Microsoft Azure HDInsight můžete zřizovat a konfigurovat přímo přístup k datům uloženým v Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory
Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodná pro ukládání nejrůznějších dat k analýze. Neuplatňuje žádná omezení, pokud jde o velikosti účtů, velikosti souborů nebo množství dat, které se dá uložit do úložiště Data Lake. Velikost jednotlivých souborů se může pohybovat od kilobajtů až po petabajty, a díky tomu je služba skvělou volbou pro ukládání libovolného typu dat. Data se ukládají spolehlivě, protože se vytváří víc kopií a není omezená doba, po kterou můžou být data uložená v úložišti Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat
Data Lake Storage Gen1 je sestavena pro spouštění rozsáhlých analytických systémů, které vyžadují mimořádně velkou propustnost pro dotazy a analýzu velkých objemů dat. Úložiště Data Lake rozděluje části souborů do několika jednotlivých serverů úložiště. Tím se zvyšuje propustnost čtení při paralelním čtení souboru pro provádění analýz dat.

### <a name="enterprise-ready-highly-available-and-secure"></a>Připraveno pro podniky: Vysoká dostupnost a zabezpečení
Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Vaše datové prostředky se ukládají odolným způsobem díky vytváření redundantních kopií, které chrání před neočekávaným selháním. Podniky můžete v rámci svých řešení použít Gen1 úložiště Data Lake jako důležitou součást svojí stávající datové platformy.

Data Lake Storage Gen1 také poskytuje zabezpečení na podnikové úrovni pro uložená data. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Všechna data
Data Lake Storage Gen1 dokáže ukládat libovolná data v nativním formátu tak, jak jsou, bez nutnosti předchozí transformace. Data Lake Storage Gen1 nevyžaduje, aby schéma být definovány před načtením dat, ale ponechává jednotlivých analytické framework interpretoval data a definovat schéma během analýzy. Schopnost ukládat soubory libovolných velikostí a formátů umožňuje Gen1 úložiště Data Lake pro zpracování strukturovaných, částečně strukturovaná a Nestrukturovaná data.

Data Lake Storage Gen1 kontejnery na data jsou v podstatě složky a soubory. S uloženými daty pracujete pomocí sady SDK, webu Azure Portal a prostředí Azure Powershell. Pokud ukládáte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete ukládat jakýkoli typ dat. Data Lake Storage Gen1 neprovádí žádným zvláštním způsobem na základě typu dat, která ukládá data.

## <a name="DataLakeStoreSecurity"></a>Zabezpečení dat v Data Lake Storage Gen1
Data Lake Storage Gen1 používá Azure Active Directory pro ověřování a přístup k řízení seznamy ACL můžete spravovat přístup k vašim datům.

| Funkce | Popis |
| --- | --- |
| Authentication |Gen1 úložiště data Lake se integruje s Azure Active Directory (AAD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. V důsledku integrace výhody Gen1 úložiště Data Lake všechny funkce AAD, včetně služby Multi-Factor authentication, podmíněného přístupu, řízení přístupu na základě rolí, sledování využití aplikací, sledování a výstrah zabezpečení atd. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování pomocí rozhraní REST. Zobrazit [ověřování Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu díky podpoře oprávnění ve stylu POSIX vystavený protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů řízení přístupu v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také zajišťuje šifrování dat uložených v účtu. Nastavení šifrování se zadává při vytváření účtu Data Lake Storage Gen1. Můžete zvolit, aby se vaše data šifrovala, nebo zvolit možnost bez šifrování. Další informace najdete v tématu [šifrování ve službě Data Lake Storage Gen1](data-lake-store-encryption.md). Pokyny pro poskytnutí konfigurace související se šifrováním najdete v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-get-started-portal.md). |

Chcete se dozvědět víc o zabezpečení dat v Data Lake Storage Gen1? Použijte následující odkazy.

* Pokyny k zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).
* Dáváte přednost videu? [V tomto videu](https://mix.office.com/watch/1q2mgzh9nn5lx) o tom, jak zabezpečit dat uložených v Data Lake Storage Gen1.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplikace kompatibilní s Data Lake Storage Gen1
Data Lake Storage Gen1 je kompatibilní s nejvíce open source komponenty v ekosystému Hadoop. Výborně se taky integruje s jinými službami Azure. Díky tomu je Data Lake Storage Gen1 ideální volbou pro ukládání vašich dat. Postupujte podle níže odkazy, které další informace o tom, jak Gen1 úložiště Data Lake můžete použít i pomocí open source komponenty, jakož i dalšími službami Azure.

* Zobrazit [aplikace a služby kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) seznam aplikací typu open source spolupracují se službou Data Lake Storage Gen1.
* Zobrazit [integrací s ostatními službami Azure](data-lake-store-integrate-with-other-services.md) pochopit, jak Gen1 úložiště Data Lake je možné s ostatními službami Azure umožňující širší škálu scénářů.
* Zobrazit [scénáře použití Data Lake Storage Gen1](data-lake-store-data-scenarios.md) se naučíte používat Data Lake Storage Gen1 ve scénářích, jako je příjem dat, zpracování dat, stahování dat a vizualizace dat.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Co je systém souborů Data Lake Storage Gen1 (adl: / /)?
Data Lake Storage Gen1 je přístupná prostřednictvím nového systému souborů, AzureDataLakeFilesystem (adl: / /), v prostředích Hadoop (dostupné s clusterem HDInsight). Aplikace a služby, které používají adl://, můžou těžit z další optimalizace výkonu, která není ve WebHDFS aktuálně dostupná. V důsledku toho Gen1 úložiště Data Lake poskytuje flexibilní buď využít nejlepší výkon s parametrem doporučené použití adl: / / nebo zachovat stávající kód a dál přímo používat rozhraní API WebHDFS. Azure HDInsight plně využívá systém AzureDataLakeFilesystem k zajištění nejlepšího výkonu v Data Lake Storage Gen1.

Můžete přistupovat k datům v Data Lake Storage Gen1 pomocí `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1 najdete v tématu [zobrazení vlastností uložených dat](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Další postup

* [Začínáme se službou Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-get-started-portal.md)
* [Začínáme s Azure Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)