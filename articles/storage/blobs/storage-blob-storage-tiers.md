---
title: Horká studená a archivní úroveň přístupu pro objekty BLOB – Azure Storage
description: Horká studená a archivní úroveň přístupu pro účty úložiště Azure.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.reviewer: clausjor
ms.subservice: blobs
ms.openlocfilehash: 8e9e27f14c4ccd6f5822033baa21aaafcf96c428
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148496"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob storage: horká, studená a archivní úroveň přístupu

Azure storage nabízí různý přístup úrovně, které vám umožňují ukládat data objektu blob cenově nejvýhodnější způsobem. Dostupné přístupové vrstvy patří:

- **Horké** – optimalizované pro ukládání dat, která se využívají často.
- **Studená** – optimalizované pro ukládání dat, která se nevyužívají často a ukládají nejméně na 30 dnů.
- **Archiv** – optimalizované pro ukládání dat, která je jen zřídka často a ukládají nejméně na 180 dnů vyžadují flexibilní latenci (v řádu hodin).

Pro přístup různé úrovně, platí následující aspekty:

- Archivní úroveň přístupu je k dispozici pouze na úrovni objektů blob a ne na úrovni účtu úložiště.
- Data ve studené vrstvě přístupu může tolerovat trochu horší dostupnost, ale stále se vyžaduje vysoká odolnost a podobná čas přístupu a propustnost vlastnosti jako u horkých dat. Pro studená data, trochu nižší dostupnost smlouvu o úrovni služeb (SLA) a vyšší přístup nákladů ve srovnání s horkými daty jsou přijatelné výrazně nižší náklady na úložiště.
- Úložiště archivu je offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup.
- Lze nastavit pouze horká a studená vrstva přístupu na úrovni účtu.
- Horké, studené a archivní úrovni lze nastavit na úrovni objektu.

Data uložená v cloudu roste exponenciální rychlostí. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se liší tím, jak má vygenerována, zpracování a využívají. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data nečinný v cloudu a je jen zřídka, pokud vůbec někdy po je uložen.

Každá z těchto scénářů přístupu k datům těží z vrstvy různý přístup, která je optimalizovaná pro určitý vzor přístupu. S horkou, studenou a archivní úrovni přístupu, objektů Blob v Azure storage adresy vyžadují DIFERENCOVANÝ přístup úrovně s odlišnými cenovými modely.

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Může pouze vrstvy data v úložišti objektů na horkou, studenou nebo archivní úrovně v úložišti objektů Blob a obecné účely v2 (GPv2) účty. Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Však můžou snadno převést existující účty úložiště GPv1 nebo Blob na účty GPv2 prostřednictvím jedním kliknutím na webu Azure Portal. Účty GPv2 poskytují novou cenovou strukturu pro objekty BLOB, soubory a fronty a přístup k různým dalším novým funkcím úložiště. Od této chvíle, budou kusy některé nové funkce a snížení cen nabízet pouze v účtech GPv2. Proto měli vyhodnotit používání účtů GPv2 ale používat pouze po kontrole cen za všechny služby. Některé úlohy můžou být v účtech GPv2 dražší než v účtech GPv1. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

BLOB storage a GPv2 zpřístupňují účty **úroveň přístupu** atribut na úrovni účtu. Tento atribut umožňuje určit výchozí úroveň přístupu jako horkou nebo studenou pro všechny objekty blob v účtu úložiště, který nemá explicitně nastavená na úrovni objektu úroveň. Na objekty s nastavenou úrovní na úrovni objektu se úroveň účtu nebude vztahovat. Archivní úrovni lze použít pouze na úrovni objektu. Můžete přepínat mezi úrovněmi přístupu kdykoli.

## <a name="premium-performance-block-blob-storage"></a>Úložiště objektů blob bloku výkon úrovně Premium

Premium výkonu blokovou službou blob storage zpřístupňuje často používaná data prostřednictvím vysoce výkonnému hardwaru. Data na této úrovni výkonu se ukládají na jednotky SSD (Solid-State Drive), které jsou optimalizovány pro konzistentní a nízkou latencí. Disky SSD poskytují vyšší míru transakční a propustnosti ve srovnání s tradičními pevné disky.

Premium výkonu blokovou službou blob storage je ideální pro úlohy vyžadující rychlé a konzistentní odezvy. Je nejvhodnější pro úlohy, které provádějí velký počet malých transakcí, jako je zachytávání telemetrická data, zasílání zpráv a transformace dat. Data, která zahrnuje koncovým uživatelům, jako je například interaktivní úpravy videa, statický webový obsah a online transakce jsou také vhodnými kandidáty.

Úložiště objektů blob bloku výkon úrovně Premium je dostupná jenom přes tento typ účtu úložiště objektů blob bloku a aktuálně nepodporuje výběr vrstvy na horkou, studenou nebo archivní úroveň přístupu.

## <a name="hot-access-tier"></a>Horká vrstva přístupu

Horká vrstva přístupu má vyšší náklady na uložení než studené a archivní úrovně, ale nejnižší náklady na přístup. Příklad scénáře použití pro horká vrstva přístupu patří:

- Data, která je používán aktivně nebo má často přistupovat (čtení a zápis).
- Data, která jsou určená ke zpracování a eventuální migraci na vrstvu přístupu cool.

## <a name="cool-access-tier"></a>Studená vrstva přístupu

Úroveň přístupu cool má nižší náklady na úložiště a porovnání s horkou vyšší náklady na přístup. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Příklad scénáře využití studené vrstvě přístupu patří:

- Krátkodobé zálohování a datové sady pro zotavení po havárii.
- Starší obsah a média, které se již nezobrazují často, ale které by však měly být na vyžádání okamžitě dostupné.
- Velké datové sady, které je potřeba levně uložit, zatímco se shromažďují další data pro budoucí zpracování. (*Příklady:* Dlouhodobé uložení vědeckých dat, nezpracovaná telemetrická data z výrobního závodu.)

## <a name="archive-access-tier"></a>Úroveň přístupu Archive

Archivní vrstva přístupu má nejnižší náklady na úložiště a vyšší náklady na načtení dat v porovnání s horkou a studenou úrovní. Tato úroveň je určená pro data, u kterých se toleruje latence načtení několik hodin a která zůstanou v archivní vrstvě nejméně 180 dnů.

Když je objekt blob v archivním úložišti, jsou data objektu blob je offline a nejde přečíst, zkopírovaný, přepsat či jinak změněn. Nelze pořizovat snímky objektů blob v archivním úložišti. Metadata objektu blob ale zůstává online a dostupná, umožňuje výpis objektu blob a jeho vlastnosti. Jediné platné operace pro objekty BLOB v archivní úrovni jsou GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier a DeleteBlob.

Příklad scénáře použití pro archivní úroveň přístupu patří:

- dlouhodobé zálohování, sekundární zálohování a archivní datové sady
- Původní (hrubá, nezpracovaná) data, která je potřeba zachovat i po jejich zpracování do konečné, použitelné podoby. (*Příklad:* Původní multimediální záznamy po překódování do jiných formátů.)
- Data pro soulad a archivaci, které je potřeba uchovat po dlouhou dobu a téměř se k nim nepřistupuje. (*Například*, bezpečnostních kamer, staré X-rentgenové snímky/snímky magnetické rezonance zařízení pro zdravotnická, zvukové záznamy a přepisy zákaznických hovorů pro finanční služby)

### <a name="blob-rehydration"></a>Dosazování objektů blob

Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces se označuje jako dosazování a jeho dokončení může trvat až 15 hodin. Velké objekty BLOB se doporučují pro zajištění optimálního výkonu. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit.

Během dosazování můžete zkontrolovat vlastnost **Stav archivu** objektu blob a ověřit, jestli se úroveň změnila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost **Access Tier** objektu blob odpovídá nové horké nebo studené úrovni.  

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Ovládání datových vrstev na úrovni objektů blob umožňuje změnit vrstvu dat na úrovni objektu pomocí jediné operace s názvem [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Současně se změnou vzorů využití můžete pro objekt blob snadno změnit vrstvu přístupu (studená, horká nebo archivní) a nemusíte přitom přesouvat data mezi účty. Všechny změny vrstvy dojde okamžitě. Dosazování objektu blob z archivní úrovně může trvat několik hodin.

Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. Pokud je objekt blob v archivní úrovni, ho nelze přepsat, aby nahrávání stejný objekt blob není povolena v tomto scénáři. Můžete přepsat objektů blob v horké nebo studené úrovně, ve kterém případě nový objekt blob zdědí úroveň objektu blob, který se přepsal.

Objekty BLOB ve všech úrovních tři přístup mohou existovat vedle sebe ve stejném účtu. Každý objekt blob, který nemá explicitně přiřazenou vrstvu, odvozuje nastavení vrstvy přístupu z účtu. Pokud je úroveň přístupu odvozuje z účtu, zobrazí se **Access Tier Inferred** blob nastavenou na hodnotu "true" a objektu blob **úroveň přístupu** vlastností objektu blob odpovídá úrovni účtu. Na webu Azure Portal, odvození úrovně přístupu, zobrazí se vlastnosti s úrovní přístupu objektu blob (například **horká (odvozeno)** nebo **studená (odvozeno)** ).

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku. Nejde také změnit úroveň objektu blob bloku, který má snímky.

> [!NOTE]
> Data uložená v účtu úložiště objektů blob bloku nemůže být rozvrstvena aktuálně horká, studená nebo archivní pomocí [Set Blob Tier](/rest/api/storageservices/set-blob-tier) nebo pomocí správy životního cyklu úložiště objektů Blob v Azure.
> Pro přesun dat, musíte synchronně zkopírovat objekty BLOB z účtu úložiště objektů blob bloku do horká vrstva přístupu do jiného účtu pomocí [Vložit blok z adresy URL rozhraní API](/rest/api/storageservices/put-block-from-url) nebo verzi AzCopy, který podporuje toto rozhraní API.
> *Vložit blok z adresy URL* rozhraní API synchronně zkopíruje data na serveru, což znamená dokončení volání pouze jednou všechna data se přesunou z původního umístění serveru do cílového umístění.

### <a name="blob-lifecycle-management"></a>Správa životního cyklu objektu BLOB

Správa životního cyklu úložiště objektů BLOB nabízí bohatě vybaveným a podle pravidel zásad, který vám pomůže se převést vaše data, aby nejlepší úroveň přístupu a vypršení platnosti dat na konci svého životního cyklu. Zobrazit [Správa životního cyklu úložiště objektů Blob v Azure](storage-lifecycle-management-concepts.md) Další informace.  

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když se objekt blob přesune do chladnější úrovně (horká -> studená, horká -> archivní nebo studená -> archivní), operace se fakturuje jako operace zápisu do cílové vrstvy, kde účtují operace zápisu (za 10 000) a poplatky za data zápisu (za GB) pro cílovou úroveň.

Když se objekt blob přesune do teplejší úrovně (archive -> studená, archivní -> horká nebo studená -> horká), operace se fakturuje jako čtení ze zdrojové vrstvy, které platí operace čtení (za 10 000) a načítání (za GB) dat pro zdrojovou vrstvu. Následující tabulka shrnuje, jak se účtují změny úrovně.

| | **Zápis poplatky (operace + přístup)** | **Poplatky za čtení (operace + přístup)**
| ---- | ----- | ----- |
| **Směr SetBlobTier** | Horká -> studená, horká -> archivní, cool -> archive | archivní -> studená, archivní -> horká, studená -> horká

Pokud přepnete úroveň účtu z horké na studenou, budou se vám účtovat operace zápisu (za 10 000) za všechny objekty blob bez nastavené úrovně pouze v účtech GPv2. Neplatí žádné poplatky pro tuto změnu v účtech Blob storage. Pokud přepnete svůj účet Blob Storage nebo GPv2 z horké na studenou úroveň, budou se vám účtovat operace čtení (za 10 000) i načítání dat (za GB). Můžou se účtovat také poplatky za předčasné odstranění objektů blob přesunutých ze studené nebo archivní úrovně.

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Kromě měsíčních poplatků za GB se na všechny objekty blob přesunuté do studené úrovně (pouze účty GPv2) vztahuje období předčasného odstranění ze studené úrovně v délce 30 dnů, a na všechny objekty blob přesunuté do archivní úrovně se vztahuje období předčasného odstranění z archivní úrovně v délce 180 dnů. Tento poplatek se účtuje poměrnou částí. Pokud se například objekt blob přesune do archivní úrovně a po 45 dnech pak odstraní nebo přesune do horké úrovně, bude se vám účtovat poplatek za předčasné odstranění, který odpovídá 135 (180 − 45) dnům uložení tohoto objektu blob v archivní úrovni.

Předčasné odstranění může vypočítat pomocí vlastnosti objektu blob **čas vytvoření**, pokud nepřichází žádný přístup změny úrovně. Jinak můžete použít při úrovně přístupu byla naposledy upravena na studené nebo archivní zobrazením vlastností objektu blob: **přístup úroveň změnit čas**. Další informace o vlastnosti objektu blob najdete v tématu [získat vlastnosti objektu Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porovnání možností úložiště objektů blob bloku

Následující tabulce najdete porovnání premium výkonu blokovou službou blob storage a horké, studené a archivní úroveň přístupu.

|                                           | **Výkon úrovně Premium.** | **Horká úroveň** | **Studená vrstva přístupu** | **Archivní vrstva**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Dostupnost**                          | 99,9 %            | 99,9 %        | 99 % | neuvedeno |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**  | neuvedeno              | 99,99 %       | 99,9 % | neuvedeno |
| **Poplatky za využití**                         | Vyšší náklady na úložiště, nižší přístup a transakční náklady | Vyšší náklady na úložiště, nižší přístup a transakce | Nižší náklady na úložiště, vyšší přístup a transakce | Nejnižší náklady na úložiště, nejvyšší přístup a transakce |
| **Minimální velikost objektu**                   | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| **Minimální doba uložení**              | neuvedeno | neuvedeno | 30 dnů (pouze GPv2) | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | Řádu milisekund | milisekundy | milisekundy | < 15 hodin

> [!NOTE]
> Škálovatelnost a výkonnostní cíle, naleznete v tématu [škálovatelnost a výkonnostní cíle Azure Storage pro účty úložiště](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V tomto oddílu jsou předvedené následující scénáře s využitím webu Azure Portal:

- Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage.
- Změna úrovně objektu blob v účtu GPv2 nebo Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Klikněte na možnost Všechny prostředky a kliknutím na účet úložiště do účtu přejděte.

1. V okně Nastavení klikněte na **Konfigurace**, odkud můžete zobrazit nebo změnit konfiguraci účtu.

1. Vyberte úroveň přístupu pro vaše potřeby: Nastavte **úroveň přístupu** buď **Cool** nebo **Hot**.

1. V horní části okna klikněte na **Uložit**.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změna úrovně objektu blob v účtu GPv2 nebo Blob storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Chcete-li přejít na objekt blob ve vašem účtu úložiště, vyberte **všechny prostředky**, vyberte svůj účet úložiště, vyberte kontejner a pak vyberte objekt blob.

1. V **vlastnosti objektu Blob** okno, vyberte **úroveň přístupu** rozevírací nabídce vyberte **Hot**, **Cool**, nebo **archivu**  úroveň přístupu.

1. V horní části okna klikněte na **Uložit**.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny účty úložiště používají cenový model pro úložiště objektů Blob založeného na úrovních jednotlivých objektů blob. Mějte na paměti následující aspekty fakturace:

- **Náklady na úložiště**: Kromě objemu uložených dat náklady na ukládání dat se liší podle úrovně přístupu. Pokud je úroveň chladnější, cena za gigabajt se snižuje.
- **Cena za přístup**: Přístup k datům za úroveň chladnější. Pro data ve studené a archivní úroveň přístupu bude se vám účtovat poplatek za GB dat přístup pro čtení.
- **Cena za transakce**: Se účtuje poplatek za transakce pro všechny úrovně, které zvýší úroveň chladnější.
- **Cena za přenosy dat geografické replikace**: Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.
- **Cena za přenosy odchozích dat**: Přenosy odchozích dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné využití šířky pásma na základě sazby za gigabajt konzistentní s účty úložiště pro obecné účely.
- **Změna úrovně přístupu**: Změna úrovně přístupu účtu ze studené na horkou je zpoplatněna částkou, která odpovídá přečtení všech dat v aktuálním účtu úložiště. Ale změna úrovně přístupu účtu z horké na studenou je zpoplatněna částkou, která odpovídá zápisu všech dat do studené vrstvy (pouze účty GPv2).

> [!NOTE]
> Další informace o cenách pro účty Blob storage, najdete v části [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) stránky. Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Nejčastější dotazy

**Pokud chci vrstvit svá data, mám použít účet Blob Storage nebo GPv2?**

Pro účely vrstvení doporučujeme místo účtů Blob Storage používat účty GPv2. Účty GPv2 podporují všechny funkce jako účty Blob Storage a navíc řadu dalších. Ceny účtů Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen budou k dispozici pouze v účtech GPv2. Účty GPv1 vrstvení nepodporují.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

**Můžete ukládat objekty ve všech třech (horká, studená a archivní) úrovních v rámci stejného účtu přístupu?**

Ano. Atribut **Access Tier** nastavený na úrovni účtu představuje výchozí úroveň, která se použije pro všechny objekty v příslušném účtu, které nemají explicitně nastavenou úroveň. Ovládání datových vrstev na úrovni objektů blob však umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaké je nastavení úrovně přístupu v příslušném účtu. Objekty BLOB ve všech tří přístup úrovně (horká, studená nebo archivní) může existovat ve stejném účtu.

**Můžete změnit výchozí úrovně přístupu u účtu GPv2 nebo Blob storage?**

Ano, můžete změnit výchozí úroveň přístupu tak, že nastavíte **úroveň přístupu** atribut v účtu úložiště. Změna úrovně přístupu se projeví u všech objektů uložených v účtu, které nemají explicitně nastavenou úroveň. Při přepínání úroveň přístupu můžete z horké na studenou vznikají náklady operace zápisu (za 10 000) pro všechny objekty BLOB bez nastavené úrovně v účtech GPv2 a při přepínání ze studené na horkou s sebou nese náklady operace čtení (za 10 000) a poplatky za načítání dat (za GB) pro všechny objekty BLOB v úložišti objektů Blob a účty GPv2.

**Můžu u účtu nastavit výchozí úroveň přístupu na archivní?**

Ne. Horká a studená vrstva přístupu může nastavit pouze jako výchozí úrovně přístupu u účtu. Archivní úroveň je možné nastavit pouze na úrovni objektu.

**Ve které oblasti jsou horké, studené a archivní úroveň přístupu k dispozici v?**

Úrovně přístupu hot a cool spolu s ovládání datových vrstev na úrovni objektů blob jsou dostupné ve všech oblastech. Úložiště archivu bude zpočátku dostupné pouze ve vybraných oblastech. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

**Objekty BLOB ve studené vrstvě přístupu chovat jinak než v horká vrstva přístupu?**

Objekty BLOB v horká vrstva přístupu mají stejnou latenci jako objekty BLOB v účtech GPv1, GPv2 a Blob storage. Objekty BLOB ve studené vrstvě přístupu mají podobnou latenci (v milisekundách) jako objekty BLOB v účtech GPv1, GPv2 a Blob storage. Objekty BLOB v archivní úroveň přístupu mají latenci několik hodin v účtech GPv1, GPv2 a Blob storage.

Objekty BLOB ve studené vrstvě přístupu mají trochu nižší dostupnost služby (SLA) než objekty BLOB uložené v horká vrstva přístupu. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Jsou operace mezi horkou, studenou a archivní úrovní stejné?**

Ano. Všechny operace mezi horkou a studenou úrovní jsou 100% konzistentní. Všechny platné operace archivní úrovně, včetně odstranění, výpisu, získání vlastností nebo metadat objektu blob a nastavení úrovně objektu blob jsou 100% konzistentní mezi horkou a studenou úrovní. Objekt blob není možné načíst ani upravit, dokud je v archivní úrovni.

**Jak se dozvím o dokončení dosazování objektu blob z archivní úrovně do horké nebo studené úrovně?**

Během dosazování můžete ověřit dokončení změny úrovně dotazováním atributu **Stav archivu** pomocí operace získání vlastností objektu blob. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost **Access Tier** objektu blob odpovídá nové horké nebo studené úrovni.  

**Kdy se mi po nastavení úrovně objektu blob začne účtovat podle odpovídající sazby?**

Každý objekt blob se vždy účtuje podle úrovně, kterou indikuje jeho **úroveň přístupu** vlastnost. Když nastavíte novou úroveň pro objekt blob **úroveň přístupu** vlastnost okamžitě odpovídá na nové úrovni pro všechny přechody. Dosazování objektu blob z archivní úrovně na horkou nebo studenou úroveň ale může trvat několik hodin. V takovém případě se účtují sazbami archivu až do dokončení, v tomto okamžiku **úroveň přístupu** vlastnost odráží novou úroveň. V tomto okamžiku se účtují tohoto objektu blob na horkou nebo studenou rychlost.

**Jak zjistím, jestli se mi bude účtovat poplatek za předčasné odstranění, když odstraním nebo přesunu objekt blob ze studené nebo archivní úrovně?**

Poplatek za předčasné odstranění se bude účtovat za každý objekt blob, který se odstraní nebo přesune ze studené (pouze účty GPv2) úrovně před uplynutím 30 dnů nebo z archivní úrovně před uplynutím 180 dnů. Můžete určit, jak dlouho objektu blob byla ve studené nebo archivní úrovně tak, že zkontrolujete **Access Tier Change Time** vlastnost, která obsahuje časové razítko poslední změny úrovně objektu blob. Další informace najdete v tématu [předčasné odstranění ze studené a archivní](#cool-and-archive-early-deletion).

**Které nástroje a sady SDK Azure podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu?**

Ovládání datových vrstev na úrovni objektů blob a úložiště archivu podporují nástroje pro Azure Portal, PowerShell a rozhraní příkazového řádku a klientské knihovny .NET, Java, Python a Node.js.  

**Kolik dat můžu ukládat v horké, studené a archivní úrovni?**

Úložiště dat společně s dalšími limity se nastavují na úrovni účtu a ne podle úrovně přístupu. Proto můžete použít celý limit v jedné úrovni nebo napříč všemi třemi úrovněmi. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Další postup

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Vyhodnocení horké, studené a archivní úrovně v účtech GPv2 a Blob Storage

[Ověření dostupnosti horké, studené a archivní úrovně v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)

[Správa životního cyklu úložiště objektů Blob v Azure](storage-lifecycle-management-concepts.md)

[Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)

[Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)

[Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
