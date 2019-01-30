---
title: Osvědčené postupy pro používání Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Podívejte se na osvědčené postupy o příjem dat, data zabezpečení a výkonu související s používáním Azure Data Lake Storage Gen2 (dříve označované jako Azure Data Lake Store)
services: storage
author: sachinsbigdata
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: sachins
ms.openlocfilehash: ad77204f0c5d916b4006ffa68a9608429f93f87a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246054"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Doporučené postupy pro používání Azure Data Lake Storage Gen2

V tomto článku se dozvíte o osvědčených postupech a důležité informace pro práci s Azure Data Lake Storage Gen2. Tento článek obsahuje informace týkající se zabezpečení, výkon, odolnost proti chybám a monitorování pro Data Lake Storage Gen2. Před Data Lake Storage Gen2 byl složitý. práce se skutečně velkých objemů dat do služby, jako je Azure HDInsight. Jste měli sdílení dat napříč několika účty úložiště Blob tak, aby bylo možné dosáhnout petabajty úložiště a optimální výkon při, které se škálují. S Data Lake Storage Gen2 jsou odstraněna většina pevných limitů velikosti a výkonu. Existují však stále některé aspekty, které v tomto článku najdete tak, aby získáte nejlepší výkon s Data Lake Storage Gen2.

## <a name="security-considerations"></a>Aspekty zabezpečení

Azure Data Lake Storage Gen2 nabízí řízení přístupu POSIX pro uživatele, skupiny a instanční objekty Azure Active Directory (Azure AD). Tyto ovládací prvky přístupu je možné nastavit na existujících souborů a adresářů. Řízení přístupu je také možné vytvořit výchozí oprávnění, která mohou být automaticky použity na nové soubory nebo adresáře. Další informace o Data Lake Storage Gen2 seznamy řízení přístupu najdete na adrese [řízení přístupu v Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Pomocí skupin zabezpečení a jednotlivé uživatele

Práce s velkými objemy dat v Data Lake Storage Gen2 WWhen, je pravděpodobné, že instanční objekt služby slouží k povolení služeb, jako je Azure HDInsight pro práci s daty. Ale může být případy, kdy jednotliví uživatelé potřebují přístup k datům stejně. Ve všech případech se důkladně zvážit možnost pomocí Azure Active Directory [skupiny zabezpečení](../common/storage-auth-aad.md) místo jednotlivým uživatelům přiřadí adresářů a souborů.

Po přiřazení oprávnění skupiny zabezpečení, přidání nebo odebrání uživatele ze skupiny nevyžaduje žádné aktualizace na Gen2 úložiště Data Lake. Také pomůžete tím zajistit, že abyste nepřekročili maximální počet položek řízení přístupu na seznam řízení přístupu (ACL). V současné době toto číslo je 32 (včetně čtyři stylu POSIX seznamy ACL, které jsou vždy spojené s každou souborů a adresářů): vlastnícího uživatele, vlastnící skupinu, maska a další. Každý adresář můžete mít dva typy seznamu ACL, přístup k seznamu ACL a výchozího seznamu ACL, a cena celkem 64 položky řízení přístupu. Další informace o těchto seznamů řízení přístupu najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Zabezpečení pro skupiny

Pokud vy nebo vaši koncoví uživatelé potřebují přístup k datům v účtu úložiště s hierarchického oboru názvů povolené, je nejvhodnější používat skupiny zabezpečení Azure Active Directory. Několik doporučených skupiny začít s může být **ReadOnlyUsers**, **WriteAccessUsers**, a **FullAccessUsers** kořenového souboru systémů a dokonce i těch, které jsou pro oddělení klíče podadresářů. Pokud existují další očekávané skupiny uživatelů, kteří mohou být přidány později, ale nebyly identifikovány dosud jste zvažte vytvoření skupiny fiktivní zabezpečení, které mají přístup k určitým složkám. Pomocí skupiny zabezpečení zajišťuje, že se můžete vyhnout dlouhá doba zpracování po přiřazení nového oprávnění až po tisíce souborů.

### <a name="security-for-service-principals"></a>Zabezpečení pro instanční objekty

Instanční objekty Azure Active Directory jsou obvykle používány služeb, jako je Azure Databricks pro přístup k datům v Data Lake Storage Gen2. Pro mnoho zákazníků může stačit jeden objekt služby Azure Active Directory a může mít úplná oprávnění v kořenové složce systému souborů Data Lake Storage Gen2. Ostatní zákazníci mohou vyžadovat víc clusterů pomocí různých instančních objektů kde jeden cluster má plný přístup k datům a jiný cluster s oprávněním pouze ke čtení. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Povolit bránu firewall Data Lake Storage Gen2 s přístup služby Azure

Data Lake Storage Gen2 podporuje možnost zapnutí brány firewall a omezení přístupu jenom na služby Azure, která se doporučuje omezit vektor externím útokům. Na webu Azure Portal prostřednictvím účtu úložiště může být povolená brána firewall **brány Firewall** > **povolit bránu Firewall (ON)** > **povolit přístup ke službám Azure** možnosti.

Přidání clusterů Azure Databricks k virtuální síti, která bude dát získat přístup přes bránu Firewall úložiště vyžaduje použití funkce ve verzi preview Databricks. Pokud chcete povolit tuto funkci, doplňte žádost o podporu.

## <a name="resiliency-considerations"></a>Důležité informace o odolnosti

Při navrhování systém s Data Lake Storage Gen2 nebo kteroukoli cloudovou službu, zvažte vaše požadavky na dostupnost a jak reagovat na potenciální přerušení služby. Problém může být lokalizována do určité instance nebo dokonce celou oblast, tak plán pro obě, je důležité. V závislosti na plánovanou dobu obnovení a obnovení bodu cíl smlouvy o úrovni služeb pro své úlohy, můžete zvolit více nebo méně agresivní strategie vysoké dostupnosti a zotavení po havárii.

### <a name="high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii

Vysoká dostupnost (HA) a zotavení po havárii (DR) v některých případech se dá zkopírovat dohromady, i když každá má mírně odlišné strategie, zejména v případě, že jde o data. Data Lake Storage Gen2 již zpracovává 3 x replikaci pod pokličkou pro ochranu proti lokalizovanému selhání hardwaru. Kromě toho ostatní možnosti replikace, jako je například ZRS zlepšit vysokou dostupnost při GRS a RA-GRS zvýšení zotavení po Havárii. Při vytváření plánu pro vysokou dostupnost, v případě přerušení služby zatížení potřebuje přístup k nejnovější data nejrychleji díky přepínání samostatně replikovaná instance místně nebo v nové oblasti.

Strategie zotavení po Havárii Příprava nepravděpodobném případě závažného selhání oblasti, je také důležité mít data replikovat do jiné oblasti pomocí replikace GRS nebo RA-GRS. Musíte taky zvážit požadavky na hraniční případy, například poškození dat, kde můžete chtít vytvořit pravidelné snímky vrátit zpět. V závislosti na závažnosti a množství dat zvažte vrácení rozdílů snímky 1 – 6 a období 24 hodin, podle rizika tolerance.

Větší odolnost dat. s Data Lake Storage Gen2 se doporučuje geograficky je replikovat data prostřednictvím GRS nebo RA-GRS, který splňuje vaše požadavky na vysokou dostupnost/zotavení po Havárii. Kromě toho byste měli zvážit možnosti pro aplikaci pomocí Data Lake Storage Gen2 automaticky převzít služby při selhání do sekundární oblasti sledováním aktivační události nebo délka neúspěšných pokusů o nebo alespoň odeslat oznámení správcům ručního zásahu. Uvědomte si, že je kompromis přebírání služeb při selhání a čekání na službu do režimu online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Použití Distcp pro přesun dat mezi dvěma umístěními

Zkratka pro distribuované kopírování DistCp je nástroj příkazového řádku systému Linux, která se dodává s Hadoop a poskytuje přesun distribuovaných dat mezi dvěma umístěními. Těmito dvěma umístěními může být Data Lake Storage Gen2, HDFS nebo S3. Tento nástroj používá úlohy mapreduce je možné na clusteru Hadoop (třeba HDInsight) pro horizontální navýšení kapacity na všech uzlech. Distcp se považuje za nejrychlejší způsob, jak přesunout velké objemy dat bez komprese speciální síťová. Distcp také nabízí možnost aktualizovat pouze rozdíly mezi dvěma umístěními, Automatické opakované pokusy obslužné rutiny, jakož i dynamické škálování výpočetního výkonu. Tento přístup je mimořádně efektivní, pokud jde o replikaci věci, jako je tabulek Hive/Spark, které může mít mnoho velkých souborů v jednom adresáři a chcete zkopírovat upravený data. Z těchto důvodů je Distcp nejvíce doporučeným nástrojem pro kopírování dat mezi úložišti velkých objemů dat.

Kopírování úlohy můžete aktivovat Apache Oozie pracovní postupy pomocí frekvence nebo data triggery, stejně jako úlohy cron systému Linux. Pro úlohy náročné na replikace se doporučuje zprovoznění jiném clusteru HDInsight Hadoop, která může vyladěná a škálovat specificky pro úlohy kopírování. Tím se zajistí, že kopírování úlohy nejsou v konfliktu s kritické úlohy. Pokud používáte dost široké, frekvenci replikace, může být clusteru i odstavit mezi každou úlohu. Pokud převzetí služeb při selhání do sekundární oblasti, ujistěte se, že jiný cluster také podařilo v sekundární oblasti replikovat nová data zpět do primárního účtu Data Lake Storage Gen2, jakmile se vrátí zpět. Příklady použití Distcp, naleznete v tématu [použití Distcp ke kopírování dat mezi objekty BLOB Azure Storage a Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Použití Azure Data Factory k naplánování úloh kopírování

Azure Data Factory můžete také použít k naplánování úloh kopírování pomocí aktivit kopírování a můžete nastavit i na frekvenci pomocí Průvodce kopírováním. Mějte na paměti, že Azure Data Factory má limit jednotek pohybu dat v cloudu (DMUs) a nakonec caps propustnost/výkon pro rozsáhlé datové úlohy. Kromě toho Azure Data Factory aktuálně nenabízí aktualizace rozdílů mezi účty Data Lake Storage Gen2, takže adresářů, jako tabulek Hive by vyžadovalo kompletní kopii k replikaci. Odkazovat [data factory článku](../../data-factory/load-azure-data-lake-storage-gen2.md) Další informace o kopírování do služby Data Factory.

## <a name="monitoring-considerations"></a>Důležité informace o monitorování

Data Lake Storage Gen2 poskytuje metriky na webu Azure Portal pomocí účtu Data Lake Storage Gen2 a ve službě Azure Monitor. Dostupnost služby Data Lake Storage Gen2 se zobrazí na webu Azure Portal. Chcete-li získat nejaktuálnější dostupnost účtu Data Lake Storage Gen2, musíte spustit syntetické testy k ověření dostupnosti. Jiné metriky, jako je například celkový úložiště využití, čtení a zápis požadavků a příchozí a odchozí přenos je možné využít při sledování aplikací a můžete také aktivovat upozornění při překročení prahové hodnoty (například Průměrná latence nebo počet chyb za minutu).

## <a name="directory-layout-considerations"></a>Důležité informace o rozložení adresáře

Při doručení dat do data lake, je důležité dopředu plánovat strukturu dat tak, aby mohli být zabezpečení, vytváření oddílů a zpracování efektivně využívá. Následující doporučení platí pro všechny úlohy s velkými objemy dat. Každé zatížení má jiné požadavky na způsob data se spotřebovává, ale v následující tabulce jsou některé běžné rozložení vzít v úvahu při práci se službou IoT a batch scénáře.

### <a name="iot-structure"></a>Struktura IoT

V úlohách IoT může být spoustu dat se dostali do úložiště dat, který zahrnuje mnoho produkty, zařízení, organizace a zákazníků. Je důležité dopředu plánovat, rozložení adresáře pro organizace, zabezpečení a efektivní zpracování datového proudu uživatelům. Obecné šablony vzít v úvahu může být následující rozložení:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Například úvodní telemetrická data motoru letadla ve Spojeném království může vypadat jako následující strukturu:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Je důležité důvod umístit data na konci adresářovou strukturu. Pokud chcete uzamknout určité oblasti nebo záležitostí na uživatele nebo skupiny, pak lze snadno provést s oprávněními POSIX. V opačném případě pokud se třeba omezit určité skupiny zabezpečení k zobrazení pouze data Velká Británie nebo určitých rovin, se strukturou datum v popředí samostatné oprávnění by byla zapotřebí pro mnoho adresáře v adresáři každou hodinu. Kromě toho s struktura datum v popředí exponenciálně zvýší počet adresářů jako čas se nepovedlo.

### <a name="batch-jobs-structure"></a>Struktura úlohy služby batch

Z hlavní běžně používaná přístupem v dávkové zpracování je dostat data do adresáře "in". Poté po zpracování dat vložte nová data do podřízené procesy využívají adresáře "out". Tato adresářová struktura se někdy používá pro úlohy, které vyžadují zpracování jednotlivých souborů a nemusí výkonným paralelním zpracováním velké datové sady. Stejně jako výše doporučených strukturu IoT má dobré adresářovou strukturu nadřazené adresáře pro takové věci, jako jsou oblasti a záležitostí (například organizace, produktu nebo výrobce). Tato struktura pomáhá se zabezpečením dat napříč vaší organizace a lepší správu dat ve vašich úloh. Kromě toho zvažte datum a čas ve struktuře umožňující lepší uspořádání, filtrovaný vyhledává, zabezpečení a automatizace ve zpracování. Úroveň podrobností pro strukturu datum je určen podle intervalu, na kterém je data nahráli nebo zpracovat, jako například každou hodinu, denně, nebo dokonce každý měsíc.

Někdy souboru zpracování neproběhne úspěšně kvůli poškození dat nebo neočekávaný formát. V takových případech by mohlo prospět adresářovou strukturu **/chybný** složky pro přesun souborů k další kontroly. Úlohy služby batch může zpracovávat také vytváření sestav nebo oznámení z nich *chybný* soubory ručního zásahu. Vezměte v úvahu následující strukturu šablony:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Například marketingové pevně přijímá denní extrahuje data zákazníků aktualizací ze svých klientech v Severní Americe. Může vypadat jako následující fragment kódu před a po zpracování dat:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

V případě běžných dat dávkové zpracování přímo do databáze, jako jsou Hive nebo tradiční databází SQL, není potřeba **/in** nebo **/out** složky, protože výstup již přejde do samostatné složky pro tabulky Hive nebo externí databáze. Například denní extrahuje od zákazníků by dostat do jejich odpovídajících složek a Orchestrace pomocí něco jako Azure Data Factory, Apache Oozie nebo Apache vzduchu aktivuje každodenní úlohu Hive nebo Spark ke zpracování a zápis dat do tabulky Hive.
