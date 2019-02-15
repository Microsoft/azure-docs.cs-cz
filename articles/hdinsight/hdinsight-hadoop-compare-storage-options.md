---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled o typech úložiště a jak fungují s Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 91b6808e5f74d82a980dc633b2fa2bb0fe6752f1
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301345"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Uživatelé Microsoft Azure HDInsight můžete vybrat z několika možností jiného úložiště při vytváření clusterů HDInsight:

* Azure Data Lake Storage Gen2
* Azure Storage
* Azure Data Lake Storage Gen1

Tento článek poskytuje přehled o těchto typech úložiště a jejich jedinečné funkce.

## <a name="azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure Data Lake Storage Gen2 s Apache Hadoop v Azure HDInsight

Azure Data Lake Storage Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 a integruje do úložiště objektů Blob v Azure. Tyto funkce patří systém souborů, který je kompatibilní se systémem Hadoop, Azure Active Directory (Azure AD), a seznamů řízení přístupu na základě POSIX (ACL). Tato kombinace umožňuje využít výkon Azure Data Lake Storage Gen1 a zároveň využívat Správa životního cyklu vrstvy a dat Blob Storage.

Další informace o Azure Data Lake Storage Gen2 najdete v tématu [Úvod do služby Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

* **Přístup, který je kompatibilní se systémem Hadoop:** V Azure Data Lake Storage Gen2 můžete spravovat a přistupovat k datům, stejně jako byste to udělali s souboru systému HDFS (Hadoop Distributed). Ovladač Azure Blob File System (ABFS) je k dispozici ve všech prostředích Apache Hadoop, včetně Azure HDInsight a Azure Databricks. Použijte ABFS přístup k datům uloženým v Data Lake Storage Gen2.

* **Nadmnožina POSIX oprávnění:** Model zabezpečení pro Data Lake Gen2 podporuje oprávnění řízení přístupu a POSIX spolu s nějaké další specifické pro Data Lake Storage Gen2 členitosti. Nastavení je možné nakonfigurovat pomocí nástroje pro správu nebo architektur, jako je Apache Hivu a Apache Sparku.

* **Finanční efektivita:** Data Lake Storage Gen2 nabízí kapacitu úložiště s nízkými náklady a transakce. Funkce, jako je životní cyklus úložiště objektů Blob v Azure pomůže snížit náklady na úpravou fakturační sazby při přesunu dat prostřednictvím jejich životního cyklu.

* **Kompatibilita s Blob storage nástroje, platformy a aplikace:** Data Lake Storage Gen2 se nadále využívat širokou škálu nástroje, platformy a aplikace pro úložiště objektů Blob.

* **Optimalizované ovladače:** Ovladač ABFS je speciálně pro analýzy velkých objemů dat optimalizovaná. Zobrazují se prostřednictvím koncového bodu systému souborů DFS souborů DFS, odpovídající rozhraní REST API dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co je nového v Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro přístup k souborům zabezpečení

Azure HDInsight pomocí spravované identity zabezpečení clusteru přístup k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkcí služby Azure Active Directory, která poskytuje služby Azure se sadou automaticky spravované přihlašovací údaje. Tyto přihlašovací údaje slouží k ověření na libovolnou službu, která podporuje ověřování služby Active Directory. Použití spravované identity nevyžaduje k uložení přihlašovacích údajů v kódu nebo konfiguračního souboru.

Další informace najdete v tématu [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Ovladač systému souborů Azure Blob Azure

Apache Hadoop aplikace nativně očekávají, že ke čtení a zápisu dat z úložiště na místním disku. Ovladač systému souborů Hadoop jako ABFS umožňuje aplikacím Hadoop pro práci s cloudovým úložištěm emulací standardních operací systému souborů Hadoop. Ovladač převede tyto příkazy obdržený od aplikace do operací, které rozumí skutečnou cloudovou platformu úložiště.

Ovladač systému souborů Hadoop dříve, převést všechny operace systému souborů na volání REST API služby Azure Storage na straně klienta a následně vyvolány rozhraní REST API. Tento na straně klienta převod, ale výsledkem více rozhraní REST API je volání pro jeden soubor systémová operace jako je přejmenování souboru. ABFS se přesunul některé logiky systému souborů Hadoop ze strany klienta na straně serveru. Rozhraní API Azure Data Lake Storage Gen2 se teď bude spouštět souběžně s rozhraním API pro objekt Blob. Tato migrace zlepší výkon, protože teď můžete provádět běžné operace souborového systému Hadoop pomocí jednoho volání rozhraní REST API.

Další informace najdete v tématu [ovladačů systém souborů Azure Blob (ABFS): Vyhrazené ovladač Azure Storage pro Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schéma identifikátoru URI pro Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 nové schéma identifikátoru URI používá pro přístup k souborům ve službě Azure Storage z HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma identifikátoru URI poskytuje přístup zašifrované protokolem SSL (`abfss://` předpony) a nešifrované přístup (`abfs://` předponu). Použití `abfss` kdykoli je to možné, i v případě, že přístup k datům, umístěným uvnitř stejné oblasti v Azure.

`<FILE_SYSTEM_NAME>` Určuje cestu systému souborů Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Určuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>` je název cesty HDFS souboru nebo adresáře.

Pokud hodnoty `<FILE_SYSTEM_NAME>` a `<ACCOUNT_NAME>` nejsou zadané, se používá výchozí systém souborů. Pro soubory na výchozí systém souborů použijte relativní cestu nebo absolutní cesta. Například `hadoop-mapreduce-examples.jar` soubor, který se dodává s clustery HDInsight lze odkazovat pomocí jedné z následujících cest:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2.1 a 1.6. Při práci se soubory mimo HDInsight, zjistíte, že většina nástrojů nerozpozná ABFS formátování, ale místo toho očekávají základní formát cesty, jako například `example/jars/hadoop-mapreduce-examples.jar`.

Další informace najdete v tématu [použít identifikátor URI služby Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure Storage je řešení robustní úložiště pro obecné účely, který se bez problémů integruje s HDInsight. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Pomocí rozhraní HDFS celá sada komponent v HDInsight můžete pracovat přímo s strukturovanými i nestrukturovanými daty uloženými jako objekty BLOB.

Při vytváření účtu služby Azure storage, můžete z několika typů účtu úložiště. Následující tabulka obsahuje informace o možnostech, které jsou podporovány s HDInsight.

| **Typ účtu úložiště** | **Podporované služby** | **Úrovně výkonu podporované** | **Podporované přístupu** |
|----------------------|--------------------|-----------------------------|------------------------|
| Pro obecné účely V2   | Objekt blob               | Standard                    | Horká, studená, archivní *    |
| Pro obecné účely V1   | Objekt blob               | Standard                    | neuvedeno                    |
| Blob Storage         | Objekt blob               | Standard                    | Horká, studená, archivní *    |

* Úroveň přístupu Archive je vrstvu offline latence načtení několik hodin. Nepoužívejte tuto vrstvu s HDInsight. Další informace najdete v tématu [archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

> [!WARNING]  
> Nedoporučujeme používat výchozí kontejner objektu blob ukládat firemní data. Výchozí kontejner obsahuje protokoly aplikace a systému. Ujistěte se, že tyto protokoly načíst před odstraněním výchozí kontejner objektu blob. Odstraňte kontejner blogu po každém použití ke snížení nákladů na úložiště. Také vzít v úvahu, že jedna kontejner objektů blob nelze použít jako výchozí systém souborů pro několik clusterů.


### <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight
Následující diagram představuje abstraktní zobrazení architektury úložiště Azure v HDInsight:

![Diagram znázorňující, jak clustery Hadoop používají rozhraní API HDFS pro přístup a ukládání strukturovaných a nestrukturovaných dat v úložišti objektů Blob](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "architektury úložiště HDInsight")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Pomocí HDInsight můžete také přístup k datům ve službě Azure Storage. Syntaxe vypadá takto:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Při použití účtu služby Azure Storage s clustery HDInsight, zvažte následující zásady:

* **Kontejnery v účtech úložiště, které jsou připojené ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou přidružené ke clusteru během vytváření, máte plný přístup k objektům BLOB v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které jsou *není* připojené ke clusteru:** Máte oprávnění jen pro čtení k objektům BLOB v kontejnerech.
  
  > [!NOTE]  
  > Veřejné kontejnery umožňují získat seznam všech objektů BLOB, které jsou k dispozici v tomto kontejneru a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Privátní kontejnery v účtech úložiště, které jsou *není* připojené ke clusteru:** Objekty BLOB v kontejnerech nelze získat přístup, dokud nedefinujete účet úložiště při odesílání úlohy WebHCat. 

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. HDInsight používá ve výchozím nastavení všechny účty úložišť, které jsou definovány v souboru core-site.xml. Toto nastavení lze upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Více úloh WebHCat, včetně Apache Hive, MapReduce, streamování Apache Hadoop a Apache Pig, může obsahovat popis účtů úložiště a spojených metadat. (To je aktuálně true pro Pig s účty úložiště, ale ne pro metadata.) Další informace najdete v tématu [pomocí clusteru služby HDInsight s účty alternativní úložiště a metaúložišti](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů BLOB ukládají data jako dvojice klíč/hodnota a mít žádná hierarchie adresářů. Název klíče, který ale může obsahovat lomítko (/), aby vypadal jako soubor je uložen v rámci adresářové struktury. Klíč k objektu blob může být například `input/log1.txt`. Žádný skutečný `input` adresář existuje, ale kvůli lomítku v názvu klíče, klíče vypadá jako cestu k souboru.

### <a id="benefits"></a>Výhody služby Azure Storage
Výpočetní clustery a prostředky úložiště, které nejsou společně umístěná mají implicitní nákladů na výkon. Tyto náklady jsou omezeny tak, jak výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure. V této oblasti výpočetních uzlů můžete efektivně přístup k datům prostřednictvím vysokorychlostní sítě ve službě Azure Storage.

Při ukládání dat ve službě Azure Storage namísto HDFS, získáte několik výhod:

* **Opakované použití dat a sdílení:** Data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data ve službě Azure Storage, naopak je přístupný prostřednictvím rozhraní API HDFS nebo úložiště objektů Blob rozhraní REST API. Z důvodu tohoto uspořádání větší sadu aplikací (včetně jiných clusterů HDInsight) a nástrojů lze vytvářet a využívat data.

* **Archivace dat:** Pokud jsou data uložená ve službě Azure Storage, clusterů HDInsight, které jsou používány pro výpočty můžete bezpečně odstranit bez ztráty dat uživatele.

* **Náklady na úložiště dat:** Pro dlouhodobé ukládání dat v systému souborů DFS je proto dražší než ukládání dat ve službě Azure Storage, protože náklady na výpočetní cluster jsou vyšší než náklady na úložiště Azure. Navíc vzhledem k tomu, že data nebude muset znovu načíst pro každou generaci výpočetních clusterů, které ukládáte i náklady na načtení dat.

* **Elastické horizontální navýšení kapacity:** I když HDFS poskytuje systém horizontálním navýšením kapacity souborů, škála se určuje podle počtu uzlů, které vytvoříte pro váš cluster. Změna škálování může být složitější než využití elastického škálování, které je automaticky ve službě Azure Storage.

* **Geografická replikace:** Úložiště Azure může být geograficky replikovaný. I když geografická replikace umožňuje geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon, a to může způsobit dodatečné náklady. Proto zvolte geografické replikace opatrně a pouze pokud datová hodnota odůvodňuje další poplatky.

Určité úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které není vhodné ukládat ve službě Azure Storage. V takovém případě můžete zvolit k uložení dat do místní HDFS. HDInsight používá DFS pro některé z těchto mezilehlých výsledků v úlohách Hive a jiných procesů.

> [!NOTE]  
> Většina příkazů HDFS (například `ls`, `copyFromLocal`, a `mkdir`) fungovat podle očekávání ve službě Azure Storage. Jenom příkazy, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), jako je například `fschk` a `dfsadmin`, zobrazit různé chování v Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Přehled služby Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 je podnikové úrovni velkokapacitní úložiště pro analytické úlohy s velkými objemy dat. Pomocí Azure Data Lake, můžete zaznamenávat data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a zjišťovací analýzy.

Přístup k Data Lake Storage Gen1 z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Data Lake Storage Gen1 slouží k povolení analýzy uložených dat a je optimalizovaná pro výkon v scénáře analýzy dat. Hned po spuštění zahrnuje funkce, které jsou nezbytné pro podnikové případy použití. Mezi tyto možnosti patří, zabezpečení, spravovatelnosti, škálovatelnosti, spolehlivosti a dostupnosti.

Další informace o Azure Data Lake Storage Gen1, najdete v podrobné [přehled o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Klíčové funkce Gen1 úložiště Data Lake patří následující.

### <a name="compatibility-with-hadoop"></a>Kompatibilita s Hadoopem

Gen1 úložiště data Lake je systém souborů Apache Hadoop, který je kompatibilní s HDFS a pracuje s ekosystémem Hadoop.  Vaše aplikace HDInsight nebo služby, které používají rozhraní API WebHDFS můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také poskytuje rozhraní kompatibilních s WebHDFS REST pro aplikace.

Data uložená v Data Lake Storage Gen1 se dají snadno analyzovat pomocí analytických rámců Hadoop, jako je například MapReduce nebo Hive. Clustery Azure HDInsight můžete zřizovat a konfigurovat přímo přístup k datům uloženým v Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodná pro ukládání nejrůznějších dat k analýze. Neuplatňuje omezení velikosti účtů, velikosti souborů nebo množství dat, které mohou být uloženy ve službě data lake. Velikost od kilobajtů až petabajty a že Data Lake Storage Gen1 skvělou volbou pro ukládání libovolného typu dat můžou být jednotlivé soubory. Data se ukládají odolným způsobem díky vytváření více kopií a na jak dlouho může být data uložená v data lake neplatí žádné limity.

### <a name="performance-tuning-for-big-data-analytics"></a>Ladění výkonu pro big data analytics

Gen1 úložiště data Lake je určený pro spouštění rozsáhlých analytických systémů, které vyžadují mimořádně velkou propustnost pro dotazy a analýzu velkých objemů dat. Data lake šíří části souborů prostřednictvím několika jednotlivých serverů úložiště. Když analyzujete data, toto nastavení se zvyšuje propustnost čtení souboru přečtení paralelně.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Připravenost pro podniky: Vysoce dostupnou a zabezpečenou

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Datové prostředky se ukládají odolným způsobem: redundantní kopie pomáhalo chránit před neočekávaným selháním. Podniky můžete v rámci svých řešení použít Gen1 úložiště Data Lake jako důležitou součást svojí stávající datové platformy.

Data Lake Storage Gen1 také poskytuje zabezpečení na podnikové úrovni pro uložená data. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexibilní datové struktury

Data Lake Storage Gen1 dokáže ukládat libovolná data v nativním formátu tak, jak jsou, bez nutnosti předchozí transformace. Schéma je definovat před načtením dat nevyžaduje, aby Gen1 úložiště data Lake. Jednotlivé analytické framework interpretuje data a definuje schéma během analýzy. Protože může ukládat soubory libovolných velikostí a formátů, dokáže zpracovat Data Lake Storage Gen1 strukturovaných, částečně strukturovaných a nestrukturovaných dat.

Data Lake Storage Gen1 kontejnery na data jsou v podstatě složky a soubory. Můžete pracovat u uložených dat pomocí sady SDK, webu Azure portal a Azure Powershellu. Za předpokladu, nechte svá data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete ukládat data libovolného typu. Data Lake Storage Gen1 neprovádí žádným zvláštním způsobem na základě typu dat, která ukládá data.

## <a name="DataLakeStoreSecurity"></a>Zabezpečení dat v Data Lake Storage Gen1
Data Lake Storage Gen1 používá Azure Active Directory pro přístup k ověřování a používá řídit seznamy ACL můžete spravovat přístup k vašim datům.

| **Funkce** | **Popis** |
| --- | --- |
| Authentication |Gen1 úložiště data Lake se integruje s Azure Active Directory (Azure AD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Díky integraci využívá výhod Gen1 úložiště Data Lake všechny funkce služby Azure AD. Tyto funkce patří vícefaktorové ověřování, podmíněného přístupu, řízení přístupu na základě rolí, sledování využití aplikací, monitorování zabezpečení a upozorňování a tak dále. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování v rámci rozhraní REST. Zobrazit [ověřování v rámci Azure Data Lake Storage Gen1 pomocí Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu díky podpoře oprávnění ve stylu POSIX, které jsou vystaveny protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů řízení přístupu v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také zajišťuje šifrování dat uložených v účtu. Nastavení šifrování se zadává při vytváření účtu Data Lake Storage Gen1. Můžete se vaše data šifrovala, nebo zvolit možnost bez šifrování. Další informace najdete v tématu [šifrování ve službě Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Pokyny pro poskytnutí konfigurace související se šifrováním najdete v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Další informace o zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat uložených v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikace, které jsou kompatibilní s Data Lake Storage Gen1
Data Lake Storage Gen1 je kompatibilní s Většina komponent open source v ekosystému Hadoop. Výborně se taky integruje s jinými službami Azure.  Postupujte podle níže odkazy, které další informace o tom, jak Gen1 úložiště Data Lake můžete použít i pomocí open source komponent, jakož i dalšími službami Azure.

* Zobrazit [Open-source aplikací velkých objemů dat, které fungují s Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) seznam open-source aplikace, které spolupracují s Data Lake Storage Gen1.
* Zobrazit [integraci Azure Data Lake Storage Gen1 s ostatními službami Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) pochopit, jak používat Data Lake Storage Gen1 s ostatními službami Azure umožňující širší škálu scénářů.
* Zobrazit [pomocí Azure Data Lake Storage Gen1 pro potřeby velkého objemu dat](../data-lake-store/data-lake-store-data-scenarios.md) se naučíte používat Data Lake Storage Gen1 ve scénářích, jako je příjem dat, zpracování dat, stahování dat a vizualizace dat.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Systém souborů data Lake Storage Gen1 (adl: / /)
V prostředích Hadoop (dostupné s clusterem HDInsight), můžete Data Lake Storage Gen1 přístup prostřednictvím nového systému souborů, AzureDataLakeFilesystem (adl: / /). Výkon aplikací a služeb, které používají adl: / / lze optimalizovat takovým způsobem, který není ve WebHDFS aktuálně dostupná. V důsledku toho při použití Data Lake Storage Gen1 získáte flexibilitu pro buď avail co nejlepšího výkonu pomocí doporučených adl: / / nebo zachovat stávající kód a dál přímo používat rozhraní API WebHDFS. Azure HDInsight plně využívá systém AzureDataLakeFilesystem k zajištění nejlepšího výkonu v Data Lake Storage Gen1.

Přístup k datům v Data Lake Storage Gen1 pomocí následující:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1 najdete v tématu [akce dostupné u uložených dat](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)