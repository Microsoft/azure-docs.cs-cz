---
title: Co je Azure Data Lake Storage Gen1? | Dokumenty Microsoft
description: Přehled Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store) a hodnota, kterou nabízí oproti jiným úložištím dat
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885552"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Co je Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 je podnikové úrovni hyperškálovatelné úložiště pro analytické úlohy s velkými objemy dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu do jediného místa pro účely provozní a zjišťovací analýzy.

Data Lake Storage Gen1 je přístupná z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Je navržena tak, aby povolení analýzy uložených dat a je vyladění z hlediska výkonu pro scénáře analýzy dat. Data Lake Storage Gen1 včetně všech schopností na podnikové úrovni: zabezpečení, spravovatelnosti, škálovatelnosti, spolehlivosti a dostupnosti.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Klíčové funkce

Mezi klíčové funkce Gen1 úložiště Data Lake patří následující.

### <a name="built-for-hadoop"></a>Sestaveno pro Hadoop

Gen1 úložiště data Lake je systém souborů Apache Hadoop, který je kompatibilní s HDFS Hadoop Distributed File System () a spolupracuje s ekosystémem Hadoop. Vaše aplikace HDInsight nebo služby, které používají rozhraní API WebHDFS můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také poskytuje rozhraní kompatibilních s WebHDFS REST pro aplikace.

Můžete snadno analyzovat data uložená v Data Lake Storage Gen1 pomocí analytických rámců Hadoop, jako je například MapReduce nebo Hive. Můžete vytvářet clustery Azure HDInsight a nakonfigurujete je, aby přímo přístup k datům uloženým v Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a můžete ukládat různá data pro analýzu. Neuplatňuje žádná omezení velikosti účtů, velikosti souborů nebo množství dat, které mohou být uloženy ve službě data lake. Jednotlivých souborů může pohybovat od kilobajtů až petabajty velikosti. Data se ukládají odolným způsobem díky vytváření více kopií. Neexistuje žádné omezení na doba, po kterou můžete být data uložená ve službě data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat

Gen1 úložiště data Lake je určený pro spouštění rozsáhlých analytických systémů, které vyžadují mimořádně velkou propustnost pro dotazy a analýzu velkých objemů dat. Úložiště Data Lake rozděluje části souborů do několika jednotlivých serverů úložiště. Tím se zvyšuje propustnost čtení při paralelním čtení souboru pro provádění analýz dat.

### <a name="enterprise-ready-highly-available-and-secure"></a>Připraveno pro podniky: Vysoce dostupnou a zabezpečenou

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Vaše datové prostředky se ukládají odolným způsobem díky vytváření redundantních kopií, které chrání před neočekávaným selháním.

Data Lake Storage Gen1 také poskytuje zabezpečení na podnikové úrovni pro uložená data. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Všechna data

Data Lake Storage Gen1 dokáže ukládat libovolná data v nativním formátu bez nutnosti předchozí transformace. Data Lake Storage Gen1 nevyžaduje, aby schéma být definovány před načtením dat, ale ponechává jednotlivých analytické framework interpretoval data a definovat schéma během analýzy. Možnost ukládat soubory libovolných velikostí a formátů umožňuje Gen1 úložiště Data Lake pro zpracování strukturovaných, částečně strukturovaná a Nestrukturovaná data.

Data Lake Storage Gen1 kontejnery na data jsou v podstatě složky a soubory. Pracují s uloženými daty pracujete pomocí sady SDK, webu Azure portal a Azure Powershellu. Když vložíte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete ukládat data libovolného typu. Data Lake Storage Gen1 neprovádí žádným zvláštním způsobem na základě typu dat, která ukládá data.

## <a name="DataLakeStoreSecurity"></a>Zabezpečení dat

Data Lake Storage Gen1 používá Azure Active Directory (Azure AD) pro ověřování a přístup k řízení seznamy ACL můžete spravovat přístup k vašim datům.

| Funkce | Popis |
| --- | --- |
| Authentication |Gen1 úložiště data Lake se integruje s Azure AD pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Díky integraci Data Lake Storage Gen1 výhody všech Azure AD funkce jako je například vícefaktorové ověřování, podmíněného přístupu, řízení přístupu na základě rolí, sledování využití aplikací, monitorování zabezpečení a upozorňování a tak dále. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování v rámci rozhraní REST. Zobrazit [ověřování Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu díky podpoře oprávnění ve stylu POSIX vystavený protokolem WebHDFS. Můžete povolit seznamy řízení přístupu u kořenové složky, podsložek a jednotlivých souborů. Další informace o fungování seznamů řízení přístupu v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také zajišťuje šifrování dat uložených v účtu. Nastavení šifrování se zadává při vytváření účtu Data Lake Storage Gen1. Můžete se vaše data šifrovala, nebo zvolit možnost bez šifrování. Další informace najdete v tématu [šifrování ve službě Data Lake Storage Gen1](data-lake-store-encryption.md). Pokyny pro poskytnutí konfigurace související se šifrováním najdete v tématu [Začínáme s Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md). |

Pokyny k zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Kompatibilita aplikací

Data Lake Storage Gen1 je kompatibilní s Většina komponent open source v ekosystému Hadoop. Taky se integruje s dalšími službami Azure. Další informace o použití Data Lake Storage Gen1 s open source komponenty a další služby Azure, použijte následující odkazy:

- Zobrazit [aplikace a služby kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) seznam spolupracují se službou Data Lake Storage Gen1 open-source aplikací.
- Zobrazit [integrací s ostatními službami Azure](data-lake-store-integrate-with-other-services.md) pochopit, jak používat Data Lake Storage Gen1 s ostatními službami Azure umožňující širší škálu scénářů.
- Zobrazit [scénáře použití Data Lake Storage Gen1](data-lake-store-data-scenarios.md) se naučíte používat Data Lake Storage Gen1 ve scénářích, jako je příjem dat, zpracování dat, stahování dat a vizualizace dat.

## <a name="data-lake-storage-gen1-file-system"></a>Systém souborů data Lake Storage Gen1

Data Lake Storage Gen1 je přístupná prostřednictvím systému souborů, AzureDataLakeFilesystem (adl: / /) v prostředích Hadoop (dostupné s clusterem HDInsight). Aplikace a služby, které používají adl: / / můžete využít další optimalizace výkonu, které nejsou ve WebHDFS aktuálně dostupná. V důsledku toho Gen1 úložiště Data Lake poskytuje flexibilitu při buď provedete pomocí nejlepší výkon doporučená možnost použití adl: / / nebo zachovat stávající kód a dál přímo používat rozhraní API WebHDFS. Azure HDInsight plně využívá systém AzureDataLakeFilesystem k zajištění nejlepšího výkonu v Data Lake Storage Gen1.

Můžete přistupovat k datům v Data Lake Storage Gen1 pomocí `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1 najdete v tématu [zobrazení vlastností uložených dat](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Další postup

- [Začínáme s Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md)
- [Začínáme s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)