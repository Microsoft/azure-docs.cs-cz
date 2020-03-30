---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled typů úložišť a jejich fungování s Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095545"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Při vytváření clusterů HDInsight si můžete vybrat mezi několika různými službami úložiště Azure:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Tento článek obsahuje přehled těchto typů úložišť a jejich jedinečné funkce.

Následující tabulka shrnuje služby Azure Storage, které jsou podporované různými verzemi HDInsightu:

| Služba úložiště | Typ účtu | Typ oboru názvů | Podporované služby | Podporované úrovně výkonu | Podporované úrovně přístupu | Verze HDInsight | Typ clusteru |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Pro všeobecné účely V2 | Hierarchický (souborový systém) | Objekt blob | Standard | Horké, Cool, Archiv | 3.6+ | Všechny kromě Spark 2.1 a 2.2|
|Azure Storage| Pro všeobecné účely V2 | Objekt | Objekt blob | Standard | Horké, Cool, Archiv | 3.6+ | Všechny |
|Azure Storage| Pro všeobecné účely V1 | Objekt | Objekt blob | Standard | Není dostupné. | Všechny | Všechny |
|Azure Storage| Úložiště objektů blob** | Objekt | Objekt blob bloku | Standard | Horké, Cool, Archiv | Všechny | Všechny |
|Azure Data Lake Storage Gen1| Není dostupné. | Hierarchický (souborový systém) | Není dostupné. | Není dostupné. | Není dostupné. | 3.6 Pouze | Všechny kromě HBase |

**Pro clustery HDInsight mohou být pouze účty sekundárního úložiště typu BlobStorage a Objekt blob stránky není podporovanou možností úložiště.

Další informace o typech účtů Azure Storage najdete v tématu [Přehled účtu úložiště Azure.](../storage/common/storage-account-overview.md)

Další informace o úrovních přístupu k Azure Storage najdete v [tématu Úložiště objektů blob Azure: Premium (preview), Hot, Cool a Archive úrovně úložiště](../storage/blobs/storage-blob-storage-tiers.md)

Cluster můžete vytvořit pomocí různých kombinací služeb pro primární a volitelné sekundární úložiště. Následující tabulka shrnuje konfigurace úložiště clusteru, které jsou aktuálně podporovány v HDInsight:

| Verze HDInsight | Primární úložiště | Sekundární úložiště | Podporuje se |
|---|---|---|---|
| 3,6 & 4,0 | Obecný účel V1 , obecný účel V2 | Obecné použití V1 , pro obecné účely V2, BlobStorage (objekty BLOB bloku) | Ano |
| 3,6 & 4,0 | Obecný účel V1 , obecný účel V2 | Data Lake Storage Gen2 | Ne |
| 3,6 & 4,0 | Úložiště datových jezer Gen2* | Data Lake Storage Gen2 | Ano |
| 3,6 & 4,0 | Úložiště datových jezer Gen2* | Obecné použití V1 , pro obecné účely V2, BlobStorage (objekty BLOB bloku) | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Ne |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ano |
| 3.6 | Data Lake Storage Gen1 | Obecné použití V1 , pro obecné účely V2, BlobStorage (objekty BLOB bloku) | Ano |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Ne |
| 4.0 | Data Lake Storage Gen1 | Všechny | Ne |
| 4.0 | Obecný účel V1 , obecný účel V2 | Data Lake Storage Gen1 | Ne |

*=Může se jedná o jeden nebo více účtů Data Lake Storage Gen2, pokud jsou všechny nastaveny tak, aby používaly stejnou spravovanou identitu pro přístup ke clusteru.

> [!Note] 
> Primární úložiště Data Lake Storage Gen2 není podporováno pro clustery Spark 2.1 nebo 2.2. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Použití Azure Data Lake Storage Gen2 s Apache Hadoop v Azure HDInsight

Azure Data Lake Storage Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 a integruje je do úložiště objektů blob Azure. Mezi tyto funkce patří systém souborů, který je kompatibilní s Hadoop, Azure Active Directory (Azure AD) a SEZNAMY řízení přístupu na základě POSIX (ACL). Tato kombinace umožňuje využít výhod výkonu Azure Data Lake Storage Gen1 a zároveň pomocí vrstvení a správy životního cyklu dat úložiště objektů blob.

Další informace o Azure Data Lake Storage Gen2 najdete [v tématu Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

* **Přístup, který je kompatibilní s Hadoopem:** V Azure Data Lake Storage Gen2 můžete spravovat a přistupovat k datům stejně jako pomocí distribuovaného souborového systému Hadoop (HDFS). Ovladač Azure Blob File System (ABFS) je k dispozici ve všech prostředích Apache Hadoop, včetně Azure HDInsight a Azure Databricks. Pomocí ABFS pro přístup k datům uloženým v datovém úložišti data Č. 2.

* **Nadmnožina oprávnění POSIX:** Model zabezpečení pro Data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými extra rozlišovací schopnost specifické pro Data Lake Storage Gen2. Nastavení lze konfigurovat pomocí nástrojů pro správu nebo rámců, jako je Apache Hive a Apache Spark.

* **Efektivita nákladů:** Data Lake Storage Gen2 nabízí nízkonákladovou úložnou kapacitu a transakce. Funkce, jako je životní cyklus úložiště objektů blob Azure, pomáhají snížit náklady úpravou fakturačních sazeb při pohybu dat v průběhu životního cyklu.

* **Kompatibilita s nástroji, architekturami a aplikacemi pro ukládání objektů Blob:** Data Lake Storage Gen2 pokračuje v práci s širokou škálou nástrojů, architektur a aplikací pro úložiště objektů Blob.

* **Optimalizovaný ovladač:** Ovladač ABFS je optimalizován speciálně pro analýzu velkých objemů dat. Odpovídající rest API jsou konsurfaced prostřednictvím koncového bodu distribuovaného systému souborů (DFS), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co je nového pro Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro zabezpečený přístup k souborům

Azure HDInsight používá spravované identity k zabezpečení přístupu clusteru k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkce služby Azure Active Directory, která poskytuje službám Azure sadu automaticky spravovaných přihlašovacích údajů. Tato pověření lze použít k ověření jakékoli služby, která podporuje ověřování služby Active Directory. Použití spravovaných identit nevyžaduje ukládání přihlašovacích údajů do kódových nebo konfiguračních souborů.

Další informace najdete v tématu [Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Ovladač systému souborů Objektů blob Azure

Aplikace Apache Hadoop nativně očekávají, že budou číst a zapisovat data z místního úložiště disků. Ovladač systému souborů Hadoop, jako je ABFS, umožňuje aplikacím Hadoop pracovat s cloudovým úložištěm emulací běžných operací systému souborů Hadoop. Ovladač převede tyto příkazy přijaté z aplikace na operace, které rozumí skutečná platforma cloudového úložiště.

Dříve ovladač souborového systému Hadoop převedl všechny operace systému souborů na volání rozhraní REST azure na straně klienta a pak vyvolal rozhraní REST API. Tento převod na straně klienta však za následek více volání rozhraní REST API pro jednu operaci systému souborů, jako je přejmenování souboru. ABFS přesunul některé logiky systému souborů Hadoop ze strany klienta na stranu serveru. Rozhraní API Azure Data Lake Storage Gen2 teď běží paralelně s rozhraním BLOB API. Tato migrace zlepšuje výkon, protože nyní běžné operace systému souborů Hadoop lze provést s jedním voláním rozhraní REST API.

Další informace najdete [v tématu ovladač souborového systému objektů blob Azure (ABFS): Vyhrazený ovladač úložiště Azure pro Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schéma URI pro úložiště datových jezer Azure Gen 2 

Azure Data Lake Storage Gen2 používá nové schéma URI pro přístup k souborům v Azure Storage z HDInsightu:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma URI poskytuje přístup šifrovaný s ssl.

`<FILE_SYSTEM_NAME>`identifikuje cestu systému souborů Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identifikuje název účtu Úložiště Azure. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>`je název cesty HDFS souboru nebo adresáře.

Pokud nejsou `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` zadány hodnoty pro a nejsou zadány, použije se výchozí systém souborů. Pro soubory ve výchozím systému souborů použijte relativní cestu nebo absolutní cestu. Například `hadoop-mapreduce-examples.jar` soubor, který je dodáván s clustery HDInsight, může být odkazován pomocí jedné z následujících cest:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Název souboru `hadoop-examples.jar` je v clusterech HDInsight verze 2.1 a 1.6. Při práci se soubory mimo HDInsight zjistíte, že většina nástrojů nerozpozná formát ABFS, ale místo toho `example/jars/hadoop-mapreduce-examples.jar`očekává základní formát cesty, například .

Další informace najdete [v tématu Použití identifikátoru URI úložiště datového jezera Azure.](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)

## <a name="azure-storage"></a>Azure Storage

Azure Storage je robustní řešení úložiště pro obecné účely, které se bezproblémově integruje s HDInsightem. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní HDFS může úplná sada součástí v HDInsight pracovat přímo na strukturovaných nebo nestrukturovaných datech uložených jako objekty BLOB.

Doporučujeme použít samostatné kontejnery úložiště pro výchozí úložiště clusteru a obchodní data, izolovat protokoly HDInsight a dočasné soubory z vlastních obchodních dat. Doporučujeme také po každém použití smazat výchozí kontejner objektů blob, který obsahuje protokoly aplikací a systému, aby se snížily náklady na úložiště. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Pokud se rozhodnete zabezpečit svůj účet úložiště pomocí **omezení brány firewall a virtuálních sítí** ve vybraných **sítích**, nezapomeňte povolit výjimku **Povolit důvěryhodné služby Microsoftu...** aby hdinsight měl přístup k vašemu účtu úložiště.

### <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram poskytuje abstraktní zobrazení architektury HDInsight služby Azure Storage:

![Architektura úložiště HDInsight](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Architektura úložiště HDInsight")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Prostřednictvím HDInsightu můžete také přistupovat k datům ve službě Azure Storage. Syntaxe je následující:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Při používání účtu Azure Storage s clustery HDInsight zvažte následující principy:

* **Kontejnery v účtech úložiště, které jsou připojeny ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou během vytváření přidružené  ke clusteru, máte plný přístup k objektům blob v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které *nejsou* připojené ke clusteru:** Máte oprávnění jen pro čtení k objektům BLOB v kontejnerech.
  
  > [!NOTE]  
  > Veřejné kontejnery umožňují získat seznam všech objektů BLOB, které jsou k dispozici v tomto kontejneru a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace naleznete v [tématu Správa anonymního přístupu pro čtení ke kontejnerům a objektům BLOB](../storage/blobs/storage-manage-access-to-resources.md).

* **Soukromé kontejnery v účtech úložiště, které *nejsou* připojené ke clusteru:** K objektům BLOB v kontejnerech nelze přistupovat, pokud nedefinujete účet úložiště při odesílání úloh WebHCat. 

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. Ve výchozím nastavení používá HDInsight účty úložiště definované v souboru core-site.xml. Toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Několik úloh WebHCat, včetně Apache Hive, MapReduce, Apache Hadoop streaming a Apache Pig, může mít s sebou popis účtů úložiště a metadat. (To platí v současné době pro Pig s účty úložiště, ale ne pro metadata.) Další informace naleznete [v tématu Použití clusteru HDInsight s alternativními účty úložiště a metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů blob ukládají data jako dvojice klíč/hodnota a nemají žádnou hierarchii adresářů. Název klíče však může obsahovat znak lomítka ( / ), aby vypadal, jako by byl soubor uložen v adresářové struktuře. Klíč objektu blob může být `input/log1.txt`například . Neexistuje `input` žádný skutečný adresář, ale kvůli znaku lomítka v názvu klíče klíč vypadá jako cesta k souboru.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Výhody služby Azure Storage
Výpočetní clustery a prostředky úložiště, které nejsou společně přiděleny, mají předpokládané náklady na výkon. Tyto náklady jsou zmírněny způsobem, jakým se vytvoří výpočetní clustery v blízkosti prostředků účtu úložiště v oblasti Azure. V této oblasti výpočetní uzly můžete efektivně přistupovat k datům přes vysokorychlostní sítě uvnitř Azure Storage.

Když ukládáte data ve službě Azure Storage místo HDFS, získáte několik výhod:

* **Opakované použití dat a sdílení:** data v HDFS se nachází uvnitř výpočetního clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. K datům ve službě Azure Storage lze naopak přistupovat prostřednictvím rozhraní API HDFS nebo rozhraní REST rozhraní API úložiště objektů blob. Z důvodu tohoto uspořádání větší sadu aplikací (včetně jiných clusterů HDInsight) a nástroje lze použít k výrobě a využití dat.

* **Archivace dat:** Když jsou data uložená ve službě Azure Storage, clustery HDInsight používané pro výpočty lze bezpečně odstranit bez ztráty uživatelských dat.

* **Náklady na ukládání dat:** Dlouhodobé ukládání dat v dfs je nákladnější než ukládání dat ve službě Azure Storage, protože náklady na výpočetní cluster jsou vyšší než náklady na Azure Storage. Také vzhledem k tomu, že data nemusí být znovu načtena pro každou generaci výpočetního clusteru, šetříte také náklady na načítání dat.

* **Elastické škálování:** I když HDFS poskytuje škálovaný systém souborů, škála se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna škálování může být složitější než spoléhání se na možnosti elastického škálování, které získáte automaticky ve službě Azure Storage.

* **Geografická replikace:** Vaše Azure Storage může být geograficky replikované. Přestože geografická replikace poskytuje geografické obnovení a redundanci dat, převzetí služeb při selhání do geograficky replikovaného umístění vážně ovlivňuje výkon a může vám vzniknout další náklady. Takže zvolte geografickou replikaci opatrně a pouze v případě, že hodnota dat odůvodňuje dodatečné náklady.

Některé úlohy a balíčky MapReduce mohou vytvářet průběžné výsledky, které byste nechtěli ukládat ve službě Azure Storage. V takovém případě můžete zvolit uložení dat v místním HDFS. HDInsight používá dfs pro několik z těchto zprostředkujících výsledků v úlohách Hive a dalších procesech.

> [!NOTE]  
> Většina příkazů HDFS (například `ls`, `copyFromLocal`, a `mkdir`) pracovat podle očekávání v Azure Storage. Pouze příkazy, které jsou specifické pro nativní implementaci HDFS (která `fschk` `dfsadmin`se označuje jako DFS), jako jsou například a , zobrazit různé chování ve službě Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Přehled Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 je celopodnikové úložiště hyperškálování pro analytické úlohy velkých objemů dat. Pomocí Azure Data Lake můžete zachytit data libovolné velikosti, typu a rychlosti ingestování na jednom místě pro provozní a průzkumné analýzy.

Přístup k datům Lake Storage Gen1 z Hadoopu (k dispozici v clusteru HDInsight) pomocí rozhraní REST API kompatibilních s WebHDFS. Data Lake Storage Gen1 je navržen tak, aby umožnil analýzu uložených dat a je vyladěn pro výkon ve scénářích analýzy dat. Po vybalení obsahuje funkce, které jsou nezbytné pro případy použití v reálném světě. Mezi tyto funkce patří zabezpečení, možnosti správy, škálovatelnost, spolehlivost a dostupnost.

Další informace o Azure Data Lake Storage Gen1 najdete v podrobném [přehledu Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Mezi klíčové funkce data Lake Storage Gen1 patří následující.

### <a name="compatibility-with-hadoop"></a>Kompatibilita s Hadoopem

Data Lake Storage Gen1 je souborový systém Apache Hadoop, který je kompatibilní s HDFS a pracuje s ekosystémem Hadoop.  Vaše stávající aplikace nebo služby HDInsight, které používají rozhraní WebHDFS API, se mohou snadno integrovat s programem Data Lake Storage Gen1. Data Lake Storage Gen1 také zpřístupňuje rozhraní REST kompatibilní s WebHDFS pro aplikace.

Data uložená v data lake storage gen1 lze snadno analyzovat pomocí analytických rámců Hadoop, jako je MapReduce nebo Hive. Clustery Azure HDInsight můžou být zřízená a nakonfigurovaná tak, aby měla přímý přístup k datům uloženým v Gen1 úložiště datového jezera.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodný pro ukládání různých dat pro analýzy. Neukládá omezení velikosti účtů, velikosti souborů nebo množství dat, která mohou být uložena v datovém jezeře. Jednotlivé soubory se mohou pohybovat v rozsahu od kilobajtů až po petabajty, takže data Lake Storage Gen1 je skvělou volbou pro ukládání jakéhokoli typu dat. Data jsou uložena trvale vytvořením více kopií a neexistují žádná omezení, jak dlouho mohou být data uložena v datovém jezeře.

### <a name="performance-tuning-for-big-data-analytics"></a>Ladění výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je navržen tak, aby spouštěl rozsáhlé analytické systémy, které vyžadují obrovskou propustnost pro dotazování a analýzu velkého množství dat. Datové jezero šíří části souboru přes několik jednotlivých serverů úložiště. Při analýze dat toto nastavení zlepšuje propustnost čtení při paralelním čtení souboru.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Připravenost pro podniky: Vysoce dostupná a bezpečná

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost. Datové prostředky jsou uloženy trvale: redundantní kopie chrání před neočekávanými chybami. Podniky mohou používat Data Lake Storage Gen1 ve svých řešeních jako důležitou součást své stávající datové platformy.

Data Lake Storage Gen1 také poskytuje podnikové zabezpečení pro uložená data. Další informace najdete [v tématu Zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexibilní datové struktury

Data Lake Storage Gen1 můžete ukládat všechna data v jeho nativním formátu, jak je, bez nutnosti předchozí transformace. Úložiště datového jezera Gen1 nevyžaduje, aby bylo před načtením dat definováno schéma. Individuální analytický rámec interpretuje data a definuje schéma v době analýzy. Vzhledem k tomu, že může ukládat soubory libovolných velikostí a formátů, může Data Lake Storage Gen1 zpracovávat strukturovaná, polostrukturovaná a nestrukturovaná data.

Kontejnery Data Lake Storage Gen1 pro data jsou v podstatě složky a soubory. Na uložených datech pracujete pomocí sad SDK, portálu Azure a Azure PowerShellu. Pokud vložíte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete uložit libovolný typ dat. Data Lake Storage Gen1 neprovádí žádné zvláštní zpracování dat na základě typu dat, které ukládá.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Zabezpečení dat v úložišti datového jezera Gen1
Data Lake Storage Gen1 používá Azure Active Directory pro ověřování a používá seznamy řízení přístupu (ACLs) ke správě přístupu k vašim datům.

| **Funkce** | **Popis** |
| --- | --- |
| Ověřování |Data Lake Storage Gen1 se integruje s Azure Active Directory (Azure AD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Díky integraci využívá Data Lake Storage Gen1 všechny funkce Azure AD. Mezi tyto funkce patří vícefaktorové ověřování, podmíněný přístup, řízení přístupu na základě rolí, monitorování využití aplikací, monitorování zabezpečení a upozorňování a tak dále. Data Lake Storage Gen1 podporuje protokol OAuth 2.0 pro ověřování v rámci rozhraní REST. Viz [Ověřování v rámci Azure Data Lake Storage Gen1 pomocí Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu podporou oprávnění stylu POSIX, která jsou vystavena protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o tom, jak akly fungují v kontextu Data Lake Storage Gen1, naleznete [v tématu Řízení přístupu v souboru Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 také poskytuje šifrování pro data, která jsou uložena v účtu. Nastavení šifrování zadáte při vytváření účtu Data Lake Storage Gen1. Můžete zvolit šifrování dat nebo zvolit žádné šifrování. Další informace naleznete [v tématu Šifrování v úložišti datového jezera Gen1](../data-lake-store/data-lake-store-encryption.md). Pokyny, jak poskytnout konfiguraci související s šifrováním, najdete v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](../data-lake-store/data-lake-store-get-started-portal.md). |

Další informace o zabezpečení dat v gen1 úložiště datových jezer, najdete [v tématu Zabezpečení dat uložených v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikace, které jsou kompatibilní s Date Lake Storage Gen1
Data Lake Storage Gen1 je kompatibilní s většinou open-source komponent v ekosystému Hadoop. Výborně se taky integruje s jinými službami Azure.  Další informace o tom, jak lze funkci Data Lake Storage Gen1 používat jak s open source komponentami, tak s dalšími službami Azure.

* Seznam open [source aplikací pro velké objemy dat, které spolupracují s Azure Data Lake Storage Gen1,](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) najdete v tématu Open source aplikace pro velké objemy dat, které spolupracují s aplikací Data Lake Storage Gen1.
* V [tématu Integrace Azure Data Lake Storage Gen1 s jinými službami Azure,](../data-lake-store/data-lake-store-integrate-with-other-services.md) kde zjistíte, jak používat Data Lake Storage Gen1 s jinými službami Azure, abyste umožnili širší škálu scénářů.
* V [článku Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](../data-lake-store/data-lake-store-data-scenarios.md) se dozvíte, jak používat Data Lake Storage Gen1 ve scénářích, jako je ingestování dat, zpracování dat, stahování dat a vizualizace dat.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Datový lake storage Gen1 souborový systém (adl://)
V prostředích Hadoop (k dispozici v clusteru HDInsight) můžete přistupovat k Data Lake Storage Gen1 prostřednictvím nového systému souborů AzureDataLakeFilesystem (adl://). Výkon aplikací a služeb, které používají adl:// lze optimalizovat způsobem, který není aktuálně k dispozici v webHDFS. V důsledku toho při použití Data Lake Storage Gen1 získáte flexibilitu buď využít nejlepší výkon pomocí doporučené adl:// nebo udržovat existující kód nadále používat rozhraní API WebHDFS přímo. Azure HDInsight plně využívá azuredatalakefilesystém poskytovat nejlepší výkon na datového jezera Storage Gen1.

Přístup k datům v aplikaci Data Lake Storage Gen1 pomocí následujících možností:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Další informace o tom, jak získat přístup k datům v gen1 úložiště datového jezera, naleznete v [tématu Akce dostupné na uložených datech](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Další kroky

* [Úvod do úložiště datových jezer Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
