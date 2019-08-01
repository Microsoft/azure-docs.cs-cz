---
title: Horké, studené a archivní úrovně přístupu pro objekty blob – Azure Storage
description: Horká, studená a archivní úroveň přístupu pro účty Azure Storage.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.reviewer: clausjor
ms.subservice: blobs
ms.openlocfilehash: dcfec25cd2bf01d59a7c0b3f72d8b1a618a9f699
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565993"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: horká, studená a archivní úroveň přístupu

Služba Azure Storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů BLOB nejefektivnějším způsobem. Dostupné úrovně přístupu zahrnují:

- **Horká** – optimalizovaná pro ukládání dat, ke kterým dochází často.
- **Studená** – optimalizovaná pro ukládání dat, která se nepoužívají zřídka a ukládají se aspoň na 30 dní.
- **Archivní** – optimalizované pro ukládání dat, která se zřídka používají a ukládají se nejméně 180 dní s požadavky flexibilní latence (v řádu hodin).

U různých úrovní přístupu platí následující požadavky:

- Úroveň přístupu archivu je k dispozici pouze na úrovni objektu blob, nikoli na úrovni účtu úložiště.
- Data ve studené úrovni přístupu můžou tolerovat mírně nižší dostupnost, ale stále vyžadují vysokou odolnost a podobné vlastnosti pro přístup k datům a propustnost jako aktivní data. U studených dat jsou pro nižší náklady na úložiště přijatelné kompromisy smlouvy o úrovni služeb (SLA) a vyšších nákladů na přístup v porovnání s horkými daty.
- Úložiště archivu je offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup.
- Na úrovni účtu se dá nastavit jenom horká a studená úroveň přístupu.
- Horké, studené a archivní úrovně lze nastavit na úrovni objektu.

Data uložená v cloudu se rozšiřují exponenciálním tempem. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se můžou lišit podle toho, jak se generují, zpracovávají a přistupovaly během své životnosti. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data zůstávají v cloudu nečinná a v případě potřeby jsou po uložení k dispozici zřídka.

Každý z těchto scénářů přístupu k datům je výhodou z jiné úrovně přístupu, která je optimalizována pro konkrétní vzor přístupu. Díky horké, studené a archivní úrovni přístupu Azure Blob Storage tuto potřebu vyžaduje pro rozdílné úrovně přístupu s oddělenými cenovými modely.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Data úložiště objektů můžete vrstvit na horkou, studenou nebo archivní úroveň v účtech BLOB Storage a Pro obecné účely v2 (GPv2). Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Stávající účty GPv1 nebo BLOB Storage ale můžete snadno převést na účty GPv2 prostřednictvím procesu jedním kliknutím v Azure Portal. GPv2 poskytuje novou cenovou strukturu pro objekty blob, soubory a fronty a přístup k řadě dalších nových funkcí úložiště. Dál, některé nové funkce a snížení cen se budou nabízet jenom v účtech GPv2. Proto byste měli hodnotit pomocí účtů GPv2, ale používat je jenom po kontrole cen pro všechny služby. Některé úlohy můžou být dražší na GPv2 než GPv1. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Účty BLOB Storage a GPv2 zpřístupňují atribut **úroveň přístupu** na úrovni účtu. Tento atribut umožňuje určit výchozí úroveň přístupu jako horkou nebo studenou pro všechny objekty BLOB v účtu úložiště, které nemají explicitně nastavenou úroveň na úrovni objektu. Na objekty s nastavenou úrovní na úrovni objektu se úroveň účtu nebude vztahovat. Archivní úroveň je možné použít pouze na úrovni objektu. Mezi těmito úrovněmi přístupu můžete kdykoli přepínat.

## <a name="premium-performance-block-blob-storage"></a>Úložiště objektů blob bloku úrovně Premium

Úložiště objektů blob bloku úrovně Premium zpřístupňuje často dostupná data prostřednictvím vysoce výkonného hardwaru. Data v této úrovni výkonu jsou uložena na jednotkách SSD (Solid-State Drive), která jsou optimalizována pro nízkou a konzistentní latenci. SSD poskytují vyšší transakční míry a propustnost v porovnání s tradičními pevnými disky.

Úložiště objektů blob bloku Premium je ideální pro úlohy, které vyžadují rychlou a konzistentní dobu odezvy. Je nejvhodnější pro úlohy, které provádějí mnoho malých transakcí, jako je například zachycení dat telemetrie, zasílání zpráv a transformace dat. Data, která zahrnují koncové uživatele, jako jsou interaktivní úpravy videa, statický webový obsah a online transakce, jsou také dobrými kandidáty.

Úložiště objektů blob bloku Premium je dostupné jenom přes typ účtu úložiště objektů blob bloku a v současné době nepodporuje vrstvení na horkou, studenou nebo archivní úroveň přístupu.

## <a name="hot-access-tier"></a>Horká vrstva přístupu

Úroveň Hot Access má vyšší náklady na úložiště než studená a archivní úroveň, ale nejnižší náklady na přístup. Mezi příklady scénářů použití pro vrstvu Hot Access patří:

- Data, která jsou v aktivním použití nebo se očekávají pro přístup k častým datům (ke čtení a zápisu).
- Data, která jsou připravená pro zpracování a případné migrace na studenou úroveň přístupu.

## <a name="cool-access-tier"></a>Studená vrstva přístupu

Studená úroveň přístupu má nižší náklady na úložiště a vyšší náklady na přístup v porovnání s horkým úložištěm. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Mezi příklady scénářů použití pro studenou úroveň přístupu patří:

- Krátkodobé zálohování a datové sady pro zotavení po havárii.
- Starší obsah a média, které se již nezobrazují často, ale které by však měly být na vyžádání okamžitě dostupné.
- Velké datové sady, které je potřeba levně uložit, zatímco se shromažďují další data pro budoucí zpracování. (*Příklady:* Dlouhodobé uložení vědeckých dat, nezpracovaná telemetrická data z výrobního závodu.)

## <a name="archive-access-tier"></a>Úroveň přístupu Archive

Úroveň přístupu archivu má nejnižší náklady na úložiště a vyšší náklady na načtení dat ve srovnání s horkou a studenou úrovní. Tato úroveň je určená pro data, u kterých se toleruje latence načtení několik hodin a která zůstanou v archivní vrstvě nejméně 180 dnů.

Když je objekt BLOB v archivním úložišti, data objektů BLOB jsou offline a nelze je číst, kopírovat, přepisování ani upravovat. Nemůžete pořizovat snímky objektu BLOB v archivním úložišti. Metadata objektu BLOB zůstávají ale online a k dispozici, což vám umožní zobrazit seznam objektů BLOB a jejích vlastností. V případě objektů BLOB v archivu jsou jedinými platnými operacemi GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier a DeleteBlob.

Mezi scénáře použití archivní úrovně přístupu patří:

- dlouhodobé zálohování, sekundární zálohování a archivní datové sady
- Původní (hrubá, nezpracovaná) data, která je potřeba zachovat i po jejich zpracování do konečné, použitelné podoby. (*Příklad:* Původní multimediální záznamy po překódování do jiných formátů.)
- Data pro soulad a archivaci, které je potřeba uchovat po dlouhou dobu a téměř se k nim nepřistupuje. (*Například*bezpečnostní kamera záběry, staré X paprsky/snímky magnetické rezonance pro zdravotní péči, zvukové nahrávky a Přepisy zákaznických hovorů pro finanční služby)

### <a name="blob-rehydration"></a>Dosazování objektů blob

Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces se označuje jako dosazování a jeho dokončení může trvat až 15 hodin. Pro zajištění optimálního výkonu se doporučují velké velikosti objektů BLOB. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit.

Během dosazování můžete zkontrolovat vlastnost **Stav archivu** objektu blob a ověřit, jestli se úroveň změnila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost **Access Tier** objektu blob odpovídá nové horké nebo studené úrovni.  

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Ovládání datových vrstev na úrovni objektů blob umožňuje změnit vrstvu dat na úrovni objektu pomocí jediné operace s názvem [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Současně se změnou vzorů využití můžete pro objekt blob snadno změnit vrstvu přístupu (studená, horká nebo archivní) a nemusíte přitom přesouvat data mezi účty. Všechny změny úrovně se nastávají hned. Dosazování objektu BLOB z archivu ale může trvat několik hodin.

Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. Pokud je objekt BLOB v archivní úrovni, nejde ho přepsat, takže odeslání stejného objektu BLOB není v tomto scénáři povolené. Objekt blob můžete přepsat na horké nebo studené úrovni. v takovém případě nový objekt BLOB zdědí vrstvu objektu blob, který se přepsal.

Objekty blob ve všech třech úrovních přístupu můžou existovat v rámci jednoho účtu. Každý objekt blob, který nemá explicitně přiřazenou vrstvu, odvozuje nastavení vrstvy přístupu z účtu. Pokud se úroveň přístupu odvozuje z účtu, zobrazí se vlastnost odvozená vlastnost objektu BLOB **vrstvy přístupu** nastavená na hodnotu "true" a vlastnost BLOB **vrstvy přístupu** objektu BLOB odpovídá úrovni účtu. V Azure Portal se vlastnost invoditd úrovně přístupu zobrazuje s úrovní přístupu objektu BLOB (například **Hot (odvoditelné)** nebo **studená (odvozená)** ).

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku. Nejde také změnit úroveň objektu blob bloku, který má snímky.

> [!NOTE]
> Data uložená v účtu úložiště objektů blob bloku nejde v současné době rozvrstvit na horkou, studenou nebo archivní pomocí [nastavené úrovně objektu BLOB](/rest/api/storageservices/set-blob-tier) nebo pomocí správy životního cyklu Azure Blob Storage.
> Chcete-li přesunout data, je nutné synchronně zkopírovat objekty BLOB z účtu bloku BLOB Storage do vrstvy Hot Access v jiném účtu pomocí [bloku Put z URL API](/rest/api/storageservices/put-block-from-url) nebo verze AzCopy, která podporuje toto rozhraní API.
> *Blok Put z* rozhraní API URL synchronně kopíruje data na serveru, což znamená, že se volání dokončí až po přesunu všech dat z původního umístění serveru do cílového umístění.

### <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů BLOB

Blob Storage Správa životního cyklu nabízí bohatou zásadu založenou na pravidlech, kterou můžete použít k převodu dat do nejvyšší úrovně přístupu a vypršení platnosti dat na konci jejího životního cyklu. Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md) .  

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když se objekt BLOB přesune do úrovně chladicího objektu (Hot-> studená, archivní > nebo archivní >), operace se fakturuje jako operace zápisu do cílové úrovně, kde se platí poplatky za operace zápisu (za 10 000) a zápis dat (za GB) pro cílovou úroveň.

Když se objekt BLOB přesune do teplé úrovně (archivní > studenou, archivní > horkou nebo studenou > horkou), operace se fakturuje jako přečtená ze zdrojové vrstvy, kde se účtují poplatky za operace čtení (za 10 000) a načtení dat (za GB) pro zdrojovou úroveň. Následující tabulka shrnuje, jak se účtují změny vrstvy.

| | **Poplatky za zápis (operace + přístup)** | **Poplatky za čtení (operace + přístup)**
| ---- | ----- | ----- |
| **Směr SetBlobTier** | horká > studená, horká > archiv, studená > Archiv | Archiv – > studenou, archivní > horkou, studenou > horkou

Pokud přepnete úroveň účtu z horké na studenou, budou se vám účtovat operace zápisu (za 10 000) za všechny objekty blob bez nastavené úrovně pouze v účtech GPv2. Tato změna v účtech BLOB Storage se neúčtuje. Pokud přepnete svůj účet Blob Storage nebo GPv2 z horké na studenou úroveň, budou se vám účtovat operace čtení (za 10 000) i načítání dat (za GB). Můžou se účtovat také poplatky za předčasné odstranění objektů blob přesunutých ze studené nebo archivní úrovně.

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Kromě měsíčních poplatků za GB se na všechny objekty blob přesunuté do studené úrovně (pouze účty GPv2) vztahuje období předčasného odstranění ze studené úrovně v délce 30 dnů, a na všechny objekty blob přesunuté do archivní úrovně se vztahuje období předčasného odstranění z archivní úrovně v délce 180 dnů. Tento poplatek se účtuje poměrnou částí. Pokud se například objekt blob přesune do archivní úrovně a po 45 dnech pak odstraní nebo přesune do horké úrovně, bude se vám účtovat poplatek za předčasné odstranění, který odpovídá 135 (180 − 45) dnům uložení tohoto objektu blob v archivní úrovni.

Předčasné odstranění můžete vypočítat pomocí vlastnosti objektu blob, **času vytvoření**, pokud se nezměnily žádné změny úrovně přístupu. V opačném případě můžete použít, když se úroveň přístupu naposledy změnila na studenou nebo archivní, zobrazením vlastnosti objektu BLOB: **přístup-vrstva-doba změny**. Další informace o vlastnostech objektu BLOB najdete v tématu [získání vlastností objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porovnávání možností úložiště objektů blob bloku

V následující tabulce jsou popsány porovnání úložiště objektů blob bloku Premium a horké, studené a archivní úrovně přístupu.

|                                           | **Výkon úrovně Premium** | **Horká vrstva** | **Studená vrstva** | **Úroveň archivu**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Dostupnost**                          | 99,9 %            | 99,9 %        | 99 % | Není k dispozici |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**  | Není k dispozici              | 99,99 %       | 99,9 % | Není k dispozici |
| **Poplatky za využití**                         | Vyšší náklady na úložiště, nižší náklady na přístup a transakce | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Snížení nákladů na úložiště, vyššího přístupu a transakčních nákladů | Nejnižší náklady na úložiště, nejvyšší přístup a náklady na transakce |
| **Minimální velikost objektu**                   | Není k dispozici | Není k dispozici | Není k dispozici | Není k dispozici |
| **Minimální doba uložení**              | Není k dispozici | Není k dispozici | 30 dnů (pouze GPv2) | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | Jednociferné číslo milisekund | milisekundy | milisekundy | < 15 hodin

> [!NOTE]
> Cíle škálovatelnosti a výkonu najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle pro účty úložiště](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V tomto oddílu jsou předvedené následující scénáře s využitím webu Azure Portal:

- Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage.
- Změna úrovně objektu blob v účtu GPv2 nebo Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Klikněte na možnost Všechny prostředky a kliknutím na účet úložiště do účtu přejděte.

1. V okně Nastavení klikněte na **Konfigurace**, odkud můžete zobrazit nebo změnit konfiguraci účtu.

1. Vyberte správnou úroveň přístupu podle vašich potřeb: Nastavte **úroveň přístupu** buď na **studenou** , nebo na **horkou**.

1. V horní části okna klikněte na **Uložit**.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změna úrovně objektu BLOB v účtu GPv2 nebo BLOB Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Pokud chcete přejít na objekt BLOB ve vašem účtu úložiště, vyberte **všechny prostředky**, vyberte svůj účet úložiště, vyberte svůj kontejner a pak vyberte svůj objekt BLOB.

1. V okně **vlastností objektu BLOB** vyberte rozevírací nabídku **úroveň přístupu** a vyberte úroveň přístupu **Hot**, **studená**nebo **archivní** .

1. V horní části okna klikněte na **Uložit**.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny účty úložiště používají cenový model pro úložiště objektů blob na základě úrovně jednotlivých objektů BLOB. Pamatujte na následující poznámky k fakturaci:

- **Náklady na úložiště**: Kromě množství uložených dat se cena za ukládání dat liší v závislosti na úrovni přístupu. Pokud je úroveň chladnější, cena za gigabajt se snižuje.
- **Náklady na přístup k datům**: Poplatky za přístup k datům se zvyšují, protože se úroveň nedostává. Pro data ve studené a archivní úrovni přístupu se účtují poplatky za přístup k datům za GB.
- **Náklady transakce**: Pro všechny úrovně, které se zvyšují jako nenáročné, se účtuje poplatek za transakci.
- **Náklady na přenos dat geografické replikace**: Tento poplatek se vztahuje jenom na účty s nakonfigurovanou geografickou replikací, včetně GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.
- **Náklady na přenos odchozích dat**: Přenosy odchozích dat (data přenesená mimo oblast Azure) účtují náklady na využití šířky pásma po gigabajtech, stejně jako v případě účtů úložiště pro obecné účely.
- **Změna úrovně přístupu**: Změna úrovně přístupu k účtu ze studené na horkou má poplatek, který se rovná čtení všech dat existujících v účtu úložiště. Změna úrovně přístupu k účtu z horké na studenou se ale účtuje za poplatek, který se rovná zápisu všech dat do studené úrovně (pouze účty GPv2).

> [!NOTE]
> Další informace o cenách pro účty BLOB Storage najdete na stránce s [cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/) . Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Nejčastější dotazy

**Pokud chci vrstvit svá data, mám použít účet Blob Storage nebo GPv2?**

Pro účely vrstvení doporučujeme místo účtů Blob Storage používat účty GPv2. Účty GPv2 podporují všechny funkce jako účty Blob Storage a navíc řadu dalších. Ceny účtů Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen budou k dispozici pouze v účtech GPv2. Účty GPv1 vrstvení nepodporují.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

**Můžu ve stejném účtu ukládat objekty ve všech třech (horká, studená a archivní) úrovních?**

Ano. Atribut **Access Tier** nastavený na úrovni účtu představuje výchozí úroveň, která se použije pro všechny objekty v příslušném účtu, které nemají explicitně nastavenou úroveň. Ovládání datových vrstev na úrovni objektů blob však umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaké je nastavení úrovně přístupu v příslušném účtu. V rámci stejného účtu můžou existovat objekty BLOB v některé ze tří úrovní přístupu (horká, studená nebo archivní).

**Můžu změnit výchozí úroveň přístupu účtu úložiště BLOB nebo GPv2?**

Ano, výchozí úroveň přístupu můžete změnit nastavením atributu **úroveň přístupu** v účtu úložiště. Změna úrovně přístupu se projeví u všech objektů uložených v účtu, které nemají explicitně nastavenou úroveň. Přepínání úrovně přístupu z horké na studenou má za následek operace zápisu (za 10 000) pro všechny objekty blob bez nastavené úrovně v účtech GPv2 a přepínání ze studené na horkou (za GB) – poplatky za operace čtení (za 10 000) i načítání dat (za GB) pro všechny objekty BLOB v úložišti objektů BLOB. a účty GPv2.

**Můžu u účtu nastavit výchozí úroveň přístupu na archivní?**

Ne. Jako výchozí úroveň přístupu se dá nastavit jenom horká a studená úroveň přístupu. Archivní úroveň je možné nastavit pouze na úrovni objektu.

**Ve kterých oblastech jsou horké, studené a archivní úrovně přístupu dostupné v?**

Horká a studená úroveň přístupu spolu s ovládáním datových vrstev na úrovni objektů BLOB jsou dostupná ve všech oblastech. Úložiště archivu bude zpočátku dostupné pouze ve vybraných oblastech. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

**Chovají se objekty blob ve studené úrovni přístupu jinak než ty, které jsou v úrovni Hot Accessu?**

Objekty BLOB v úrovni Hot Access mají stejnou latenci jako objekty BLOB v účtech GPv1, GPv2 a BLOB Storage. Objekty blob ve studené úrovni přístupu mají podobnou latenci (v milisekundách) jako objekty BLOB v účtech GPv1, GPv2 a BLOB Storage. Objekty BLOB v archivní úrovni mají několik hodin latence v účtech GPv1, GPv2 a BLOB Storage.

Objekty blob ve studené úrovni přístupu mají mírně nižší dostupnost služby (SLA) než objekty blob uložené v úrovni Hot Accessu. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Jsou operace mezi horkou, studenou a archivní úrovní stejné?**

Ano. Všechny operace mezi horkou a studenou úrovní jsou 100% konzistentní. Všechny platné operace archivní úrovně, včetně odstranění, výpisu, získání vlastností nebo metadat objektu blob a nastavení úrovně objektu blob jsou 100% konzistentní mezi horkou a studenou úrovní. Objekt blob není možné načíst ani upravit, dokud je v archivní úrovni.

**Jak se dozvím o dokončení dosazování objektu blob z archivní úrovně do horké nebo studené úrovně?**

Během dosazování můžete ověřit dokončení změny úrovně dotazováním atributu **Stav archivu** pomocí operace získání vlastností objektu blob. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost **Access Tier** objektu blob odpovídá nové horké nebo studené úrovni.  

**Kdy se mi po nastavení úrovně objektu blob začne účtovat podle odpovídající sazby?**

Každý objekt BLOB se vždycky účtuje podle úrovně označené vlastností **úroveň přístupu** objektu BLOB. Když nastavíte novou úroveň pro objekt blob, vlastnost **úroveň přístupu** okamžitě odráží novou úroveň pro všechny přechody. Dosazování objektu blob z archivní úrovně na horkou nebo studenou úroveň ale může trvat několik hodin. V takovém případě se vám budou účtovat sazby za sekundu, dokud se nedokončí vypravení. v takovém případě vlastnost **úroveň přístupu** odráží novou úroveň. V tomto okamžiku se vám bude účtovat tento objekt blob na horkou nebo studenou sazbu.

**Jak zjistím, jestli se mi bude účtovat poplatek za předčasné odstranění, když odstraním nebo přesunu objekt blob ze studené nebo archivní úrovně?**

Poplatek za předčasné odstranění se bude účtovat za každý objekt blob, který se odstraní nebo přesune ze studené (pouze účty GPv2) úrovně před uplynutím 30 dnů nebo z archivní úrovně před uplynutím 180 dnů. To, jak dlouho je objekt BLOB ve studené nebo archivní úrovni, můžete zjistit tak, že zkontrolujete vlastnost objektu BLOB s **časem změny úrovně přístupu** , která poskytuje razítko poslední změny vrstvy. Další informace najdete v tématu [studená a archivní předčasné odstranění](#cool-and-archive-early-deletion).

**Které nástroje a sady SDK Azure podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu?**

Ovládání datových vrstev na úrovni objektů blob a úložiště archivu podporují nástroje pro Azure Portal, PowerShell a rozhraní příkazového řádku a klientské knihovny .NET, Java, Python a Node.js.  

**Kolik dat můžu ukládat v horké, studené a archivní úrovni?**

Úložiště dat spolu s dalšími omezeními se nastavuje na úrovni účtu a ne na úrovni přístupu. Proto se můžete rozhodnout použít celý limit v jedné vrstvě nebo ve všech třech vrstvách. Další informace najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Další postup

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Vyhodnocení horké, studené a archivní úrovně v účtech GPv2 a Blob Storage

[Ověření dostupnosti horké, studené a archivní úrovně v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)

[Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md)

[Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)

[Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)

[Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
