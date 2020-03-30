---
title: Doporučené postupy pro používání Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft
description: Seznamte se s doporučenými postupy pro ingestování dat, zabezpečení dat a výkon související s používáním Azure Data Lake Storage Gen2 (dříve známé jako Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299672"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Doporučené postupy pro používání Azure Data Lake Storage Gen2

V tomto článku se dozvíte o doporučených postupech a aspektech práce s Azure Data Lake Storage Gen2. Tento článek obsahuje informace o zabezpečení, výkonu, odolnosti proti chybám a monitorování pro úložiště datového jezera Gen2. Před Data Lake Storage Gen2 byla práce se skutečně velkými daty ve službách, jako je Azure HDInsight, složitá. Bylo třeba horizontálního oddílu dat přes více účtů úložiště objektů blob tak, aby petabajt úložiště a optimální výkon v tomto měřítku by bylo možné dosáhnout. Data Lake Storage Gen2 podporuje jednotlivé velikosti souborů až 5 TB a většina pevných limitů pro výkon byla odstraněna. Existují však stále některé důležité informace, které tento článek pokrývá, takže můžete získat nejlepší výkon s Dat Lake Storage Gen2.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Azure Data Lake Storage Gen2 nabízí ovládací prvky přístupu POSIX pro uživatele, skupiny a instanční objekty Azure Active Directory (Azure AD). Tyto ovládací prvky přístupu lze nastavit na existující soubory a adresáře. Ovládací prvky přístupu lze také použít k vytvoření výchozích oprávnění, která lze automaticky použít pro nové soubory nebo adresáře. Další podrobnosti o aklů ACL úložiště datového jezera jsou k dispozici na [webu Access control v Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Použití skupin zabezpečení oproti jednotlivým uživatelům

Při práci s velkými objemy dat v Data Lake Storage Gen2 je pravděpodobné, že instanční objekt se používá k povolení služeb, jako je Azure HDInsight pracovat s daty. Mohou však nastat případy, kdy jednotliví uživatelé potřebují také přístup k datům. Ve všech případech důrazně zvažte použití [skupin zabezpečení služby](../common/storage-auth-aad.md) Azure Active Directory namísto přiřazení jednotlivých uživatelů k adresářům a souborům.

Jakmile je skupině zabezpečení přiřazena oprávnění, přidání nebo odebrání uživatelů ze skupiny nevyžaduje žádné aktualizace programu Data Lake Storage Gen2. To také pomáhá zajistit, že nepřekročíte maximální počet položek řízení přístupu na seznam řízení přístupu (ACL). V současné době je toto číslo 32 (včetně čtyř seznamů ACL ve stylu POSIX, které jsou vždy přidruženy ke každému souboru a adresáři): vlastnící uživatel, vlastnící skupina, maska a další. Každý adresář může mít dva typy seznamu Řízení přístupu, přístupový seznam ACL a výchozí seznam ACL, celkem 64 položek řízení přístupu. Další informace o těchto aklů, najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny

Když vy nebo vaši uživatelé potřebujete přístup k datům v účtu úložiště s povoleným hierarchickým oborem názvů, je nejlepší použít skupiny zabezpečení Azure Active Directory. Některé doporučené skupiny, které mají být zahájeny, mohou být **ReadOnlyUsers**, **WriteAccessUsers**a **FullAccessUsers** pro kořen kontejneru a dokonce i samostatné skupiny pro klíčové podadresáře. Pokud existují další očekávané skupiny uživatelů, které mohou být přidány později, ale dosud nebyly identifikovány, můžete zvážit vytvoření fiktivních skupin zabezpečení, které mají přístup k určitým složkám. Použití skupiny zabezpečení zajišťuje, že se můžete vyhnout dlouhé době zpracování při přiřazování nových oprávnění tisícům souborů.

### <a name="security-for-service-principals"></a>Zabezpečení pro instanční objekty

Objekty služby Azure Active Directory se obvykle používají služby, jako jsou Azure Databricks pro přístup k datům v Data Lake Storage Gen2. Pro mnoho zákazníků může být odpovídající jeden objekt zabezpečení služby Azure Active Directory a může mít úplná oprávnění v kořenovém adresáři kontejneru Gen2 úložiště datového jezera. Jiní zákazníci mohou vyžadovat více clusterů s různými objekty zabezpečení služeb, kde jeden cluster má úplný přístup k datům a jiný cluster s přístupem pro čtení pouze. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Povolení brány firewall Data Lake Storage Gen2 s přístupem ke službě Azure

Data Lake Storage Gen2 podporuje možnost zapnutí brány firewall a omezení přístupu jenom ke službám Azure, což se doporučuje omezit vektor externích útoků. Bránu firewall lze povolit na účtu úložiště na webu Azure Portal prostřednictvím **brány firewall** > **povolit firewall (ON)** > **Povolit přístup k** možnostem služeb Azure.

Pokud chcete získat přístup k účtu úložiště z Azure Databricks, nasaďte Azure Databricks do virtuální sítě a pak přidejte tuto virtuální síť do brány firewall. Viz [Konfigurace firewallů azure storage a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti

Při navrhování systému s Data Lake Storage Gen2 nebo jakékoli cloudové služby, musíte zvážit vaše požadavky na dostupnost a jak reagovat na potenciální přerušení služby. Problém může být lokalizován na konkrétní instance nebo dokonce celé oblasti, takže s plánem pro oba je důležité. V závislosti na cíli doby obnovení a cíle sla bodu obnovení pro vaše úlohy můžete zvolit více či méně agresivní strategii pro vysokou dostupnost a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Vysoká dostupnost (HA) a zotavení po havárii (DR) lze někdy kombinovat dohromady, i když každý má mírně odlišnou strategii, zejména pokud jde o data. Data Lake Storage Gen2 již zpracovává 3x replikace pod kapotou, aby se ochránila před selháním lokalizovaného hardwaru. Kromě toho další možnosti replikace, jako je například ZRS nebo GZRS (náhled), zlepšit HA, zatímco GRS & RA-GRS zlepšit ZOTAVENÍ po havárii. Při vytváření plánu pro HA, v případě přerušení služby zatížení potřebuje přístup k nejnovějším datům co nejrychleji přepnutím na samostatně replikovanou instanci místně nebo v nové oblasti.

Ve strategii zotavení po havárii, připravit se na nepravděpodobné události katastrofické selhání oblasti, je také důležité mít data replikována do jiné oblasti pomocí replikace GRS nebo RA-GRS. Je také nutné zvážit vaše požadavky na hraniční případy, jako je například poškození dat, kde můžete chtít vytvořit periodické snímky, na které se můžete vrátit. V závislosti na důležitosti a velikosti dat zvažte postupné rozdílové snímky 1-, 6 a 24 hodin podle tolerancí rizika.

Pro odolnost dat s Data Lake Storage Gen2 se doporučuje geograficky replikovat data pomocí GRS nebo RA-GRS, který splňuje vaše požadavky NA/DR. Kromě toho byste měli zvážit způsoby, jak pro aplikaci pomocí Data Lake Storage Gen2 automaticky převzetí služeb při selhání do sekundární oblasti prostřednictvím monitorování aktivačních událostí nebo délku neúspěšných pokusů, nebo alespoň odeslat oznámení správcům pro ruční zásah. Mějte na paměti, že existuje kompromis selhání nad versus čekání na službu, aby se vrátil online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití funkce Distcp pro přesun dat mezi dvěma umístěními

Zkratka pro distribuované kopie, DistCp je linuxový nástroj příkazového řádku, který je dodáván s Hadoop a poskytuje distribuovaný pohyb dat mezi dvěma umístěními. Dvě umístění mohou být Data Lake Storage Gen2, HDFS nebo S3. Tento nástroj používá MapReduce úlohy v clusteru Hadoop (například HDInsight) na škálování na všech uzlech. Distcp je považován za nejrychlejší způsob, jak přesunout velká data bez speciálních síťových kompresních zařízení. Distcp také poskytuje možnost aktualizovat pouze rozdíly mezi dvěma umístěními, zpracovává automatické opakování, stejně jako dynamické škálování výpočetních prostředků. Tento přístup je neuvěřitelně efektivní, pokud jde o replikaci věcí, jako je Hive/Spark tabulky, které mohou mít mnoho velkých souborů v jednom adresáři a chcete pouze kopírovat přes upravená data. Z těchto důvodů distcp je nejvíce doporučený nástroj pro kopírování dat mezi úložišti velkých objemů dat.

Kopírovat úlohy mohou být spuštěny pracovními postupy Apache Oozie pomocí frekvenčních nebo datových aktivačních událostí, stejně jako linuxových cron úloh. Pro úlohy intenzivní replikace se doporučuje svést samostatný cluster HDInsight Hadoop, který lze vyladit a škálovat speciálně pro úlohy kopírování. Tím je zajištěno, že úlohy kopírování nenarušují kritické úlohy. Pokud běží replikace na dostatečně široké frekvenci, clusteru lze dokonce vzít dolů mezi každou úlohu. Pokud přejdeme k selhání do sekundární oblasti, ujistěte se, že jiný cluster je také stočil v sekundární oblasti replikovat nová data zpět do primárního účtu Data Lake Storage Gen2, jakmile se vrátí zpět nahoru. Příklady použití Distcp, najdete [v tématu použití Distcp ke kopírování dat mezi objekty BLOB úložiště Azure a Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Plánování úloh kopírování pomocí Azure Data Factory

Azure Data Factory lze také naplánovat úlohy kopírování pomocí aktivity kopírování a lze dokonce nastavit na frekvenci pomocí Průvodce kopírováním. Mějte na paměti, že Azure Data Factory má limit cloudových jednotek přesunu dat (DMU) a nakonec omezuje propustnost/výpočetní prostředky pro velké datové úlohy. Azure Data Factory navíc aktuálně nenabízí rozdílové aktualizace mezi účty Data Lake Storage Gen2, takže adresáře, jako jsou tabulky Hive, by vyžadovaly úplnou kopii k replikaci. Další informace o kopírování pomocí datové továrny naleznete v [článku datové továrny.](../../data-factory/load-azure-data-lake-storage-gen2.md)

## <a name="monitoring-considerations"></a>Aspekty monitorování

Data Lake Storage Gen2 poskytuje metriky na portálu Azure pod účtem Data Lake Storage Gen2 a v Azure Monitoru. Dostupnost Data Lake Storage Gen2 se zobrazí na webu Azure Portal. Chcete-li získat nejaktuálnější dostupnost účtu Gen2 úložiště datového jezera, musíte spustit vlastní syntetické testy k ověření dostupnosti. Další metriky, jako je celkové využití úložiště, požadavky na čtení a zápis a příchozí přenos dat/odchozí přenos dat jsou k dispozici pro využití pomocí monitorování aplikací a můžete také aktivovat výstrahy při překročení prahových hodnot (například průměrná latence nebo # chyb za minutu).

## <a name="directory-layout-considerations"></a>Důležité informace o rozložení adresáře

Při vykládce dat do datového jezera je důležité předem naplánovat strukturu dat tak, aby bylo možné efektivně využívat zabezpečení, dělení a zpracování. Mnoho z následujících doporučení platí pro všechny úlohy velkých objemů dat. Každé pracovní vytížení má různé požadavky na způsob spotřebování dat, ale níže jsou některé běžné rozložení, které je třeba zvážit při práci s IoT a dávkovými scénáři.

### <a name="iot-structure"></a>Struktura IoT

V úlohách IoT může být velké množství dat vykládané v úložišti dat, která se rozprostírá napříč mnoha produkty, zařízeními, organizacemi a zákazníky. Je důležité předem naplánovat rozložení adresáře pro organizaci, zabezpečení a efektivní zpracování dat pro spotřebitele s následným proudem. Obecnou šablonou, kterou je třeba zvážit, může být následující rozložení:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Například přistávací telemetrie pro letecký motor ve Velké Británii může vypadat jako následující struktura:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Existuje důležitý důvod, proč dát datum na konec adresářové struktury. Pokud chcete uzamknout určité oblasti nebo předmět záležitosti pro uživatele / skupiny, pak můžete snadno tak učinit s oprávněními POSIX. V opačném případě, pokud by bylo nutné omezit určitou skupinu zabezpečení na prohlížení pouze dat ve Velké Británii nebo určitých letadel, s datovou strukturou vpředu by bylo vyžadováno samostatné oprávnění pro mnoho adresářů pod každou hodinou adresáře. Navíc s datovou strukturu vpředu by exponenciálně zvýšit počet adresářů, jak šel čas.

### <a name="batch-jobs-structure"></a>Struktura dávkových úloh

Z vysoké úrovně, běžně používaný přístup v dávkovém zpracování je přistát data v adresáři "in". Poté, co jsou data zpracována, vložte nová data do adresáře "out", aby mohly příjemprocesů využívat. Tato adresářová struktura je někdy vidět pro úlohy, které vyžadují zpracování na jednotlivé soubory a nemusí vyžadovat masivně paralelní zpracování přes velké datové sady. Stejně jako výše doporučená struktura IoT má dobrá adresářová struktura adresářové adresáře adresáře na úrovni nadřazených pro věci, jako je oblast a předmět (například organizace, produkt/výrobce). Tato struktura pomáhá při zabezpečení dat v celé organizaci a lepší správě dat ve vašich úlohách. Dále zvažte datum a čas ve struktuře, aby bylo možné lépe organizace, filtrované vyhledávání, zabezpečení a automatizace ve zpracování. Úroveň rozlišovací schopnost pro strukturu kalendářních dat je určena intervalem, ve kterém jsou data nahrána nebo zpracována, například každou hodinu, denně nebo dokonce měsíčně.

Někdy je zpracování souborů neúspěšné z důvodu poškození dat nebo neočekávaných formátů. V takových případech může mít adresářová struktura prospěch z **/bad** složky přesunout soubory pro další kontrolu. Dávková úloha může také zpracovat vykazování nebo oznámení těchto *chybných* souborů pro ruční zásah. Zvažte následující strukturu šablony:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Marketingová firma například dostává denní datové výpisy zákaznických aktualizací od svých klientů v Severní Americe. Před zpracováním a po zpracování může vypadat jako následující úryvek:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

V běžném případě dávkových dat zpracovávaných přímo do databází, jako je Například Hive nebo tradiční databáze SQL, není potřeba složky **/in** nebo **/out,** protože výstup již přejde do samostatné složky pro tabulku Hive nebo externí databázi. Například denní výpisy od zákazníků by přistát do svých příslušných složek a orchestrace něco jako Azure Data Factory, Apache Oozie nebo Apache Airflow by spustit denní Hive nebo Spark úlohy ke zpracování a zápisu dat do tabulky Hive.
