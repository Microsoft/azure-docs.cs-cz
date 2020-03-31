---
title: Co je Azure Data Lake Storage Gen1? | Dokumentace Microsoftu
description: Přehled Data Lake Storage Gen1 (dříve známý jako Azure Data Lake Store) a hodnota, kterou poskytuje přes jiná úložiště dat
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118808"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Co je Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 je celopodnikové úložiště v měřítku velkých objemů dat pro analytické úlohy velkých objemů dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu do jediného místa pro účely provozní a zjišťovací analýzy.

Data Lake Storage Gen1 lze přistupovat z Hadoop (k dispozici v clusteru HDInsight) pomocí rozhraní REST API kompatibilní s WebHDFS. Je navržen tak, aby umožňoval analýzy uložených dat, a je vyladěn pro výkon pro scénáře analýzy dat. Data Lake Storage Gen1 zahrnuje všechny podnikové funkce: zabezpečení, možnosti správy, škálovatelnost, spolehlivost a dostupnost.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Klíčové funkce

Některé z klíčových funkcí Úložiště dat Lake Gen1 patří následující.

### <a name="built-for-hadoop"></a>Sestaveno pro Hadoop

Data Lake Storage Gen1 je souborový systém Apache Hadoop, který je kompatibilní s hadoopským distribuovaným souborovým systémem (HDFS) a pracuje s ekosystémem Hadoop. Vaše stávající aplikace nebo služby HDInsight, které používají rozhraní WebHDFS API, se mohou snadno integrovat s programem Data Lake Storage Gen1. Data Lake Storage Gen1 také zpřístupňuje rozhraní REST kompatibilní s WebHDFS pro aplikace.

Data uložená v gen1 úložiště datového jezera můžete snadno analyzovat pomocí analytických rámců Hadoop, jako je MapReduce nebo Hive. Clustery Azure HDInsight můžete zřídit a nakonfigurovat tak, aby měly přímý přístup k datům uloženým v gen1 úložiště datového jezera.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a může ukládat různá data pro analýzy. Neukládá žádná omezení pro velikosti účtů, velikosti souborů nebo množství dat, která mohou být uložena v datovém jezeře. Jednotlivé soubory mohou být v rozsahu od kilobajtů až po velikost petabajtů. Data jsou uložena trvale vytvořením více kopií. Neexistuje žádné omezení doby, po kterou mohou být data uložena v datovém jezeře.

### <a name="performance-tuned-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je vytvořen pro provoz rozsáhlých analytických systémů, které vyžadují obrovskou propustnost pro dotazování a analýzu velkého množství dat. Úložiště Data Lake rozděluje části souborů do několika jednotlivých serverů úložiště. Tím se zvyšuje propustnost čtení při paralelním čtení souboru pro provádění analýz dat.

### <a name="enterprise-ready-highly-available-and-secure"></a>Připraveno pro podniky: Vysoce dostupné a bezpečné

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Vaše datové prostředky se ukládají odolným způsobem díky vytváření redundantních kopií, které chrání před neočekávaným selháním.

Data Lake Storage Gen1 také poskytuje podnikové zabezpečení pro uložená data. Další informace najdete [v tématu Zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Všechna data

Data Lake Storage Gen1 můžete ukládat všechna data v nativním formátu, bez nutnosti předchozí transformace. Data Lake Storage Gen1 nevyžaduje schéma, které mají být definovány před načtením dat, takže je na jednotlivé analytické rozhraní interpretovat data a definovat schéma v době analýzy. Možnost ukládání souborů libovolných velikostí a formátů umožňuje datovým jezerům Gen1 zpracovávat strukturovaná, částečně strukturovaná a nestrukturovaná data.

Kontejnery Data Lake Storage Gen1 pro data jsou v podstatě složky a soubory. Na uložených datech pracujete pomocí sad SDK, portálu Azure a Azure Powershellu. Pokud vložíte data do úložiště pomocí těchto rozhraní a pomocí příslušných kontejnerů, můžete uložit libovolný typ dat. Data Lake Storage Gen1 neprovádí žádné zvláštní zpracování dat na základě typu dat, které ukládá.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Zabezpečení dat

Data Lake Storage Gen1 používá Azure Active Directory (Azure AD) pro ověřování a seznamy řízení přístupu (ACLs) ke správě přístupu k vašim datům.

| Funkce | Popis |
| --- | --- |
| Ověřování |Data Lake Storage Gen1 se integruje s Azure AD pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Z důvodu integrace, Data Lake Storage Gen1 těží ze všech funkcí Azure AD, jako je vícefaktorové ověřování, podmíněný přístup, řízení přístupu na základě rolí, monitorování využití aplikací, monitorování zabezpečení a výstrahy a tak dále. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování v rámci rozhraní REST. Viz [Ověřování Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu podporou oprávnění ve stylu POSIX vystavených protokolem WebHDFS. Seznamy AC můžete povolit v kořenové složce, v podsložkách a v jednotlivých souborech. Další informace o tom, jak akly fungují v kontextu Data Lake Storage Gen1, naleznete [v tématu Řízení přístupu v souboru Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také poskytuje šifrování pro data uložená v účtu. Nastavení šifrování zadáte při vytváření účtu Data Lake Storage Gen1. Můžete zvolit šifrování dat nebo zvolit žádné šifrování. Další informace naleznete [v tématu Šifrování v úložišti datového jezera Gen1](data-lake-store-encryption.md). Pokyny k poskytování konfigurace související s šifrováním najdete v tématu [Začínáme s datem Úložiště Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md). |

Pokyny k zabezpečení dat v gen1 úložiště datového jezera najdete [v tématu Zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Kompatibilita aplikací

Data Lake Storage Gen1 je kompatibilní s většinou open-source komponent v ekosystému Hadoop. Dobře se také integruje s dalšími službami Azure. Další informace o tom, jak používat Data Lake Storage Gen1 s open source komponentami a dalšími službami Azure, použijte následující odkazy:

- Seznam aplikací [a služeb kompatibilních s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) najdete v tématu Seznam open source aplikací interoperabilních s Date Lake Storage Gen1.
- V části [Integrace s jinými službami Azure,](data-lake-store-integrate-with-other-services.md) kde zjistíte, jak používat Data Lake Storage Gen1 s jinými službami Azure, abyste povolili širší škálu scénářů.
- V [článku Scénáře pro použití Data Lake Storage Gen1](data-lake-store-data-scenarios.md) se dozvíte, jak používat Data Lake Storage Gen1 ve scénářích, jako je ingestování dat, zpracování dat, stahování dat a vizualizace dat.

## <a name="data-lake-storage-gen1-file-system"></a>Datový lake storage gen1 souborový systém

K úložišti datového jezera Gen1 lze přistupovat prostřednictvím souborového systému AzureDataLakeFilesystem (adl://) v prostředích Hadoop (k dispozici v clusteru HDInsight). Aplikace a služby, které používají adl://, mohou využít další optimalizace výkonu, které nejsou aktuálně k dispozici ve službě WebHDFS. V důsledku toho data Lake Storage Gen1 poskytuje flexibilitu buď využít nejlepší výkon s doporučenou možnost používat adl:// nebo udržovat existující kód nadále používat rozhraní API WebHDFS přímo. Azure HDInsight plně využívá AzureDataLakeFilesystem k poskytování nejlepšího výkonu na Data Lake Storage Gen1.

Ke svým datům můžete přistupovat `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`v aplikaci Data Lake Storage Gen1 pomocí aplikace . Další informace o přístupu k datům v gen1 úložiště datového jezera naleznete v [tématu Zobrazení vlastností uložených dat](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Další kroky

- [Začínáme s Date Lake Storage Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md)
- [Začínáme s programem Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)