---
title: Přehled Azure Data Lake Storage Gen1 v HDInsight
description: Přehled Data Lake Storage Gen1 v HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938885"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Přehled Azure Data Lake Storage Gen1 v HDInsight

Azure Data Lake Storage Gen1 je škálovatelné úložiště s velkými objemy dat v podnikové síti pro analytické úlohy s velkým objemem dat. Pomocí Azure Data Lake můžete zachytit data libovolné velikosti, typu a rychlosti přijímání. A na jednom místě pro provozní a průzkumné analýzy.

Přístup k Data Lake Storage Gen1 z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilního s WebHDFS. Data Lake Storage Gen1 je navržená tak, aby povolovala analýzy uložených dat a vyladěna se na výkon ve scénářích analýzy dat. Gen1 zahrnuje možnosti, které jsou nezbytné pro reálné podnikové případy použití. Mezi tyto možnosti patří zabezpečení, možnosti správy, přizpůsobivost, spolehlivost a dostupnost.

Další informace o Azure Data Lake Storage Gen1 najdete v podrobném [přehledu Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Mezi klíčové funkce Data Lake Storage Gen1 patří následující.

## <a name="compatibility-with-hadoop"></a>Kompatibilita se systémem Hadoop

Data Lake Storage Gen1 je Apache Hadoop systém souborů, který je kompatibilní s prostředím HDFS a Hadoop.  Aplikace nebo služby HDInsight, které používají rozhraní WebHDFS API, se můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také zpřístupňuje rozhraní REST kompatibilní s WebHDFS pro aplikace.

Data uložená v Data Lake Storage Gen1 lze snadno analyzovat pomocí analytických rozhraní Hadoop. Architektury, jako je MapReduce nebo podregistr. Clustery Azure HDInsight je možné zřídit a nakonfigurovat tak, aby přímý přístup k datům uloženým v Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodný pro ukládání různých druhů dat pro analýzy. Neomezuje limity velikosti účtů ani velikosti souborů. Nebo množství dat, které lze uložit do data Lake. Velikost jednotlivých souborů je v rozsahu od kilobajtů až po petabajty, což Data Lake Storage Gen1 skvělou volbou pro ukládání libovolného typu dat. Data se ukládají trvale pomocí několika kopií. A neexistují žádná omezení, jak dlouho je možné data ukládat do data Lake.

## <a name="performance-tuning-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je navržena pro analytické systémy. Systémy, které vyžadují obrovské propustnost pro dotazování a analýzu velkých objemů dat. Data Lake rozšíří části souboru na několik jednotlivých úložných serverů. Při analýze dat tato nastavení zvyšují propustnost čtení, pokud je soubor paralelně čten.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Připravenost pro podniky: vysoce dostupné a zabezpečené

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost v oboru. Datové assety jsou uložené trvale: redundantní kopie chrání před neočekávanými chybami. Podniky můžou ve svých řešeních používat Data Lake Storage Gen1 jako důležitou součást své stávající datové platformy.

Data Lake Storage Gen1 taky poskytuje zabezpečení uložených dat na podnikové úrovni. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Flexibilní datové struktury

Data Lake Storage Gen1 může ukládat jakákoli data v nativním formátu, jak je, a to bez nutnosti předchozí transformace. Data Lake Storage Gen1 nevyžaduje, aby bylo definováno schéma před načtením dat. Jednotlivé analytické rozhraní interpretuje data a definuje schéma v době analýzy. Data Lake Storage Gen1 může zpracovávat strukturovaná data. A částečně strukturovaných a nestrukturovaná data.

Data Lake Storage Gen1 kontejnery pro data jsou v podstatě složky a soubory. Pomocí sad SDK, Azure Portal a Azure PowerShell pracujete s uloženými daty. Data vložená do úložiště pomocí těchto rozhraní a kontejnerů můžou ukládat libovolný datový typ. Data Lake Storage Gen1 neprovádí žádné speciální zpracování dat na základě typu dat.

## <a name="data-security-in-data-lake-storage-gen1"></a>Zabezpečení dat v Data Lake Storage Gen1

Data Lake Storage Gen1 používá pro ověřování Azure Active Directory a ke správě přístupu k datům používá seznamy řízení přístupu (ACL).

| **Funkce** | **Popis** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 se integruje s Azure Active Directory (Azure AD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Kvůli integraci Data Lake Storage Gen1 výhody ze všech funkcí služby Azure AD. Mezi tyto funkce patří: vícefaktorového ověřování, podmíněný přístup a řízení přístupu na základě role v Azure. Také monitorování využití aplikací, monitorování zabezpečení a výstrahy a tak dále. Data Lake Storage Gen1 podporuje protokol OAuth 2,0 pro ověřování v rámci rozhraní REST. Viz [ověřování v rámci Azure Data Lake Storage Gen1 pomocí Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu podporou oprávnění ve stylu POSIX, která jsou vystavená protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů ACL v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 taky poskytuje šifrování pro data, která jsou uložená v účtu. Nastavení šifrování určíte při vytváření účtu Data Lake Storage Gen1. Můžete se rozhodnout, že vaše data budou zašifrovaná nebo se můžou rozhodnout bez šifrování. Další informace najdete v tématu [šifrování v Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Pokyny k poskytnutí konfigurace související s šifrováním najdete v tématu Začínáme [s Azure Data Lake Storage Gen1 pomocí Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Další informace o zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat uložených v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikace, které jsou kompatibilní s Data Lake Storage Gen1

Data Lake Storage Gen1 je kompatibilní s většinou open source komponent v prostředí Hadoop. Výborně se taky integruje s jinými službami Azure.  Pomocí odkazů níže můžete získat další informace o tom, jak Data Lake Storage Gen1 lze používat s komponentami open source a dalšími službami Azure.

* Viz [Open Source aplikace pro velké objemy dat, které fungují s Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Další informace o tom, jak používat Data Lake Storage Gen1 s ostatními službami Azure k zajištění širší škály scénářů, najdete v tématu věnovaném [integraci Azure Data Lake Storage Gen1 s dalšími službami Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) .
* [Požadavky na velké](../data-lake-store/data-lake-store-data-scenarios.md)objemy dat najdete v tématu použití Azure Data Lake Storage Gen1.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Systém souborů Data Lake Storage Gen1 (adl://)

V prostředích Hadoop máte přístup k Data Lake Storage Gen1 pomocí nového systému souborů AzureDataLakeFilesystem (adl://). Výkon aplikací a služeb, které se používají, `adl://` se dají optimalizovat způsobem, který v WebHDFS aktuálně není dostupný. V důsledku toho získáte flexibilitu, abyste mohli využít nejlepší výkon pomocí doporučených adl://. Nebo můžete zachovat existující kód tím, že budete nadále používat rozhraní API pro WebHDFS přímo. Azure HDInsight plně využívá výhod AzureDataLakeFilesystem k zajištění co nejlepšího výkonu pro Data Lake Storage Gen1.

K datům v Data Lake Storage Gen1 přistupovat pomocí následujícího identifikátoru URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1, najdete v tématu [akce, které jsou k dispozici u uložených dat](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
* [Přehled služby Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)