---
title: Úrovně přístupu pro Azure Blob Storage – horká, studená a archivní
description: Přečtěte si o horké, studené a archivní úrovni přístupu pro Azure Blob Storage. Zkontrolujte účty úložiště, které podporují vrstvení.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 1a1cb8e1676405cbfbb3f4f61c86d8136b688b88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656834"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Úrovně přístupu pro Azure Blob Storage – horká, studená a archivní

Služba Azure Storage nabízí různé úrovně přístupu, což umožňuje ukládat data objektů BLOB nejefektivnějším způsobem. Dostupné úrovně přístupu zahrnují:

- **Horká** – optimalizovaná pro ukládání dat, ke kterým dochází často.
- **Studená** – optimalizovaná pro ukládání dat, která se nepoužívají zřídka a ukládají se aspoň na 30 dní.
- **Archivační** aplikace optimalizované pro ukládání dat, která se nepoužívají zřídka a jsou ukládána minimálně 180 dní a jsou v řádu hodin flexibilní požadavky na latenci.

Informace platné pro různé úrovně přístupu:

- Úroveň přístupu se dá nastavit u objektu BLOB během nebo po nahrání.
- Na úrovni účtu je možné nastavit jenom horkou nebo studenou úroveň přístupu. Úroveň přístupu archivu lze nastavit pouze na úrovni objektu BLOB.
- Data ve studené úrovni přístupu mají mírně nižší dostupnost, ale mají stále vysokou odolnost, latenci načítání a charakteristiky propustnosti, podobně jako aktivní data. U studených dat jsou v porovnání s aktivními daty přijatelné kompromisy nižší dostupnosti a vyšších nákladů na přístup za účelem snížení celkových nákladů na úložiště. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Data z archivní úrovně přístupu se ukládají offline. Úroveň archivu nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na přístup a latenci.
- Horká a studená vrstva podporují všechny možnosti redundance. Úroveň archivu podporuje jenom LRS, GRS a RA-GRS.
- Limity úložiště dat se nastavují na úrovni účtu a ne na úrovni přístupu. Můžete zvolit, že se má celý limit používat v jedné vrstvě nebo ve všech třech vrstvách.

Data uložená v cloudu se rozšiřují exponenciálním tempem. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se můžou během svojí existence lišit způsobem generování, zpracování a přístupu k nim. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data zůstanou v cloudu neaktivní, protože se k nim po uložení přistupuje velice vzácně (pokud vůbec).

Každý z těchto scénářů přístupu k datům je výhodou z jiné úrovně přístupu, která je optimalizována pro konkrétní vzor přístupu. V případě horké, studené a archivní úrovně přístupu Azure Blob Storage řeší tuto potřebu různých úrovní přístupu s oddělenými cenovými modely.

V následujících nástrojích a knihovnách klienta je podpora vrstvení na úrovni objektů BLOB a úložiště archivu.

- portál Azure
- PowerShell
- Nástroje rozhraní příkazového řádku Azure
- Klientská knihovna v .NET
- Klientská knihovna v Javě
- Klientská knihovna v Pythonu
- Klientská knihovna Node.js

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Mezi horkou, studenou a archivní úrovní dat úložiště objektů se podporuje Blob Storage a účty Pro obecné účely v2 (GPv2). Účty Pro obecné účely V1 (GPv1) nepodporují vrstvení. Stávající účty GPv1 nebo Blob Storage můžete snadno převést na účty GPv2 prostřednictvím Azure Portal. GPv2 poskytuje nové ceny a funkce pro objekty blob, soubory a fronty. Některé funkce a cenové řezy se nabízejí jenom v účtech GPv2. Některé úlohy můžou být dražší na GPv2 než GPv1. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Účty Blob Storage a GPv2 zpřístupňují atribut **úroveň přístupu** na úrovni účtu. Tento atribut umožňuje určit výchozí úroveň přístupu pro libovolný objekt blob, který není explicitně nastaven na úrovni objektu. Pro objekty s explicitně nastavenou vrstvou se tato úroveň účtu nepoužije. Archivní úroveň je možné použít pouze na úrovni objektu. Můžete kdykoli přepínat mezi úrovněmi přístupu.

Místo Blob Storagech účtů pro vrstvení použijte GPv2. GPv2 podporuje všechny funkce, které Blob Storage účty podporují, a navíc ještě více. Ceny mezi Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen jsou k dispozici pouze v účtech GPv2.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Horká úroveň úložiště

Horká úroveň přístupu má vyšší náklady na úložiště než studená a archivní úroveň, ale má nejnižší náklady na přístup. Mezi příklady scénářů použití horké úrovně přístupu patří:

- Data, která jsou v aktivním použití nebo mají být čtena z často a zapsána do nich
- Data, která jsou připravená pro zpracování a následné migrace do studené úrovně přístupu

## <a name="cool-access-tier"></a>Studená úroveň úložiště

Studená úroveň přístupu má v porovnání s horkou úrovní nižší náklady na uložení a vyšší náklady na přístup. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Mezi příklady scénářů použití studené úrovně přístupu patří:

- Krátkodobé zálohování a zotavení po havárii
- Starší data se často nepoužívají, ale očekává se, že budou k dispozici hned po otevření.
- Velké sady dat, které je potřeba ukládat nákladově efektivně, zatímco se shromažďují další data pro budoucí zpracování

## <a name="archive-access-tier"></a>Archivní úroveň úložiště

Úroveň přístupu archivu má nejnižší náklady na úložiště, ale vyšší náklady na načtení dat ve srovnání s horkou a studenou úrovní. Data musí zůstat v archivní úrovni po dobu minimálně 180 dní nebo musí platit poplatky za předčasné odstranění. Načtení dat v archivní úrovni může trvat několik hodin v závislosti na zadané prioritě pro vysazování. U malých objektů může být při redehydrataci objektu za hodinu načten objekt z archivní úrovně. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](storage-blob-rehydration.md) .

V případě, že je objekt BLOB v archivním úložišti, data objektů BLOB jsou offline a nelze je číst ani upravovat. Chcete-li číst nebo stahovat objekt BLOB v archivu, je nutné jej nejprve znovu vyprogramovat do online úrovně. Nemůžete pořizovat snímky objektu BLOB v archivním úložišti. Metadata objektu BLOB zůstávají ale online a k dispozici, což vám umožní zobrazit seznam objektů blob, vlastností, metadat a indexů objektů BLOB. Nastavení nebo úprava metadat objektů BLOB v archivu není povolená. Můžete ale nastavit a upravit značky indexu objektu BLOB. U objektů BLOB v archivu jsou jedinými platnými operacemi [vlastnosti objektů BLOB](/rest/api/storageservices/get-blob-properties), [získávat metadata objektů BLOB](/rest/api/storageservices/get-blob-metadata), [nastavovat značky objektů BLOB](/rest/api/storageservices/set-blob-tags), [získávat značky objektů BLOB](/rest/api/storageservices/get-blob-tags), [vyhledávat objekty blob podle značek](/rest/api/storageservices/find-blobs-by-tags), [objekty blob seznamů](/rest/api/storageservices/list-blobs), [nastavit úroveň objektu](/rest/api/storageservices/set-blob-tier)blob, [Kopírovat objekt BLOB](/rest/api/storageservices/copy-blob)a [Odstranit objekt BLOB](/rest/api/storageservices/delete-blob).

Mezi scénáře použití archivní úrovně přístupu patří:

- dlouhodobé zálohování, sekundární zálohování a archivní datové sady
- Původní (hrubá) data, která musí být zachována, i když byla zpracována do konečné použitelné formy
- Data o kompatibilitě a archivaci, která je potřeba ukládat po dlouhou dobu a která se špatně při použití

> [!NOTE]
> Archivní úroveň není podporována pro účty ZRS, GZRS nebo RA-GZRS. Migrace z LRS na GRS není podporovaná, pokud účet úložiště obsahuje objekty BLOB v archivní úrovni.

## <a name="account-level-tiering"></a>Vrstvení na úrovni účtu

Objekty blob ve všech třech úrovních přístupu můžou existovat v rámci jednoho účtu. Objekt blob, který nemá explicitně přiřazenou vrstvu, odvodí úroveň z nastavení vrstvy přístupu k účtu. Pokud úroveň přístupu pochází z účtu, vidíte vlastnost **odvozenou objekt BLOB vrstvy přístupu** nastavenou na hodnotu "true" a vlastnost objektu BLOB **vrstvy přístupu** odpovídá úrovni účtu. V Azure Portal se vlastnost _odvoditelné vrstvy přístupu_ zobrazuje s úrovní přístupu objektu BLOB jako **horkou (odvozená)** nebo **studená (odvozená)**.

Změna úrovně přístupu k účtu se vztahuje na všechny objekty _odvozené z úrovně přístupu_ , které jsou uložené v účtu, který nemá explicitně nastavenou úroveň. Pokud přepnete vrstvu účtu z horké na studenou, budou se vám účtovat operace zápisu (za 10 000) pro všechny objekty blob bez nastavené úrovně jenom v účtech GPv2. Za tuto změnu v Blob Storage účtech se neúčtují žádné poplatky. Pokud přepnete ze studené na horkou v účtu Blob Storage nebo GPv2, budou se vám účtovat operace čtení (za 10 000) i načítání dat (za GB).

Pouze horké a studené úrovně přístupu lze nastavit jako výchozí úroveň přístupu k účtu. Archivní úroveň je možné nastavit pouze na úrovni objektu. Při nahrávání objektu blob můžete určit úroveň přístupu podle vaší volby na horkou, studenou nebo archivní, a to bez ohledu na výchozí úroveň účtu. Tato funkce umožňuje zapisovat data přímo do archivní úrovně a ušetřit tak náklady od chvíle, kdy vytvoříte data v úložišti objektů BLOB.

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Vrstvení na úrovni objektů BLOB umožňuje odeslat data do úrovně přístupu podle vašeho výběru pomocí operací [Put BLOB](/rest/api/storageservices/put-blob) nebo [Put list](/rest/api/storageservices/put-block-list) a změnit úroveň dat na úrovni objektu pomocí funkce [Nastavení](/rest/api/storageservices/set-blob-tier) operace nebo [správy životního cyklu](#blob-lifecycle-management) . Data můžete nahrát do požadované úrovně přístupu a potom snadno změnit úroveň přístupu objektu BLOB na horkou, studenou nebo archivní úroveň, protože se mění vzorce použití, aniž byste museli přesouvat data mezi účty. Okamžitě se dostanou všechny žádosti o změnu vrstvy a mezi horkou a studenou úrovní jsou okamžité změny vrstev. Dehydratované objekt BLOB z archivní úrovně může trvat několik hodin.

Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. **Doba změny úrovně přístupu** je vlastnost na úrovni objektů BLOB a při změně výchozí úrovně účtu se neaktualizuje. Vlastnosti účtu a vlastnosti objektu BLOB jsou oddělené. V případě změny výchozí úrovně přístupu k účtu by se v každém objektu BLOB v účtu úložiště měla denáročná aktualizace **času změny úrovně přístupu** .

Při přepisování objektu BLOB na horkou nebo studenou úroveň zdědí nově vytvořený objekt BLOB vrstvu objektu blob, který se přepsal, pokud při vytváření není explicitně nastavená nová vrstva přístupu k objektu BLOB. Pokud je objekt BLOB v archivní úrovni, nejde ho přepsat, takže odeslání stejného objektu BLOB není v tomto scénáři povolené.

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku.

### <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů BLOB

Správa životního cyklu úložiště objektů BLOB nabízí bohatou zásadu založenou na pravidlech, kterou můžete použít k převodu dat do nejvyšší úrovně přístupu a vypršení platnosti dat na konci svého životního cyklu. Další informace najdete v tématu [optimalizace nákladů díky automatizaci úrovní přístupu Azure Blob Storage](storage-lifecycle-management-concepts.md) .

> [!NOTE]
> Data uložená v účtu úložiště objektů blob bloku (výkon Premium) nejde v současné době rozvrstvit na horkou, studenou nebo archivní pomocí [nastavené úrovně objektu BLOB](/rest/api/storageservices/set-blob-tier) nebo pomocí správy životního cyklu Azure Blob Storage.
> Chcete-li přesunout data, je nutné synchronně zkopírovat objekty BLOB z účtu bloku BLOB Storage do vrstvy Hot Access v jiném účtu pomocí [bloku Put z URL API](/rest/api/storageservices/put-block-from-url) nebo verze AzCopy, která podporuje toto rozhraní API.
> **Blok Put z** rozhraní API URL synchronně kopíruje data na serveru, což znamená, že se volání dokončí až po přesunu všech dat z původního umístění serveru do cílového umístění.

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když se objekt BLOB nahraje nebo přesune mezi úrovněmi, účtuje se okamžitě podle odpovídající míry po nahrání nebo změně úrovně.

Když se objekt BLOB přesune do úrovně chladicího objektu (Hot->studená, archivní >nebo archivní >), operace se fakturuje jako operace zápisu do cílové úrovně, kde se platí poplatky za operace zápisu (za 10 000) a zápis dat (za GB) pro cílovou úroveň.

Když se objekt BLOB přesune do teplé úrovně (archivní >studenou, archivní >horkou nebo studenou >horkou), operace se fakturuje jako přečtená ze zdrojové vrstvy, kde se účtují poplatky za operace čtení (za 10 000) a načtení dat (za GB) pro zdrojovou úroveň. Můžou se účtovat i poplatky za [předčasné odstranění](#cool-and-archive-early-deletion) objektů blob, které se přesunuly ze studené nebo archivní úrovně. Dosazování [dat z archivní úrovně](storage-blob-rehydration.md) bere čas a data se účtují za archivní ceny, dokud se data neobnoví online a úroveň objektu BLOB se změní na horkou nebo studenou.

Následující tabulka shrnuje, jak se účtují změny vrstvy.

| | **Poplatky za zápis (operace + přístup)** | **Poplatky za čtení (operace + přístup)** |
| ---- | ----- | ----- |
| **Nastavení úrovně objektu blob** | horká > studená<br> Archiv Hot-><br> Archiv studených > | Archiv – > studena<br> Archiv-> horká<br> horká – > horká

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Všechny objekty blob, které se přesunou do studené úrovně (pouze účty GPv2), podléhají době předčasného odstranění po dobu 30 dnů. Každý objekt blob, který je přesunut do archivní úrovně, podléhá období předčasného odstranění archivu 180 dnů. Tento poplatek se účtuje poměrnou částí. Pokud se například objekt BLOB přesune do archivní a po 45 dnů odstraní nebo přesune do horké úrovně, bude se vám účtovat poplatek za předčasné odstranění, který odpovídá hodnotě 135 (180 minus 45) dnů uložení tohoto objektu BLOB v archivu.

Při přechodu mezi studenou a archivní vrstvou jsou k dispozici nějaké podrobnosti:

1. Pokud je objekt BLOB odvozený jako studený na základě výchozí úrovně přístupu účtu úložiště a objekt BLOB se přesune do archivu, neúčtují se žádné poplatky za předčasné odstranění.
1. Pokud je objekt BLOB explicitně přesunut do studené úrovně a potom přesunut do archivu, platí poplatek za předčasné odstranění.

Vypočítá čas předčasného odstranění pomocí **Poslední změněné** vlastnosti objektu blob, pokud se nezměnily žádné změny úrovně přístupu. V opačném případě použijte při poslední změně úrovně přístupu na studenou nebo archivní zobrazením vlastnosti objektu BLOB: **přístup-vrstva-doba změny**. Další informace o vlastnostech objektu BLOB najdete v tématu [získání vlastností objektu BLOB](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porovnávání možností úložiště objektů blob bloku

V následující tabulce jsou popsány porovnání úložiště objektů blob bloku Premium a horké, studené a archivní úrovně přístupu.

|                                           | **Výkon úrovně Premium**   | **Horká vrstva** | **Studená vrstva**       | **Úroveň archivu**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Dostupnost**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**  | –                       | 99,99 %       | 99,9 %               | Offline           |
| **Poplatky za využití**                         | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Snížení nákladů na úložiště, vyššího přístupu a transakčních nákladů | Nejnižší náklady na úložiště, nejvyšší přístup a náklady na transakce |
| **Minimální doba uložení**              | N/A                       | N/A          | 30 dnů<sup>1</sup> | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | Jednociferné číslo milisekund | milisekundy | milisekundy        | hodiny<sup>2</sup> |

<sup>1</sup> objekty ve studené vrstvě na účtech GPv2 mají minimální dobu uchování 30 dnů. Účty Blob Storage pro studenou úroveň nemají minimální dobu uchování.

<sup>2</sup> archiv služby Storage aktuálně podporují dvě priority pro vysazování, vysoké a standardní, které nabízejí různé latence při načítání a náklady. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](storage-blob-rehydration.md).

> [!NOTE]
> Účty Blob Storage podporují stejné cíle výkonu a škálovatelnosti jako účty úložiště pro obecné účely v2. Další informace najdete v tématu [škálovatelnost a cíle výkonnosti pro Blob Storage](scalability-targets.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny účty úložiště používají cenový model pro úložiště objektů blob bloku na základě úrovně jednotlivých objektů BLOB. Pamatujte na následující poznámky k fakturaci:

- **Cena za uložení**: Vedle uloženého množství dat se ceny za uložení dat liší také podle úrovně přístupu. Pokud je úroveň chladnější, cena za gigabajt se snižuje.
- **Cena za přístup k datům:** Pokud je úroveň chladnější, cena za přístup k datům se zvyšuje. Pro data ve studené a archivní úrovni přístupu se účtují poplatky za přístup k datům za GB.
- **Náklady na transakci**: pro všechny úrovně, které se zvyšují, když je úroveň nenáročné, se účtuje poplatek za transakci.
- **Náklady na přenos dat geografické replikace**: Tento poplatek platí jenom pro účty s nakonfigurovanou geografickou replikací, včetně GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.
- **Cena za odchozí přenosy dat**: Odchozí přenosy dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné podle využití šířky pásma sazbou za GB, stejně jako je tomu u účtů úložiště pro obecné účely.
- **Změna úrovně přístupu**: Změna úrovně přístupu k účtu bude mít za následek poplatky za změnu úrovně všech objektů blob, které nemají explicitně nastavenou úroveň. Informace o změně úrovně přístupu pro jeden objekt BLOB najdete v tématu [fakturace vrstev na úrovni objektů BLOB](#blob-level-tiering-billing).

    Změna úrovně přístupu pro objekt blob, pokud je povolená Správa verzí, nebo pokud má objekt BLOB snímky, může mít za následek další poplatky. Informace o objektech blob s povoleným správou verzí najdete v tématu [ceny a fakturace](versioning-overview.md#pricing-and-billing) v dokumentaci k verzím objektů BLOB. Informace o objektech blob s snímky najdete v tématu [ceny a fakturace](snapshots-overview.md#pricing-and-billing) v dokumentaci k snímkům objektů BLOB.

> [!NOTE]
> Další informace o cenách pro objekty blob bloku najdete v tématu [ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/). Další informace o poplatcích za odchozí přenosy dat najdete na stránce s [podrobnostmi o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/) .

## <a name="availability"></a>Dostupnost

V vybraných oblastech jsou k dispozici různé úrovně přístupu společně s ovládáním datových vrstev na úrovni objektů BLOB. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Další kroky

Naučte se spravovat objekty BLOB a účty napříč úrovněmi přístupu.

- [Správa úrovně objektu BLOB v účtu Azure Storage](manage-access-tier.md)
- [Správa výchozí úrovně přístupu účtu Azure Storage účtu](../common/manage-account-default-access-tier.md)
- [Optimalizujte náklady díky automatizaci úrovní přístupu Azure Blob Storage.](storage-lifecycle-management-concepts.md)
