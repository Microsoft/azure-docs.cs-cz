---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled o typech úložiště a jak fungují s Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/20/2019
ms.openlocfilehash: 14db76068cc11d3f57a72e3e540a5e0da7e1c254
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853608"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Uživatelé Azure HDInsight můžete si vybrat mezi několik různých možností ukládání při vytváření clusterů HDInsight:

* Azure Data Lake Storage Gen2
* Azure Storage
* Azure Data Lake Storage Gen1

Tento článek obsahuje přehled těchto různé typy úložišť a jejich jedinečné funkce.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Použití Azure Data Lake Storage Gen2 s Apache Hadoop v Azure HDInsight

Další informace o Azure Data Lake Storage Gen2 najdete v tématu [Úvod do služby Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Azure Data Lake Storage Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 například kompatibilní souborový systém Hadoop, Azure Active Directory a přístupu na základě POSIX řídit seznamy ACL a integruje do služby Azure Blob Storage. Tato kombinace umožňuje využít výkon Azure Data Lake Storage Gen1 a zároveň využívat vrstvení úložiště objektů Blob a správa životního cyklu data.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

* Hadoop kompatibilní přístup: Azure Data Lake Storage Gen2 umožňuje spravovat a přistupovat k datům, stejně jako byste to udělali s souboru systému HDFS (Hadoop Distributed). Ovladač systému souborů objektů Blob v Azure (ABFS) je k dispozici ve všech prostředích Apache Hadoop, včetně Azure HDInsight a Azure Databricks pro přístup k datům uloženým v Data Lake Storage Gen2.

* Nadmnožina POSIX oprávnění: Model zabezpečení pro Data Lake Gen2 podporuje oprávnění řízení přístupu a POSIX spolu s nějaké další specifické pro Data Lake Storage Gen2 členitosti. Nastavení lze nakonfigurovat prostřednictvím správce nástroje nebo architektury, jako je Apache Hivu a Apache Sparku.

* Cenová přístupnost: Data Lake Storage Gen2 nabízí kapacitu úložiště s nízkými náklady a transakce. Funkce, jako je životní cyklus úložiště objektů Blob v Azure pomůže snížit náklady na úpravou fakturační sazby podle dat prochází přes životního cyklu.

* Funguje s Blob storage nástroje, platformy a aplikace: Data Lake Storage Gen2 se nadále využívat širokou škálu nástroje, platformy a aplikace, které existují ještě dnes pro úložiště objektů Blob.

* Optimalizované ovladače: Ovladač ABFS je speciálně pro analýzy velkých objemů dat optimalizovaná. Zobrazují se prostřednictvím koncového bodu systému souborů dfs, odpovídající rozhraní REST API dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Co je nového v Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro přístup k souborům zabezpečení

Azure HDInsight pomocí spravované identity zabezpečení clusteru přístup k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkcí služby Azure Active Directory, která poskytuje služby Azure se sadou automaticky spravované přihlašovací údaje. Tyto přihlašovací údaje slouží k ověření na libovolnou službu, která podporuje ověřování AD. Použití spravované identity nevyžaduje k uložení přihlašovacích údajů v kódu nebo konfiguračního souboru.

Další informace najdete v tématu [co je spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Ovladač systému souborů (ABFS) služby Azure Blob

Apache Hadoop aplikace nativně očekávají, že ke čtení a zápisu dat z úložiště na místním disku. Ovladač systému souborů Hadoop jako ABFS umožňuje aplikacím Hadoop pro práci s cloudovým úložištěm emulací standardních operací systému souborů Hadoop. Ovladač převede tyto příkazy obdržený od aplikace do operací, které rozumí skutečnou cloudovou platformu úložiště.

Ovladač systému souborů Hadoop by dříve, převést všechny operace systému souborů na volání REST API služby Azure Storage na straně klienta a pak vyvoláte rozhraní REST API. Tento na straně klienta převod, ale výsledkem více rozhraní REST API je volání pro jeden vykonání operace systému souborů jako soubor přejmenovat. ABFS přesunul některé logiky systému souborů Hadoop ze strany klienta na straně serveru a rozhraní API Azure Data Lake Storage Gen2 teď běží paralelně s rozhraním API pro objekt Blob. Tato migrace zlepší výkon, protože nyní mohou být provedeny běžných operací systému souborů Hadoop pomocí jednoho volání rozhraní REST API.

Další informace najdete v tématu [ovladačů systém souborů Azure Blob (ABFS): Vyhrazené ovladač Azure Storage pro Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Azure Data Lake Storage Gen 2 URI schématu

Azure Data Lake Storage Gen2 používá nové schéma identifikátoru URI pro přístup k souborům ve službě Azure storage v HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma identifikátoru URI poskytuje přístup šifrovanou protokolem SSL (`abfss://` předpony) a nešifrované přístup (`abfs://` předponu). Doporučujeme používat `abfss` kdykoli je to možné, i v případě, že přístup k datům, umístěným uvnitř stejné oblasti v Azure.

`<FILE_SYSTEM_NAME>` Určuje cestu systému souborů Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Určuje název účtu úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>` Je název cesty HDFS souboru nebo adresáře.

Pokud hodnoty `<FILE_SYSTEM_NAME>` a `<ACCOUNT_NAME>` nejsou zadané, se používá výchozí systém souborů. Pro soubory ve výchozím systému souborů můžete použít relativní cestu nebo absolutní cestu. Například `hadoop-mapreduce-examples.jar` soubor, který se dodává s clustery HDInsight lze odkazovat pomocí jedné z následujících cest:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2.1 a 1.6. Při práci se soubory mimo HDInsight Většina nástrojů ABFS formátování a místo toho očekávají základní formát cesty, jako například nebyla rozpoznána `example/jars/hadoop-mapreduce-examples.jar`.

Další informace najdete v tématu [použít identifikátor URI služby Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Použití služby Azure Storage

Azure Storage je robustní řešení úložiště pro obecné účely, které se jednoduše integruje se službou HDInsight. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní systému souborů Hadoop DFS (HDFS) může celá sada komponent ve službě HDInsight pracovat přímo se strukturovanými nebo nestrukturovanými daty uloženými jako objekty blob.

> [!WARNING]  
> Při vytváření účtu služby Azure Storage je k dispozici několik možností. Následující tabulka poskytuje informace o podporovaných možnostech se službou HDInsight:

| Typ účtu úložiště | Podporované služby | Úrovně výkonu podporované | Podporované přístupu |
|----------------------|--------------------|-----------------------------|------------------------|
| Pro obecné účely V2   | Objekt blob               | Standard                    | Horká, studená, archivní *    |
| Pro obecné účely V1   | Objekt blob               | Standard                    | neuvedeno                    |
| Blob Storage         | Objekt blob               | Standard                    | Horká, studená, archivní *    |

Nedoporučujeme používat výchozí kontejner objektu blob pro ukládání firemních dat. Ideální postup je výchozí kontejner objektů blob po každém použití odstranit a snížit tak náklady na úložiště. Výchozí kontejner obsahuje aplikaci a systémové protokoly. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Použití jednoho kontejneru objektů blob jako výchozí systém souborů pro několik clusterů se nepodporuje.
 
 > [!NOTE]  
 > Archivní úroveň přístupu je offline vrstvy, který má několik latence načtení hodinu a nedoporučuje se používat pro použití s HDInsight. Další informace najdete v tématu [archivní úroveň přístupu](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight
Následující diagram představuje abstraktní zobrazení architektury úložiště HDInsight, které používá službu Azure Storage:

![Clustery Hadoop používají rozhraní API HDFS pro přístup a ukládání strukturovaných i nestrukturovaných dat do služby Blob Storage.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "Architektura HDInsight Storage")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Služba HDInsight navíc umožňuje přístup k datům uloženým ve službě Azure Storage. Syntaxe je:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Při použití účtu Azure Storage s clustery HDInsight je potřeba zvážit tyto aspekty.

* **Kontejnery v účtech úložiště, které jsou připojené ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou přidružené ke clusteru během vytváření, máte plný přístup k objektům BLOB v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které nejsou připojené ke clusteru:** Máte oprávnění jen pro čtení k objektům BLOB v kontejnerech.
  
  > [!NOTE]  
  > Veřejné kontejnery umožňují získat seznam všech objektů blob, které jsou k dispozici v tomto kontejneru a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [Správa přístupu ke kontejnerům a objektům blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Privátní kontejnery v účtech úložiště, které nejsou připojené ke clusteru:** Objekty BLOB v kontejnerech nelze získat přístup, dokud nedefinujete účet úložiště při odesílání úlohy WebHCat. To se vysvětluje dále v tomhle článku.

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. Výchozím chováním služby HDInsight je používání účtů úložiště, které jsou definovány v souboru core-site.xml. Je toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Více úloh WebHCat, včetně Apache Hive, MapReduce, streamování Apache Hadoop a Apache Pig, může obsahovat popis účtů úložiště a spojených metadat. (To aktuálně funguje pro Pig s účty úložiště, ale ne pro metadata.) Více informací najdete v části [Použití clusteru HDInsight s alternativními účty úložiště a metaúložišti](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů blob ukládají data jako páry klíč/hodnota a neexistuje žádná hierarchie adresářů. V názvu klíče se dá použít lomítko (/), aby název klíče připomínal  cestu k souboru. Klíč k objektu blob může být například `input/log1.txt`. Žádný skutečný `input` adresář existuje, ale vzhledem k lomítku v názvu klíče, má podobu cestu k souboru.

### <a id="benefits"></a>Výhody služby Azure Storage
Předpokládaná výkonová náročnost společně umístěných výpočetních clusterů a prostředků úložiště je zmírněna tím, že výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště uvnitř oblasti Azure, kde vysokorychlostní síť umožňuje efektivní přístup výpočetních uzlů k datům ve službě Azure Storage.

S ukládáním dat ve službě Azure Storage namísto HDFS je spojeno několik výhod:

* **Opakované použití dat a sdílení:** Data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data ve službě Azure storage je přístupná prostřednictvím rozhraní API HDFS nebo prostřednictvím rozhraní REST API služby Blob Storage. Proto s větším počtem aplikací (včetně jiných clusterů HDInsight) a nástrojů  se dají vytvářet a využívat data.
* **Archivace dat:** Ukládání dat ve službě Azure storage umožňuje clusterů HDInsight, které jsou používány pro výpočty bezpečně odstranit, aniž by se ztratila uživatelská data.
* **Náklady na úložiště dat:** Pro dlouhodobé ukládání dat v systému souborů DFS je proto dražší než ukládání dat ve službě Azure storage, protože náklady na výpočetní cluster jsou vyšší než náklady na úložiště Azure. Navíc vzhledem k tomu, že data není nutné znovu načíst pro každou generaci výpočetních clusterů, které také ukládáte náklady na nahrávání dat.
* **Elastické horizontální navýšení kapacity:** I když HDFS poskytuje systém horizontálním navýšením kapacity souborů, škála se určuje podle počtu uzlů, které vytvoříte pro váš cluster. Změna škálování může být složitější než využití elastického škálování, které je automaticky k dispozici ve službě Azure Storage.
* **Geografická replikace:** Úložiště Azure může být geograficky replikovaný. I když to přináší geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může vést k dalším nákladům. Doporučujeme proto geografickou replikaci dobře zvážit a zvolit jen v případě, že hodnota dat je vyšší než náklady na celou operaci.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které ve službě Azure Storage ve skutečnosti uložit nechcete. V takovém případě můžete zvolit k uložení dat do místní HDFS. Ve skutečnosti služba HDInsight používá DFS pro některé z těchto mezilehlých výsledků v úlohách Hive a jiných procesech.

> [!NOTE]  
> Většina příkazů HDFS (například `ls`, `copyFromLocal` a `mkdir`) i nadále fungovat podle očekávání. Jenom příkazy, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), jako je například `fschk` a `dfsadmin`, zobrazit různé chování ve službě Azure storage.

## <a name="use-azure-data-lake-storage-gen1"></a>Použití Azure Data Lake Storage Gen1

Další informace o Azure Data Lake Storage Gen1 najdete v tématu [přehled o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Azure Data Lake Storage Gen1 je podnikové úrovni hyperškálovatelné úložiště pro analytické úlohy s velkými objemy dat. Azure Data Lake umožňuje zaznamenávat data libovolné velikosti, typu a rychlosti příjmu do jediného místa pro účely provozní a zjišťovací analýzy.

Data Lake Storage Gen1 je přístupná z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Slouží k povolení analýzy uložených dat a je vyladění z hlediska výkonu pro scénáře analýzy dat. Ihned poskytuje veškeré funkce na podnikové úrovni – zabezpečení, správu, škálovatelnost, spolehlivost a dostupnost – které jsou v podmínkách reálného podnikového použití nezbytné.

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "architektury úložiště HDInsight")

Mezi klíčové funkce Gen1 úložiště Data Lake patří následující.

### <a name="built-for-hadoop"></a>Sestaveno pro Hadoop

Gen1 úložiště data Lake je systém souborů Apache Hadoop kompatibilní s Hadoop Distributed File System (HDFS) a pracuje s ekosystémem Hadoop.  Vaše aplikace HDInsight nebo služby, které používají rozhraní API WebHDFS můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také poskytuje rozhraní kompatibilních s WebHDFS REST pro aplikace

Data uložená v Data Lake Storage Gen1 se dají snadno analyzovat pomocí analytických rámců Hadoop, jako je například MapReduce nebo Hive. Clustery Microsoft Azure HDInsight můžete zřizovat a konfigurovat přímo přístup k datům uloženým v Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodná pro ukládání nejrůznějších dat k analýze. Neuplatňuje žádná omezení, pokud jde o velikosti účtů, velikosti souborů nebo množství dat, které se dá uložit do úložiště Data Lake. Velikost jednotlivých souborů se může pohybovat od kilobajtů až po petabajty, a díky tomu je služba skvělou volbou pro ukládání libovolného typu dat. Data se ukládají spolehlivě, protože se vytváří víc kopií a není omezená doba, po kterou můžou být data uložená v úložišti Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je sestavena pro spouštění rozsáhlých analytických systémů, které vyžadují mimořádně velkou propustnost pro dotazy a analýzu velkých objemů dat. Úložiště Data Lake rozděluje části souborů do několika jednotlivých serverů úložiště. Tím se zvyšuje propustnost čtení při paralelním čtení souboru pro provádění analýz dat.

### <a name="enterprise-ready-highly-available-and-secure"></a>Připraveno pro podniky: Vysoce dostupnou a zabezpečenou

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Vaše datové prostředky se ukládají odolným způsobem díky vytváření redundantních kopií, které chrání před neočekávaným selháním. Podniky můžete v rámci svých řešení použít Gen1 úložiště Data Lake jako důležitou součást svojí stávající datové platformy.

Data Lake Storage Gen1 také poskytuje zabezpečení na podnikové úrovni pro uložená data. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Všechna data

Data Lake Storage Gen1 dokáže ukládat libovolná data v nativním formátu tak, jak jsou, bez nutnosti předchozí transformace. Data Lake Storage Gen1 nevyžaduje, aby schéma být definovány před načtením dat, ale ponechává jednotlivých analytické framework interpretoval data a definovat schéma během analýzy. Schopnost ukládat soubory libovolných velikostí a formátů umožňuje Gen1 úložiště Data Lake pro zpracování strukturovaných, částečně strukturovaná a Nestrukturovaná data.

Data Lake Storage Gen1 kontejnery na data jsou v podstatě složky a soubory. Pracují s uloženými daty pracujete pomocí sady SDK, webu Azure portal a Azure Powershellu. Pokud ukládáte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete ukládat jakýkoli typ dat. Data Lake Storage Gen1 neprovádí žádným zvláštním způsobem na základě typu dat, která ukládá data.

## <a name="DataLakeStoreSecurity"></a>Zabezpečení dat v Data Lake Storage Gen1
Data Lake Storage Gen1 používá Azure Active Directory pro ověřování a přístup k řízení seznamy ACL můžete spravovat přístup k vašim datům.

| Funkce | Popis |
| --- | --- |
| Authentication |Gen1 úložiště data Lake se integruje s Azure Active Directory (AAD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. V důsledku integrace výhody Gen1 úložiště Data Lake všechny funkce AAD, včetně služby Multi-Factor authentication, podmíněného přístupu, řízení přístupu na základě rolí, sledování využití aplikací, sledování a výstrah zabezpečení atd. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování pomocí rozhraní REST. Zobrazit [ověřování Data Lake Storage Gen1](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu díky podpoře oprávnění ve stylu POSIX vystavený protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů řízení přístupu v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také zajišťuje šifrování dat uložených v účtu. Nastavení šifrování se zadává při vytváření účtu Data Lake Storage Gen1. Můžete zvolit, aby se vaše data šifrovala, nebo zvolit možnost bez šifrování. Další informace najdete v tématu [šifrování ve službě Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Pokyny pro poskytnutí konfigurace související se šifrováním najdete v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Chcete se dozvědět víc o zabezpečení dat v Data Lake Storage Gen1? Použijte následující odkazy.

* Pokyny k zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplikace kompatibilní s Data Lake Storage Gen1
Data Lake Storage Gen1 je kompatibilní s Většina komponent open source v ekosystému Hadoop. Výborně se taky integruje s jinými službami Azure.  Postupujte podle níže odkazy, které další informace o tom, jak Gen1 úložiště Data Lake můžete použít i pomocí open source komponent, jakož i dalšími službami Azure.

* Zobrazit [aplikace a služby kompatibilní s Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) seznam spolupracují se službou Data Lake Storage Gen1 open-source aplikací.
* Zobrazit [integrací s ostatními službami Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) pochopit, jak Gen1 úložiště Data Lake je možné s ostatními službami Azure umožňující širší škálu scénářů.
* Zobrazit [scénáře použití Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) se naučíte používat Data Lake Storage Gen1 ve scénářích, jako je příjem dat, zpracování dat, stahování dat a vizualizace dat.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Co je systém souborů Data Lake Storage Gen1 (adl: / /)?
Data Lake Storage Gen1 je přístupná prostřednictvím nového systému souborů, AzureDataLakeFilesystem (adl: / /), v prostředích Hadoop (dostupné s clusterem HDInsight). Aplikace a služby, které používají adl://, můžou těžit z další optimalizace výkonu, která není ve WebHDFS aktuálně dostupná. V důsledku toho Gen1 úložiště Data Lake poskytuje flexibilní buď využít nejlepší výkon s parametrem doporučené použití adl: / / nebo zachovat stávající kód a dál přímo používat rozhraní API WebHDFS. Azure HDInsight plně využívá systém AzureDataLakeFilesystem k zajištění nejlepšího výkonu v Data Lake Storage Gen1.

Můžete přistupovat k datům v Data Lake Storage Gen1 pomocí `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1 najdete v tématu [zobrazení vlastností uložených dat](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)