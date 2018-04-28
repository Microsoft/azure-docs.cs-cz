---
title: Horké, studené a archivní úložiště Azure pro objekty blob | Microsoft Docs
description: Horké, studené a archivní úložiště pro účty úložiště Azure.
services: storage
documentationcenter: ''
author: kuhussai
manager: jwillis
editor: ''
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: 21b09d9c428f9c29e0048faa32ce5349a127be89
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Azure Blob Storage: Horká, studená a archivní úroveň úložiště

## <a name="overview"></a>Přehled

Úložiště Azure nabízí tři úrovně úložiště pro ukládání objektů blob, abyste mohli data ukládat co nejhospodárněji – to znamená podle toho, jak je používáte. **Úroveň horkého úložiště** Azure je optimalizovaná pro ukládání dat, která se využívají často. **Studená úroveň úložiště** Azure je optimalizovaná pro ukládání dat, která se nevyužívají často a ukládají se nejméně na 30 dnů. **Archivní úroveň úložiště** Azure je optimalizovaná pro ukládání zřídka používaných dat, která se ukládají nejméně na 180 dnů a vyžadují flexibilní latenci (v řádu hodin). Úroveň úložiště Archive je k dispozici jenom na úrovni objektů blob, a ne na úrovni účtu úložiště. U dat ve studeném úložišti se toleruje horší dostupnost, ale přesto se u nich vyžaduje vysoká odolnost a podobná přístupová a přenosová rychlost jako u horkých dat. U studených dat jsou poplatky za uložení ve srovnání s horkými daty levnější, ovšem za cenu mírně horší dostupnosti a vyšších nákladů na přístup. Úložiště archivu je offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup. Na úrovni účtu se dá nastavit jen horká a studená úroveň úložiště (ne archiv). Všechny tři úrovně můžete nastavit na úrovni objektu.

Data uložená v cloudu dnes narůstají exponenciální rychlostí. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se liší tím, jak se generují, zpracovávají a jak se k nim přistupuje po celou dobu jejich životnosti. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data se po uložení v cloudu využívají zřídka, pokud vůbec někdy.

Pro každý z těchto scénářů přístupu k datům je vhodná jiná vrstva úložiště, která je optimalizovaná pro určitý vzor přístupu. Se zavedením horké, studené a archivní vrstvy úložiště služba Azure Blob Storage vychází vstříc potřebě různých úrovní úložiště s odlišnými cenovými modely.

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Data v úložišti objektů je možné vrstvit na horkou, studenou nebo archivní úroveň pouze v účtech Blob Storage nebo účtech pro obecné účely v2 (GPv2). Účty pro obecné účely v1 (GPv1) vrstvení nepodporují. Zákazníci však můžou snadno převést své stávající účty GPv1 nebo Blob Storage na účty GPv2 prostřednictvím prostého kliknutí na webu Azure Portal. Účty GPv2 poskytují novou cenovou strukturu pro objekty blob, soubory a fronty a také přístup k různým dalším novým funkcím úložiště. Kromě toho se postupně budou některé nové funkce a snížení cen nabízet pouze v účtech GPv2. Zákazníci by proto měli vyhodnotit používání účtů GPv2, ale začít je používat až po kontrole cen za všechny služby, protože některé úlohy můžou být v účtech GPv2 dražší než v účtech GPv1. Další informace najdete v tématu [Možnosti účtu úložiště Azure](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Účty Blob Storage a GPv2 zpřístupňují atribut **Access Tier** na úrovni účtu, který vám umožňuje určit výchozí úroveň úložiště jako horkou nebo studenou pro všechny objekty blob v účtu úložiště, které nemají nastavenou úroveň na úrovni objektu. Na objekty s nastavenou úrovní na úrovni objektu se úroveň účtu nebude vztahovat. Archivní úroveň je možné použít pouze na úrovni objektu. Mezi těmito úrovněmi úložiště můžete kdykoli přepínat.

## <a name="hot-access-tier"></a>Horká vrstva přístupu

Horké úložiště má vyšší náklady na uložení než studené a archivní úložiště, ale má nejnižší náklady na přístup. Na úrovni horkého úložiště můžete mít třeba tyhle typy dat:

* Data, která se aktivně používají nebo se k nim očekává častý aktivní přístup (čtení a zápis).
* Data, která jsou určená ke zpracování a eventuální migraci na úroveň studeného úložiště.

## <a name="cool-access-tier"></a>Studená vrstva přístupu

Studená úroveň úložiště má v porovnání s horkou úrovní nižší náklady na uložení a vyšší náklady na přístup. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Studená úroveň úložiště se hodí například pro tahle data:

* Krátkodobé zálohování a datové sady pro zotavení po havárii.
* Starší obsah a média, které se již nezobrazují často, ale které by však měly být na vyžádání okamžitě dostupné.
* Velké datové sady, které je potřeba levně uložit, zatímco se shromažďují další data pro budoucí zpracování. (*Příklady:* Dlouhodobé uložení vědeckých dat, nezpracovaná telemetrická data z výrobního závodu.)

## <a name="archive-access-tier"></a>Úroveň přístupu Archive

Archivní úložiště má nejnižší náklady na úložiště a v porovnání s horkým a studeným úložištěm vyšší náklady na načtení dat. Tato úroveň je určená pro data, u kterých se toleruje latence načtení několik hodin a která zůstanou v archivní vrstvě nejméně 180 dnů.

Když je objekt blob v archivním úložišti, je offline a nejde načíst (kromě metadat, která jsou online a dostupná), kopírovat, přepsat ani změnit. Není také možné pořizovat snímky objektů blob v archivním úložišti. Můžete ale využívat stávající operace k odstranění, vytvoření seznamu nebo získání vlastností/metadat objektu blob nebo ke změně jeho vrstvy.

Archivní vrstva úložiště se hodí například pro tyto scénáře použití:

* dlouhodobé zálohování, sekundární zálohování a archivní datové sady
* Původní (hrubá, nezpracovaná) data, která je potřeba zachovat i po jejich zpracování do konečné, použitelné podoby. (*Příklad:* Původní multimediální záznamy po překódování do jiných formátů.)
* Data pro soulad a archivaci, které je potřeba uchovat po dlouhou dobu a téměř se k nim nepřistupuje. (*Příklady:*  Záznamy z bezpečnostních kamer, staré rentgenové snímky / snímky magnetické rezonance pro zdravotnická zařízení, zvukové záznamy a přepisy zákaznických hovorů pro finanční služby.)

### <a name="blob-rehydration"></a>Dosazování objektů blob
Pokud chcete načíst data v archivním úložišti, musíte nejdřív změnit vrstvu příslušného objektu blob na studenou nebo horkou. Tento proces se označuje jako dosazování a jeho dokončení může trvat až 15 hodin. Pro zajištění optimálního výkonu se důrazně doporučuje používat velké objekty blob. Současné dosazování několika malých objektů blob může dobu dokončení prodloužit.

Během dosazování můžete zkontrolovat vlastnost **Stav archivu** objektu blob a ověřit, jestli se úroveň změnila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost **Access Tier** objektu blob odpovídá nové horké nebo studené úrovni.  

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Ovládání datových vrstev na úrovni objektů blob umožňuje změnit vrstvu dat na úrovni objektu pomocí jediné operace s názvem [Set Blob Tier](/rest/api/storageservices/set-blob-tier). Současně se změnou vzorů využití můžete pro objekt blob snadno změnit vrstvu přístupu (studená, horká nebo archivní) a nemusíte přitom přesouvat data mezi účty. Všechny změny úrovně se provádějí okamžitě, s výjimkou dosazování objektu blob z archivu, což může trvat několik hodin. Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. Pokud je objekt blob v archivní úrovni, nesmí se přepsat. Proto v tomto scénáři není možné znovu uložit stejný objekt blob. Objekt blob v horké nebo studené úrovni přepsat jde a v takovém případě nový objekt blob zdědí úroveň starého objektu blob, který se přepsal.

V rámci jednoho účtu mohou současně existovat objekty blob ve všech třech vrstvách úložiště. Každý objekt blob, který nemá explicitně přiřazenou vrstvu, odvozuje nastavení vrstvy přístupu z účtu. Pokud se úroveň přístupu odvozuje z účtu, je vlastnost **Access Tier Inferred** objektu blob nastavená na true a vlastnost **Access Tier** objektu blob odpovídá úrovni účtu. Na webu Azure Portal se vlastnost odvození úrovně přístupu zobrazuje společně s úrovní přístupu objektu blob (například Horká (odvozeno) nebo Studená (odvozeno)).

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku. Nejde také změnit úroveň objektu blob bloku, který má snímky.

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když se objekt blob přesune do chladnější úrovně (horká -> studená, horká -> archivní nebo studená -> archivní), operace se fakturuje jako zápis cílové úrovně. Účtují se poplatky za operace zápisu (za 10 000) a zápis dat (za GB) pro cílovou úroveň. Když se objekt blob přesune do teplejší úrovně (archivní -> studená, archivní -> horká nebo studená -> horká), operace se fakturuje jako čtení ze zdrojové úrovně. Účtují se poplatky za operace čtení (za 10 000) a načítání dat (za GB) pro zdrojovou úroveň.

Pokud přepnete úroveň účtu z horké na studenou, budou se vám účtovat operace zápisu (za 10 000) za všechny objekty blob bez nastavené úrovně pouze v účtech GPv2. V účtech Blob Storage to není nijak zpoplatněno. Pokud přepnete svůj účet Blob Storage nebo GPv2 z horké na studenou úroveň, budou se vám účtovat operace čtení (za 10 000) i načítání dat (za GB). Můžou se účtovat také poplatky za předčasné odstranění objektů blob přesunutých ze studené nebo archivní úrovně.

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Kromě měsíčních poplatků za GB se na všechny objekty blob přesunuté do studené úrovně (pouze účty GPv2) vztahuje období předčasného odstranění ze studené úrovně v délce 30 dnů, a na všechny objekty blob přesunuté do archivní úrovně se vztahuje období předčasného odstranění z archivní úrovně v délce 180 dnů. Tento poplatek se účtuje poměrnou částí. Pokud se například objekt blob přesune do archivní úrovně a po 45 dnech pak odstraní nebo přesune do horké úrovně, bude se vám účtovat poplatek za předčasné odstranění, který odpovídá 135 (180 − 45) dnům uložení tohoto objektu blob v archivní úrovni.

## <a name="comparison-of-the-storage-tiers"></a>Srovnání vrstev úložiště

V následující tabulce najdete porovnání studené, horké a archivní úrovně úložiště.

| | **Horká vrstva úložiště** | **Studená vrstva úložiště** | **Úroveň úložiště Archive**
| ---- | ----- | ----- | ----- |
| **Dostupnost** | 99,9 % | 99 % | neuvedeno |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**| 99,99 % | 99,9 % | neuvedeno |
| **Poplatky za využití** | Vyšší poplatky za úložiště, nižší poplatky za přístup a transakce | Nižší poplatky za úložiště, vyšší poplatky za přístup a transakce | Nejnižší poplatky za úložiště, nejvyšší poplatky za přístup a transakce |
| **Minimální velikost objektu** | neuvedeno | neuvedeno | neuvedeno |
| **Minimální doba uložení** | neuvedeno | 30 dnů (pouze GPv2) | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | milisekundy | milisekundy | < 15 hodin
| **Škálovatelnost a cíle výkonnosti** | Stejné jako u účtů úložiště pro obecné účely | Stejné jako u účtů úložiště pro obecné účely | Stejné jako u účtů úložiště pro obecné účely |

> [!NOTE]
> Účty úložiště Blob podporují stejnou škálovatelnost a cíle výkonnosti jako účty úložiště pro obecné účely. Další informace najdete v tématu [Škálovatelnost a cíle výkonnosti Azure Storage Scalability](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V tomto oddílu jsou předvedené následující scénáře s využitím webu Azure Portal:

* Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage.
* Změna úrovně objektu blob v účtu GPv2 nebo Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Klikněte na možnost Všechny prostředky a kliknutím na účet úložiště do účtu přejděte.

3. V okně Nastavení klikněte na **Konfigurace**, odkud můžete zobrazit nebo změnit konfiguraci účtu.

4. Vyberte si úroveň úložiště, která vám vyhovuje: **Úroveň přístupu** nastavte na **Studená** nebo **Horká**.

5. V horní části okna klikněte na možnost Uložit.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změňte úroveň objektu blob v účtu GPv2 nebo Blob Storage.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Pokud chcete přejít na objekt blob ve vašem účtu úložiště, vyberte Všechny prostředky, vyberte účet úložiště a potom vyberte objekt blob.

3. V okně vlastností objektu blob v rozevírací nabídce **Úroveň přístupu** kliknutím vyberte úroveň úložiště (**horká**, **studená** nebo **archivní**).

5. V horní části okna klikněte na možnost Uložit.

## <a name="faq"></a>Nejčastější dotazy

**Pokud chci vrstvit svá data, mám použít účet Blob Storage nebo GPv2?**

Pro účely vrstvení doporučujeme místo účtů Blob Storage používat účty GPv2. Účty GPv2 podporují všechny funkce jako účty Blob Storage a navíc řadu dalších. Ceny účtů Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen budou k dispozici pouze v účtech GPv2. Účty GPv1 vrstvení nepodporují.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Možnosti účtu úložiště Azure](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Můžu v rámci jednoho účtu ukládat objekty ve všech třech (horká, studená a archivní) úrovních úložiště?**

Ano. Atribut **Access Tier** nastavený na úrovni účtu představuje výchozí úroveň, která se použije pro všechny objekty v příslušném účtu, které nemají explicitně nastavenou úroveň. Ovládání datových vrstev na úrovni objektů blob však umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaké je nastavení úrovně přístupu v příslušném účtu. V rámci stejného účtu mohou existovat objekty blob ve všech třech vrstvách úložiště (horká, studená nebo archivní).

**Můžu u svého účtu úložiště Blob Storage nebo GPv2 změnit výchozí úroveň úložiště?**

Ano, výchozí úroveň úložiště můžete změnit nastavením atributu **Access Tier** v účtu úložiště. Změna vrstvy úložiště se projeví u všech objektů uložených v tomto účtu, u kterých není explicitně nastavená úroveň. Při přepnutí úrovně úložiště z horké na studenou vznikají náklady na operace zápisu (za 10 000) pro všechny objekty blob bez nastavené úrovně v účtech GPv2, zatímco přepnutí úrovně úložiště ze studené na horkou s sebou nese náklady na operace čtení (za 10 000) a načítání dat (za GB) pro všechny objekty blob v účtech Blob Storage a GPv2.

**Můžu u účtu nastavit výchozí úroveň přístupu na archivní?**

Ne. Jako výchozí úroveň přístupu je u účtu možné nastavit pouze horkou a studenou úroveň úložiště. Archivní úroveň je možné nastavit pouze na úrovni objektu.

**Ve kterých oblastech jsou dostupné horké, studené a archivní úrovně úložiště?**

Horká a studená úroveň úložiště spolu s ovládáním datových vrstev na úrovni objektů blob jsou k dispozici ve všech oblastech. Úložiště archivu bude zpočátku dostupné pouze ve vybraných oblastech. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

**Budou se objekty blob ve studené vrstvě úložiště chovat jinak než objekty blob v horké vrstvě úložiště?**

Objekty blob v horké úrovni úložiště mají stejnou latenci jako objekty blob v účtech GPv1, GPv2 a Blob Storage. Objekty blob ve studené úrovni úložiště mají podobnou latenci (v řádech milisekund) jako objekty blob v účtech GPv1, GPv2 a Blob Storage. Objekty blob v archivní úrovni úložiště v účtech GPv1, GPv2 a Blob Storage mají latenci několik hodin.

Objekty blob ve studené vrstvě úložiště budou mít trochu nižší úroveň dostupnosti služeb (SLA) než objekty blob uložené v horké vrstvě úložiště. Další informace najdete v tématu [SLA pro úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Jsou operace mezi horkou, studenou a archivní úrovní stejné?**

Ano. Všechny operace mezi horkou a studenou úrovní jsou 100% konzistentní. Všechny platné operace archivní úrovně, včetně odstranění, výpisu, získání vlastností nebo metadat objektu blob a nastavení úrovně objektu blob jsou 100% konzistentní mezi horkou a studenou úrovní. Objekt blob není možné načíst ani upravit, dokud je v archivní úrovni.

**Jak se dozvím o dokončení dosazování objektu blob z archivní úrovně do horké nebo studené úrovně?**

Během dosazování můžete ověřit dokončení změny úrovně dotazováním atributu **Stav archivu** pomocí operace získání vlastností objektu blob. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost **Access Tier** objektu blob odpovídá nové horké nebo studené úrovni.  

**Kdy se mi po nastavení úrovně objektu blob začne účtovat podle odpovídající sazby?**

Každý objekt blob se vždy účtuje podle úrovně, kterou indikuje vlastnost **Access Tier** objektu blob. Když nastavíte novou úroveň objektu blob, ve vlastnosti **Access Tier** se nová úroveň projeví pro všechny transakce s výjimkou dosazování objektu blob z archivní úrovně do horké nebo studené úrovně, což může trvat několik hodin. V tomto případě se vám bude účtovat podle sazeb archivní úrovně až do dokončení dosazování, kdy se ve vlastnosti **Access Tier** projeví nová úroveň. Teprve pak se vám bude účtovat podle nové sazby horké nebo studené úrovně.

**Jak zjistím, jestli se mi bude účtovat poplatek za předčasné odstranění, když odstraním nebo přesunu objekt blob ze studené nebo archivní úrovně?**

Poplatek za předčasné odstranění se bude účtovat za každý objekt blob, který se odstraní nebo přesune ze studené (pouze účty GPv2) úrovně před uplynutím 30 dnů nebo z archivní úrovně před uplynutím 180 dnů. Jak dlouho je objekt blob ve studené nebo archivní úrovni můžete zjistit kontrolou vlastnosti **Access Tier Time Change** objektu blob, kde najdete časové razítko poslední změny úrovně. Další podrobnosti najdete v části [Předčasné odstranění ve studené a archivní úrovni](#cool-and-archive-early-deletion).

**Které nástroje a sady SDK Azure podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu?**

Ovládání datových vrstev na úrovni objektů blob a úložiště archivu podporují nástroje pro Azure Portal, PowerShell a rozhraní příkazového řádku a klientské knihovny .NET, Java, Python a Node.js.  

**Kolik dat můžu ukládat v horké, studené a archivní úrovni?**

Limit úložiště dat je společně s dalšími limity nastavený na úrovni účtu, a ne na úrovni úložiště. Proto se můžete rozhodnout využít celý limit v jedné úrovni nebo napříč všemi třemi úrovněmi. Další informace najdete v tématu [Škálovatelnost a cíle výkonnosti Azure Storage Scalability](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Další kroky

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Vyhodnocení horké, studené a archivní úrovně v účtech GPv2 a Blob Storage

[Ověření dostupnosti horké, studené a archivní úrovně v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)

[Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)

[Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
