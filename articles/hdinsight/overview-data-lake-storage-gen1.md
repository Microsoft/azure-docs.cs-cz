---
title: Přehled úložiště datového jezera Azure v HDInsightu
description: Přehled data lake storage gen1 v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 15d7f0621ffbf883d267d389fb634a13aa927430
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873352"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Přehled úložiště datového jezera Azure v HDInsightu

Azure Data Lake Storage Gen1 je celopodnikové úložiště hyperškálování pro analytické úlohy velkých objemů dat. Pomocí Azure Data Lake můžete zachytit data libovolné velikosti, typu a rychlosti ingestování. A na jednom místě pro provozní a průzkumnou analýzu.

Přístup k datům Lake Storage Gen1 z Hadoopu (k dispozici v clusteru HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Data Lake Storage Gen1 je navržen tak, aby umožnil analýzu uložených dat a je vyladěn pro výkon ve scénářích analýzy dat. Gen1 obsahuje funkce, které jsou nezbytné pro případy použití v reálném světě. Mezi tyto funkce patří zabezpečení, možnosti správy, přizpůsobivost, spolehlivost a dostupnost.

Další informace o Azure Data Lake Storage Gen1 najdete v podrobném [přehledu Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Mezi klíčové funkce data Lake Storage Gen1 patří následující.

## <a name="compatibility-with-hadoop"></a>Kompatibilita s Hadoopem

Data Lake Storage Gen1 je souborový systém Apache Hadoop kompatibilní s hdfs a hadoopským prostředím.  Aplikace nebo služby HDInsight, které používají rozhraní WEBHDFS API, se mohou snadno integrovat s programem Data Lake Storage Gen1. Data Lake Storage Gen1 také zpřístupňuje rozhraní REST kompatibilní s WebHDFS pro aplikace.

Data uložená v data Lake Storage Gen1 lze snadno analyzovat pomocí analytických rámců Hadoop. Architektury jako MapReduce nebo Hive. Clustery Azure HDInsight můžou být zřízená a nakonfigurovaná tak, aby měla přímý přístup k datům uloženým v Gen1 úložiště datového jezera.

## <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodný pro ukládání různých druhů dat pro analýzu. Neukládá omezení pro velikosti účtů ani velikosti souborů. Nebo množství dat, která mohou být uložena v datovém jezeře. Jednotlivé soubory se pohybují ve velikosti od kilobajtů až po petabajty, takže Data Lake Storage Gen1 je skvělou volbou pro ukládání jakéhokoli typu dat. Data jsou uložena trvale vytvořením více kopií. A neexistují žádná omezení, jak dlouho mohou být data uložena v datovém jezeře.

## <a name="performance-tuning-for-big-data-analytics"></a>Ladění výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je určen pro analytické systémy. Systémy, které vyžadují obrovskou propustnost pro dotazování a analýzu velkého množství dat. Datové jezero šíří části souboru přes několik jednotlivých serverů úložiště. Při analýze dat toto nastavení zlepšuje propustnost čtení při paralelním čtení souboru.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Připravenost pro podniky: Vysoce dostupná a bezpečná

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Datové prostředky jsou uloženy trvale: redundantní kopie chrání před neočekávanými chybami. Podniky mohou používat Data Lake Storage Gen1 ve svých řešeních jako důležitou součást své stávající datové platformy.

Data Lake Storage Gen1 také poskytuje podnikové zabezpečení pro uložená data. Další informace najdete [v tématu Zabezpečení dat v Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Flexibilní datové struktury

Data Lake Storage Gen1 můžete ukládat všechna data v jeho nativním formátu, jak je, bez nutnosti předchozí transformace. Úložiště datového jezera Gen1 nevyžaduje, aby bylo před načtením dat definováno schéma. Individuální analytický rámec interpretuje data a definuje schéma v době analýzy. Data Lake Storage Gen1 může zpracovávat strukturovaná data. A polostrukturovaná a nestrukturovaná data.

Kontejnery Data Lake Storage Gen1 pro data jsou v podstatě složky a soubory. Na uložených datech pracujete pomocí sad SDK, portálu Azure a Azure PowerShellu. Data vložena do úložiště s těmito rozhraními a kontejnery, můžete uložit libovolný datový typ. Data Lake Storage Gen1 neumožňuje žádné zvláštní zpracování dat na základě typu dat.

## <a name="data-security-in-data-lake-storage-gen1"></a>Zabezpečení dat v úložišti datového jezera Gen1

Data Lake Storage Gen1 používá Azure Active Directory pro ověřování a používá seznamy řízení přístupu (ACLs) ke správě přístupu k vašim datům.

| **Funkce** | **Popis** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 se integruje s Azure Active Directory (Azure AD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Díky integraci využívá Data Lake Storage Gen1 všechny funkce Azure AD. Mezi tyto funkce patří: vícefaktorové ověřování, podmíněný přístup a řízení přístupu na základě rolí. Také monitorování využití aplikací, monitorování zabezpečení a upozorňování a tak dále. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování v rámci rozhraní REST. Viz [Ověřování v rámci Azure Data Lake Storage Gen1 pomocí Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu podporou oprávnění stylu POSIX, která jsou vystavena protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o tom, jak akly fungují v kontextu Data Lake Storage Gen1, naleznete [v tématu Řízení přístupu v souboru Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také poskytuje šifrování pro data, která jsou uložena v účtu. Nastavení šifrování zadáte při vytváření účtu Data Lake Storage Gen1. Můžete zvolit šifrování dat nebo zvolit žádné šifrování. Další informace naleznete [v tématu Šifrování v úložišti datového jezera Gen1](../data-lake-store/data-lake-store-encryption.md). Pokyny, jak poskytnout konfiguraci související s šifrováním, najdete v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Další informace o zabezpečení dat v gen1 úložiště datových jezer, najdete [v tématu Zabezpečení dat uložených v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikace, které jsou kompatibilní s Date Lake Storage Gen1

Data Lake Storage Gen1 je kompatibilní s většinou open source komponent v prostředí Hadoop. Výborně se taky integruje s jinými službami Azure.  Další informace o tom, jak lze gen1 úložiště datového jezera gen1 používat jak s open source komponentami, tak s dalšími službami Azure.

* Viz [Open source aplikace pro velké objemy dat, které fungují s Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* V [tématu Integrace Azure Data Lake Storage Gen1 s jinými službami Azure,](../data-lake-store/data-lake-store-integrate-with-other-services.md) kde zjistíte, jak používat Data Lake Storage Gen1 s jinými službami Azure, abyste umožnili širší škálu scénářů.
* Požadavky na velké objemy dat najdete v tématu [Používání Azure Data Lake Storage Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Datový lake storage Gen1 souborový systém (adl://)

V prostředích Hadoop, můžete přistupovat Data Lake Storage Gen1 prostřednictvím nového systému souborů, AzureDataLakeFilesystem (adl://). Výkon aplikací a služeb, `adl://` které používají, lze optimalizovat způsobem, který není aktuálně k dispozici v systému WebHDFS. V důsledku toho získáte flexibilitu buď využít nejlepší výkon pomocí doporučené adl://. Nebo udržovat existující kód tím, že bude pokračovat v používání rozhraní WebHDFS API přímo. Azure HDInsight plně využívá azuredatalakefilesystém poskytovat nejlepší výkon na datového jezera Storage Gen1.

Přístup k datům v gen1 úložiště datového jezera pomocí následujícího identifikátoru URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Další informace o tom, jak získat přístup k datům v gen1 úložiště datového jezera, naleznete v [tématu Akce dostupné na uložených datech](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Další kroky

* [Úvod do úložiště datových jezer Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
* [Přehled úložiště datového jezera Azure 2](./overview-data-lake-storage-gen2.md)