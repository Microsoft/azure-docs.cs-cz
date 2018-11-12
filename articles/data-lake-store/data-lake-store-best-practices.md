---
title: Osvědčené postupy pro používání Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Podívejte se na osvědčené postupy o příjem dat, data zabezpečení a výkonu související s používáním Azure Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 2c7e624344605b24e78962ac2b6d23278c06c0cc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255144"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Doporučené postupy pro používání Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

V tomto článku se dozvíte o osvědčených postupech a důležité informace pro práci s Azure Data Lake Storage Gen1. Tento článek obsahuje informace týkající se zabezpečení, výkon, odolnost proti chybám a monitorování pro Data Lake Storage Gen1. Před Gen1 úložiště Data Lake byl složitý. práce se skutečně velkých objemů dat do služby, jako je Azure HDInsight. Jste měli sdílení dat napříč několika účty úložiště Blob tak, aby bylo možné dosáhnout petabajty úložiště a optimální výkon při, které se škálují. S Data Lake Storage Gen1 jsou odstraněna většina pevných limitů velikosti a výkonu. Existují však stále některé aspekty, které v tomto článku najdete tak, aby získáte nejlepší výkon s Data Lake Storage Gen1. 

## <a name="security-considerations"></a>Aspekty zabezpečení

Azure Data Lake Storage Gen1 nabídky POSIX ovládací prvky přístupu a podrobné auditování pro Azure Active Directory (Azure AD) uživatele, skupiny nebo instanční objekty. Tyto ovládací prvky přístupu je možné nastavit pro existující soubory a složky. Řízení přístupu lze také vytvořit výchozí hodnoty, které lze použít u nových souborů nebo složek. Pokud jsou oprávnění nastavena na existující složky a podřízených objektů, musí být rozšíří rekurzivně na každém objektu oprávnění. Pokud existuje velký počet souborů, šíření oprávnění může trvat dlouhou dobu. Doba trvání může být v rozsahu mezi objekty 30 – 50 zpracovaných za sekundu. Proto složky strukturu a uživatelské skupiny odpovídající plán. V opačném případě může způsobit neočekávané prodlevy a problémů při práci s vašimi daty. 

Předpokládejme, že máte složku s 100 000 podřízené objekty. Pokud budete postupovat dolní mez 30 objekty zpracovaných za sekundu, chcete-li aktualizovat oprávnění pro celou složku může trvat hodiny. Další informace o Data Lake Storage Gen1 seznamy řízení přístupu najdete na adrese [řízení přístupu v Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Za účelem vylepšení výkonu v přiřazení rekurzivně seznamy ACL můžete použít nástroj příkazového řádku Azure Data Lake. Nástroj vytvoří několik vláken a logiku navigace rekurzivní rychle použít seznamy ACL na miliony souborů. Nástroj je k dispozici pro systémy Linux a Windows a [dokumentaci](https://github.com/Azure/data-lake-adlstool) a [stáhne](https://aka.ms/adlstool-download) pro tento nástroj najdete na Githubu. Tyto stejné vylepšení výkonu se dá nastavit podle svých vlastních nástrojů napsané v Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) a [Java](data-lake-store-get-started-java-sdk.md) sady SDK.

### <a name="use-security-groups-versus-individual-users"></a>Pomocí skupin zabezpečení a jednotlivé uživatele 

Při práci s velkými objemy dat v Data Lake Storage Gen1, pravděpodobně objekt služby slouží k povolení služeb, jako je Azure HDInsight pro práci s daty. Ale může být případy, kdy jednotliví uživatelé potřebují přístup k datům stejně. V takových případech je nutné použít Azure Active Directory [skupiny zabezpečení](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) místo jednotlivým uživatelům přiřadí složek a souborů. 

Po přiřazení oprávnění skupiny zabezpečení, přidání nebo odebrání uživatele ze skupiny nevyžaduje žádné aktualizace Gen1 úložiště Data Lake. To také pomáhá zajistit, abyste nepřekročili limit [32 přístupu a výchozí seznamy ACL](../azure-subscription-service-limits.md#data-lake-store-limits) (to zahrnuje čtyři stylu POSIX seznamy ACL, které jsou vždy přidružené všech souborů a složek: [vlastnícího uživatele](data-lake-store-access-control.md#the-owning-user), [vlastnící skupina](data-lake-store-access-control.md#the-owning-group), [maska](data-lake-store-access-control.md#the-mask)a další).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny 

Jak je popsáno, když uživatelé potřebují přístup k Data Lake Storage Gen1, je nejvhodnější používat skupiny zabezpečení Azure Active Directory. Několik doporučených skupiny začít s může být **ReadOnlyUsers**, **WriteAccessUsers**, a **FullAccessUsers** pro kořenový účet a dokonce i oddělení těch, které jsou pro klíč podsložky. Pokud existují další očekávané skupiny uživatelů, kteří mohou být přidány později, ale nebyly identifikovány dosud jste zvažte vytvoření skupiny fiktivní zabezpečení, které mají přístup k určitým složkám. Pomocí skupiny zabezpečení zajišťuje, že později nepotřebujete dlouho doba zpracování pro přiřazení nového oprávnění až po tisíce souborů. 

### <a name="security-for-service-principals"></a>Zabezpečení pro instanční objekty 

Instanční objekty Azure Active Directory jsou obvykle používány služeb, jako je Azure HDInsight pro přístup k datům v Data Lake Storage Gen1. V závislosti na požadavky na přístup napříč více úloh může být některé důležité informace, abyste zajistili bezpečnost uvnitř i vně organizace. Pro mnoho zákazníků může stačit jeden objekt služby Azure Active Directory a může mít úplná oprávnění v kořenové složce účtu Data Lake Storage Gen1. Ostatní zákazníci mohou vyžadovat víc clusterů pomocí různých instančních objektů kde jeden cluster má plný přístup k datům a jiný cluster s oprávněním pouze ke čtení. Stejně jako u skupiny zabezpečení, zvažte provedení hlavního názvu služby pro každou po vytvoření účtu Data Lake Storage Gen1 očekávané výsledky scénář (čtení, zápisu, úplná). 

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Povolit bránu firewall Gen1 úložiště Data Lake s přístup služby Azure 

Data Lake Storage Gen1 podporuje možnost zapnutí brány firewall a omezení přístupu jenom na služby Azure, která se doporučuje pro menší útoku z vnějšího vniknutí. Může být povolená brána firewall v účtu Data Lake Storage Gen1 na webu Azure Portal prostřednictvím **brány Firewall** > **povolit bránu Firewall (ON)** > **povolit přístup k Azure služby** možnosti.  

![Nastavením brány firewall v Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "nastavením brány Firewall v Data Lake Storage Gen1")

Jakmile je brána firewall povolena, pouze Azure služby, jako je HDInsight, Data Factory, SQL Data Warehouse a podobně mají přístup k Data Lake Storage Gen1. Z důvodu interní překlad síťových adres Azure používá Brána firewall Gen1 úložiště Data Lake nepodporuje omezení konkrétních služeb podle IP a je určena pouze pro omezení koncové body mimo Azure, jako je místní. 

## <a name="performance-and-scale-considerations"></a>Aspekty týkající se výkonu a škálování

Jednou z nejúčinnějších funkcí Gen1 úložiště Data Lake je, že odebere pevných limitů na propustnost dat. Odebrání omezení umožňuje zákazníkům rozšířit jejich velikost dat a spolu s požadavky na výkon bez nutnosti data do horizontálního oddílu. Jeden z nejdůležitějších aspektů pro optimalizaci výkonu Gen1 úložiště Data Lake je, že funguje nejlépe při dané paralelismu.

### <a name="improve-throughput-with-parallelism"></a>Zlepšit propustnost s paralelismu 

Zvažte 8-12 vlákna podle počtu jader pro optimální propustnosti čtení a zápisu. Je to z důvodu blokování čtení/zápisu v jednom vlákně a více vláken umožňuje vyšší souběžnosti na virtuálním počítači. K zajištění, že úrovně jsou v pořádku a je možné zvýšit paralelismu, je potřeba sledovat využití procesoru Virtuálního počítače.   

### <a name="avoid-small-file-sizes"></a>Vyhněte se malé velikosti

Oprávnění POSIX a auditování v Data Lake Storage Gen1 součástí režijní náklady, který začíná být zřejmá při práci s mnoha malých souborů. Jako osvědčený postup musíte batch vaše data do větších souborů a zápis do Data Lake Storage Gen1 tisíce nebo miliony malých souborů. Předcházení malé velikosti mají několik výhod, například:

* Snížení kontroly ověřování do více souborů
* Snížili připojení otevřené souborů
* Rychlejší kopírování a replikace
* Menší počet souborů ke zpracování při aktualizaci Data Lake Storage Gen1 POSIX oprávnění 

V závislosti na tom, jaké služby a úlohy používají data, je vhodné velikosti vzít v úvahu pro soubory 256 MB nebo vyšší. Pokud velikost souboru nelze sjednotit při doručení Gen1 úložiště Data Lake, můžete mít samostatné komprimace úlohu, která kombinuje tyto soubory do většími. Další informace a doporučení ohledně velikosti souborů a uspořádání dat v Data Lake Storage Gen1 najdete v tématu [struktury datové sady](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Velikost velkých souborů a potenciálního dopadu na výkon

I když Gen1 úložiště Data Lake podporuje velkých souborů až po petabajty velikosti, pro zajištění optimálního výkonu a v závislosti na proces načtení dat, nemusí být ideální v průměru přejdete vyšší než 2 GB. Například při použití **Distcp** ke kopírování dat mezi umístění nebo jiný účet úložiště, jsou soubory na nejpodrobnější úrovni členitosti používá k určení mapování úlohy. Ano Pokud kopírujete 10 souborů, které jsou 1 TB, maximálně 10 mapovačů přidělují. Také pokud máte mnoho souborů s mapovačů přiřazen, nejdřív mapovačů paralelní práci pro přesun velkých souborů. Jako úloha začne větru jenom pár mapovačů zůstanou přidělené a jeden Mapovač přiřazená velkých souborů je zablokuje. Microsoft odeslal vylepšení Distcp k vyřešení tohoto problému v budoucích verzích systému Hadoop.  

Dalším příkladem vzít v úvahu je při používání Azure Data Lake Analytics s Data Lake Storage Gen1. V závislosti na zpracování, provádí Extraktor, může být některé soubory, které nelze rozdělit (například XML, JSON) negativně v výkon, pokud je větší než 2 GB. V případech, kde lze rozdělit soubory extraktoru (například sdíleného svazku clusteru) jsou upřednostňované velkých souborů.

### <a name="capacity-plan-for-your-workload"></a>Kapacitní plánování úlohy 

Azure Data Lake Storage Gen1 odebere pevné vstupně-výstupních operací omezení, které se umístí na účty úložiště Blob. Existují však stále doporučené omezení, které je potřeba považovat za. Výchozí příchozí a odchozí přenos omezení potřeb většiny scénářů. Pokud vaše úloha musí mít omezení zvýšit, pracujete s podporou Microsoftu. Také podívejte se na limity během fáze testování konceptu tak, aby omezení vstupně-výstupní operace nejsou přístupů během produkčního prostředí. Pokud k tomu dojde, potřebovat čekání na ruční zvýšení technického týmu Microsoftu. Pokud dojde k omezení šířky pásma vstupně-výstupní operace, Azure Data Lake Storage Gen1 vrátí kód chyby 429 a v ideálním případě neúspěšného se zásadami příslušné exponenciálního omezení rychlosti. 

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimalizace "zapíše" s využitím vyrovnávací paměti ovladače Gen1 úložiště Data Lake 

K optimalizaci výkonu a snížení vstupně-výstupních operací při zápisu do Data Lake Storage Gen1 z Hadoopu, provádění operací zápisu co nejblíže velikost vyrovnávací paměti ovladače Data Lake Storage Gen1 nejvíce. Pokuste se přesáhla velikost vyrovnávací paměti před vyprazdňování, například při streamování využívající Apache Storm nebo Spark streaming úlohy. Při zápisu do Data Lake Storage Gen1 z HDInsight nebo Hadoop, je důležité vědět, že Data Lake Storage Gen1 je ovladač s vyrovnávací pamětí 4 MB. Stejně jako mnoho ovladačů systému souborů můžete ručně vyprázdní tuto vyrovnávací paměť před dosažením velikosti 4 MB. V opačném případě ji okamžitě vyprázdní do úložiště Pokud další zápis překračuje maximální velikost vyrovnávací paměti. Kde je to možné, je třeba se vyvarovat přetečení nebo významné podjetí vyrovnávací paměti při synchronizaci/vyprazdňování zásady podle počtu nebo času okna.

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti 

Při navrhování systém s Data Lake Storage Gen1 nebo kteroukoli cloudovou službu, zvažte vaše požadavky na dostupnost a jak reagovat na potenciální přerušení služby. Problém může být lokalizována do určité instance nebo dokonce celou oblast, tak plán pro obě, je důležité. V závislosti na tom **plánovaná doba obnovení** a **cíl bodu obnovení** smlouvy SLA pro vaše úlohy, můžete zvolit více nebo méně agresivní strategie vysoké dostupnosti a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii 

Vysoká dostupnost (HA) a zotavení po havárii (DR) v některých případech se dá zkopírovat dohromady, i když každá má mírně odlišné strategie, zejména v případě, že jde o data. Data Lake Storage Gen1 již zpracovává 3 x replikaci pod pokličkou pro ochranu proti lokalizovanému selhání hardwaru. Ale vzhledem k tomu, že replikace napříč oblastmi není vestavěný, musíte spravovat sami. Při vytváření plánu pro vysokou dostupnost, v případě přerušení služby zatížení potřebuje přístup k nejnovější data nejrychleji díky přepínání samostatně replikovaná instance místně nebo v nové oblasti.  

Strategie zotavení po Havárii Příprava nepravděpodobném případě závažného selhání oblasti, je také důležité mít data replikovat do jiné oblasti. Tato data může zpočátku být stejný jako replikovaná data vysokou dostupnost. Ale musíte taky zvážit požadavky na hraniční případy, například poškození dat, kde můžete chtít vytvořit pravidelné snímky vrátit zpět. V závislosti na závažnosti a množství dat zvažte delta snímky 1 – 6 a období 24 hodin se zajištěním provozu v úložišti místní a/nebo sekundární podle rizika tolerance. 

Větší odolnost dat. s Data Lake Storage Gen1 se doporučuje geograficky je replikovat data do samostatné oblasti s frekvencí, která splňuje vaše požadavky na vysokou dostupnost/zotavení po Havárii v ideálním případě každou hodinu. Tato četnost replikace minimalizuje přesuny velké objemy dat, které mají konkurenční propustnost potřebuje hlavní systému a lepší cíle bodu obnovení (RPO). Kromě toho byste měli zvážit možnosti pro aplikaci pomocí Data Lake Storage Gen1 automaticky převzít služby při selhání na sekundární účet prostřednictvím monitorování aktivační události nebo délka neúspěšných pokusů o nebo alespoň odeslat oznámení správcům ručního zásahu. Uvědomte si, že je kompromis přebírání služeb při selhání a čekání na službu do režimu online. Pokud data nebyla dokončena, které se replikují, převzetí služeb při selhání může způsobit potenciální ztráty dat, nekonzistence nebo komplexní sloučení dat. 

Níže jsou tři hlavní doporučené možnosti pro orchestrací replikace mezi účty Data Lake Storage Gen1 a hlavní rozdíly mezi každou z nich.


|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limity škálování**     | Ohraničené pracovní uzly        | Omezeno daty, která jednotek přesunu dat Max cloudu        | Tím jednotky analýzy        |
|**Podporuje kopírování rozdíly**     |   Ano      | Ne         | Ne         |
|**Integrované Orchestrace**     |  Ne (použití Oozie vzduchu nebo cron úloh)       | Ano        | Ne (pomocí služby Azure Automation nebo plánovače úloh Windows)         |
|**Podporované souborové systémy**     | ADL, HDFS, WASB, S3, GS, CFS        |Mnoho, viz [konektory](../data-factory/connector-azure-blob-storage.md).         | ADL k ADL WASB k ADL (pouze stejné oblasti)        |
|**Podporu operačního systému**     |Libovolný operační systém Hadoop         | neuvedeno          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití Distcp pro přesun dat mezi dvěma umístěními 

Zkratka pro distribuované kopírování Distcp je nástroj příkazového řádku systému Linux, která se dodává s Hadoop a poskytuje přesun distribuovaných dat mezi dvěma umístěními. Těmito dvěma umístěními může být Data Lake Storage Gen1, HDFS, WASB nebo S3. Tento nástroj používá úlohy mapreduce je možné na clusteru Hadoop (třeba HDInsight) pro horizontální navýšení kapacity na všech uzlech. Distcp se považuje za nejrychlejší způsob, jak přesunout velké objemy dat bez komprese speciální síťová. Distcp také nabízí možnost aktualizovat pouze rozdíly mezi dvěma umístěními, Automatické opakované pokusy obslužné rutiny, jakož i dynamické škálování výpočetního výkonu. Tento přístup je mimořádně efektivní, pokud jde o replikaci věci, jako je tabulek Hive/Spark, které může mít mnoho velkých souborů v jednom adresáři a chcete zkopírovat upravený data. Z těchto důvodů je Distcp nejvíce doporučeným nástrojem pro kopírování dat mezi úložišti velkých objemů dat. 

Kopírování úlohy můžete aktivovat Apache Oozie pracovní postupy pomocí frekvence nebo data triggery, stejně jako úlohy cron systému Linux. Pro úlohy náročné na replikace se doporučuje zprovoznění jiném clusteru HDInsight Hadoop, která může vyladěná a škálovat specificky pro úlohy kopírování. Tím se zajistí, že kopírování úlohy nejsou v konfliktu s kritické úlohy. Pokud používáte dost široké, frekvenci replikace, může být clusteru i odstavit mezi každou úlohu. Pokud převzetí služeb při selhání do sekundární oblasti, ujistěte se, že jiný cluster také podařilo v sekundární oblasti replikovat nová data zpět do primárního účtu Data Lake Storage Gen1, jakmile se vrátí zpět. Příklady použití Distcp, naleznete v tématu [použití Distcp ke kopírování dat mezi objekty BLOB Azure Storage a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Použití Azure Data Factory k naplánování úloh kopírování 

Azure Data Factory je také možné plánovat úlohy kopírování pomocí **aktivity kopírování**a můžete nastavit i na frekvenci prostřednictvím **Průvodce kopírováním**. Mějte na paměti, že Azure Data Factory má limit jednotek pohybu dat v cloudu (DMUs) a nakonec caps propustnost/výkon pro rozsáhlé datové úlohy. Kromě toho Azure Data Factory aktuálně nenabízí aktualizace rozdílů mezi účty Data Lake Storage Gen1, takže složky, například tabulek Hive by vyžadovalo kompletní kopii k replikaci. Odkazovat [Průvodce laděním aktivity kopírování](../data-factory/copy-activity-performance.md) Další informace o kopírování do služby Data Factory. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy je nástroj příkazového řádku Windows, který umožňuje kopírovat data mezi dva účty Data Lake Storage Gen1 pouze v rámci stejné oblasti. Nástroj AdlCopy poskytuje možnost samostatného nebo možnost spuštění úlohy kopírování se pomocí účtu Azure Data Lake Analytics. I když byla původně vytvořena kopie na vyžádání na rozdíl od robustní replikaci, je další možností provedete distribuované kopírování mezi různými účty Data Lake Storage Gen1 ve stejné oblasti. Spolehlivosti doporučuje se použít možnost Data Lake Analytics úrovně premium pro všechny produkční úlohy. Samostatnou verzi může vrátit zaneprázdněný odpovědi a má omezenou škálování a monitorování. 

AdlCopy jako Distcp, musí být orchestrovaných službou něco jako je Azure Automation nebo plánovače úloh Windows. Stejně jako u služby Data Factory AdlCopy nepodporuje kopírování aktualizované soubory, ale zkopírují a přepsat existující soubory. Další informace a příklady použití AdlCopy najdete v tématu [kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Důležité informace o monitorování 

Data Lake Storage Gen1 poskytuje podrobné diagnostické protokoly a auditování. Data Lake Storage Gen1 poskytuje některé základní metriky na webu Azure Portal pomocí účtu Data Lake Storage Gen1 a ve službě Azure Monitor. Dostupnost služby Data Lake Storage Gen1 se zobrazí na webu Azure Portal. Ale tato metrika je aktualizováno každých sedm minut a nelze se dotazovat veřejně vystavené rozhraní API. Chcete-li získat nejaktuálnější dostupnost účtu Data Lake Storage Gen1, musíte spustit syntetické testy k ověření dostupnosti. Jiné metriky, jako je například celková využití úložiště a požadavky na čtení/zápis, příchozí a odchozí transakce může trvat až 24 hodin k aktualizaci. Proto další aktuální metriky je nutné vypočítat ručně prostřednictvím nástrojů příkazového řádku Hadoopu nebo agregovat informace o protokolu. Nejrychlejší způsob, jak získat nejnovější využití úložiště je spuštění tohoto příkazu HDFS z uzlu clusteru Hadoop (například hlavní uzel):   

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportovat diagnostické údaje Gen1 úložiště Data Lake 

Jedním z nejrychleji způsoby, jak získat přístup k prohledávání protokolů z Data Lake Storage Gen1 je povolit přesouvání protokolu k **Log Analytics** pod **diagnostiky** okno pro účet Data Lake Storage Gen1. Získáte okamžitý přístup k příchozí protokoly v čase a filtry obsahu, spolu s možností (e-mailu nebo webhooku) výstrah, aktivuje v intervalech 15 minut. Pokyny najdete v tématu [přístup k protokolům diagnostiky pro Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md). 

Pro další výstrahy v reálném čase a větší kontrolu, ve kterém objevil v protokolech vezměte v úvahu export protokolů do centra událostí Azure, kde obsahu mohou být analyzovány samostatně nebo v časovém intervalu aby bylo možné odeslat oznámení v reálném čase do fronty. Samostatná aplikace, jako [aplikace logiky](../connectors/connectors-create-api-azure-event-hubs.md) můžete poté používat a komunikovat výstrah za účelem odpovídající kanál, jakož i odeslání metrik do monitorovacích nástrojů jako nástrojů NewRelic, služby Datadog a AppDynamics. Případně, pokud používáte nástroje třetích stran, jako je ElasticSearch, můžete exportovat protokoly do úložiště objektů Blob a použít [modul plug-in pro Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) využívat dat do Elasticsearch, Kibana a Logstash (ELK) zásobníku.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Zapnutí protokolování na úrovni ladění v HDInsight 

Pokud se Data Lake Storage Gen1 přesouvání protokolu není zapnuté, Azure HDInsight také poskytuje způsob, jak zapnout [klientské protokolování pro Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) prostřednictvím log4j. Následující vlastnost je nutné nastavit **Ambari** > **YARN** > **Config** > **Advanced yarn log4j konfigurace**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

Jakmile je vlastnost nastavena a uzly se restartují, Data Lake Storage Gen1 diagnostiky do protokolů se zapíšou YARN na uzlech (/tmp/<user>/yarn.log) a důležité podrobnosti, jako jsou chyby nebo omezení šířky pásma (kód chyby protokolu HTTP 429), je možné monitorovat. Tytéž informace můžete také monitorovat a mít v Log Analytics nebo všude, kde jsou protokoly dodáno ve [diagnostiky](data-lake-store-diagnostic-logs.md) okně účtu Data Lake Storage Gen1. Doporučuje se alespoň zapnout protokolování na straně klienta, nebo využít možnost s Data Lake Storage Gen1 nabízí přehled o provozu a snadnější ladění přesouvání protokolu.

### <a name="run-synthetic-transactions"></a>Spouštění syntetických transakcí 

Metriky dostupnosti služby Data Lake Storage Gen1 na webu Azure Portal v současné době má okno aktualizace 7 minut. Kromě toho nelze dotazovat, pomocí rozhraní API veřejně vystavené. Proto se doporučuje vytvořit základní aplikaci, která provádí syntetickými transakcemi pro Data Lake Storage Gen1, který můžete zadat až minutu dostupnosti. Příkladem může být vytvoření webové úlohy, aplikaci logiky nebo aplikaci Azure Function App k provedení pro čtení, vytvořit a aktualizovat pro Data Lake Storage Gen1 a výsledky odeslat do vašeho řešení pro monitorování. Operace můžete provádět v dočasné složce a pak odstraní po testu, které mohou spouštět každých 30 – 60 sekund, v závislosti na požadavcích.

## <a name="directory-layout-considerations"></a>Důležité informace o rozložení adresáře

Při doručení dat do data lake, je důležité dopředu plánovat strukturu dat tak, aby mohli být zabezpečení, vytváření oddílů a zpracování efektivně využívá. Následující doporučení je možné, ať už s Azure Data Lake Storage Gen1 a úložiště objektů Blob, HDFS. Každé zatížení má jiné požadavky na způsob data se spotřebovává, ale v následující tabulce jsou některé běžné rozložení vzít v úvahu při práci se službou IoT a batch scénáře.

### <a name="iot-structure"></a>Struktura IoT 

V úlohách IoT může být spoustu dat se dostali do úložiště dat, který zahrnuje mnoho produkty, zařízení, organizace a zákazníků. Je důležité dopředu plánovat, rozložení adresáře pro organizace, zabezpečení a efektivní zpracování datového proudu uživatelům. Obecné šablony vzít v úvahu může být následující rozložení: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Například úvodní telemetrická data motoru letadla ve Spojeném království může vypadat jako následující strukturu: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Je důležité důvod umístit data na konci strukturu složek. Pokud chcete uzamknout určité oblasti nebo záležitostí na uživatele nebo skupiny, pak lze snadno provést s oprávněními POSIX. V opačném případě pokud se třeba omezit určité skupiny zabezpečení k zobrazení pouze data Velká Británie nebo určitých rovin, se strukturou datum v popředí samostatné oprávnění by byla zapotřebí pro mnoho složky pod složkou každou hodinu. Kromě toho s struktura datum v popředí exponenciálně zvýší počet složek jako čas se nepovedlo.

### <a name="batch-jobs-structure"></a>Struktura úlohy služby batch 

Z hlavní běžně používaná přístupem v dávkové zpracování je dostat data do složky "in". Poté po zpracování dat vložte nová data do složky "out" pro podřízené procesy využívají. Tato adresářová struktura se někdy používá pro úlohy, které vyžadují zpracování jednotlivých souborů a nemusí výkonným paralelním zpracováním velké datové sady. Stejně jako výše doporučených strukturu IoT má dobré adresářovou strukturu nadřazené složky pro takové věci, jako jsou oblasti a záležitostí (například organizace, produktu nebo výrobce). Tato struktura pomáhá se zabezpečením dat napříč vaší organizace a lepší správu dat ve vašich úloh. Kromě toho zvažte datum a čas ve struktuře umožňující lepší uspořádání, filtrovaný vyhledává, zabezpečení a automatizace ve zpracování. Úroveň podrobností pro strukturu datum je určen podle intervalu, na kterém je data nahráli nebo zpracovat, jako například každou hodinu, denně, nebo dokonce každý měsíc. 

Někdy souboru zpracování neproběhne úspěšně kvůli poškození dat nebo neočekávaný formát. V takových případech by mohlo prospět adresářovou strukturu **/chybný** složky pro přesun souborů k další kontroly. Úlohy služby batch může zpracovávat také vytváření sestav nebo oznámení z nich *chybný* soubory ručního zásahu. Vezměte v úvahu následující strukturu šablony: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Například marketingové pevně přijímá denní extrahuje data zákazníků aktualizací ze svých klientech v Severní Americe. Může vypadat jako následující fragment kódu před a po zpracování dat: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
V případě běžných dat dávkové zpracování přímo do databáze, jako jsou Hive nebo tradiční databází SQL, není potřeba **/in** nebo **/out** složky, protože výstup již přejde do samostatné složky pro tabulky Hive nebo externí databáze. Například denní extrahuje od zákazníků by dostat do jejich odpovídajících složek a Orchestrace pomocí něco jako Azure Data Factory, Apache Oozie nebo Apache vzduchu aktivuje každodenní úlohu Hive nebo Spark ke zpracování a zápis dat do tabulky Hive.

## <a name="next-steps"></a>Další postup     

* [Přehled služby Azure Data Lake Storage Gen1](data-lake-store-overview.md) 
* [Řízení přístupu v Azure Data Lake Storage Gen1](data-lake-store-access-control.md) 
* [Zabezpečení v Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Optimalizace výkonu pro Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-guidance.md)
* [Průvodce laděním výkonu pro Spark v HDInsight pomocí Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Průvodce laděním výkonu pro Hive v HDInsight pomocí Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Orchestrace dat pomocí Azure Data Factory pro Azure Data Lake Storage Gen1](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Vytvoření clusterů HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md) 
