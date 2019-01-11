---
title: Premium, horké, studené a archivní úložiště pro objekty BLOB – Azure Storage
description: Premium, vrstvy Hot, Cool a Archive storage pro účty úložiště Azure.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 01/09/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 19a9ef3e3f6a33e1f8e4d1e47ddc7562c0b90e37
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197670"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Azure Blob storage: Premium (preview), vrstvy úložiště Hot, Cool a archiv

## <a name="overview"></a>Přehled

Azure storage nabízí úrovně jiného úložiště, které vám umožňují ukládat data objektu Blob cenově nejvýhodnější způsobem. Dostupné úrovně zahrnují:

- **Storage úrovně Premium (preview)** poskytuje vysoce výkonnému hardwaru pro data, která se využívají často.
 
- **Horké úložiště**: je optimalizovaná pro ukládání dat, která se využívají často. 

- **Studené úložiště** je optimalizovaná pro ukládání dat, která se nevyužívají často a ukládají nejméně na 30 dnů.
 
- **Úložiště archivu** je optimalizovaná pro ukládání dat, která je jen zřídka často a ukládají nejméně na 180 dnů vyžadují flexibilní latenci (v řádu hodin).

Následující aspekty doprovázet úrovně jiného úložiště:

- Úroveň úložiště Archive je k dispozici pouze na úrovni objektů blob a ne na úrovni účtu úložiště.
 
- Data ve studené úrovni úložiště může tolerovat trochu horší dostupnost, ale stále se vyžaduje vysoká odolnost a podobná čas přístupu a propustnost vlastnosti jako u horkých dat. Pro studená data, trochu nižší dostupnost SLA a vyšší přístup nákladů ve srovnání s horkými daty jsou přijatelné výrazně nižší náklady na úložiště.

- Úložiště archivu je offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup.
 
- Pouze vrstvy úložiště Hot a Cool můžete nastavit na úrovni účtu. Archivní vrstva aktuálně nelze nastavit na úrovni účtu.
 
- Horké, studené, a archivní úrovni můžete nastavit na úrovni objektu.

Data uložená v cloudu roste exponenciální rychlostí. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se liší tím, jak se generují, zpracovávají a jak se k nim přistupuje po celou dobu jejich životnosti. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data se po uložení v cloudu využívají zřídka, pokud vůbec někdy.

Pro každý z těchto scénářů přístupu k datům je vhodná jiná vrstva úložiště, která je optimalizovaná pro určitý vzor přístupu. Horké studené a archivní vrstvy úložiště služba Azure Blob storage vychází vstříc potřebě úrovní úložiště s odlišnými cenovými modely.

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Data v úložišti objektů na horké, studené ani archivní úrovně v úložišti objektů Blob a obecné účely v2 (GPv2) účty mohou pouze vrstvy. Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Zákazníci však můžou snadno převést své stávající účty GPv1 nebo Blob Storage na účty GPv2 prostřednictvím prostého kliknutí na webu Azure Portal. Účty GPv2 poskytují novou cenovou strukturu pro objekty blob, soubory a fronty a také přístup k různým dalším novým funkcím úložiště. Kromě toho se postupně budou některé nové funkce a snížení cen nabízet pouze v účtech GPv2. Zákazníci by proto měli vyhodnotit používání účtů GPv2, ale začít je používat až po kontrole cen za všechny služby, protože některé úlohy můžou být v účtech GPv2 dražší než v účtech GPv1. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

BLOB storage a GPv2 zpřístupňují účty **úroveň přístupu** atribut na úrovni účtu, který vám umožňuje určit výchozí úroveň úložiště jako horkou nebo studenou pro všechny objekty blob v účtu úložiště, který nemá explicitně nastavená na úroveň úroveň objektu. Na objekty s nastavenou úrovní na úrovni objektu se úroveň účtu nebude vztahovat. Archivní úrovni lze použít pouze na úrovni objektu. Mezi těmito úrovněmi úložiště můžete kdykoli přepínat.

## <a name="premium-access-tier"></a>Přístup na úrovni Premium

Přístup k vrstvě Premium, díky kterému budou často používaná data k dispozici prostřednictvím vysoce výkonnému hardwaru je dostupná ve verzi preview. Data uložená na této úrovni se ukládají na jednotky SSD, které jsou optimalizovány pro nižší latenci a vyšší míra transakční ve srovnání s tradičními pevné disky. Přístup k úrovni Premium je k dispozici prostřednictvím pouze typ účtu úložiště objektů Blob bloku.

Tato úroveň je ideální pro úlohy vyžadující rychlé a konzistentní odezvy. Data, která zahrnuje koncovým uživatelům, jako jsou úpravy, statický webový obsah, online transakce a podobně, jsou vhodnými kandidáty přístup k vrstvě Premium pro interaktivní videa. Tato úroveň je vytvořený na míru pro úlohy, které provádějí velký počet malých transakcí, jako je zachytávání telemetrická data, zasílání zpráv a transformace dat.

Pokud chcete použít tuto úroveň, zřiďte nový účet úložiště objektů Blob bloku a začněte vytvářet kontejnerům a objektům BLOB pomocí [rozhraní REST API služby Blob](/rest/api/storageservices/blob-service-rest-api), [AzCopy](/azure/storage/common/storage-use-azcopy), nebo [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

Ve verzi preview, na úrovni Premium přístupu:

- Je k dispozici jako místně redundantní úložiště (LRS)
- Je k dispozici pouze v těchto oblastech: USA – východ 2, USA – střed a USA – západ
- Nepodporuje vrstev na úrovni objektu nebo automatizovaným vrstvením s správu životního cyklu dat.

Zjistěte, jak zaregistrovat verzi preview přístup úrovně Premium, najdete v článku [Úvod do Azure Blob Storage úrovně Premium](https://aka.ms/premiumblob).

## <a name="hot-access-tier"></a>Horká vrstva přístupu

Horké úložiště má vyšší náklady na úložiště než ze studené a archivní úložiště, ale nejnižší náklady na přístup. Příklad scénáře použití pro horké úrovni úložiště zahrnují:

* Data, která se aktivně používají nebo se k nim očekává častý aktivní přístup (čtení a zápis).
* Data, která jsou určená ke zpracování a eventuální migraci na úroveň studeného úložiště.

## <a name="cool-access-tier"></a>Studená vrstva přístupu

Studená úroveň úložiště má nižší náklady na úložiště a porovnání s horkou vyšší náklady na přístup. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Příklad scénáře využití studené úrovni úložiště zahrnují:

* Krátkodobé zálohování a datové sady pro zotavení po havárii.
* Starší obsah a média, které se již nezobrazují často, ale které by však měly být na vyžádání okamžitě dostupné.
* Velké datové sady, které je potřeba levně uložit, zatímco se shromažďují další data pro budoucí zpracování. (*Příklady:* Dlouhodobé uložení vědeckých dat, nezpracovaná telemetrická data z výrobního závodu.)

## <a name="archive-access-tier"></a>Úroveň přístupu Archive

Archivní úložiště má nejnižší náklady na úložiště a vyšší náklady na načtení dat v porovnání s horkou a studenou. Tato úroveň je určená pro data, která se toleruje latence načtení několik hodin a zůstanou v archivní úrovni po dobu nejméně na 180 dnů.

Když je objekt blob v archivním úložišti, jsou data objektu blob je offline a nejde přečíst, zkopírovaný, přepsat či jinak změněn. Není také možné pořizovat snímky objektů blob v archivním úložišti. Metadata objektu blob ale zůstává online a dostupná, umožňuje výpis objektu blob a jeho vlastnosti. Jediné platné operace pro objekty blob v archivní úrovni jsou GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier a DeleteBlob. 


Příklad scénáře použití pro archivní úrovni úložiště zahrnují:

* dlouhodobé zálohování, sekundární zálohování a archivní datové sady
* Původní (hrubá, nezpracovaná) data, která je potřeba zachovat i po jejich zpracování do konečné, použitelné podoby. (*Příklad:* Původní multimediální záznamy po překódování do jiných formátů.)
* Data pro soulad a archivaci, které je potřeba uchovat po dlouhou dobu a téměř se k nim nepřistupuje. (*Příklady:* Záznamy z bezpečnostních kamer, staré rentgenové snímky / snímky magnetické rezonance pro zdravotnická zařízení, zvukové záznamy a přepisy zákaznických hovorů pro finanční služby.)

### <a name="blob-rehydration"></a>Dosazování objektů blob
Číst data v archivním úložišti, musíte nejdřív změnit úroveň objektu blob na horkou nebo studenou. Tento proces se označuje jako dosazování a jeho dokončení může trvat až 15 hodin. Velké objekty BLOB se doporučují pro zajištění optimálního výkonu. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit.

Během dosazování můžete zkontrolovat vlastnost **Stav archivu** objektu blob a ověřit, jestli se úroveň změnila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a **úroveň přístupu** vlastnost objektu blob odpovídá nové horké nebo studené vrstvy.  

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Ovládání datových vrstev na úrovni objektů blob umožňuje změnit vrstvu dat na úrovni objektu pomocí jediné operace s názvem [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Můžete snadno změnit úroveň přístupu objektu blob z vrstvy Hot, Cool nebo Archive změnou vzorů využití, aniž byste museli přesouvat data mezi účty. Všechny změny vrstvy dojde okamžitě. Dosazování objektu blob z archivní úrovně může trvat několik hodin. 

Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. Pokud je objekt blob v archivní úrovni, ho nelze přepsat, aby nahrávání stejný objekt blob není povolena v tomto scénáři. Můžete přepsat objektů blob v horké nebo studené úrovně, ve kterém případě nový objekt blob zdědí úroveň objektu blob, který se přepsal.

V rámci jednoho účtu mohou současně existovat objekty blob ve všech třech vrstvách úložiště. Každý objekt blob, který nemá explicitně přiřazenou vrstvu, odvozuje nastavení vrstvy přístupu z účtu. Pokud je úroveň přístupu odvozuje z účtu, zobrazí se **Access Tier Inferred** blob nastavenou na hodnotu "true" a objektu blob **úroveň přístupu** vlastností objektu blob odpovídá úrovni účtu. Na webu Azure Portal se vlastnost odvození úrovně přístupu zobrazuje společně s úrovní přístupu objektu blob (například Horká (odvozeno) nebo Studená (odvozeno)).

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku. Nejde také změnit úroveň objektu blob bloku, který má snímky.

> [!NOTE]
> Data uložená na úrovni Premium přístupu nemůže být rozvrstvena aktuálně horká, studená nebo archivní pomocí [Set Blob Tier](/rest/api/storageservices/set-blob-tier) nebo pomocí správy životního cyklu úložiště objektů Blob v Azure. Pro přesun dat, je nutné synchronně zkopírovat objekty BLOB ze Premium přístup k výměně pomocí [Vložit blok z adresy URL rozhraní API](/rest/api/storageservices/put-block-from-url) nebo verzi AzCopy, který podporuje toto rozhraní API. *Vložit blok z adresy URL* rozhraní API synchronně zkopíruje data na serveru, což znamená dokončení volání pouze jednou všechna data se přesunou z původního umístění serveru do cílového umístění.

### <a name="blob-lifecycle-management"></a>Správa životního cyklu objektu BLOB
Správa životního cyklu úložiště objektů BLOB (Preview) nabízí bohatě vybaveným a podle pravidel zásad, který vám pomůže se převést vaše data, aby nejlepší úroveň přístupu a vypršení platnosti dat na konci svého životního cyklu. Zobrazit [Správa životního cyklu úložiště objektů Blob v Azure](storage-lifecycle-management-concepts.md) Další informace.  

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když se objekt blob přesune do chladnější úrovně (horká -> studená, horká -> archivní nebo studená -> archivní), operace se fakturuje jako operace zápisu do cílové vrstvy, kde účtují operace zápisu (za 10 000) a poplatky za data zápisu (za GB) pro cílovou úroveň. Když se objekt blob přesune do teplejší úrovně (Archive -> studená, Archive -> horká nebo studená -> horká), operace se fakturuje jako čtení ze zdrojové vrstvy, které platí operace čtení (za 10 000) a načítání (za GB) dat pro zdrojovou vrstvu. Následující tabulka shrnuje, jak se účtují změny úrovně.

| | **Zápis poplatky (operace + přístup)** | **Poplatky za čtení (operace + přístup)** 
| ---- | ----- | ----- |
| **Směr SetBlobTier** | Horká -> studená, horká -> archivní, studená -> Archive | Archivní -> studená, archivní -> horká, studená -> horká

Pokud přepnete úroveň účtu z horké úrovně na studenou, vám bude účtovat operace zápisu (za 10 000) pro všechny objekty BLOB bez nastavené úrovně v účtech GPv2. Neplatí žádné poplatky pro tuto změnu v účtech Blob storage. Pokud přepnete účet Blob storage nebo GPv2 účet z vrstvy Cool na Hot bude účtovat pro operace čtení (za 10 000) i načítání dat (za GB). Také se můžou vztahovat poplatky předčasné odstranění pro každý objekt blob přesune ze studené nebo archivní úrovně.

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Kromě za GB, za poplatek za měsíc, všechny objekty blob přesunuté do studené vrstvy (pouze účty GPv2) vztahuje studené období předčasného odstranění z 30 dnů a všechny objekty blob přesunuté do archivní úroveň je v souladu období předčasného odstranění archivní 180 dnů. Tento poplatek se účtuje poměrnou částí. Například, pokud objekt blob se přesune do archivní úrovně a pak odstraní nebo přesune do horké úrovně po 45 dnech se vám bude účtovat poplatek za předčasné odstranění odpovídá 135 (180 − 45) dnům uložení tohoto objektu blob v archivní úrovni.

Předčasné odstranění může vypočítat pomocí vlastnosti objektu blob **čas vytvoření**, pokud nepřichází žádný přístup změny úrovně. Jinak můžete použít při poslední změny úrovně přístupu ze studené nebo archivní zobrazením vlastností objektu blob: **přístup úroveň změnit čas**. Další informace o vlastnosti objektu blob najdete v tématu [získat vlastnosti objektu Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparison-of-the-storage-tiers"></a>Srovnání vrstev úložiště

V následující tabulce najdete porovnání horké, studené a archivní úroveň úložiště.

| | **Horká vrstva úložiště** | **Studená vrstva úložiště** | **Úroveň úložiště Archive**
| ---- | ----- | ----- | ----- |
| **Dostupnost** | 99,9 % | 99 % | neuvedeno |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**| 99,99 % | 99,9 % | neuvedeno |
| **Poplatky za využití** | Vyšší náklady na úložiště, nižší přístup a transakce | Nižší náklady na úložiště, vyšší přístup a transakce | Nejnižší náklady na úložiště, nejvyšší přístup a transakce |
| **Minimální velikost objektu** | neuvedeno | neuvedeno | neuvedeno |
| **Minimální doba uložení** | neuvedeno | 30 dnů (pouze GPv2) | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | milisekundy | milisekundy | < 15 hodin
| **Škálovatelnost a cíle výkonnosti** | Stejné jako u účtů úložiště pro obecné účely | Stejné jako u účtů úložiště pro obecné účely | Stejné jako u účtů úložiště pro obecné účely |

> [!NOTE]
> Účty úložiště Blob podporují stejnou škálovatelnost a cíle výkonnosti jako účty úložiště pro obecné účely. Další informace najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V tomto oddílu jsou předvedené následující scénáře s využitím webu Azure Portal:

* Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage.
* Změna úrovně objektu blob v účtu GPv2 nebo Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Klikněte na možnost Všechny prostředky a kliknutím na účet úložiště do účtu přejděte.

3. V okně Nastavení klikněte na **Konfigurace**, odkud můžete zobrazit nebo změnit konfiguraci účtu.

4. Vyberte vhodnou úrovní úložiště pro vaše potřeby: Nastavte **úroveň přístupu** buď **Cool** nebo **Hot**.

5. V horní části okna klikněte na možnost Uložit.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změňte úroveň objektu blob v účtu GPv2 nebo Blob Storage.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Pokud chcete přejít na objekt blob ve vašem účtu úložiště, vyberte Všechny prostředky, vyberte účet úložiště a potom vyberte objekt blob.

3. V okně vlastností objektu blob v rozevírací nabídce **Úroveň přístupu** kliknutím vyberte úroveň úložiště (**horká**, **studená** nebo **archivní**).

5. V horní části okna klikněte na možnost Uložit.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny účty úložiště používají cenový model pro úložiště objektů Blob založeného na úrovních jednotlivých objektů blob. Mějte na paměti následující aspekty fakturace:

* **Náklady na úložiště**: Kromě objemu uložených dat náklady na ukládání dat se liší podle úrovně úložiště. Pokud je úroveň chladnější, cena za gigabajt se snižuje.
* **Cena za přístup**: Přístup k datům za úroveň chladnější. Pro data v studené a archivní úrovni úložiště bude se vám účtovat poplatek za GB dat přístup pro čtení.
* **Cena za transakce**: Se účtuje poplatek za transakce pro všechny úrovně, které zvýší úroveň chladnější.
* **Cena za přenosy dat geografické replikace**: Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.
* **Cena za přenosy odchozích dat**: Přenosy odchozích dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné využití šířky pásma na základě sazby za gigabajt konzistentní s účty úložiště pro obecné účely.
* **Změna vrstvy úložiště**: Změna vrstvy úložiště účtu ze studené na horkou je zpoplatněna částkou, která odpovídá přečtení všech dat v aktuálním účtu úložiště. Naproti tomu změna vrstvy úložiště účtu z horké úrovně na studenou je zpoplatněna částkou, která odpovídá zápisu všech dat do studené vrstvy (pouze účty GPv2).

> [!NOTE]
> Další informace o cenách pro účty Blob storage, najdete v části [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) stránky. Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Nejčastější dotazy

**Pokud chci vrstvit svá data, mám použít účet Blob Storage nebo GPv2?**

Pro účely vrstvení doporučujeme místo účtů Blob Storage používat účty GPv2. Účty GPv2 podporují všechny funkce jako účty Blob Storage a navíc řadu dalších. Ceny účtů Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen budou k dispozici pouze v účtech GPv2. Účty GPv1 vrstvení nepodporují.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

**Můžete ukládat objekty ve všech třech (Hot, Cool a archiv) vrstvách úložiště ve stejném účtu?**

Ano. Atribut **Access Tier** nastavený na úrovni účtu představuje výchozí úroveň, která se použije pro všechny objekty v příslušném účtu, které nemají explicitně nastavenou úroveň. Ovládání datových vrstev na úrovni objektů blob však umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaké je nastavení úrovně přístupu v příslušném účtu. V rámci stejného účtu mohou existovat objekty BLOB ve všech třech vrstvách úložiště (Hot, Cool nebo archiv).

**Můžu u svého účtu úložiště Blob Storage nebo GPv2 změnit výchozí úroveň úložiště?**

Ano, výchozí úroveň úložiště můžete změnit nastavením atributu **Access Tier** v účtu úložiště. Změna vrstvy úložiště se projeví u všech objektů uložených v tomto účtu, u kterých není explicitně nastavená úroveň. Při přepnutí úrovně úložiště z horké úrovně na studenou vznikají náklady operace zápisu (za 10 000) pro všechny objekty BLOB bez nastavené úrovně v účtech GPv2 a přepnete z vrstvy Cool na Hot vznikají náklady na operace čtení (za 10 000) a načítání dat (za GB) poplatky za všechny objekty BLOB v úložišti objektů Blob a účty GPv2.

**Můžete nastavit Moje výchozí úroveň přístupu účtu do archivní úrovně?**

Ne. Pouze vrstvy úložiště Hot a Cool, může být nastaven jako výchozí úrovně přístupu u účtu. Archivní úroveň je možné nastavit pouze na úrovni objektu.

**V které oblasti jsou vrstvy úložiště Hot, Cool a archiv k dispozici v?**

Vrstvy úložiště Hot a Cool spolu s ovládání datových vrstev na úrovni objektů blob jsou k dispozici ve všech oblastech. Úložiště archivu bude zpočátku dostupné pouze ve vybraných oblastech. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

**Objekty BLOB ve studené úrovni úložiště chovat jinak než v horké úrovni úložiště?**

Objekty BLOB v horké úrovni úložiště mají stejnou latenci jako objekty BLOB v účtech GPv1, GPv2 a Blob storage. Objekty BLOB ve studené úrovni úložiště mají podobnou latenci (v milisekundách) jako objekty BLOB v účtech GPv1, GPv2 a Blob storage. Objekty BLOB v archivní úrovni úložiště mají latenci několik hodin v účtech GPv1, GPv2 a Blob storage.

Objekty BLOB ve studené úrovni úložiště mají trochu nižší dostupnost služby (SLA) než objekty BLOB uložené v horké úrovni úložiště. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Jsou operace mezi úrovně Hot, Cool a archiv stejné?**

Ano. Všechny operace mezi horkou a studenou jsou 100 % konzistentní. Všechny platné archivní operace včetně delete, list, get vlastnosti nebo metadata objektu blob a nastavení úrovně objektu blob jsou 100 % konzistentní s Hot a Cool. Objekt blob se nedají přečíst ani upravit v archivní úrovni.

**Dosazování objektu blob z archivní úrovně do horké nebo studené vrstvy, jak poznám, že dokončení?**

Během dosazování můžete ověřit dokončení změny úrovně dotazováním atributu **Stav archivu** pomocí operace získání vlastností objektu blob. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a **úroveň přístupu** vlastnost objektu blob odpovídá nové horké nebo studené vrstvy.  

**Kdy se mi po nastavení úrovně objektu blob začne účtovat podle odpovídající sazby?**

Každý objekt blob se vždy účtuje podle úrovně, kterou indikuje jeho **úroveň přístupu** vlastnost. Když nastavíte novou úroveň pro objekt blob **úroveň přístupu** vlastnost okamžitě odpovídá na nové úrovni pro všechny přechody. Dosazování objektu blob z archivní úrovně na horkou nebo studenou úroveň může trvat několik hodin. V takovém případě se účtují sazbami archivu až do dokončení, v tomto okamžiku **úroveň přístupu** vlastnost odráží novou úroveň. V tomto okamžiku se účtují tohoto objektu blob na Hot nebo Cool frekvence.

**Jak zjistím, pokud se mi bude účtovat poplatek za předčasné odstranění, když odstraním nebo přesunu objekt blob ze studené nebo archivní úrovni?**

Každý objekt blob, který se odstraní nebo přesune ze studené (pouze účty GPv2) nebo archivní úrovně před 30 dnů po 180 dnech v uvedeném pořadí budou účtovat poměrná část poplatek za předčasné odstranění. Můžete určit, jak dlouho objektu blob byla ve studené nebo archivní úrovně tak, že zkontrolujete **Access Tier Change Time** vlastnost, která obsahuje časové razítko poslední změny úrovně objektu blob. Další informace najdete v tématu [ze studené a archivní předčasné odstranění](#cool-and-archive-early-deletion).

**Sady SDK a nástroji Azure podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu?**

Azure portal, PowerShell a rozhraní příkazového řádku podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu nástroje a klientské knihovny .NET, Java, Python a Node.js.  

**Kolik dat můžu ukládat do vrstvy Hot, Cool a archiv?**

Limit úložiště dat je společně s dalšími limity nastavený na úrovni účtu, a ne na úrovni úložiště. Proto můžete použít celý limit v jedné úrovni nebo napříč všemi třemi úrovněmi. Další informace najdete v tématu [Azure Storage škálovatelnost a cíle výkonnosti](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Další postup

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Vyhodnocení Hot, Cool a archiv v účtech GPv2 a Blob storage

[Zkontrolovat dostupnost této vrstvy Hot, Cool a archiv podle oblastí](https://azure.microsoft.com/regions/#services)

[Správa životního cyklu úložiště objektů Blob v Azure](storage-lifecycle-management-concepts.md)

[Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)

[Zkontrolujte Hot, Cool a archiv v Blob storage a GPv2 ceny účtů v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)

[Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
