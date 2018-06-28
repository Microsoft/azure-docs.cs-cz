---
title: Osvědčené postupy pro používání Azure Data Lake Storage Gen1 | Microsoft Docs
description: Další informace osvědčené postupy týkající se přijímání dat, datum zabezpečení a výkonu související s použitím Azure Data Lake Storage Gen1 (dříve označované jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 00eb2b6b60aa6c3224b58556f6dad64d4294c308
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035785"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Doporučené postupy pro používání Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

V tomto článku se dozvíte o osvědčených postupech a důležité informace týkající se práce s Azure Data Lake Store. Tento článek obsahuje informace o zabezpečení, výkonu, odolnost proti chybám a monitorování pro Data Lake Store. Před Data Lake Store práci s skutečně velkých objemů dat v služby, jako je Azure HDInsight byl složitý. Bylo sdílení dat mezi více účtů úložiště Blob tak, aby bylo možné dosáhnout petabajty úložiště a optimální výkon v tomto měřítku. S Data Lake Store jsou odstraněna většina pevných limitů pro velikost a výkon. Existují však stále některé aspekty, které tento článek se týká, abyste měli k dosažení nejlepšího výkonu s Data Lake Store. 

## <a name="security-considerations"></a>Aspekty zabezpečení

Azure Data Lake Store nabízí přístup POSIX ovládací prvky a podrobné auditování pro Azure Active Directory (Azure AD) uživatele, skupiny a objekty služby. Tato řízení přístupu můžete nastavit pro existující soubory a složky. Řízení přístupu lze také vytvořit výchozí hodnoty, které můžete použít pro nové soubory nebo složky. Pokud jsou oprávnění nastavená do stávající složky a podřízených objektů, musí být šířený rekurzivně na každém objektu oprávnění. Pokud existuje velký počet souborů, šíření oprávnění může trvat dlouhou dobu. Doba, může být v rozsahu mezi objekty 30 – 50 zpracovaných za sekundu. Proto naplánujte skupiny složek strukturu a uživatel správně. Jinak může to způsobit neočekávané prodlevy a problémy při práci s daty. 

Předpokládejme, že máte složku s 100 000 podřízené objekty. Pokud pořídíte dolní hranice 30 objektů zpracovaných za sekundu, chcete-li aktualizovat oprávnění pro celou složku může trvat jednu hodinu. Další informace o Data Lake Store seznamy ACL jsou k dispozici na [řízení přístupu v Azure Data Lake Store](data-lake-store-access-control.md). Pro zlepšení výkonu na přiřazení rekurzivně seznamy ACL můžete použít nástroj příkazového řádku služby Azure Data Lake. Nástroj vytváří více vláken a logiku navigační rekurzivní rychle použít seznamy řízení přístupu na miliony souborů. Nástroj je k dispozici pro Linux a Windows a [dokumentace](https://github.com/Azure/data-lake-adlstool) a [stáhne](http://aka.ms/adlstool-download) pro tento nástroj najdete na Githubu. Tyto stejné vylepšení výkonu se dá nastavit podle vlastních nástrojů napsané pomocí Data Lake Store [.NET](data-lake-store-data-operations-net-sdk.md) a [Java](data-lake-store-get-started-java-sdk.md) sady SDK.

### <a name="use-security-groups-versus-individual-users"></a>Použití skupin zabezpečení a jednotlivé uživatele 

Při práci s velkým objemem dat v Data Lake Store, pravděpodobně objekt služby používá k povolení služeb, jako je Azure HDInsight pro práci s daty. Však může být případech, kde jednotlivé uživatelé potřebovat přístup k datům také. V takových případech musíte použít Azure Active Directory [skupiny zabezpečení](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) místo přiřazování jednotlivých uživatelů do složek a souborů. 

Jakmile skupinu zabezpečení je přiřazena oprávnění k přidávání nebo odebírání uživatelů ze skupiny nevyžaduje žádné aktualizace do Data Lake Store. To také pomáhá zajistit nepřekračují maximální počet [32 přístupu a výchozí seznamy ACL](../azure-subscription-service-limits.md#data-lake-store-limits) (to zahrnuje čtyři stylu POSIX seznamy ACL, které jsou vždy přidružený všechny soubory a složky: [vlastnícím uživatele](data-lake-store-access-control.md#the-owning-user), [vlastnícím skupiny](data-lake-store-access-control.md#the-owning-group), [maska](data-lake-store-access-control.md#the-mask-and-effective-permissions)a další).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny 

Jak je popsáno, když uživatelé potřebovat přístup k Data Lake Store, je nejvhodnější použít skupiny zabezpečení Azure Active Directory. Některé doporučené skupiny začínat mohou být **ReadOnlyUsers**, **WriteAccessUsers**, a **FullAccessUsers** pro kořenový účet a to i v samostatné těch, které jsou pro klíč podsložky. Pokud neexistují žádné jiné předpokládaného skupiny uživatelů, kteří mohou být přidány později, ale nebyly identifikovány ještě můžete zvážit vytváření skupin fiktivní zabezpečení, které mají přístup k určitým složkám. Pomocí skupiny zabezpečení zajistí, že později není nutné s dlouhým doba zpracování pro přiřazení nové oprávnění tisíce souborů. 

### <a name="security-for-service-principals"></a>Zabezpečení pro objekty služby 

Objekty služby Azure Active Directory jsou obvykle používány služby, jako je Azure HDInsight pro přístup k datům v Data Lake Store. V závislosti na požadavky na přístup napříč více úloh může být některé aspekty určené k zajištění zabezpečení uvnitř i vně organizace. Pro mnoho zákazníků může stačit jeden objekt služby Azure Active Directory a může mít úplná oprávnění v kořenovém adresáři Data Lake Store. Zákazníci navzájem může vyžadovat více clusterů s objekty různé služby, kde jeden cluster má úplný přístup k datům a jiného clusteru s přístupem jen pro čtení. Stejně jako u skupin zabezpečení, zvažte provedení objekt služby pro každý scénář (čtení, zápisu, úplná) očekává po vytvoření účtu Data Lake Store. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>Povolit bránu firewall Data Lake Store s přístup ke službě Azure 

Data Lake Store podporuje možnost zapnutí brány firewall a omezení přístupu jenom ke službám Azure, který se doporučuje pro menší vektor útoku z mimo vniknutí. Může být povolená brána firewall na účtu Data Lake Store na portálu Azure prostřednictvím **brány Firewall** > **povolit bránu Firewall (zapnuto)** > **povolit přístup ke službám Azure**  možnosti.  

![Nastavení v Data Lake Store brány firewall](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "nastavením brány Firewall v Data Lake Store")

Jakmile je povoleno, jenom Azure služby, jako je HDInsight brána firewall, objekt pro vytváření dat SQL Data Warehouse, atd mají přístup do Data Lake Store. Z důvodu překlad interní síťové adresy používané Azure brány firewall Data Lake Store nepodporuje omezení konkrétní služby, na základě IP a je určena pouze pro omezení koncových bodů mimo Azure, jako je například místní. 

## <a name="performance-and-scale-considerations"></a>Aspekty týkající se výkonu a škálování

Jedním z nejúčinnějších funkce Data Lake Store je, že jej odebere pevných limitů na propustnost dat. Odebrání omezení umožňuje zákazníkům růst jejich velikost dat a bez nutnosti horizontálního oddílu data společně požadavky na výkon. Jedním z nejdůležitějších aspektů pro optimalizaci výkonu Data Lake Store je provede nejlepší při dané stupně paralelního zpracování.

### <a name="improve-throughput-with-parallelism"></a>Zlepšit propustnost s paralelismus 

Zvažte 8 12 vláken na jádra pro optimální propustnosti pro čtení a zápis. Je to z důvodu blokování čtení/zápisu na jedno vlákno a další podprocesy můžete povolit vyšší souběžnosti na virtuálním počítači. Zajistit, že jsou v pořádku úrovně a může být zvýšena paralelismus, je nutné sledovat využití procesoru Virtuálního počítače.   

### <a name="avoid-small-file-sizes"></a>Vyhněte se malý soubor velikosti

POSIX oprávnění a auditování v Data Lake Store se dodává s režijní náklady, vyvstává zřejmá při práci s velkého množství malých souborů. Jako osvědčený postup musíte dávky dat do větší souborů a zápis tisíce nebo milióny malých souborů do Data Lake Store. Zabraňující malý soubor velikosti příkaz může mít několik výhod, jako například:

* Snížení kontroly ověřování napříč více souborů
* Snížená otevření souboru připojení
* Rychlejší kopírování/replikaci
* Méně soubory ke zpracování při aktualizaci Data Lake Store POSIX oprávnění 

V závislosti na tom, jaké služby a úlohy používají data, je dobré velikost vzít v úvahu pro soubory 256 MB nebo vyšší. Pokud velikosti souborů nelze zpracovat v dávce při úvodní v Data Lake Store, můžete mít samostatné komprimace úlohu, která spojuje tyto soubory do těch větší. Další informace a doporučení o velikosti souborů a uspořádání dat v Data Lake Store najdete v tématu [struktury sadu dat](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Velké soubory a potenciálního vlivu na výkon

I když se Data Lake Store podporuje velkých souborů až petabajtů velikosti, pro zajištění optimálního výkonu a v závislosti na proces čtení dat, nemusí být ideální v průměru přejděte výše 2 GB. Například při použití **Distcp** ke kopírování dat mezi umístění nebo jiným účtům úložiště, soubory jsou nejlepší úrovně podrobností, které používá k určení mapy úlohy. Ano Pokud kopírujete 10 souborů, které jsou 1 TB, maximálně 10 mappers jsou přiděleny. Navíc pokud máte velký počet souborů s mappers přiřazen, původně mappers fungovat paralelně přesunout velkých souborů. Jako úloha spustí brání jenom pár mappers zůstanou přidělené a vám může zablokuje se jeden mapper přiřazené velkého souboru. Vylepšení Distcp Chcete-li vyřešit tento problém v budoucích verzích systému Hadoop byla odeslána společnosti Microsoft.  

Dalším příkladem vzít v úvahu je při použití Azure Data Lake Analytics s Data Lake Store. V závislosti na zpracování provádí Extraktor, může dojít k některé soubory, které nelze rozdělit (například XML, JSON) v výkon, pokud je větší než 2 GB. V případech, kde lze rozdělit soubory extrakci (například CSV) jsou upřednostněny velkých souborů.

### <a name="capacity-plan-for-your-workload"></a>Kapacity plán pro úlohy 

Azure Data Lake Store odebere pevný vstupně-výstupní operace omezení, které jsou umístěny na účty úložiště Blob. Existují však stále doporučené omezení, které je potřeba zvážit. Výchozí omezení omezení vstupní/výstupní vyhovovaly většině scénářů. Pokud vaše úlohy musí mít vyšší limity, pracujete s podporu společnosti Microsoft. Také podívejte se na omezení během fáze testování konceptu, aby během produkční nejsou dosáhl omezení limity vstupně-výstupní operace. Pokud k tomu dojde, se může vyžadovat ruční zvětšování od technického týmu Microsoft čekání. Pokud dojde k omezení šířky pásma vstupně-výstupní operace, vrátí chybový kód 429 Azure Data Lake Store a v ideálním případě je třeba opakovat zásadami příslušné exponenciálního omezení rychlosti. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>Optimalizace "zapíše" s Data Lake Store ovladač velikost vyrovnávací paměti 

K optimalizaci výkonu a snížení IOPS při zápisu do Data Lake Store z Hadoop, proveďte operace zápisu co nejblíže velikost vyrovnávací paměti ovladač Data Lake Store míře. Zkuste není překročit velikost vyrovnávací paměti před vyčištění, například při streamování využívající Apache Storm nebo vysílání datového proudu úlohy Spark. Při zápisu do Data Lake Store z HDInsight nebo Hadoop, je důležité vědět, že Data Lake Store je ovladač s vyrovnávací paměti 4 MB. Stejně jako mnoho ovladače systému souborů můžete ručně Vyprázdněné této vyrovnávací paměti dříve, než dorazila velikost 4 MB. V opačném případě ji okamžitě vyprazdňuje do úložiště další zápisu překročí maximální velikost vyrovnávací paměti je-li. Pokud je to možné, musí neměli překročení nebo významné proti podjetí vyrovnávací paměti při synchronizaci nebo spotřebě zásady podle počtu nebo čas.

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti 

Při architektury systému se Data Lake Store nebo všechny cloudové služby, zvažte své požadavky na dostupnosti a jak reagovat na potenciální přerušení služby. Problém může být lokalizovaný pro určitou instanci, nebo i celou oblast, je důležité, tak s plánu pro obě. V závislosti na tom **plánovanou dobu obnovení** a **plánovaného bodu obnovení** SLA pro úlohy, můžete vybrat více nebo méně agresivní strategie pro vysokou dostupnost a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii 

Vysoká dostupnost (HA) a zotavení po havárii (DR) někdy dá zkopírovat dohromady, i když každá z nich má mírně odlišné strategie, zejména v případě, že jde o data. Data Lake Store již zpracovává 3 x replikace pod pokličkou pro ochranu proti selhání lokalizované hardwaru. Ale vzhledem k tomu, že není součástí replikace v oblastech, je třeba spravovat sobě. Při vytváření plánu pro HA, v případě přerušení poskytování služeb zatížení potřebuje přístup k nejnovější data co nejrychleji při přechodu samostatně replikovaná instance místně nebo v nové oblasti.  

V strategie zotavení po Havárii připravte k nepravděpodobnému k závažnému selhání oblasti, je také potřeba replikovat data na jiné oblasti. Tato data původně může být stejný jako replikovaná data HA. Musí však také zvažte své požadavky na hraniční případech, jako je například místo vytvoření pravidelné snímky k poškození dat. V závislosti na důležitosti a velikost dat zvažte vrácení rozdílů snímky 1 - 6- a období 24 hodin v úložišti místní a/nebo sekundární podle riziko tolerance. 

Záleží na odolnosti dat s Data Lake Store se doporučuje geograficky replikovat data v samostatné oblasti s frekvencí, která splňuje vaše požadavky HA/DR v ideálním případě každou hodinu. Tato četnost replikace minimalizuje pohybů velkých dat, které je možné pomocí hlavní systému a lepší plánovaného bodu obnovení (RPO) musí konkurenční propustnost. Kromě toho byste měli zvážit způsoby, kterými aplikace pomocí Data Lake Store k automatické převzetí služeb při selhání na sekundární účet prostřednictvím monitorování aktivační události nebo délka neúspěšných pokusů o nebo alespoň odeslat oznámení správcům pro ruční zásah. Uvědomte si, že je kompromis převzetí služeb při selhání a čeká se služby vrátí do režimu online. Pokud data se nedokončila replikace, převzetí služeb při selhání by mohl způsobit ztrátu dat., nekonzistence nebo komplexní slučování dat. 

V následující tabulce jsou tři hlavní doporučené možnosti pro orchestruje replikaci mezi účty Data Lake Store a hlavní rozdíly mezi každý z nich.


|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Limity škálování**     | Ohraničené uzlů pracovního procesu        | Omezené přesunu dat cloudové maximální počet jednotek        | Svázaná s Analytics jednotky        |
|**Podporuje kopírování rozdílů**     |   Ano      | Ne         | Ne         |
|**Předdefinované orchestration**     |  Ne (použijte úlohy Oozie vzduchu nebo cron)       | Ano        | Ne (pomocí služby Azure Automation nebo plánovače úloh systému Windows)         |
|**Podporované souborové systémy**     | ADL, HDFS, WASB, S3, GS, CFS        |Řada, viz [konektory](../data-factory/connector-azure-blob-storage.md).         | ADL k ADL WASB k ADL (pouze stejné oblasti)        |
|**Podpora operačního systému**     |Všechny operačního systému spuštěné Hadoop         | neuvedeno          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití Distcp pro přesun dat mezi dvěma umístěními 

Zkratka pro distribuované kopie, Distcp je nástroj příkazového řádku systému Linux, který se dodává s Hadoop a poskytuje přesun distribuované dat mezi dvěma umístěními. Těmito dvěma umístěními může být Data Lake Store, HDFS, WASB nebo S3. Tento nástroj používá úloh MapReduce na cluster Hadoop (například HDInsight) pro horizontální škálování na všech uzlech. Distcp považuje za nejrychlejší způsob, jak přesunout velké objemy dat bez speciální síťových zařízení komprese. Distcp také nabízí možnost pouze aktualizovat rozdílů mezi dvěma umístěními, automatické opakování pokusů obslužných rutin, jakož i dynamické škálování výpočtů. Tento přístup je mimořádně efektivní při rozhodování o replikaci věcmi, jako jsou tabulky Hive nebo Spark, které může mít mnoho velkých souborů v jednom adresáři, a chcete zkopírovat přes upravené data. Z těchto důvodů je Distcp nejvíce doporučeným nástrojem pro kopírování dat mezi úložišti velkých objemů dat. 

Kopírování úlohy můžete spustit pomocí Apache Oozie workflowů pomocí frekvence nebo data aktivační události, jakož i Linux cron úlohy. Pro úlohy náročné replikace se doporučuje začne pracovat samostatné clusteru HDInsight Hadoop, který může být přizpůsobená a škálovat speciálně pro kopírování úlohy. Tím se zajistí, že kopírování úlohy nezpůsobují konflikt s kritické úlohy. Pokud se spouští na dost široké, četnost replikace, můžete být clusteru i ukončena mezi každou úlohu. Pokud přebírání služeb při selhání pro sekundární oblast, ujistěte se, že jiný cluster také spuštěné v sekundární oblasti k replikaci zpět na primární účtu Data Lake Store nová data, po uvede do provozu. Příklady použití Distcp najdete v tématu [použití Distcp ke kopírování dat mezi objektů BLOB služby Azure Storage a Data Lake Store](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Plánování úloh kopírování pomocí Azure Data Factory 

Azure Data Factory slouží také k plánování úloh kopírování pomocí **aktivity kopírování**a můžete nastavit i na frekvenci prostřednictvím **Průvodce kopírováním**. Mějte na paměti, že Azure Data Factory může mít jednotky přesun dat cloudu (DMUs) a nakonec caps propustnost nebo výpočetní pro velká data úlohy. Kromě toho Azure Data Factory aktuálně nenabízí aktualizace rozdílů mezi účty Data Lake Store, takže složky, například tabulek Hive by vyžadovalo kompletní kopie k replikaci. Odkazovat [aktivity kopírování vyladění průvodce](../data-factory/copy-activity-performance.md) Další informace o kopírování pomocí služby Data Factory. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy je nástroj příkazového řádku Windows, která umožňuje kopírovat data mezi dva účty Data Lake Store pouze v rámci stejné oblasti. Nástroj AdlCopy poskytuje možnost samostatný nebo možnost spustit úlohu kopírování se pomocí účtu Azure Data Lake Analytics. I když byla původně vytvořena kopie na vyžádání a robustní replikace, poskytuje další možnost udělat distribuované kopírování mezi účty Data Lake Store v rámci stejné oblasti. Pro spolehlivost se doporučuje použít možnost Data Lake Analytics premium pro libovolnou úlohu produkční. Samostatnou verzi může vrátit zaneprázdněn odpovědí a má omezený škálování a monitorování. 

Jako Distcp musí AdlCopy něco být řízená jako Azure Automation nebo plánovače úloh systému Windows. Stejně jako u služby Data Factory AdlCopy nepodporuje kopírování pouze aktualizované soubory, ale znovu zkopíruje a přepsat existující soubory. Další informace a příklady použití AdlCopy najdete v tématu [kopírování dat z úložiště objektů BLOB Azure do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Důležité informace o monitorování 

Data Lake Store poskytuje podrobné diagnostické protokoly a auditování. Data Lake Store poskytuje některé základní metriky v portálu Azure v rámci účtu Data Lake Store a monitorování Azure. Dostupnost Data Lake Store se zobrazí na portálu Azure. Tato metrika je však aktualizovat každých sedm minut a nemůže být dotazována prostřednictvím veřejně vystavené rozhraní API. Chcete-li získat nejaktuálnější dostupnost účtu Data Lake Store, musíte spustit syntetické testy pro ověření dostupnosti. Další metriky, například využití celkový úložiště, požadavky na čtení a zápis a vstupní/výstupní může trvat až 24 hodin k aktualizaci. Ano musí další metriky aktuální vypočítat ručně prostřednictvím příkazového řádku nástroje Hadoop nebo totožný informace protokolu. Nejrychlejší způsob, jak získat nejnovější využití úložiště se spustí tento příkaz HDFS z uzlu clusteru Hadoop (například hlavního uzlu):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Diagnostika export Data Lake Store 

Jedním z nejrychlejší způsobů, jak získat přístup k protokolům prohledávatelné z Data Lake Store je umožnit přesouvání protokolu pro **analýzy protokolů** pod **diagnostiky** okně účtu Data Lake Store. To poskytuje okamžitý přístup k příchozí protokoly čas a obsahu filtry, společně s možností (e-mailu nebo webhooku) výstrah, aktivuje v intervalech 15 minut. Pokyny najdete v tématu [přístupu k diagnostickým protokolům pro Azure Data Lake Store](data-lake-store-diagnostic-logs.md). 

Pro další výstrahy v reálném čase a větší kontrolu, ve kterém můžete zobrazovat v protokolech vezměte v úvahu export protokolů k centru EventHub Azure, kde obsah lze analyzovat jednotlivě nebo po časové okno aby bylo možné odeslat oznámení v reálném čase do fronty. Samostatná aplikace, jako [aplikace logiky](../connectors/connectors-create-api-azure-event-hubs.md) můžete pak využívat a komunikaci výstrahy na správný kanál, a také odeslání pro monitorování nástroje, například nástrojů NewRelic, Datadog nebo AppDynamics nastavení. Případně, pokud používáte nástroje třetích stran, jako je ElasticSearch, můžete exportovat protokoly do úložiště objektů Blob a použít [modul plug-in Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) využívat data do vaší Elasticsearch, Kibana a Logstash (ELK) zásobníku.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Zapnout protokolování na úrovni ladění v HDInsight 

Pokud Data Lake Store přesouvání protokolu není zapnutý, Azure HDInsight také poskytuje způsob, jak zapnout [klienta protokolování pro Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) prostřednictvím log4j. Nastavte následující vlastnost **Ambari** > **YARN** > **konfigurace** > **rozšířené yarn log4j konfigurace**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

Jakmile je vlastnost nastavena a uzly se restartují, diagnostiky Data Lake Store je zapsán do protokolů YARN na uzlech (/tmp/<user>/yarn.log) a důležité podrobnosti jako chyby nebo omezení (kód chyby HTTP 429) lze sledovat. Stejné informace je možné monitorovat také v analýzy protokolů nebo kdekoli jsou protokoly odeslaná do v [diagnostiky](data-lake-store-diagnostic-logs.md) okně účtu Data Lake Store. Doporučuje se alespoň zapnout protokolování na straně klienta nebo využívat možnost s Data Lake Store provozní viditelnost a snadnější ladění přesouvání protokolu.

### <a name="run-synthetic-transactions"></a>Spustit syntetické transakce 

V současné době metriku dostupnosti služby Data Lake Store na portálu Azure má okno aktualizace 7 minut. Navíc nelze dotazovat, pomocí veřejně vystavené rozhraní API. Proto se doporučuje vytvořit základní aplikaci, která nemá do Data Lake Store, které můžete zadat až minut dostupnost syntetické transakce. Příklad může vytváření webové úlohy, aplikace logiky nebo funkce aplikaci Azure k provedení pro čtení, vytvořit a aktualizovat proti Data Lake Store a odešlou výsledky do řešení pro monitorování. Operace, které můžete provést v dočasné složce a odstraní po test, který může být spustit každých 30 – 60 sekund, v závislosti na požadavcích.

## <a name="directory-layout-considerations"></a>Důležité informace o rozložení adresáře

Pokud cílová data do úložiště data lake, je potřeba před naplánování strukturu dat tak, aby zabezpečení, vytváření oddílů a zpracování může být efektivně využívat. Řadu následující doporučení je možné, zda je v Azure Data Lake Store, úložiště objektů Blob nebo HDFS. Každé zatížení má jiné požadavky na tom, jak data zpracován, ale tady jsou některé běžné rozložení vzít v úvahu při práci se službou IoT a dávky scénáře.

### <a name="iot-structure"></a>Struktura IoT 

V úlohách IoT může být značnou část dat se dostali v úložišti dat, které fungují v rámci řada produktů, zařízení, organizace a zákazníků. Je důležité, abyste před naplánování rozložení adresář pro organizaci, zabezpečení a efektivní zpracování dat pro spotřebitele proudu. Obecné šablony vzít v úvahu, může být následující rozložení: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Například cílová stránka telemetrická data motoru letadle v rámci Spojené království může vypadat podobně jako následující strukturou: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Není k dispozici z důvodu důležité uvést datum na konci struktura složek. Pokud chcete zamknout určitých oblastí nebo subjektu záleží na uživatele nebo skupiny, potom můžete snadno tak učinit s oprávněními POSIX. Jinak hodnota pokud se třeba omezit určité skupiny zabezpečení k zobrazení pouze data, UK nebo určité roviny, se strukturou datum vpředu samostatné oprávnění by byla zapotřebí pro mnoho složek složce každou hodinu. Kromě toho má strukturu datum vpředu by exponenciálnímu prodloužení počet složek, jako čas se.

### <a name="batch-jobs-structure"></a>Struktura úlohy batch 

Z hlavní běžně používané přístupem v dávkové zpracování je zobrazovat data ve složce "v". Potom po zpracování dat uvést nová data do podřízené procesy využívají složky "na". Tato struktura adresáře je někdy vidět pro úlohy, které vyžadují zpracování na jednotlivé soubory a nemusí vyžadují masivně paralelní zpracování rozsáhlých datových sad. Stejně jako strukturu IoT výše doporučených má dobrou adresářovou strukturu nadřazené složky pro akcí, například oblasti a záležitosti subjektu (například organizace, produktu nebo producent). Tato struktura pomáhá s zabezpečení dat napříč vaší organizace a lepší správu data ve vašich úloh. Kromě toho zvažte datum a čas ve struktuře umožňující lepší uspořádání, filtrované hledání, zabezpečení a automatizace ve zpracování. Úrovně podrobností pro strukturu datum je určen podle interval, ve kterém se data nahrál nebo zpracována, například hodinový, denní nebo dokonce měsíční. 

Někdy souboru neúspěšný kvůli poškození dat nebo neočekávané formáty zpracování. V takových případech může využívat adresářovou strukturu **/špatný** složku pro přesun souborů k další kontroly. Dávková úloha může také zpracovat vytváření sestav nebo oznámení o těchto *chybný* soubory pro ruční zásah. Vezměte v úvahu následující strukturu šablony: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Například marketing firma přijímá denní extrahuje data zákazníků aktualizací od jejich klientů v Severní Americe. Může vypadat například následující fragment kódu před a po zpracování dat: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
V běžně dávky dat zpracovává přímo do databáze, například Hive nebo tradiční databází SQL, není potřeba **/in** nebo **/out** vzhledem k tomu, že výstup již přejde do složky samostatné složky pro tabulku Hive nebo externí databáze. Například by denní extrahuje od zákazníků zobrazovat do svých příslušných složek a orchestration podle něco podobného jako Azure Data Factory Apache Oozie nebo Apache vzduchu by aktivaci každodenní úlohy Hive nebo Spark ke zpracování a zápis dat do tabulky Hive.

## <a name="next-steps"></a>Další postup     

* [Přehled Azure Data Lake Store](data-lake-store-overview.md) 
* [Řízení přístupu v Azure Data Lake Store](data-lake-store-access-control.md) 
* [Zabezpečení v Azure Data Lake Store](data-lake-store-security-overview.md)
* [Ladění výkonu Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
* [Pokyny pro použití s Azure Data Lake Store HDInsight Spark optimalizace výkonu](data-lake-store-performance-tuning-spark.md)
* [Pokyny pro HDInsight Hive pomocí Azure Data Lake Store optimalizace výkonu](data-lake-store-performance-tuning-hive.md)
* [Orchestrace dat pomocí Azure Data Factory pro Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Vytvoření clusterů HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
