---
title: Osvědčené postupy pro používání Azure Data Lake Storage Gen1 | Microsoft Docs
description: Seznamte se s osvědčenými postupy při přijímání, zabezpečení dat a výkonu souvisejícím s používáním Azure Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store).
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 291a5850540ea7d7d24a4a544c1eb65183df8ffb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667737"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Osvědčené postupy pro používání Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

V tomto článku se seznámíte s osvědčenými postupy a pokyny pro práci s Azure Data Lake Storage Gen1. Tento článek poskytuje informace o zabezpečení, výkonu, odolnosti a monitorování pro Data Lake Storage Gen1. Před Data Lake Storage Gen1 práce s skutečně velkými objemy dat ve službách, jako je Azure HDInsight, byla složitá. Museli jste horizontálních oddílů data napříč několika účty BLOB Storage, aby bylo možné dosáhnout úložiště řádu petabajtů a optimálního výkonu v tomto rozsahu. Při Data Lake Storage Gen1 se většina pevných omezení velikosti a výkonu odebere. Existují však i některé okolnosti, které tento článek popisuje, abyste dosáhli nejlepšího výkonu s použitím Data Lake Storage Gen1.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Azure Data Lake Storage Gen1 nabízí řízení přístupu POSIX a podrobné auditování pro uživatele, skupiny a instanční objekty služby Azure Active Directory (Azure AD). Tyto ovládací prvky přístupu můžou být nastavené na existující soubory a složky. Ovládací prvky přístupu lze také použít k vytvoření výchozích hodnot, které lze použít pro nové soubory nebo složky. Pokud jsou oprávnění nastavena na existující složky a podřízené objekty, musí být oprávnění šířena rekurzivně u každého objektu. Pokud existuje velký počet souborů, může rozšíření oprávnění trvat dlouhou dobu. Doba trvání může být v rozsahu od 30-50 objektů zpracovaných za sekundu. Proto Naplánujte strukturu složek a skupiny uživatelů odpovídajícím způsobem. V opačném případě může dojít k neočekávaným zpožděním a problémům při práci s daty.

Předpokládejme, že máte složku s 100 000 podřízenými objekty. Pokud převezmete dolní mez 30 objektů zpracovaných za sekundu, můžete aktualizovat oprávnění pro celou složku, což může trvat hodinu. Další podrobnosti o Data Lake Storage Gen1ech ACL jsou k dispozici v [řízení přístupu v Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Pro zlepšení výkonu při rekurzivním přiřazování seznamů ACL můžete použít nástroj Azure Data Lake Command-Line. Nástroj vytvoří více vláken a rekurzivní logiku navigace pro rychlé použití seznamů ACL na miliony souborů. Nástroj je k dispozici pro Linux a Windows a [dokumentaci](https://github.com/Azure/data-lake-adlstool) a [soubory ke stažení](https://aka.ms/adlstool-download) pro tento nástroj najdete na GitHubu. Tato vylepšení výkonu lze povolit pomocí vlastních nástrojů napsaných pomocí sad Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) a [Java](data-lake-store-get-started-java-sdk.md) SDK.

### <a name="use-security-groups-versus-individual-users"></a>Použití skupin zabezpečení oproti jednotlivým uživatelům

Při práci s velkými objemy dat v Data Lake Storage Gen1 se pravděpodobně používá instanční objekt, který umožňuje službám, jako je Azure HDInsight, pracovat s daty. Mohou však nastat případy, kdy potřebují k datům přístup i jednotliví uživatelé. V takových případech je nutné použít Azure Active Directory [skupiny zabezpečení](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) místo přiřazování jednotlivých uživatelů ke složkám a souborům.

Když je skupině zabezpečení přiřazena oprávnění, přidání nebo odebrání uživatelů ze skupiny nevyžaduje žádné aktualizace Data Lake Storage Gen1. To taky pomáhá zajistit, abyste nepřekročili limit [32 přístupu a výchozí seznamy ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) (to zahrnuje čtyři seznamy ACL ve stylu POSIX, které jsou vždycky přidružené ke všem souborům a složkám: [vlastnící uživatel](data-lake-store-access-control.md#the-owning-user), [vlastnící skupina](data-lake-store-access-control.md#the-owning-group), [Maska](data-lake-store-access-control.md#the-mask)a další).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny

Jak je popsáno, když uživatelé potřebují přístup k Data Lake Storage Gen1, je nejvhodnější použít skupiny zabezpečení Azure Active Directory. Některé doporučené skupiny, které je třeba začít používat, mohou být **ReadOnlyUsers**, **WriteAccessUsers**a **FullAccessUsers** pro kořen účtu a dokonce oddělené pro klíčové podsložky. Pokud existují nějaké jiné očekávané skupiny uživatelů, které by mohly být později přidány, ale ještě nebyly identifikovány, můžete zvážit vytvoření fiktivních skupin zabezpečení, které mají přístup k určitým složkám. Použití skupiny zabezpečení zajišťuje, že později nepotřebujete dlouhou dobu zpracování pro přiřazení nových oprávnění k tisícům souborů.

### <a name="security-for-service-principals"></a>Zabezpečení instančních objektů

Služby, jako je Azure HDInsight, se obvykle používají pro přístup k datům v Data Lake Storage Gen1 Azure Active Directory instanční objekty. V závislosti na požadavcích na přístup mezi několika úlohami může být nutné zajistit některé z hlediska zabezpečení uvnitř i mimo organizaci. U mnoha zákazníků může být pro jeden objekt služby Azure Active Directory dostačující a může mít úplná oprávnění v kořenu Data Lake Storage Gen1 účtu. Jiní zákazníci mohou vyžadovat více clusterů s různými instančními objekty, kde jeden cluster má úplný přístup k datům a druhý cluster s přístupem pro čtení. Stejně jako u skupin zabezpečení můžete zvážit vytvoření instančního objektu pro každý předpokládaný scénář (čtení, zápis, úplné), jakmile se vytvoří účet Data Lake Storage Gen1.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Povolit bránu Data Lake Storage Gen1 firewall s přístupem ke službě Azure

Data Lake Storage Gen1 podporuje možnost zapnout bránu firewall a omezit přístup jenom na služby Azure, což se doporučuje pro menší vektory útoku mimo neoprávněné vniknutí. Bránu firewall lze povolit na účtu Data Lake Storage Gen1 v Azure Portal prostřednictvím **brány firewall**  >  **Povolit bránu firewall (zapnuto)**  >  **Povolit přístup k možnostem služeb Azure** .

![Nastavení brány firewall v Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Nastavení brány firewall v Data Lake Storage Gen1")

Po povolení brány firewall budou mít přístup k Data Lake Storage Gen1 jenom služby Azure, jako je HDInsight, Data Factory, Azure synapse Analytics (dřív SQL Data Warehouse) atd. Kvůli překladu interních síťových adres, které používá Azure, Data Lake Storage Gen1 firewall neumožňuje omezit konkrétní služby na IP adresu a je určený jenom pro omezení koncových bodů mimo Azure, jako je místní.

## <a name="performance-and-scale-considerations"></a>Požadavky na výkon a škálování

Jednou z nejúčinnějších funkcí Data Lake Storage Gen1 je, že se z propustnosti dat odstraňují pevné limity. Odebráním těchto limitů umožníte zákazníkům zvětšit velikost dat a požadavky na výkon, aniž byste museli data horizontálních oddílů. Jedním z nejdůležitějších důležitých informací pro optimalizaci Data Lake Storage Gen1 výkonu je to, že při daném paralelismus funguje nejlépe.

### <a name="improve-throughput-with-parallelism"></a>Zlepšení propustnosti pomocí paralelismu

Pro dosažení optimální propustnosti čtení a zápisu zvažte 8-12 vláken na jádro. Důvodem je blokování čtení/zápisu v jednom vláknu a další vlákna můžou na virtuálním počítači způsobit vyšší souběžnost. Aby bylo zajištěno, že úrovně jsou v pořádku a lze je zvýšit, nezapomeňte monitorovat využití procesoru virtuálního počítače.

### <a name="avoid-small-file-sizes"></a>Vyhnout se malým velikostem souborů

Oprávnění a auditování POSIX v Data Lake Storage Gen1 přináší s režií, které se při práci s mnoha malými soubory bude zjevné. Osvědčeným postupem je, aby se data mohla dávkovat do větších souborů a napsané tisíce nebo miliony malých souborů do Data Lake Storage Gen1. Předcházení malým velikostem souborů může mít několik výhod, například:

* Snížení kontrol ověřování napříč více soubory
* Omezená otevřená připojení souborů
* Rychlejší kopírování a replikace
* Méně souborů ke zpracování při aktualizaci Data Lake Storage Gen1 oprávnění POSIX

V závislosti na tom, jaké služby a úlohy data používají, je vhodná velikost pro soubory, které je třeba zvážit, 256 MB nebo vyšší. Pokud se velikosti souborů při vykládku v Data Lake Storage Gen1 nedají dávkovat, můžete mít samostatnou úlohu komprimace, která tyto soubory kombinuje do větších. Další informace a doporučení týkající se velikostí souborů a uspořádání dat v Data Lake Storage Gen1 najdete v tématu [Struktura sady dat](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Velké velikosti souborů a potenciální dopad na výkon

I když Data Lake Storage Gen1 podporuje velké soubory až do petabajty velikosti, optimálního výkonu a v závislosti na procesu, který data čte, nemusí být ideální pro větší než 2 GB v průměru. Například při použití **Distcp** ke kopírování dat mezi umístěními nebo různými účty úložiště jsou soubory nejlepší úrovní členitosti, která se používá k určení úloh mapy. Pokud tedy kopírujete 10 souborů o 1 TB, přidělí se maximálně 10 mapovačů. Pokud máte k dispozici spoustu souborů s přiřazenými mapovači, zpočátku budou mapovače fungovat paralelně pro přesunutí velkých souborů. Protože se ale úloha začne zasílat, zůstane přidělená jenom pár mapovačů a můžete zablokovat jedno mapovače přiřazené k velkému souboru. Společnost Microsoft odeslala vylepšení Distcp k vyřešení tohoto problému v budoucích verzích Hadoop.

Dalším příkladem, který je třeba zvážit, je použití Azure Data Lake Analytics s Data Lake Storage Gen1. V závislosti na zpracování provedené extrakcí můžou některé soubory, které se nedají rozdělit (například XML, JSON), způsobit výkon, pokud je větší než 2 GB. V případech, kdy mohou být soubory rozděleny pomocí extraktoru (například CSV), jsou upřednostňovány velké soubory.

### <a name="capacity-plan-for-your-workload"></a>Plán kapacity pro vaše zatížení

Azure Data Lake Storage Gen1 odstraní limity omezování pevných vstupně-výstupních operací, které jsou umístěné v účtech BLOB Storage. Nicméně stále existují tichá omezení, která je potřeba vzít v úvahu. Výchozí limity omezování příchozího a odchozího přenosu splňují požadavky většiny scénářů. Pokud vaše zatížení vyžaduje zvýšení limitu, pracujte s podporou Microsoftu. Podívejte se také na omezení během fáze ověření koncepce, aby při výrobě nedošlo k omezením omezení vstupu/výstupu. Pokud k tomu dojde, může to vyžadovat, abyste čekali na Ruční zvýšení od technického týmu Microsoftu. Pokud dojde k omezení vstupně-výstupních operací, Azure Data Lake Storage Gen1 vrátí kód chyby 429 a v ideálním případě by se měl opakovat s odpovídající exponenciální zásadou omezení rychlosti.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimalizujte zápisy s využitím vyrovnávací paměti ovladače Data Lake Storage Gen1.

Chcete-li optimalizovat výkon a snížit IOPS při zápisu do Data Lake Storage Gen1 z Hadoop, provádějte operace zápisu co nejblíže velikosti vyrovnávací paměti ovladače Data Lake Storage Gen1. Před vyprázdněním se snažte nepřekročit velikost vyrovnávací paměti, například při streamování pomocí Apache Storm nebo úloh streamování Sparku. Při psaní do Data Lake Storage Gen1 ze služby HDInsight/Hadoop je důležité znát, že Data Lake Storage Gen1 má ovladač s vyrovnávací pamětí o velikosti 4 MB. Podobně jako u mnoha ovladačů systému souborů je možné tuto vyrovnávací paměť ručně vyprázdnit a dosáhnout velikosti 4 MB. V takovém případě se okamžitě vyprázdní do úložiště, pokud další zápis překračuje maximální velikost vyrovnávací paměti. Pokud je to možné, musíte se vyhnout přetečení nebo významné podtečení vyrovnávací paměti při synchronizaci a vyprazdňování zásad podle počtu nebo časového okna.

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti

Při navrhování systému pomocí Data Lake Storage Gen1 nebo libovolné cloudové služby musíte vzít v úvahu požadavky na dostupnost a postup reakce na potenciální přerušení ve službě. Problém může být lokalizovaný na konkrétní instanci nebo dokonce v celé oblasti, takže plán pro oba je důležitý. V závislosti na **době obnovení** a **cíli SLA bodu obnovení** pro vaše zatížení můžete zvolit více nebo méně agresivní strategii pro vysokou dostupnost a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Vysoká dostupnost (HA) a zotavení po havárii (DR) se někdy můžou kombinovat dohromady, i když každá z nich má mírně odlišnou strategii, zejména pokud se data nacházejí. Data Lake Storage Gen1 už v digestoři zpracovává replikaci 3x, která chrání před selháním lokalizovaných hardwarových zařízení. Vzhledem k tomu, že replikace mezi oblastmi není v nástroji integrována, musíte tuto skupinu spravovat sami. Při sestavování plánu pro HA potřebuje v případě přerušení služby přístup k nejnovějším datům co nejrychleji, a to tak, že přepnete na samostatnou replikovanou instanci lokálně nebo v nové oblasti.

V strategii zotavení po havárii je pro přípravu na nepravděpodobné selhání oblasti velmi důležité, aby byla data replikována do jiné oblasti. Tato data můžou být zpočátku stejná jako replikovaná data o HA. Je však třeba vzít v úvahu také požadavky na hraniční případy, jako je poškození dat, kde můžete chtít vytvořit periodické snímky, na které se vrátí. V závislosti na významu a velikosti dat zvažte použití rozdílových snímků s 1, 6 a 24 hodinami v místním nebo sekundárním úložišti v závislosti na tolerancích rizik.

V případě odolnosti dat pomocí Data Lake Storage Gen1 se doporučuje geograficky replikovat data do samostatné oblasti s frekvencí, která splňuje požadavky na HA/DR, v ideálním případě každou hodinu. Tato frekvence replikace minimalizuje obrovský pohyb dat, který může mít v hlavním systému konkurenční požadavky na propustnost a lepší cíl bodu obnovení (RPO). Kromě toho byste měli zvážit způsob, jakým se aplikace používá Data Lake Storage Gen1 k automatickému převzetí služeb při selhání sekundárním účtem prostřednictvím triggerů monitorování nebo délky neúspěšných pokusů nebo aspoň odesílání oznámení správcům k ručnímu zásahu. Mějte na paměti, že při převzetí služeb při selhání a čekání na přechod služby zpátky do režimu online. Pokud se data nedokončila, může převzetí služeb při selhání způsobit potenciální ztrátu dat, nekonzistenci nebo složitou sloučení dat.

Níže jsou uvedené hlavní tři Doporučené možnosti pro orchestraci replikace mezi Data Lake Storage Gen1 účty a hlavní rozdíly mezi nimi.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Omezení škálování**     | Ohraničené uzly pracovního procesu        | Omezeno maximálními jednotkami pohybu cloudových dat        | Svázáno podle analytických jednotek        |
|**Podporuje kopírování rozdílových souborů.**     |   Yes      | No         | No         |
|**Integrovaná orchestrace**     |  Ne (použití Oozieho toku nebo úloh cron)       | Yes        | Ne (použít Azure Automation nebo Windows Plánovač úloh)         |
|**Podporované systémy souborů**     | ADL, HDFS, WASB, S3, GS, CFS        |Množství, viz [konektory](../data-factory/connector-azure-blob-storage.md).         | ADL na ADL, WASB na ADL (jenom stejná oblast)        |
|**Podpora operačního systému**     |Libovolný operační systém se systémem Hadoop         | Není k dispozici          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití Distcp k přesunu dat mezi dvěma místy

Pro distribuované kopírování je Distcp nástroj příkazového řádku pro Linux, který je součástí systému Hadoop a poskytuje přenos distribuovaných dat mezi dvěma umístěními. Tato dvě umístění můžou být Data Lake Storage Gen1, HDFS, WASB nebo S3. Tento nástroj používá úlohy MapReduce v clusteru Hadoop (například HDInsight) k horizontálnímu navýšení kapacity na všech uzlech. Distcp je považována za nejrychlejší způsob, jak přesunout velké objemy dat bez speciálního síťového kompresního zařízení. Distcp také nabízí možnost aktualizovat jenom rozdíly mezi dvěma umístěními, zpracovává automatické opakování a také dynamické škálování výpočetních prostředků. Tento přístup je neuvěřitelně účinný, pokud je k replikaci věcí, jako jsou tabulky podregistru nebo Sparku, které můžou mít mnoho velkých souborů v jednom adresáři a vy chcete zkopírovat jenom změněná data. Z těchto důvodů je Distcp doporučeným nástrojem pro kopírování dat mezi úložišti velkých objemů dat.

Úlohy kopírování se můžou aktivovat pracovními postupy Apache Oozie pomocí frekvencí nebo triggerů dat a také úloh Linux cron. Pro náročné úlohy replikace doporučujeme aktivovat samostatný cluster HDInsight Hadoop, který je možné vyladit a škálovat speciálně pro úlohy kopírování. Tím se zajistí, že úlohy kopírování nebudou v konfliktu s kritickými úlohami. Pokud spustíte replikaci s dostatečnou frekvencí, může být cluster dokonce mimo provoz mezi jednotlivými úlohami. Pokud se převezme služby při selhání do sekundární oblasti, ujistěte se, že se v sekundární oblasti zavedl i další cluster, aby se po návratu do primárního Data Lake Storage Gen1 účtu replikují nová data zpátky na primární účet. Příklady použití Distcp najdete v tématu [Použití Distcp ke kopírování dat mezi objekty blob Azure Storage a data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Plánování úloh kopírování pomocí Azure Data Factory

Azure Data Factory lze také použít k plánování úloh kopírování pomocí **aktivity kopírování**a je možné ji dokonce nastavit na frekvenci prostřednictvím **Průvodce kopírováním**. Mějte na paměti, že Azure Data Factory má limit DMUsch přenosů dat v cloudu, a nakonec si zajistěte, aby propustnost a výpočet pro úlohy s velkým objemem dat byly omezené. Kromě toho Azure Data Factory aktuálně nenabízí rozdílové aktualizace mezi účty Data Lake Storage Gen1, takže složky jako tabulky podregistru by vyžadovaly replikaci úplné kopie. Další informace o kopírování pomocí Data Factory najdete v [Průvodci optimalizací aktivity kopírování](../data-factory/copy-activity-performance.md) .

### <a name="adlcopy"></a>AdlCopy

AdlCopy je nástroj příkazového řádku Windows, který umožňuje kopírovat data mezi dvěma Data Lake Storage Gen1 účty pouze v rámci stejné oblasti. Nástroj AdlCopy poskytuje samostatnou možnost nebo možnost použít účet Azure Data Lake Analytics ke spuštění úlohy kopírování. I když byl původně sestaven pro kopie na vyžádání na rozdíl od robustní replikace, poskytuje další možnost provádět distribuované kopírování mezi Data Lake Storage Gen1 účty v rámci stejné oblasti. Pro zajištění spolehlivosti doporučujeme použít možnost Premium Data Lake Analytics pro všechny produkční úlohy. Samostatná verze může vracet zaneprázdněné odpovědi a má omezené škálování a monitorování.

Podobně jako Distcp musí být AdlCopy orchestrace, například Azure Automation nebo Windows Plánovač úloh. Stejně jako u Data Factory nepodporuje AdlCopy kopírování jenom aktualizovaných souborů, ale překopíruje a přepíše stávající soubory. Další informace a příklady použití AdlCopy najdete v tématu [kopírování dat z objektů blob Azure Storage do data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Monitorování – požadavky

Data Lake Storage Gen1 poskytuje podrobné diagnostické protokoly a auditování. Data Lake Storage Gen1 poskytuje základní metriky v Azure Portal pod účtem Data Lake Storage Gen1 a v Azure Monitor. V Azure Portal se zobrazí dostupnost Data Lake Storage Gen1. Tato metrika se ale aktualizuje každých sedm minut a nedá se dotazovat prostřednictvím veřejně vystaveného rozhraní API. Chcete-li získat nejaktuálnější dostupnost účtu Data Lake Storage Gen1, je nutné spustit vlastní syntetické testy k ověření dostupnosti. Další metriky, jako je například celkové využití úložiště, požadavky na čtení a zápis a příchozí nebo odchozí přenos dat, může trvat až 24 hodin, než se aktualizuje. Další aktuální metriky proto musí být vypočítávány ručně prostřednictvím nástrojů příkazového řádku Hadoop nebo agregací informací o protokolu. Nejrychlejší způsob, jak získat nejnovější využití úložiště, je spuštění tohoto příkazu HDFS z uzlu clusteru Hadoop (například hlavní uzel):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportovat diagnostiku Data Lake Storage Gen1

Jedním z nejrychlejších způsobů, jak získat přístup k prohledávatelným protokolům z Data Lake Storage Gen1, je povolit přesouvání protokolu do **Log Analytics** v okně **diagnostiky** pro účet Data Lake Storage Gen1. To poskytuje okamžitý přístup k příchozím protokolům s filtry času a obsahu společně s možnostmi upozorňování (e-mail nebo Webhook) aktivované do 15 minut. Pokyny najdete v tématu [přístup k diagnostickým protokolům pro Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Pro další upozorňování v reálném čase a lepší kontrolu nad tím, kde se mají tyto protokoly nakládat, zvažte možnost exportovat protokoly do služby Azure EventHub, kde můžete obsah analyzovat jednotlivě nebo v časovém intervalu, aby bylo možné odesílat oznámení v reálném čase do fronty. Samostatná aplikace, jako je například [Aplikace logiky](../connectors/connectors-create-api-azure-event-hubs.md) , pak může využít a sdělit výstrahy do příslušného kanálu a také odesílat metriky nástrojům pro monitorování, jako je NewRelic, služby Datadog nebo AppDynamics. Případně, pokud používáte nástroj třetí strany, jako je ElasticSearch, můžete exportovat protokoly pro Blob Storage a použít [modul plug-in Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) ke využívání dat do zásobníku Elasticsearch, Kibana a LOGSTASH (Elk).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Zapnutí protokolování na úrovni ladění v HDInsight

Pokud Data Lake Storage Gen1 přesouvání protokolu není zapnuté, Azure HDInsight také poskytuje způsob, jak zapnout [protokolování na straně klienta pro data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) prostřednictvím log4j. V konfiguraci **Ambari**  >  **nitě**  >  **Konfigurace**  >  **Advanced nitě-log4j**musíte nastavit následující vlastnost:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Jakmile je vlastnost nastavená a uzly se restartují, Data Lake Storage Gen1 diagnostika se zapisuje do protokolů PŘÍZe na uzlech (/TMP/ \<user\> /YARN.log) a důležité podrobnosti, jako jsou chyby nebo omezování (kód chyby HTTP 429), se dají monitorovat. Stejné informace je také možné monitorovat v protokolech Azure Monitor nebo kdykoli jsou do okna [Diagnostika](data-lake-store-diagnostic-logs.md) účtu Data Lake Storage Gen1 odesílány protokoly. Doporučuje se aspoň zapnout protokolování na straně klienta nebo využít možnost přesouvání protokolu s Data Lake Storage Gen1 pro provozní viditelnost a snazší ladění.

### <a name="run-synthetic-transactions"></a>Spouštění syntetických transakcí

V současné době je metrika dostupnosti služby pro Data Lake Storage Gen1 v Azure Portal okno aktualizace o 7 minut. Nedají se také dotazovat pomocí veřejně vystaveného rozhraní API. Proto se doporučuje sestavit základní aplikaci, která provádí syntetické transakce Data Lake Storage Gen1, které mohou poskytnout až minutovou dostupnost. Příkladem může být vytvoření webové úlohy, aplikace logiky nebo Azure Function App k provedení čtení, vytvoření a aktualizace Data Lake Storage Gen1 a odeslání výsledků do řešení monitorování. Operace se dají provést v dočasné složce a pak se odstraní po testu, což se může spouštět každých 30-60 sekund v závislosti na požadavcích.

## <a name="directory-layout-considerations"></a>Požadavky na rozložení adresáře

Při vykládku dat do data Lake je důležité předem naplánovat strukturu dat tak, aby bylo možné efektivně využívat zabezpečení, dělení a zpracování. Mnohé z následujících doporučení se dají použít, pokud se Azure Data Lake Storage Gen1, Blob Storage nebo HDFS. Každé zatížení má různé požadavky na to, jak se data spotřebovávají, ale níže jsou několik běžných rozložení, která je potřeba vzít v úvahu při práci se scénáři IoT a batch.

### <a name="iot-structure"></a>Struktura IoT

V úlohách IoT se můžete setkat s daty vydanými v úložišti dat, která se nacházejí v různých produktech, zařízeních, organizacích a zákaznících. Je důležité předem naplánovat rozložení adresáře pro organizaci, zabezpečení a efektivní zpracování dat pro uživatele se vzdálení datovými proudy. Obecná šablona, kterou je třeba zvážit, může být následující rozložení:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Například vykládka telemetrie pro stroj v letadle v rámci Velké Británie může vypadat jako tato struktura:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Je důležité důvod, abyste datum umístili na konec struktury složek. Pokud chcete některé oblasti nebo věci v předmětech uzamknout pro uživatele nebo skupiny, můžete k tomu snadno využít oprávnění POSIX. V opačném případě, pokud je potřeba omezit určitou skupinu zabezpečení, aby se zobrazila pouze data o Spojeném království nebo určité rovině, musí se v každé složce v rámci každé hodiny vyžadovat samostatné oprávnění. Kromě toho struktura data v popředí by měla exponenciálně zvýšit počet složek v době, kdy se provedla.

### <a name="batch-jobs-structure"></a>Struktura úloh Batch

Z vysoké úrovně se běžně používaným přístupem v dávkovém zpracování slouží k obstání dat ve složce "in". Až se data zpracují, vložte nová data do složky "out", aby se procesy pro příjem dat využily. Tato adresářová struktura se občas zobrazuje pro úlohy, které vyžadují zpracování na jednotlivých souborech a nemusí vyžadovat výkonné paralelní zpracování v rámci velkých datových sad. Podobně jako u výše zmíněné struktury IoT má vhodná adresářová struktura pro věci, jako jsou například záležitosti oblasti a předmět (například organizace, produkt/výrobce). Tato struktura pomáhá zabezpečit data napříč vaší organizací a lépe spravovat data ve vašich úlohách. Kromě toho zvažte datum a čas ve struktuře, aby bylo možné lepší organizaci, filtrovaná hledání, zabezpečení a automatizaci ve zpracování. Úroveň členitosti struktury data Určuje interval, ve kterém jsou data nahraná nebo zpracovaná, například každou hodinu, každý den nebo dokonce měsíčně.

Občas se zpracování souborů nezdařilo z důvodu poškození dat nebo neočekávaných formátů. V takových případech by adresářová struktura mohla těžit ze složky **/Bad** , aby se soubory přesunuly pro další kontrolu. Úloha služby Batch může také zpracovávat zprávy a oznámení těchto *neplatných* souborů pro ruční zásah. Vezměte v úvahu následující strukturu šablon:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Například marketingový firmu obdrží denní datové výtažky aktualizací zákazníků od jejich klientů v Severní Amerika. Může vypadat jako následující fragment kódu před a po zpracování:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

V běžném případě zpracování dat služby Batch přímo do databází, jako je například podregistr nebo tradiční databáze SQL, není nutné mít složku **/in** nebo **/out** , protože výstup již patří do samostatné složky pro tabulku podregistru nebo externí databázi. Například denní extrakce od zákazníků by se mohla zakládat do příslušných složek a orchestrace podle něčeho, jako je Azure Data Factory, Apache Oozie nebo Apache flow, může aktivovat denní podregistr nebo úlohu Sparku, která zpracuje data a zapíše je do tabulky podregistru.

## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Access Control v Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Zabezpečení v Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Ladění Azure Data Lake Storage Gen1 pro výkon](data-lake-store-performance-tuning-guidance.md)
* [Pokyny k ladění výkonu pro používání služby HDInsight Spark s Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Pokyny k ladění výkonu pro použití podregistru HDInsight s Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Vytváření clusterů HDInsight pomocí Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
