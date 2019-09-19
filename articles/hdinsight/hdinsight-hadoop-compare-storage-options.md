---
title: Porovnání možností úložiště pro použití s clustery Azure HDInsight
description: Poskytuje přehled o typech úložišť a jejich spolupráci s Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: d036e56a4ccf826ccd19fb7424b7b76568839b23
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104539"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Porovnání možností úložiště pro použití s clustery Azure HDInsight

Při vytváření clusterů HDInsight si můžete vybrat mezi několika různými službami Azure Storage:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Tento článek obsahuje přehled těchto typů úložišť a jejich jedinečných funkcí.

Následující tabulka shrnuje Azure Storage služby, které jsou podporovány v různých verzích služby HDInsight:

| Služba úložiště | Typ účtu | Typ oboru názvů | Podporované služby | Podporované úrovně výkonu | Podporované úrovně přístupu | Verze služby HDInsight | Typ clusteru |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Obecné účely v2 | Hierarchický (systém souborů) | Blob | Standard | Horká, studená, archivní | 3.6 + | Vše |
|Azure Storage| Obecné účely v2 | Object | Blob | Standard | Horká, studená, archivní | 3.6 + | Vše |
|Azure Storage| Obecné účely v1 | Object | Blob | Standard | Není k dispozici | Vše | Vše |
|Azure Storage| Blob Storage * * | Object | Objekt blob bloku | Standard | Horká, studená, archivní | Vše | Vše |
|Azure Data Lake Storage Gen1| Není k dispozici | Hierarchický (systém souborů) | Není k dispozici | Není k dispozici | Není k dispozici | jenom 3,6 | Všechny kromě adaptérů HBA |

\* * Pro clustery HDInsight může být pouze sekundární účty úložiště typu BlobStorage a objekt blob stránky není podporovanou možností úložiště.

Další informace o Azure Storage typech účtů najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md) .

Další informace o úrovních přístupu Azure Storage najdete v tématu úložiště [objektů BLOB v Azure: Vrstvy úložiště úrovně Premium (Preview), horké, studené a archivní](../storage/blobs/storage-blob-storage-tiers.md)

Cluster můžete vytvořit pomocí různých kombinací služeb pro primární a volitelné sekundární úložiště. Následující tabulka shrnuje konfigurace úložiště clusteru, které jsou aktuálně podporované v HDInsight:

| Verze služby HDInsight | Primární úložiště | Sekundární úložiště | Podporováno |
|---|---|---|---|
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Ano |
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Data Lake Storage Gen2 | Ne |
| 3,6 & 4,0 | Pro obecné účely V1, Pro obecné účely v2 | Data Lake Storage Gen1 | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Ano |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Ne |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ano |
| 3.6 | Data Lake Storage Gen1 | Pro obecné účely V1, Pro obecné účely v2, BlobStorage (objekty blob bloku) | Ano |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Ne |
| 4.0 | Data Lake Storage Gen1 | Any | Ne |

\* = Může to být jeden nebo několik účtů Data Lake Storage Gen2, pokud jsou všechny nastavené tak, aby používaly stejnou spravovanou identitu pro přístup k clusteru.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Použití Azure Data Lake Storage Gen2 s Apache Hadoop v Azure HDInsight

Azure Data Lake Storage Gen2 přebírá základní funkce z Azure Data Lake Storage Gen1 a integruje je do úložiště objektů BLOB v Azure. Mezi tyto funkce patří systém souborů, který je kompatibilní se systémy Hadoop, Azure Active Directory (Azure AD) a seznamy řízení přístupu (ACL) založenými na POSIX. Tato kombinace vám umožní využít výkon Azure Data Lake Storage Gen1 a zároveň využívat správu životního cyklu služby Blob Storage pomocí vrstev a dat.

Další informace o Azure Data Lake Storage Gen2 najdete v tématu [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Základní funkce Azure Data Lake Storage Gen2

* **Přístup kompatibilní se systémem Hadoop:** V Azure Data Lake Storage Gen2 můžete spravovat data a přistupovat k nim stejným způsobem jako v systém souborů DFS (Distributed File System) Hadoop (HDFS). Ovladač systému souborů objektů BLOB v Azure (ABFS) je k dispozici ve všech Apache Hadoop prostředích, včetně Azure HDInsight a Azure Databricks. Pro přístup k datům uloženým v Data Lake Storage Gen2 použijte ABFS.

* **Nadmnožina oprávnění POSIX:** Model zabezpečení pro Data Lake Gen2 podporuje oprávnění ACL a POSIX spolu s některými dalšími podrobnostmi, které jsou specifické pro Data Lake Storage Gen2. Nastavení lze nakonfigurovat prostřednictvím nástrojů pro správu nebo architektury, jako jsou Apache Hive a Apache Spark.

* **Efektivita nákladů:** Data Lake Storage Gen2 nabízí s nízkými náklady kapacitu a transakce úložiště. Funkce, jako je životní cyklus úložiště objektů BLOB v Azure, vám pomůžou snížit náklady úpravou fakturačních sazeb během jejího životního cyklu.

* **Kompatibilita s nástroji, rozhraními a aplikacemi služby Blob Storage:** Data Lake Storage Gen2 i nadále pracují se škálou nástrojů, platforem a aplikací pro úložiště objektů BLOB.

* **Optimalizovaný ovladač:** Ovladač ABFS se optimalizuje speciálně pro analýzy velkých objemů dat. Odpovídající rozhraní REST API se procházejí prostřednictvím koncového bodu systému souborů DFS (Distributed File System), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Co je nového pro Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Spravované identity pro zabezpečený přístup k souborům

Azure HDInsight používá spravované identity k zabezpečení přístupu clusteru k souborům v Azure Data Lake Storage Gen2. Spravované identity jsou funkcí Azure Active Directory, která poskytuje služby Azure se sadou automaticky spravovaných přihlašovacích údajů. Pomocí těchto přihlašovacích údajů se můžete ověřit u jakékoli služby, která podporuje ověřování pomocí služby Active Directory. Použití spravovaných identit nevyžaduje ukládání přihlašovacích údajů do kódu nebo konfiguračních souborů.

Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Ovladač systému souborů Azure Blob

Apache Hadoop aplikace nativně očekávají čtení a zápis dat z diskového úložiště na místním disku. Ovladač systému souborů Hadoop, jako je ABFS, umožňuje aplikacím Hadoop pracovat s cloudovým úložištěm, a to tak, že emuluje běžné operace se systémem souborů Hadoop. Ovladač převede tyto příkazy přijaté z aplikace na operace, které rozumí skutečná platforma cloudového úložiště.

Dříve ovladač systému souborů Hadoop převedl všechny operace systému souborů na Azure Storage REST API volání na straně klienta a pak vyvolal REST API. Tento převod na straně klienta však má za následek více REST API volání pro jednu operaci systému souborů, jako je přejmenování souboru. ABFS přesunula část logiky systému souborů Hadoop ze strany klienta na stranu serveru. Rozhraní Azure Data Lake Storage Gen2 API teď běží paralelně s rozhraním API objektů BLOB. Tato migrace zvyšuje výkon, protože teď můžete provádět běžné operace se systémem souborů Hadoop pomocí jednoho REST API volání.

Další informace najdete v tématu [ovladač systému souborů objektů BLOB v Azure (ABFS): Vyhrazený ovladač Azure Storage pro Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Schéma identifikátoru URI pro Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 používá nové schéma identifikátoru URI pro přístup k souborům v Azure Storage ze služby HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schéma identifikátoru URI poskytuje přístup šifrovaný protokolem`abfss://` SSL (předponu) a nezašifrovaný přístup (`abfs://` předpona). Používejte `abfss` všude tam, kde je to možné, i když přistupujete k datům umístěným uvnitř stejné oblasti v Azure.

`<FILE_SYSTEM_NAME>`Určuje cestu Data Lake Storage Gen2 systému souborů.

`<ACCOUNT_NAME>`Určuje název Azure Storage účtu. Vyžaduje se plně kvalifikovaný název domény (FQDN).

`<PATH>`je název cesty HDFS souboru nebo adresáře.

Pokud nejsou zadány `<ACCOUNT_NAME>` hodnoty pro `<FILE_SYSTEM_NAME>` a, použije se výchozí systém souborů. Pro soubory ve výchozím systému souborů použijte relativní cestu nebo absolutní cestu. Například `hadoop-mapreduce-examples.jar` soubor, který je součástí clusterů HDInsight, lze odkazovat pomocí jedné z následujících cest:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Název souboru je `hadoop-examples.jar` v clusterech HDInsight verze 2,1 a 1,6. Když pracujete se soubory mimo HDInsight, zjistíte, že většina nástrojů nerozpoznala formát ABFS, ale očekává jako základní formát cesty, například `example/jars/hadoop-mapreduce-examples.jar`.

Další informace najdete v tématu [použití identifikátoru URI Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure Storage je robustní řešení úložiště pro obecné účely, které se bezproblémově integruje se službou HDInsight. HDInsight může jako výchozí systém souborů pro cluster používat kontejner objektů blob ve službě Azure Storage. Prostřednictvím rozhraní HDFS může úplná sada komponent ve službě HDInsight pracovat přímo se strukturovanými nebo nestrukturovanými daty uloženými jako objekty blob.

Doporučujeme používat samostatné kontejnery úložiště pro vaše výchozí úložiště clusteru a podniková data k izolaci protokolů HDInsight a dočasných souborů z vašich vlastních obchodních dat. Doporučujeme také odstranit výchozí kontejner objektů blob, který obsahuje protokoly aplikací a systému, a to po každém použití ke snížení nákladů na úložiště. Než odstraníte kontejner, nezapomeňte tyto protokoly načíst.

Pokud se rozhodnete zabezpečit svůj účet úložiště s omezeními **bran firewall a virtuální sítě** u **vybraných sítí**, ujistěte se, že je **povolená výjimka Povolit důvěryhodné služby Microsoftu...** , aby HDInsight mohla získat přístup k vašemu úložišti. zohledňují.

### <a name="hdinsight-storage-architecture"></a>Architektura úložiště HDInsight

Následující diagram nabízí abstraktní zobrazení architektury HDInsight Azure Storage:

![Architektura HDInsight Storage](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "Architektura HDInsight Storage")

Služba HDInsight poskytuje přístup do systému souborů DFS, který je místně připojen k výpočetním uzlům. Tento systém souborů je přístupný pomocí plně kvalifikovaného identifikátoru URI, například:

    hdfs://<namenodehost>/<path>

Prostřednictvím HDInsight můžete také přistupovat k datům v Azure Storage. Syntaxe je následující:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Při použití účtu Azure Storage s clustery HDInsight Vezměte v úvahu následující principy:

* **Kontejnery v účtech úložiště, které jsou připojené ke clusteru:** Vzhledem k tomu, že název účtu a klíč jsou během vytváření spojeny s clusterem, máte plný přístup k objektům blob v těchto kontejnerech.

* **Veřejné kontejnery nebo veřejné objekty BLOB v účtech úložiště, které *nejsou připojené ke* clusteru:** K objektům blob v kontejnerech máte oprávnění jen pro čtení.
  
  > [!NOTE]  
  > Veřejné kontejnery umožňují získat seznam všech objektů blob, které jsou v daném kontejneru k dispozici, a získat metadata kontejneru. Veřejné objekty blob umožňují přístup k objektům blob jenom v případě, že znáte přesnou adresu URL. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](../storage/blobs/storage-manage-access-to-resources.md).

* **Privátní kontejnery v účtech úložiště, které *nejsou připojené ke* clusteru:** Nemůžete získat přístup k objektům blob v kontejnerech, pokud nedefinujete účet úložiště při odeslání úloh WebHCat. 

Účty úložiště, které se definují v procesu vytváření a jejich klíče jsou uloženy v %HADOOP_HOME%/conf/core-site.xml na uzlech clusteru. Ve výchozím nastavení používá HDInsight účty úložiště definované v souboru Core-site. XML. Toto nastavení můžete upravit pomocí [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Několik úloh WebHCat, včetně Apache Hive, MapReduce, Apache Hadoop streaming a Apache prasete, může obsahovat popis účtů úložiště a metadat. (V současné době platí pro prasete s účty úložiště, ale ne pro metadata.) Další informace najdete v tématu [použití clusteru HDInsight s alternativním účtem úložiště a metaúložiště](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Objekty blob lze použít pro strukturovaná i nestrukturovaná data. Kontejnery objektů BLOB ukládají data jako páry klíč/hodnota a nemají žádnou hierarchii adresářů. Název klíče ale může obsahovat lomítko (/), aby se zobrazil jako při uložení souboru do adresářové struktury. Klíč objektu BLOB může být `input/log1.txt`například. Neexistuje žádný `input` skutečný adresář, ale z důvodu znaku lomítka v názvu klíče vypadá klíč jako cesta k souboru.

### <a id="benefits"></a>Výhody služby Azure Storage
Výpočetní clustery a prostředky úložiště, které nejsou společně umístěny, mají předpokládané náklady na výkon. Tyto náklady jsou zmírněny způsobem, kterým se výpočetní clustery vytvářejí blízko prostředků účtu úložiště v oblasti Azure. V této oblasti můžou výpočetní uzly efektivně přistupovat k datům přes vysokorychlostní síť v rámci Azure Storage.

Když ukládáte data v Azure Storage místo HDFS, získáte několik výhod:

* **Opakované použití a sdílení dat:** Data v HDFS se nachází ve výpočetním clusteru. Jenom aplikace, které mají přístup k výpočetnímu clusteru, můžou používat data pomocí rozhraní API HDFS. Data v Azure Storage naproti tomu mají k dispozici prostřednictvím rozhraní API HDFS nebo rozhraní REST API služby Blob Storage. Z důvodu tohoto uspořádání lze k vytváření a využívání dat použít větší sadu aplikací (včetně dalších clusterů HDInsight) a nástroje.

* **Archivace dat:** Když jsou data uložená v Azure Storage, clustery HDInsight používané pro výpočty se dají bezpečně odstranit, aniž by došlo ke ztrátě uživatelských dat.

* **Náklady na úložiště dat:** Ukládání dat v systému souborů DFS je z dlouhodobého hlediska dražší než ukládání dat v Azure Storage, protože náklady na výpočetní cluster jsou vyšší než náklady na Azure Storage. I když se data pro každou generaci výpočetních clusterů nemusí znovu načíst, ukládají se i náklady na načítání dat.

* **Elastické škálování na více instancí:** I když HDFS poskytuje systém souborů se škálováním na více systémů, měřítko se určuje podle počtu uzlů, které vytvoříte pro svůj cluster. Změna měřítka může být složitější než spoléhání na možnosti elastického škálování, které se automaticky zobrazí v Azure Storage.

* **Geografická replikace:** Vaše Azure Storage může být geograficky replikované. I když geografická replikace poskytuje geografické obnovení a redundanci dat, převzetí služeb při selhání geograficky replikovaným umístěním má vážně vliv na váš výkon a může to mít za následek další náklady. Proto vyberte geografickou replikaci obezřetně a jenom v případě, že hodnota dat odůvodňuje dodatečné náklady.

Některé úlohy a balíčky MapReduce můžou vytvořit mezilehlé výsledky, které byste nemuseli ukládat v Azure Storage. V takovém případě se můžete rozhodnout ukládat data do místního HDFS. HDInsight používá DFS pro několik těchto mezilehlých výsledků v úlohách podregistru a dalších procesech.

> [!NOTE]  
> Většina příkazů HDFS (například `ls` `copyFromLocal`,, a `mkdir`) pracuje podle očekávání v Azure Storage. Pouze příkazy, které jsou specifické pro nativní implementaci HDFS (což se označuje jako DFS), `fschk` jako například a `dfsadmin`, zobrazují v Azure Storage různé chování.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Přehled Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 je škálovatelné úložiště s velkými objemy dat v podnikové síti pro analytické úlohy s velkým objemem dat. Pomocí Azure Data Lake můžete zachytit data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a průzkumné analýzy.

Přístup k Data Lake Storage Gen1 z Hadoop (dostupné s clusterem HDInsight) pomocí rozhraní REST API kompatibilního s WebHDFS. Data Lake Storage Gen1 je navržená tak, aby povolovala analýzy uložených dat a vyladěna se na výkon ve scénářích analýzy dat. Mimo pole zahrnuje možnosti, které jsou nezbytné pro reálné podnikové případy použití. Mezi tyto možnosti patří zabezpečení, možnosti správy, škálovatelnost, spolehlivost a dostupnost.

Další informace o Azure Data Lake Storage Gen1 najdete v podrobném [přehledu Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Mezi klíčové funkce Data Lake Storage Gen1 patří následující.

### <a name="compatibility-with-hadoop"></a>Kompatibilita se systémem Hadoop

Data Lake Storage Gen1 je Apache Hadoop systém souborů, který je kompatibilní se službou HDFS a pracuje s ekosystémem Hadoop.  Stávající aplikace nebo služby HDInsight, které používají rozhraní WebHDFS API, se můžou snadno integrovat s Data Lake Storage Gen1. Data Lake Storage Gen1 také zpřístupňuje rozhraní REST kompatibilní s WebHDFS pro aplikace.

Data uložená v Data Lake Storage Gen1 lze snadno analyzovat pomocí analytických rozhraní Hadoop, jako je MapReduce nebo podregistr. Clustery Azure HDInsight je možné zřídit a nakonfigurovat tak, aby přímý přístup k datům uloženým v Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Neomezené úložiště, petabajtové soubory

Data Lake Storage Gen1 poskytuje neomezené úložiště a je vhodný pro ukládání nejrůznějších dat pro analýzu. Neomezuje velikost účtů, velikosti souborů nebo množství dat, která se dají ukládat do data Lake. Jednotlivé soubory můžou být v rozsahu od kilobajtů až po petabajty, což Data Lake Storage Gen1 skvělou volbou pro ukládání libovolného typu dat. Data se ukládají trvale pomocí několika kopií a neexistují žádná omezení, jak dlouho je možné data do data Lake ukládat.

### <a name="performance-tuning-for-big-data-analytics"></a>Optimalizace výkonu pro analýzu velkých objemů dat

Data Lake Storage Gen1 je postavená na spouštění rozsáhlých analytických systémů, které vyžadují velkou propustnost pro dotazování a analýzu velkých objemů dat. Data Lake rozšíří části souboru na několik jednotlivých úložných serverů. Při analýze dat tato nastavení zvyšují propustnost čtení, pokud je soubor paralelně čten.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Připravenost pro podniky: Vysoce dostupné a zabezpečené

Data Lake Storage Gen1 poskytuje standardní dostupnost a spolehlivost v oboru. Datové assety jsou uložené trvale: redundantní kopie chrání před neočekávanými chybami. Podniky můžou ve svých řešeních používat Data Lake Storage Gen1 jako důležitou součást své stávající datové platformy.

Data Lake Storage Gen1 taky poskytuje zabezpečení uložených dat na podnikové úrovni. Další informace najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexibilní datové struktury

Data Lake Storage Gen1 může ukládat jakákoli data v nativním formátu, jak je, a to bez nutnosti předchozí transformace. Data Lake Storage Gen1 nevyžaduje, aby bylo definováno schéma před načtením dat. Jednotlivé analytické rozhraní interpretuje data a definuje schéma v době analýzy. Protože může ukládat soubory libovolných velikostí a formátů, Data Lake Storage Gen1 mohou zpracovávat strukturovaná, částečně strukturovaných a nestrukturovaná data.

Data Lake Storage Gen1 kontejnery pro data jsou v podstatě složky a soubory. Pomocí sad SDK, Azure Portal a Azure PowerShell pracujete s uloženými daty. Pokud zadáte data do úložiště pomocí těchto rozhraní a příslušných kontejnerů, můžete uložit jakýkoli typ dat. Data Lake Storage Gen1 neprovádí žádné speciální zpracování dat na základě typu uložených dat.

## <a name="DataLakeStoreSecurity"></a>Zabezpečení dat v Data Lake Storage Gen1
Data Lake Storage Gen1 používá pro ověřování Azure Active Directory a ke správě přístupu k datům používá seznamy řízení přístupu (ACL).

| **Funkce** | **Popis** |
| --- | --- |
| Ověřování |Data Lake Storage Gen1 se integruje s Azure Active Directory (Azure AD) pro správu identit a přístupu pro všechna data uložená v Data Lake Storage Gen1. Kvůli integraci Data Lake Storage Gen1 výhody ze všech funkcí služby Azure AD. Mezi tyto funkce patří vícefaktorové ověřování, podmíněný přístup, řízení přístupu na základě role, monitorování využití aplikací, monitorování zabezpečení a upozorňování atd. Data Lake Storage Gen1 podporuje protokol OAuth 2,0 pro ověřování v rámci rozhraní REST. Viz [ověřování v rámci Azure Data Lake Storage Gen1 pomocí Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Řízení přístupu |Data Lake Storage Gen1 poskytuje řízení přístupu podporou oprávnění ve stylu POSIX, která jsou vystavená protokolem WebHDFS. Seznamy ACL je možné povolit pro kořenovou složku, podsložky a jednotlivé soubory. Další informace o fungování seznamů ACL v kontextu Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Šifrování |Data Lake Storage Gen1 taky poskytuje šifrování pro data, která jsou uložená v účtu. Nastavení šifrování určíte při vytváření účtu Data Lake Storage Gen1. Můžete se rozhodnout, že vaše data budou zašifrovaná nebo se můžou rozhodnout bez šifrování. Další informace najdete v tématu [šifrování v Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Pokyny k poskytnutí konfigurace související s šifrováním najdete v tématu Začínáme [s Azure Data Lake Storage Gen1 pomocí Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Další informace o zabezpečení dat v Data Lake Storage Gen1 najdete v tématu [zabezpečení dat uložených v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Aplikace, které jsou kompatibilní s Data Lake Storage Gen1
Data Lake Storage Gen1 je kompatibilní s většinou open source komponent v ekosystému Hadoop. Výborně se taky integruje s jinými službami Azure.  Další informace o tom, jak Data Lake Storage Gen1 lze používat s komponentami Open Source i s dalšími službami Azure, najdete na následujících odkazech.

* Seznam open source aplikací, které spolupracují s Data Lake Storage Gen1, najdete v tématu [Open Source aplikace s velkými objemy dat, které fungují s Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) .
* Další informace o tom, jak používat Data Lake Storage Gen1 s ostatními službami Azure k zajištění širší škály scénářů, najdete v tématu věnovaném [integraci Azure Data Lake Storage Gen1 s dalšími službami Azure](../data-lake-store/data-lake-store-integrate-with-other-services.md) .
* Další informace o tom, jak používat Data Lake Storage Gen1 ve scénářích, jako je ingestování dat, zpracování dat, stahování dat a vizualizaci dat, najdete v tématu [použití Azure Data Lake Storage Gen1 pro požadavky na velké](../data-lake-store/data-lake-store-data-scenarios.md) objemy dat.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Systém souborů Data Lake Storage Gen1 (adl://)
V prostředích Hadoop (dostupné s clusterem HDInsight) máte přístup k Data Lake Storage Gen1 pomocí nového systému souborů AzureDataLakeFilesystem (adl://). Výkon aplikací a služeb, které používají adl://, je možné optimalizovat způsobem, který momentálně není v WebHDFS k dispozici. Výsledkem je, že když použijete Data Lake Storage Gen1, získáte flexibilitu, aby využívala nejlepší výkon pomocí doporučeného adl://u nebo zachovala existující kód tím, že budete nadále používat rozhraní WebHDFS API přímo. Azure HDInsight plně využívá výhod AzureDataLakeFilesystem k zajištění co nejlepšího výkonu pro Data Lake Storage Gen1.

K datům v Data Lake Storage Gen1 získáte přístup pomocí následujících kroků:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Další informace o tom, jak získat přístup k datům v Data Lake Storage Gen1, najdete v tématu [akce, které jsou k dispozici u uložených dat](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Další postup

* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Seznámení se službou Azure Storage](../storage/common/storage-introduction.md)
