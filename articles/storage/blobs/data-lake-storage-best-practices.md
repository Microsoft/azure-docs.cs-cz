---
title: Osvědčené postupy pro používání Azure Data Lake Storage Gen2 | Microsoft Docs
description: Seznamte se s osvědčenými postupy při přijímání, zabezpečení dat a výkonu souvisejícím s používáním Azure Data Lake Storage Gen2 (dříve označované jako Azure Data Lake Store).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 937048ce14b9b05b55cd8d76e7a8c1fd67c63e4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933704"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Osvědčené postupy pro používání Azure Data Lake Storage Gen2

V tomto článku se seznámíte s osvědčenými postupy a pokyny pro práci s Azure Data Lake Storage Gen2. Tento článek poskytuje informace o zabezpečení, výkonu, odolnosti a monitorování pro Data Lake Storage Gen2. Před Data Lake Storage Gen2 práce s skutečně velkými objemy dat ve službách, jako je Azure HDInsight, byla složitá. Museli jste horizontálních oddílů data napříč několika účty BLOB Storage, aby bylo možné dosáhnout úložiště řádu petabajtů a optimálního výkonu v tomto rozsahu. Data Lake Storage Gen2 podporuje jednotlivé velikosti souborů stejně vysoké jako 190,7 TiB a většina pevných limitů pro výkon se odebrala. Existují však i některé okolnosti, které tento článek popisuje, abyste dosáhli nejlepšího výkonu s použitím Data Lake Storage Gen2.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Azure Data Lake Storage Gen2 nabízí řízení přístupu POSIX pro uživatele, skupiny a instanční objekty služby Azure Active Directory (Azure AD). Tyto ovládací prvky přístupu můžou být nastavené na existující soubory a adresáře. Ovládací prvky přístupu lze také použít k vytvoření výchozích oprávnění, která lze automaticky použít pro nové soubory nebo adresáře. Další podrobnosti o Data Lake Storage Gen2ech ACL jsou k dispozici v [řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Použití skupin zabezpečení oproti jednotlivým uživatelům

Když pracujete s velkými objemy dat v Data Lake Storage Gen2, je pravděpodobný, že se k tomu, aby služby jako Azure HDInsight mohly pracovat s daty, použít instanční objekt. Mohou však nastat případy, kdy potřebují k datům přístup i jednotliví uživatelé. Ve všech případech důrazně zvažte použití Azure Active Directory [skupin zabezpečení](../common/storage-auth-aad.md) místo přiřazování jednotlivých uživatelů k adresářům a souborům.

Když je skupině zabezpečení přiřazena oprávnění, přidání nebo odebrání uživatelů ze skupiny nevyžaduje žádné aktualizace Data Lake Storage Gen2. To také pomáhá zajistit, abyste nepřekročili maximální počet položek řízení přístupu na seznam řízení přístupu (ACL). V současné době je toto číslo 32 (včetně čtyř seznamů ACL ve stylu POSIX, které jsou vždy spojeny s každým souborem a adresářem): vlastnící uživatel, vlastnící skupina, maska a další. Každý adresář může mít dva typy seznamů ACL, ACL přístupu a výchozí seznam ACL, celkem 64 položek řízení přístupu. Další informace o těchto seznamech ACL najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny

Když vy nebo vaši uživatelé potřebujete mít přístup k datům v účtu úložiště s povoleným hierarchickým oborem názvů, je nejvhodnější použít Azure Active Directory skupiny zabezpečení. Některé doporučené skupiny, které začínají na začátku, můžou být **ReadOnlyUsers**, **WriteAccessUsers** a **FullAccessUsers** pro kořen kontejneru a dokonce oddělené pro klíčové podadresáře. Pokud existují nějaké jiné očekávané skupiny uživatelů, které by mohly být později přidány, ale ještě nebyly identifikovány, můžete zvážit vytvoření fiktivních skupin zabezpečení, které mají přístup k určitým složkám. Pomocí skupiny zabezpečení zajistíte, že se můžete vyhnout dlouhé době zpracování při přiřazování nových oprávnění tisícům souborů.

### <a name="security-for-service-principals"></a>Zabezpečení instančních objektů

Azure Active Directory instanční objekty obvykle používají služby, jako je Azure Databricks pro přístup k datům v Data Lake Storage Gen2. U mnoha zákazníků může být pro jeden objekt služby Azure Active Directory dostačující a může mít úplná oprávnění v kořenu Data Lake Storage Gen2 kontejneru. Jiní zákazníci mohou vyžadovat více clusterů s různými instančními objekty, kde jeden cluster má úplný přístup k datům a druhý cluster s přístupem pro čtení. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Povolit bránu Data Lake Storage Gen2 firewall s přístupem ke službě Azure

Data Lake Storage Gen2 podporuje možnost zapnout bránu firewall a omezit přístup jenom na služby Azure, což doporučuje omezit vektor externích útoků. Bránu firewall je možné povolit v účtu úložiště v Azure Portal přes bránu **firewall**  >  **Povolit bránu firewall (zapnuto)**  >  **Povolit přístup k možnostem služeb Azure** .

Pokud chcete získat přístup k účtu úložiště z Azure Databricks, nasaďte Azure Databricks do vaší virtuální sítě a potom do své brány firewall přidejte tuto virtuální síť. Viz [Konfigurace bran firewall a virtuálních sítí Azure Storage](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti

Při navrhování systému pomocí Data Lake Storage Gen2 nebo libovolné cloudové služby musíte vzít v úvahu požadavky na dostupnost a postup reakce na potenciální přerušení ve službě. Problém může být lokalizovaný na konkrétní instanci nebo dokonce v celé oblasti, takže plán pro oba je důležitý. V závislosti na době obnovení a cíli SLA bodu obnovení pro vaše zatížení můžete zvolit více nebo méně agresivní strategii pro vysokou dostupnost a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Vysoká dostupnost (HA) a zotavení po havárii (DR) se někdy můžou kombinovat dohromady, i když každá z nich má mírně odlišnou strategii, zejména pokud se data nacházejí. Data Lake Storage Gen2 už v digestoři zpracovává replikaci 3x, která chrání před selháním lokalizovaných hardwarových zařízení. Kromě toho další možnosti replikace, jako je ZRS nebo GZRS, zlepšují HA, zatímco GRS & RA-GRS vylepšit DR. Při sestavování plánu pro HA potřebuje v případě přerušení služby přístup k nejnovějším datům co nejrychleji, a to tak, že přepnete na samostatnou replikovanou instanci lokálně nebo v nové oblasti.

V strategii zotavení po havárii se můžete připravit na nepravděpodobné události závažného selhání oblasti. je taky důležité replikovat data do jiné oblasti pomocí replikace GRS nebo RA-GRS. Také je nutné vzít v úvahu požadavky na hraniční případy, jako je například poškození dat, kde můžete chtít vytvořit periodické snímky, na které se bude vracet. V závislosti na důležitosti a velikosti dat zvažte použití rozdílových snímků s 1, 6 a 24 hodinovou odchylkou v závislosti na tolerancích rizik.

V případě odolnosti dat pomocí Data Lake Storage Gen2 se doporučuje geograficky replikovat data prostřednictvím GRS nebo RA-GRS, které splňuje požadavky na HA/DR. Kromě toho byste měli zvážit způsob, jakým se aplikace používá Data Lake Storage Gen2 k automatickému převzetí služeb při selhání sekundární oblastí prostřednictvím triggerů monitorování nebo délky neúspěšných pokusů nebo aspoň odeslání oznámení správcům k ručnímu zásahu. Mějte na paměti, že při převzetí služeb při selhání a čekání na přechod služby zpátky do režimu online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití Distcp k přesunu dat mezi dvěma místy

Pro distribuované kopírování je DistCp nástroj příkazového řádku pro Linux, který je součástí systému Hadoop a poskytuje přenos distribuovaných dat mezi dvěma umístěními. Tato dvě umístění můžou být Data Lake Storage Gen2, HDFS nebo S3. Tento nástroj používá úlohy MapReduce v clusteru Hadoop (například HDInsight) k horizontálnímu navýšení kapacity na všech uzlech. Distcp je považována za nejrychlejší způsob, jak přesunout velké objemy dat bez speciálního síťového kompresního zařízení. Distcp také nabízí možnost aktualizovat jenom rozdíly mezi dvěma umístěními, zpracovává automatické opakování a také dynamické škálování výpočetních prostředků. Tento přístup je neuvěřitelně účinný, pokud je k replikaci věcí, jako jsou tabulky podregistru nebo Sparku, které můžou mít mnoho velkých souborů v jednom adresáři a vy chcete zkopírovat jenom změněná data. Z těchto důvodů je Distcp doporučeným nástrojem pro kopírování dat mezi úložišti velkých objemů dat.

Úlohy kopírování se můžou aktivovat pracovními postupy Apache Oozie pomocí frekvencí nebo triggerů dat a také úloh Linux cron. Pro náročné úlohy replikace doporučujeme aktivovat samostatný cluster HDInsight Hadoop, který je možné vyladit a škálovat speciálně pro úlohy kopírování. Tím se zajistí, že úlohy kopírování nebudou v konfliktu s kritickými úlohami. Pokud spustíte replikaci s dostatečnou frekvencí, může být cluster dokonce mimo provoz mezi jednotlivými úlohami. Pokud se převezme služby při selhání do sekundární oblasti, ujistěte se, že se v sekundární oblasti zavedl i další cluster, aby se po návratu do primárního Data Lake Storage Gen2 účtu replikují nová data zpátky na primární účet. Příklady použití Distcp najdete v tématu [Použití Distcp ke kopírování dat mezi objekty blob Azure Storage a data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Plánování úloh kopírování pomocí Azure Data Factory

Azure Data Factory lze také použít k plánování úloh kopírování pomocí aktivity kopírování a je možné ji dokonce nastavit na frekvenci prostřednictvím Průvodce kopírováním. Mějte na paměti, že Azure Data Factory má limit DMUsch přenosů dat v cloudu, a nakonec si zajistěte, aby propustnost a výpočet pro úlohy s velkým objemem dat byly omezené. Kromě toho Azure Data Factory v současné době nenabízí rozdílové aktualizace mezi účty Data Lake Storage Gen2, takže adresáře, jako jsou tabulky podregistru, budou vyžadovat úplnou kopii k replikaci. Další informace o kopírování pomocí Data Factory najdete v článku věnovaném službě [Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) .

## <a name="monitoring-considerations"></a>Monitorování – požadavky

Data Lake Storage Gen2 poskytuje metriky v Azure Portal pod účtem Data Lake Storage Gen2 a v Azure Monitor. V Azure Portal se zobrazí dostupnost Data Lake Storage Gen2. Chcete-li získat nejaktuálnější dostupnost účtu Data Lake Storage Gen2, je nutné spustit vlastní syntetické testy k ověření dostupnosti. Jiné metriky, jako je například celkové využití úložiště, požadavky na čtení a zápis a příchozí/odchozí, jsou k dispozici, aby je bylo možné využívat monitorováním aplikací a mohou také aktivovat výstrahy v případě překročení prahových hodnot (například průměrná latence nebo počet chyb za minutu).

## <a name="directory-layout-considerations"></a>Požadavky na rozložení adresáře

Při vykládku dat do data Lake je důležité předem naplánovat strukturu dat tak, aby bylo možné efektivně využívat zabezpečení, dělení a zpracování. Mnohé z následujících doporučení platí pro všechny úlohy s velkými objemy dat. Každé zatížení má různé požadavky na to, jak se data spotřebovávají, ale níže jsou několik běžných rozložení, která je potřeba vzít v úvahu při práci se scénáři IoT a batch.

### <a name="iot-structure"></a>Struktura IoT

V úlohách IoT se můžete setkat s daty vydanými v úložišti dat, která se nacházejí v různých produktech, zařízeních, organizacích a zákaznících. Je důležité předem naplánovat rozložení adresáře pro organizaci, zabezpečení a efektivní zpracování dat pro uživatele se vzdálení datovými proudy. Obecná šablona, kterou je třeba zvážit, může být následující rozložení:

*Počet/{SubjectMatter: {region}}/{yyyy}/{MM}/{DD}/{hh}/*

Například vykládka telemetrie pro stroj v letadle v rámci Velké Británie může vypadat jako tato struktura:

*Velká Británie/roviny/BA1293/Engine1/2017/08/11/12/*

Na konci adresářové struktury je důležitý důvod, jak umístit datum. Pokud chcete některé oblasti nebo věci v předmětech uzamknout pro uživatele nebo skupiny, můžete k tomu snadno využít oprávnění POSIX. V opačném případě, pokud je potřeba omezit určitou skupinu zabezpečení, aby se zobrazila pouze data o Spojeném království nebo určité rovině, musí se v rámci každého adresáře v každém z hodin vyžadovat samostatné oprávnění. Kromě toho by měla struktura data předem exponenciálně zvýšit počet adresářů jako čas.

### <a name="batch-jobs-structure"></a>Struktura úloh Batch

Z vysoké úrovně se běžně používaným přístupem v dávkovém zpracování slouží k obstání dat v adresáři "v". Až se data zpracují, vložte nová data do adresáře "out", aby bylo možné procesy pro příjem dat využívat. Tato adresářová struktura se občas zobrazuje pro úlohy, které vyžadují zpracování na jednotlivých souborech a nemusí vyžadovat výkonné paralelní zpracování v rámci velkých datových sad. Podobně jako u výše zmíněné struktury IoT má vhodná adresářová struktura k dispozici adresáře na úrovni nadřazených objektů pro věci, jako jsou oblasti a záležitosti předmětu (například organizace, produkt/výrobce). Tato struktura pomáhá zabezpečit data napříč vaší organizací a lépe spravovat data ve vašich úlohách. Kromě toho zvažte datum a čas ve struktuře, aby bylo možné lepší organizaci, filtrovaná hledání, zabezpečení a automatizaci ve zpracování. Úroveň členitosti struktury data Určuje interval, ve kterém jsou data nahraná nebo zpracovaná, například každou hodinu, každý den nebo dokonce měsíčně.

Občas se zpracování souborů nezdařilo z důvodu poškození dat nebo neočekávaných formátů. V takových případech by adresářová struktura mohla těžit ze složky **/Bad** , aby se soubory přesunuly pro další kontrolu. Úloha služby Batch může také zpracovávat zprávy a oznámení těchto *neplatných* souborů pro ruční zásah. Vezměte v úvahu následující strukturu šablon:

*Počet/{SubjectMatter: {region}}/in/{yyyy}/{MM}/{DD}/{hh}/*\
*Počet/{SubjectMatter: {region}}/out/{yyyy}/{MM}/{DD}/{hh}/*\
*Počet/{SubjectMatter: {region}}/Bad/{yyyy}/{MM}/{DD}/{hh}/*

Například marketingový firmu obdrží denní datové výtažky aktualizací zákazníků od jejich klientů v Severní Amerika. Může vypadat jako následující fragment kódu před a po zpracování:

*NA/extrahuje/ACMEPaperCo/v/2017/08/14/updates_08142017.csv*\
*NA/extrahuje/ACMEPaperCo/out/2017/08/8/processed_updates_08142017.csv*

V běžném případě zpracování dat služby Batch přímo do databází, jako je například podregistr nebo tradiční databáze SQL, není nutné mít složku **/in** nebo **/out** , protože výstup již patří do samostatné složky pro tabulku podregistru nebo externí databázi. Například denní extrakce od zákazníků by se mohla zakládat do příslušných složek a orchestrace podle něčeho, jako je Azure Data Factory, Apache Oozie nebo Apache flow, může aktivovat denní podregistr nebo úlohu Sparku, která zpracuje data a zapíše je do tabulky podregistru.
