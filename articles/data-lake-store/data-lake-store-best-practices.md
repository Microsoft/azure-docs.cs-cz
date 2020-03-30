---
title: Doporučené postupy pro používání Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Seznamte se s doporučenými postupy pro ingestování dat, zabezpečení dat a výkon související s používáním Azure Data Lake Storage Gen1 (dříve známé jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: a8ca67d1ff3100aee02ed473c9cc2180de3973b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638931"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Doporučené postupy pro používání Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

V tomto článku se dozvíte o doporučených postupech a aspektech práce s Azure Data Lake Storage Gen1. Tento článek obsahuje informace o zabezpečení, výkonu, odolnosti proti chybám a monitorování pro úložiště datového jezera Gen1. Před Data Lake Storage Gen1 byla práce se skutečně velkými daty ve službách, jako je Azure HDInsight, složitá. Bylo třeba horizontálního oddílu dat přes více účtů úložiště objektů blob tak, aby petabajt úložiště a optimální výkon v tomto měřítku by bylo možné dosáhnout. S Datovým lakem Storage Gen1 se odstraní většina pevných limitů pro velikost a výkon. Existují však stále některé důležité informace, které tento článek pokrývá, takže můžete získat nejlepší výkon s Dat Lake Storage Gen1.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Azure Data Lake Storage Gen1 nabízí ovládací prvky přístupu POSIX a podrobné auditování pro uživatele, skupiny a objekty zabezpečení služby Azure Active Directory (Azure AD). Tyto ovládací prvky přístupu lze nastavit na existující soubory a složky. Ovládací prvky přístupu lze také použít k vytvoření výchozích hodnot, které lze použít pro nové soubory nebo složky. Pokud jsou oprávnění nastavena na existující složky a podřízené objekty, musí být oprávnění rekurzivně šířena u každého objektu. Pokud existuje velký počet souborů, šíření oprávnění může trvat dlouhou dobu. Doba trvalá může být v rozsahu mezi 30-50 objekty zpracovanými za sekundu. Proto naplánujte strukturu složek a skupiny uživatelů odpovídajícím způsobem. V opačném případě může způsobit neočekávaná zpoždění a problémy při práci s daty.

Předpokládejme, že máte složku s 100 000 podřízenými objekty. Pokud budete mít dolní mez 30 objektů zpracovaných za sekundu, aktualizovat oprávnění pro celou složku může trvat hodinu. Další podrobnosti o aklů AcL úložiště datového jezera jsou k dispozici na [webu Access control v Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Chcete-li zlepšit výkon při rekurzivním přiřazování aklů, můžete použít nástroj příkazového řádku Azure Data Lake. Nástroj vytvoří více vláken a rekurzivní navigační logiku pro rychlé použití seznamů ACL na miliony souborů. Nástroj je k dispozici pro Linux a Windows a [dokumentaci](https://github.com/Azure/data-lake-adlstool) a [soubory ke stažení](https://aka.ms/adlstool-download) pro tento nástroj lze nalézt na GitHubu. Tato stejná vylepšení výkonu mohou být povolena vlastními nástroji napsanými pomocí sad Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) a [Java](data-lake-store-get-started-java-sdk.md) SDK.

### <a name="use-security-groups-versus-individual-users"></a>Použití skupin zabezpečení oproti jednotlivým uživatelům

Při práci s velkými objemy dat v Gen1 úložiště datového jezera s největší pravděpodobností instanční objekt se používá k povolení služeb, jako je Azure HDInsight pracovat s daty. Mohou však nastat případy, kdy jednotliví uživatelé potřebují také přístup k datům. V takových případech je nutné použít [skupiny zabezpečení služby](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) Azure Active Directory namísto přiřazení jednotlivých uživatelů ke složkám a souborům.

Jakmile je skupině zabezpečení přiřazena oprávnění, přidání nebo odebrání uživatelů ze skupiny nevyžaduje žádné aktualizace programu Data Lake Storage Gen1. To také pomáhá zajistit, že nepřekročíte limit [32 přístupových a výchozích hodnot ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) (to zahrnuje čtyři knihovny ACL ve stylu POSIX, které jsou vždy přidruženy ke každému souboru a složce: [vlastnící uživatel](data-lake-store-access-control.md#the-owning-user), [vlastnící skupina](data-lake-store-access-control.md#the-owning-group), [maska](data-lake-store-access-control.md#the-mask)a další).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny

Jak je popsáno, když uživatelé potřebují přístup k Data Lake Storage Gen1, je nejlepší použít skupiny zabezpečení Azure Active Directory. Některé doporučené skupiny, které mají být zahájeny, mohou být **ReadOnlyUsers**, **WriteAccessUsers**a **FullAccessUsers** pro kořen účtu a dokonce i samostatné skupiny pro klíčové podsložky. Pokud existují další očekávané skupiny uživatelů, které mohou být přidány později, ale dosud nebyly identifikovány, můžete zvážit vytvoření fiktivních skupin zabezpečení, které mají přístup k určitým složkám. Použití skupiny zabezpečení zajistí, že později nebudete potřebovat dlouhou dobu zpracování pro přiřazení nových oprávnění tisícům souborů.

### <a name="security-for-service-principals"></a>Zabezpečení pro instanční objekty

Objekty služby Azure Active Directory se obvykle používají služby, jako je Azure HDInsight pro přístup k datům v Data Lake Storage Gen1. V závislosti na požadavcích na přístup napříč více úlohami mohou být některé důležité informace k zajištění zabezpečení uvnitř i vně organizace. Pro mnoho zákazníků může být odpovídající jeden objekt zabezpečení služby Azure Active Directory a může mít úplná oprávnění v kořenovém adresáři účtu Gen1 úložiště datového jezera. Jiní zákazníci mohou vyžadovat více clusterů s různými objekty zabezpečení služeb, kde jeden cluster má úplný přístup k datům a jiný cluster s přístupem pro čtení pouze. Stejně jako u skupin zabezpečení můžete zvážit vytvoření instančního objektu pro každý očekávaný scénář (čtení, zápis, úplný) po vytvoření účtu Data Lake Storage Gen1.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Povolení brány firewall Data Lake Storage Gen1 s přístupem ke službě Azure

Data Lake Storage Gen1 podporuje možnost zapnutí brány firewall a omezení přístupu jenom ke službám Azure, což se doporučuje pro menší vektor útoku z externích vniknutí. Bránu firewall můžete povolit na účtu Data Lake Storage Gen1 na webu Azure portal prostřednictvím **brány firewall** > **povolit bránu firewall (ON)** > **Povolit přístup k** možnostem služeb Azure.

![Nastavení brány firewall v úložišti datového jezera Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Nastavení brány firewall v úložišti datového jezera Gen1")

Jakmile je brána firewall povolena, přístup k Dat Lake Storage Gen1 mají jenom služby Azure, jako je HDInsight, Data Factory, SQL Data Warehouse atd. Vzhledem k překladu interní síťové adresy používané Azure, brána firewall Storage Storage Data Lake 1 nepodporuje omezení konkrétních služeb podle IP adres a je určena jenom pro omezení koncových bodů mimo Azure, jako je například místní.

## <a name="performance-and-scale-considerations"></a>Důležité informace o výkonu a rozsahu

Jednou z nejvýkonnějších funkcí Data Lake Storage Gen1 je, že odstraňuje pevné limity propustnosti dat. Odebrání limitů umožňuje zákazníkům zvětšit jejich velikost dat a doprovodné požadavky na výkon bez nutnosti oddílu dat. Jedním z nejdůležitějších aspektů pro optimalizaci výkonu Data Lake Storage Gen1 je, že funguje nejlépe při dané paralelismu.

### <a name="improve-throughput-with-parallelism"></a>Zlepšete propustnost s paralelismem

Zvažte poskytnutí 8-12 vláken na jádro pro nejoptimálnější propustnost pro čtení a zápis. To je způsobeno blokování čtení nebo zápisy na jednom vlákně a další vlákna mohou povolit vyšší souběžnost na virtuálním počítači. Chcete-li zajistit, že úrovně jsou v pořádku a paralelismus lze zvýšit, nezapomeňte sledovat využití procesoru virtuálního počítače.

### <a name="avoid-small-file-sizes"></a>Vyhněte se malým velikostem souborů

Posix oprávnění a auditování v Data Lake Storage Gen1 přichází s režií, která se projeví při práci s mnoha malých souborů. Jako osvědčený postup je nutné dávky dat do větších souborů versus zápis tisíce nebo miliony malých souborů do Data Lake Storage Gen1. Vyhýbání se malým velikostem souborů může mít několik výhod, například:

* Snížení kontrol ověřování ve více souborech
* Snížení počtu otevřených souborů
* Rychlejší kopírování/replikace
* Při aktualizaci oprávnění Data Lake Storage Gen1 POSIX je méně souborů ke zpracování

V závislosti na tom, jaké služby a úlohy používají data, je vhodné zvážit velikost pro soubory 256 MB nebo vyšší. Pokud velikost souborů nelze dávkově kombinovat při přistávání v datovém úložišti lake gen1, můžete mít samostatnou úlohu zhutnění, která kombinuje tyto soubory do větších. Další informace a doporučení týkající se velikosti souborů a uspořádání dat v modulu Gen1 úložiště datového jezera naleznete v [tématu Strukturování datové sady](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Velké velikosti souborů a potenciální dopad na výkon

Přestože Data Lake Storage Gen1 podporuje velké soubory až petabajty ve velikosti, pro optimální výkon a v závislosti na procesu čtení dat, nemusí být ideální jít nad 2 GB v průměru. Například při použití **Distcp** ke kopírování dat mezi umístěními nebo různé účty úložiště, soubory jsou nejlepší úroveň rozlišovací schopnost slouží k určení mapy úkoly. Takže pokud kopírujete 10 souborů, které jsou každý 1 TB, maximálně 10 mappers jsou přiděleny. Také, pokud máte spoustu souborů s mapovače přiřazeny, zpočátku mapovače pracovat paralelně přesunout velké soubory. Nicméně, jak úloha začne ukončit pouze několik mapovačů zůstávají přiděleny a můžete být přilepená s jedním mapper přiřazen k velkému souboru. Společnost Microsoft odeslala vylepšení Distcp k řešení tohoto problému v budoucích verzích Hadoop.

Dalším příkladem, který je třeba zvážit, je při použití Azure Data Lake Analytics s Date Lake Storage Gen1. V závislosti na zpracování provedené extraktoru, některé soubory, které nelze rozdělit (například XML, JSON) může dojít k poklesu výkonu při větší než 2 GB. V případech, kdy lze soubory rozdělit extraktorem (například CSV), jsou upřednostňovány velké soubory.

### <a name="capacity-plan-for-your-workload"></a>Plán kapacity pro vaši pracovní zátěž

Azure Data Lake Storage Gen1 odebere omezení omezení pevných vod, které jsou umístěny na účtech úložiště objektů Blob. Stále však existují měkké limity, které je třeba vzít v úvahu. Výchozí omezení příchozího přenosu dat/odchozího přenosu dat splňují potřeby většiny scénářů. Pokud vaše úloha potřebuje zvýšit limity, pracujte s podporou Microsoftu. Také podívejte se na limity během fáze proof-of-concept tak, aby limity omezení vstupně-souhlasím e nebyly přístupů během výroby. Pokud k tomu dojde, může vyžadovat čekání na ruční zvýšení od technického týmu společnosti Microsoft. Pokud dojde k omezení iO, Azure Data Lake Storage Gen1 vrátí kód chyby 429 a v ideálním případě by měl být opakován s odpovídající exponenciální zásady backoff.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimalizace "zápisů" pomocí vyrovnávací paměti ovladače Data Lake Storage Gen1

Chcete-li optimalizovat výkon a snížit iOPS při zápisu do Data Lake Storage Gen1 z Hadoopu, provádějte operace zápisu co nejblíže velikosti vyrovnávací paměti ovladače Data Lake Storage Gen1. Snažte se před vyprázdněním nepřekročit velikost vyrovnávací paměti, například při streamování pomocí úloh streamování Apache Storm nebo Spark. Při zápisu do Data Lake Storage Gen1 z HDInsight/Hadoop, je důležité vědět, že Data Lake Storage Gen1 má ovladač s vyrovnávací pamětí 4 MB. Stejně jako mnoho ovladačů systému souborů, tato vyrovnávací paměť může být ručně vyprázdněna před dosažením velikosti 4 MB. Pokud ne, je okamžitě vyprázdněna do úložiště, pokud další zápis překročí maximální velikost vyrovnávací paměti. Pokud je to možné, je nutné se vyhnout přetečení nebo významné podběh vyrovnávací paměti při synchronizaci a vyprázdnění zásady podle počtu nebo časového okna.

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti

Při navrhování systému s Data Lake Storage Gen1 nebo jakékoli cloudové služby, musíte zvážit vaše požadavky na dostupnost a jak reagovat na potenciální přerušení služby. Problém může být lokalizován na konkrétní instance nebo dokonce celé oblasti, takže s plánem pro oba je důležité. V závislosti na **cíli doby obnovení** a cíle sla bodu **obnovení** pro vaše úlohy můžete zvolit více či méně agresivní strategii pro vysokou dostupnost a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Vysoká dostupnost (HA) a zotavení po havárii (DR) lze někdy kombinovat dohromady, i když každý má mírně odlišnou strategii, zejména pokud jde o data. Data Lake Storage Gen1 již zpracovává 3x replikace pod kapotou, aby se ochránila před selháním lokalizovaného hardwaru. Vzhledem k tomu, že replikace mezi oblastmi není integrovaná, je však nutné ji spravovat sami. Při vytváření plánu pro HA, v případě přerušení služby zatížení potřebuje přístup k nejnovějším datům co nejrychleji přepnutím na samostatně replikovanou instanci místně nebo v nové oblasti.

Ve strategii zotavení po havárii, připravit se na nepravděpodobné události katastrofické selhání oblasti, je také důležité mít data replikována do jiné oblasti. Tato data mohou být zpočátku stejná jako replikovaná data HA. Je však také nutné zvážit vaše požadavky na hraniční případy, jako je například poškození dat, kde můžete chtít vytvořit periodické snímky, ke kterým se vrátíte. V závislosti na důležitosti a velikosti dat zvažte postupné rozdílové snímky 1-, 6 a 24 hodin v místním a/nebo sekundárním úložišti podle tolerancí rizika.

Pro odolnost dat s Data Lake Storage Gen1 se doporučuje geograficky replikovat data do samostatné oblasti s frekvencí, která splňuje vaše požadavky NA/DR, ideálně každou hodinu. Tato frekvence replikace minimalizuje masivní přesuny dat, které mohou mít konkurenční potřeby propustnosti s hlavním systémem a lepší cíl bodu obnovení (RPO). Kromě toho byste měli zvážit způsoby, jak pro aplikaci pomocí Data Lake Storage Gen1 automaticky převzetí služeb při selhání sekundární účet prostřednictvím monitorování aktivačních událostí nebo délku neúspěšných pokusů, nebo alespoň odeslat oznámení správcům pro ruční zásah. Mějte na paměti, že existuje kompromis selhání nad versus čekání na službu, aby se vrátil online. Pokud data nebyla dokončena replikace, převzetí služeb při selhání může způsobit potenciální ztrátu dat, nekonzistenci nebo komplexní slučování dat.

Níže jsou uvedeny tři doporučené možnosti pro orchestraci replikace mezi účty Data Lake Storage Gen1 a klíčové rozdíly mezi jednotlivými účty.

|  |Neprotokolované  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limity stupnice**     | Ohraničené pracovními uzly        | Omezeny jednotkami Max Cloud Data Movement        | Vázané jednotkami Analytics        |
|**Podporuje kopírování deltů**     |   Ano      | Ne         | Ne         |
|**Vestavěná orchestrace**     |  Ne (použijte úlohy Oozie Airflow nebo cron)       | Ano        | Ne (Použití Azure Automation nebo Plánovač úloh Windows)         |
|**Podporované systémy souborů**     | ADL, HDFS, WASB, S3, GS, CFS        |Četné, viz [Konektory](../data-factory/connector-azure-blob-storage.md).         | ADL na ADL, WASB na ADL (pouze ve stejné oblasti)        |
|**Podpora pro Operační režim**     |Jakýkoli operační systémem s hadoopem         | Není dostupné.          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití funkce Distcp pro přesun dat mezi dvěma umístěními

Zkratka pro distribuované kopie, Distcp je linuxový příkazový řádek nástroj, který je dodáván s Hadoop a poskytuje distribuovaný pohyb dat mezi dvěma místy. Dvě umístění mohou být Data Lake Storage Gen1, HDFS, WASB nebo S3. Tento nástroj používá MapReduce úlohy v clusteru Hadoop (například HDInsight) na škálování na všech uzlech. Distcp je považován za nejrychlejší způsob, jak přesunout velká data bez speciálních síťových kompresních zařízení. Distcp také poskytuje možnost aktualizovat pouze rozdíly mezi dvěma umístěními, zpracovává automatické opakování, stejně jako dynamické škálování výpočetních prostředků. Tento přístup je neuvěřitelně efektivní, pokud jde o replikaci věcí, jako je Hive/Spark tabulky, které mohou mít mnoho velkých souborů v jednom adresáři a chcete pouze kopírovat přes upravená data. Z těchto důvodů distcp je nejvíce doporučený nástroj pro kopírování dat mezi úložišti velkých objemů dat.

Kopírovat úlohy mohou být spuštěny pracovními postupy Apache Oozie pomocí frekvenčních nebo datových aktivačních událostí, stejně jako linuxových cron úloh. Pro úlohy intenzivní replikace se doporučuje svést samostatný cluster HDInsight Hadoop, který lze vyladit a škálovat speciálně pro úlohy kopírování. Tím je zajištěno, že úlohy kopírování nenarušují kritické úlohy. Pokud běží replikace na dostatečně široké frekvenci, clusteru lze dokonce vzít dolů mezi každou úlohu. Pokud přejdeme k selhání do sekundární oblasti, ujistěte se, že jiný cluster je také spředené v sekundární oblasti replikovat nová data zpět do primárního účtu Data Lake Storage Gen1, jakmile se vrátí zpět. Příklady použití Distcp, najdete [v tématu použití Distcp ke kopírování dat mezi objekty BLOB úložiště Azure a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Plánování úloh kopírování pomocí Azure Data Factory

Azure Data Factory lze také naplánovat úlohy kopírování pomocí **aktivity kopírování**a lze dokonce nastavit na frekvenci pomocí **Průvodce kopírováním**. Mějte na paměti, že Azure Data Factory má limit cloudových jednotek přesunu dat (DMU) a nakonec omezuje propustnost/výpočetní prostředky pro velké datové úlohy. Azure Data Factory navíc aktuálně nenabízí rozdílové aktualizace mezi účty Data Lake Storage Gen1, takže složky, jako jsou tabulky Hive, by vyžadovaly úplnou kopii k replikaci. Další informace o kopírování pomocí data Factory naleznete v [průvodci optimalizací aktivity kopírování.](../data-factory/copy-activity-performance.md)

### <a name="adlcopy"></a>AdlCopy

AdlCopy je nástroj příkazového řádku systému Windows, který umožňuje kopírovat data mezi dvěma účty Data Lake Storage Gen1 pouze ve stejné oblasti. Nástroj AdlCopy poskytuje samostatnou možnost nebo možnost použít účet Azure Data Lake Analytics ke spuštění úlohy kopírování. Ačkoli byl původně vytvořen pro kopie na vyžádání na rozdíl od robustní replikace, poskytuje další možnost distribuovaného kopírování mezi účty Data Lake Storage Gen1 ve stejné oblasti. Pro spolehlivost doporučujeme použít možnost Premium Data Lake Analytics pro všechny produkční úlohy. Samostatná verze může vrátit zaneprázdněné odpovědi a má omezené škálování a monitorování.

Stejně jako Distcp, AdlCopy musí být řízena něco jako Azure Automation nebo Plánovač úloh systému Windows. Stejně jako u Data Factory, AdlCopy nepodporuje kopírování pouze aktualizované soubory, ale rekopie a přepsat existující soubory. Další informace a příklady použití AdlCopy najdete [v tématu Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Aspekty monitorování

Data Lake Storage Gen1 poskytuje podrobné diagnostické protokoly a auditování. Data Lake Storage Gen1 poskytuje některé základní metriky na portálu Azure pod účtem Data Lake Storage Gen1 a v Azure Monitoru. Dostupnost Data Lake Storage Gen1 se zobrazí na webu Azure Portal. Tato metrika je však aktualizovánkaždých sedm minut a nelze dotazovánprostřednictvím veřejně vystavené rozhraní API. Chcete-li získat nejaktuálnější dostupnost účtu Gen1 úložiště datového jezera, musíte spustit vlastní syntetické testy k ověření dostupnosti. Další metriky, jako je celkové využití úložiště, požadavky na čtení a zápis a příchozí přenos dat/odchozí přenos dat může trvat až 24 hodin k aktualizaci. Takže více aktuální metriky musí být vypočteny ručně prostřednictvím hadoop nástroje příkazového řádku nebo agregaci informací protokolu. Nejrychlejší způsob, jak získat nejnovější využití úložiště, je spuštění tohoto příkazu HDFS z uzlu clusteru Hadoop (například hlavní uzel):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Diagnostika exportu dat úložiště jezera Gen1

Jedním z nejrychlejších způsobů, jak získat přístup k prohledávatelným protokolům z Data Lake Storage Gen1, je povolit přesouvání protokolu do **log Analytics** pod okně **Diagnostika** pro účet Gen1 úložiště datového jezera. To poskytuje okamžitý přístup k příchozím protokolům s filtry času a obsahu spolu s možnostmi upozornění (e-mail / webhook) aktivovaných v intervalech 15 minut. Pokyny najdete [v tématu Přístup k diagnostickým protokolům pro Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Pro více výstrah v reálném čase a větší kontrolu nad tím, kde přistát protokoly, zvažte export protokolů do Azure EventHub, kde obsah lze analyzovat jednotlivě nebo v časovém okně za účelem odeslání oznámení v reálném čase do fronty. Samostatná aplikace, jako je [například aplikace logiky](../connectors/connectors-create-api-azure-event-hubs.md) pak spotřebovávají a sdělují výstrahy příslušnému kanálu, stejně jako odesílat metriky do nástrojů pro sledování, jako je NewRelic, Datadog nebo AppDynamics. Případně pokud používáte nástroj jiného výrobce, jako je například ElasticSearch, můžete exportovat protokoly do úložiště objektů Blob a použít [modul plug-in Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) ke spotřebovávání dat do zásobníku Elasticsearch, Kibana a Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Zapnutí protokolování na úrovni ladění v HDInsightu

Pokud není zapnuto odesílání protokolu Data Lake Storage Gen1, Azure HDInsight také poskytuje způsob, jak zapnout protokolování na [straně klienta pro Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) přes log4j. V **konfiguracích Ambari** > **YARN** > **Config** > **Advanced yarn-log4j**je nutné nastavit následující vlastnost :

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

Jakmile je vlastnost nastavena a uzly jsou restartovány, diagnostika Data Lake Storage Gen1 je zapsána\<\>do protokolů YARN na uzlech (/tmp/ user /yarn.log) a důležité podrobnosti, jako jsou chyby nebo omezení (kód chyby HTTP 429) lze sledovat. Stejné informace lze také sledovat v protokolech Azure Monitor nebo všude tam, kde protokoly jsou dodávány do v okně [diagnostika](data-lake-store-diagnostic-logs.md) účtu Data Lake Storage Gen1. Doporučuje se alespoň mít protokolování na straně klienta zapnuté nebo využít možnost přesouvání protokolu s daty Lake Storage Gen1 pro provozní viditelnost a snadnější ladění.

### <a name="run-synthetic-transactions"></a>Spustit syntetické transakce

V současné době metrika dostupnosti služby pro Data Lake Storage Gen1 na webu Azure Portal má 7minutové okno aktualizace. Také nelze dotazovat pomocí veřejně vystavené rozhraní API. Proto se doporučuje vytvořit základní aplikaci, která provádí syntetické transakce do data lake storage Gen1, které mohou poskytnout až minutu dostupnosti. Příkladem může být vytvoření webjob, aplikace logiky nebo aplikace Azure function app k provedení čtení, vytvoření a aktualizace proti Data Lake Storage Gen1 a odeslat výsledky do vašeho řešení monitorování. Operace lze provést v dočasné složce a potom odstranit po testu, který může být spuštěn každých 30-60 sekund, v závislosti na požadavcích.

## <a name="directory-layout-considerations"></a>Důležité informace o rozložení adresáře

Při vykládce dat do datového jezera je důležité předem naplánovat strukturu dat tak, aby bylo možné efektivně využívat zabezpečení, dělení a zpracování. Mnoho z následujících doporučení lze použít bez ohledu na to, zda je to s Azure Data Lake Storage Gen1, Blob Storage nebo HDFS. Každé pracovní vytížení má různé požadavky na způsob spotřebování dat, ale níže jsou některé běžné rozložení, které je třeba zvážit při práci s IoT a dávkovými scénáři.

### <a name="iot-structure"></a>Struktura IoT

V úlohách IoT může být velké množství dat vykládané v úložišti dat, která se rozprostírá napříč mnoha produkty, zařízeními, organizacemi a zákazníky. Je důležité předem naplánovat rozložení adresáře pro organizaci, zabezpečení a efektivní zpracování dat pro spotřebitele s následným proudem. Obecnou šablonou, kterou je třeba zvážit, může být následující rozložení:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Například přistávací telemetrie pro letecký motor ve Velké Británii může vypadat jako následující struktura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Existuje důležitý důvod, proč umístit datum na konec struktury složek. Pokud chcete uzamknout určité oblasti nebo předmět záležitosti pro uživatele / skupiny, pak můžete snadno tak učinit s oprávněními POSIX. V opačném případě, pokud by bylo nutné omezit určitou skupinu zabezpečení na prohlížení pouze dat ve Velké Británii nebo určitých rovin, s datovou strukturou vpředu by bylo vyžadováno samostatné oprávnění pro mnoho složek v každé hodinové složce. Navíc s datovou strukturu vpředu by exponenciálně zvýšit počet složek, jak šel čas.

### <a name="batch-jobs-structure"></a>Struktura dávkových úloh

Z vysoké úrovně, běžně používaný přístup v dávkovém zpracování je přistát data ve složce "in". Poté, co jsou data zpracována, vložte nová data do složky "out", aby je mohly využívat podřízené procesy. Tato adresářová struktura je někdy vidět pro úlohy, které vyžadují zpracování na jednotlivé soubory a nemusí vyžadovat masivně paralelní zpracování přes velké datové sady. Stejně jako výše doporučená struktura IoT má dobrá adresářová struktura složky nadřazené úrovně pro věci, jako je oblast a předmět (například organizace, produkt/výrobce). Tato struktura pomáhá při zabezpečení dat v celé organizaci a lepší správě dat ve vašich úlohách. Dále zvažte datum a čas ve struktuře, aby bylo možné lépe organizace, filtrované vyhledávání, zabezpečení a automatizace ve zpracování. Úroveň rozlišovací schopnost pro strukturu kalendářních dat je určena intervalem, ve kterém jsou data nahrána nebo zpracována, například každou hodinu, denně nebo dokonce měsíčně.

Někdy je zpracování souborů neúspěšné z důvodu poškození dat nebo neočekávaných formátů. V takových případech může mít adresářová struktura prospěch z **/bad** složky přesunout soubory pro další kontrolu. Dávková úloha může také zpracovat vykazování nebo oznámení těchto *chybných* souborů pro ruční zásah. Zvažte následující strukturu šablony:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Marketingová firma například dostává denní datové výpisy zákaznických aktualizací od svých klientů v Severní Americe. Před zpracováním a po zpracování může vypadat jako následující úryvek:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

V běžném případě dávkových dat zpracovávaných přímo do databází, jako je Například Hive nebo tradiční databáze SQL, není potřeba složky **/in** nebo **/out,** protože výstup již přejde do samostatné složky pro tabulku Hive nebo externí databázi. Například denní výpisy od zákazníků by přistát do svých příslušných složek a orchestrace něco jako Azure Data Factory, Apache Oozie nebo Apache Airflow by spustit denní Hive nebo Spark úlohy ke zpracování a zápisu dat do tabulky Hive.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Řízení přístupu v Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Zabezpečení v úložišti datových jezer Azure Gen1](data-lake-store-security-overview.md)
* [Optimalizace Azure Data Lake Storage Gen1 pro výkon](data-lake-store-performance-tuning-guidance.md)
* [Pokyny k ladění výkonu pro používání HDInsight Spark s Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Pokyny pro ladění výkonu pro používání HDInsight Hive s Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Vytváření clusterů HDInsight pomocí data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
