---
title: Horké, studené a archivní úrovně přístupu pro objekty blob – Azure Storage
description: Přečtěte si o horké, studené a archivní úrovni přístupu pro úložiště objektů BLOB v Azure. Zkontrolujte účty úložiště, které podporují vrstvení. Porovná možnosti úložiště objektů blob bloku.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/28/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 569e785cd8fc3ec4bbf9960cef63258e83496847
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460726"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: Horká, studená a archivní úroveň přístupu

Služba Azure Storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů BLOB nejefektivnějším způsobem. Dostupné úrovně přístupu:

- **Horká** – optimalizovaná pro ukládání dat, ke kterým dochází často.
- **Studená** – optimalizovaná pro ukládání dat, která se nepoužívají zřídka a ukládají se aspoň na 30 dní.
- **Archivní** – optimalizované pro ukládání dat, která se zřídka používají a ukládají se nejméně 180 dní s požadavky flexibilní latence (v řádu hodin).

Informace platné pro různé úrovně přístupu:

- Na úrovni účtu je možné nastavit jenom horkou nebo studenou úroveň přístupu. Na úrovni účtu není k dispozici archivní úroveň přístupu.
- Horkou, studenou nebo archivní úroveň přístupu je možné nastavit na úrovni objektu blob při jeho nahrání nebo po něm.
- Data se studenou úrovní přístupu můžou tolerovat mírně nižší dostupnost, ale stále vyžadují podobnou vysokou odolnost, latenci načítání a propustnost jako horká data. U studených dat platí mírně horší dostupnost odpovídající smlouvě SLA a vyšší přístupové náklady oproti horkým datům. Tyto slabiny jsou přijatelným způsobem vyváženy nižšími náklady na úložiště.
- V archivním úložišti jsou data uložena offline. Tato úroveň nabízí nejnižší náklady na úložiště, a nejvyšší náklady na obnovení (rehydrování) dat a přístup k nim.

Data uložená v cloudu se rozšiřují exponenciálním tempem. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se můžou během svojí existence lišit způsobem generování, zpracování a přístupu k nim. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data zůstanou v cloudu neaktivní, protože se k nim po uložení přistupuje velice vzácně (pokud vůbec).

Každý z těchto scénářů přístupu k datům je výhodou z jiné úrovně přístupu, která je optimalizována pro konkrétní vzor přístupu. Díky horké, studené a archivní úrovni přístupu Azure Blob Storage tuto potřebu vyžaduje pro rozdílné úrovně přístupu s oddělenými cenovými modely.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Datové vrstvy úložiště objektů mezi horkou, studenou a archivní funkcí jsou podporovány pouze v účtech BLOB Storage a Pro obecné účely v2 (GPv2). Účty Pro obecné účely V1 (GPv1) nepodporují vrstvení. Zákazníci můžou snadno převést svoje stávající účty GPv1 nebo BLOB Storage na účty GPv2 prostřednictvím Azure Portal. GPv2 poskytuje nové ceny a funkce pro objekty blob, soubory a fronty. Některé funkce a ceny jsou nabízeny pouze v účtech GPv2. Vyhodnoťte pomocí účtů GPv2 po komplexní kontrole cen. Některé úlohy můžou být dražší na GPv2 než GPv1. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Účty BLOB Storage a GPv2 zpřístupňují atribut **úroveň přístupu** na úrovni účtu. Tento atribut umožňuje určit výchozí úroveň přístupu pro libovolný objekt blob, který nemá explicitně nastavenou na úrovni objektu. Pro objekty s úrovní nastavenou na úrovni objektu se úroveň účtu nepoužije. Archivní úroveň je možné použít pouze na úrovni objektu. Mezi těmito úrovněmi přístupu můžete kdykoli přepínat.

## <a name="hot-access-tier"></a>Horká úroveň úložiště

Horká úroveň přístupu má vyšší náklady na úložiště než studená a archivní úroveň, ale má nejnižší náklady na přístup. Mezi příklady scénářů použití horké úrovně přístupu patří:

- Data, která se aktivně používají nebo u kterých se očekává, že se budou často číst a zapisovat.
- Data, která jsou připravená pro zpracování a případnou migraci na studenou úroveň přístupu.

## <a name="cool-access-tier"></a>Studená úroveň úložiště

Studená úroveň přístupu má v porovnání s horkou úrovní nižší náklady na uložení a vyšší náklady na přístup. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Mezi příklady scénářů použití studené úrovně přístupu patří:

- Krátkodobé zálohování a datové sady pro zotavení po havárii.
- Starší obsah a média, které se již nezobrazují často, ale které by však měly být na vyžádání okamžitě dostupné.
- Velké datové sady, které je potřeba levně uložit, zatímco se shromažďují další data pro budoucí zpracování. (*Příklady:* Dlouhodobé uložení vědeckých dat, nezpracovaná telemetrická data z výrobního závodu.)

## <a name="archive-access-tier"></a>Archivní úroveň úložiště

Úroveň přístupu archivu má nejnižší náklady na úložiště. Ale má vyšší náklady na načtení dat ve srovnání s horkou a studenou úrovní. Data musí zůstat v archivní úrovni po dobu minimálně 180 dní nebo musí platit poplatky za předčasné odstranění. Načtení dat v archivní úrovni může trvat několik hodin v závislosti na prioritě vysazování. U malých objektů může být rehydratovanécí objekt z archivu v průběhu 1 hodiny načítán. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](storage-blob-rehydration.md) .

Když je objekt BLOB v archivním úložišti, data objektů BLOB jsou offline a nejde je přečíst, přepsat ani změnit. Chcete-li číst nebo stahovat objekt BLOB v archivu, je nutné jej nejprve znovu vyprogramovat do online úrovně. Nemůžete pořizovat snímky objektu BLOB v archivním úložišti. Metadata objektu BLOB zůstávají ale online a k dispozici, což vám umožní zobrazit seznam objektů blob, vlastností, metadat a indexů objektů BLOB. Nastavení nebo úprava metadat objektů BLOB v archivu není povolená; Můžete ale nastavit a upravit značky indexu objektu BLOB. V případě objektů BLOB v archivu jsou jedinými platnými operacemi GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier, CopyBlob a DeleteBlob.

Mezi scénáře použití archivní úrovně přístupu patří:

- dlouhodobé zálohování, sekundární zálohování a archivní datové sady
- Původní (hrubá, nezpracovaná) data, která je potřeba zachovat i po jejich zpracování do konečné, použitelné podoby.
- Data pro soulad a archivaci, které je potřeba uchovat po dlouhou dobu a téměř se k nim nepřistupuje.

> [!NOTE]
> Archivní úroveň se v současné době nepodporuje u účtů ZRS, GZRS nebo RA-GZRS.

## <a name="account-level-tiering"></a>Vrstvení na úrovni účtu

Objekty blob ve všech třech úrovních přístupu můžou existovat v rámci jednoho účtu. Objekt blob, který nemá explicitně přiřazenou vrstvu, odvodí úroveň z nastavení vrstvy přístupu k účtu. Pokud úroveň přístupu pochází z účtu, vidíte vlastnost **odvozenou objekt BLOB vrstvy přístupu** nastavenou na hodnotu "true" a vlastnost objektu BLOB **vrstvy přístupu** odpovídá úrovni účtu. V Azure Portal se vlastnost _odvoditelné vrstvy přístupu_ zobrazuje s úrovní přístupu objektu BLOB jako **horkou (odvozená)** nebo **studená (odvozená)**.

Změna úrovně přístupu k účtu se vztahuje na všechny objekty _odvozené z úrovně přístupu_ , které jsou uložené v účtu, který nemá explicitně nastavenou úroveň. Pokud přepnete vrstvu účtu z horké na studenou, budou se vám účtovat operace zápisu (za 10 000) pro všechny objekty blob bez nastavené úrovně jenom v účtech GPv2. Tato změna v účtech BLOB Storage se neúčtuje. Pokud přepnete ze studené na horkou v účtu BLOB Storage nebo GPv2, budou se vám účtovat operace čtení (za 10 000) i načítání dat (za GB).

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Vrstvení na úrovni objektů BLOB umožňuje odeslat data do úrovně přístupu podle vašeho výběru pomocí operací [Put BLOB](/rest/api/storageservices/put-blob) nebo [Put list](/rest/api/storageservices/put-block-list) a změnit úroveň dat na úrovni objektu pomocí funkce [Nastavení](/rest/api/storageservices/set-blob-tier) operace nebo [správy životního cyklu](#blob-lifecycle-management) . Data můžete nahrát do požadované úrovně přístupu a potom snadno změnit úroveň přístupu objektu BLOB na horkou, studenou nebo archivní úroveň, protože se mění vzorce použití, aniž byste museli přesouvat data mezi účty. Okamžitě se dostanou všechny žádosti o změnu vrstvy a mezi horkou a studenou úrovní jsou okamžité změny vrstev. Dosazování objektu BLOB z archivu ale může trvat několik hodin.

Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. Při přepisování objektu BLOB na horkou nebo studenou úroveň zdědí nově vytvořený objekt BLOB vrstvu objektu blob, který se přepsal, pokud při vytváření není explicitně nastavená nová vrstva přístupu k objektu BLOB. Pokud je objekt BLOB v archivní úrovni, nejde ho přepsat, takže odeslání stejného objektu BLOB není v tomto scénáři povolené. 

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku.

### <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů BLOB

Blob Storage Správa životního cyklu nabízí bohatou zásadu založenou na pravidlech, kterou můžete použít k převodu dat do nejvyšší úrovně přístupu a vypršení platnosti dat na konci jejího životního cyklu. Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md) .  

> [!NOTE]
> Data uložená v účtu úložiště objektů blob bloku (výkon Premium) nejde v současné době rozvrstvit na horkou, studenou nebo archivní pomocí [nastavené úrovně objektu BLOB](/rest/api/storageservices/set-blob-tier) nebo pomocí správy životního cyklu Azure Blob Storage.
> Chcete-li přesunout data, je nutné synchronně zkopírovat objekty BLOB z účtu bloku BLOB Storage do vrstvy Hot Access v jiném účtu pomocí [bloku Put z URL API](/rest/api/storageservices/put-block-from-url) nebo verze AzCopy, která podporuje toto rozhraní API.
> *Blok Put z* rozhraní API URL synchronně kopíruje data na serveru, což znamená, že se volání dokončí až po přesunu všech dat z původního umístění serveru do cílového umístění.

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když se objekt BLOB nahraje nebo přesune na horkou, studenou nebo archivní úroveň, účtuje se okamžitě odpovídající sazba po změně úrovně.

Když se objekt BLOB přesune do úrovně chladicího objektu (Hot->studená, archivní >nebo archivní >), operace se fakturuje jako operace zápisu do cílové úrovně, kde se platí poplatky za operace zápisu (za 10 000) a zápis dat (za GB) pro cílovou úroveň.

Když se objekt BLOB přesune do teplé úrovně (archivní >studenou, archivní >horkou nebo studenou >horkou), operace se fakturuje jako přečtená ze zdrojové vrstvy, kde se účtují poplatky za operace čtení (za 10 000) a načtení dat (za GB) pro zdrojovou úroveň. Můžou se účtovat také poplatky za předčasné odstranění objektů blob přesunutých ze studené nebo archivní úrovně. Dosazování [dat z archivu](storage-blob-rehydration.md) trvá čas a data se budou účtovat za archivní ceny, dokud se data neobnoví online a na horké nebo studené úrovni objektů BLOB se změní. Následující tabulka shrnuje, jak se účtují změny vrstvy:

| | **Poplatky za zápis (operace + přístup)** | **Poplatky za čtení (operace + přístup)**
| ---- | ----- | ----- |
| **Směr SetBlobTier** | horká >studená,<br> Archiv Hot->,<br> Archiv studených > | archivace – >studena,<br> Archiv->horká,<br> horká – >horká

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Všechny objekty blob, které se přesunou do studené úrovně (pouze účty GPv2), podléhají době předčasného odstranění po dobu 30 dnů. Každý objekt blob, který je přesunut do archivní úrovně, podléhá období předčasného odstranění archivu 180 dnů. Tento poplatek se účtuje poměrnou částí. Pokud se například objekt BLOB přesune do archivní a po 45 dnů odstraní nebo přesune do horké úrovně, bude se vám účtovat poplatek za předčasné odstranění, který odpovídá hodnotě 135 (180 minus 45) dnů uložení tohoto objektu BLOB v archivu.

Předčasné odstranění můžete vypočítat pomocí vlastnosti objektu blob, **Naposledy změněno**, pokud se nezměnily žádné změny úrovně přístupu. V opačném případě můžete použít, když se úroveň přístupu naposledy změnila na studenou nebo archivní, zobrazením vlastnosti objektu BLOB: **přístup-vrstva-doba změny**. Další informace o vlastnostech objektu BLOB najdete v tématu [získání vlastností objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porovnávání možností úložiště objektů blob bloku

V následující tabulce jsou popsány porovnání úložiště objektů blob bloku Premium a horké, studené a archivní úrovně přístupu.

|                                           | **Výkon úrovně Premium**   | **Horká vrstva** | **Studená vrstva**       | **Úroveň archivu**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Dostupnost**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**  | Není k dispozici                       | 99,99 %       | 99,9 %               | Offline           |
| **Poplatky za využití**                         | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Vyšší náklady na úložiště, nižší přístup a náklady na transakce | Snížení nákladů na úložiště, vyššího přístupu a transakčních nákladů | Nejnižší náklady na úložiště, nejvyšší přístup a náklady na transakce |
| **Minimální velikost objektu**                   | N/A                       | N/A          | N/A                 | N/A               |
| **Minimální doba uložení**              | N/A                       | N/A          | 30 dnů<sup>1</sup> | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | Jednociferné číslo milisekund | milisekundy | milisekundy        | hodiny<sup>2</sup> |

<sup>1</sup> objekty ve studené vrstvě na účtech GPv2 mají minimální dobu uchování 30 dnů. Účty úložiště BLOB nemají pro studenou vrstvu minimální dobu uchování.

<sup>2</sup> archiv služby Storage v současné době podporuje 2 rehydratované priority, vysoké a standardní, které nabízí různé latence při načítání. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](storage-blob-rehydration.md).

> [!NOTE]
> Účty úložiště BLOB podporují stejné cíle výkonu a škálovatelnosti jako účty úložiště pro obecné účely v2. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V této části se při použití Azure Portal a PowerShellu ukázaly následující scénáře:

- Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage.
- Změna úrovně objektu blob v účtu GPv2 nebo Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte **všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. V **Nastavení**vyberte **Konfigurace** , aby se zobrazila a změnila konfigurace účtu.

1. Vyberte správnou úroveň přístupu podle vašich potřeb: nastavte **úroveň přístupu** buď na **studenou** , nebo **za horkou**.

1. V horní části klikněte na **Uložit** .

![Změna výchozí úrovně účtu v Azure Portal](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ke změně úrovně účtu se dá použít následující skript PowerShellu. `$rgName`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změna úrovně objektu BLOB v účtu GPv2 nebo BLOB Storage
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte **všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. Vyberte svůj kontejner a pak vyberte objekt BLOB.

1. Ve **vlastnostech objektu BLOB**vyberte **změnit úroveň**.

1. Vyberte úroveň přístupu **horká**, **studená**nebo **archivní** . Pokud je objekt BLOB v současné době v archivu a chcete ho znovu vyhodnotit do online úrovně, můžete také vybrat možnost rehydratované prioritu **Standard** nebo **High**.

1. V dolní části vyberte **Uložit** .

![Změna úrovně objektu BLOB v Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ke změně úrovně objektu BLOB se dá použít následující skript PowerShellu. `$rgName`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště. `$containerName`Proměnná musí být inicializována s názvem kontejneru. `$blobName`Proměnná musí být inicializována s vaším názvem objektu BLOB. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny účty úložiště používají cenový model pro úložiště objektů blob bloku na základě úrovně jednotlivých objektů BLOB. Pamatujte na následující poznámky k fakturaci:

- **Cena za uložení**: Vedle uloženého množství dat se ceny za uložení dat liší také podle úrovně přístupu. Pokud je úroveň chladnější, cena za gigabajt se snižuje.
- **Cena za přístup k datům:** Pokud je úroveň chladnější, cena za přístup k datům se zvyšuje. Pro data ve studené a archivní úrovni přístupu se účtují poplatky za přístup k datům za GB.
- **Náklady na transakci**: pro všechny úrovně, které se zvyšují, když je úroveň nenáročné, se účtuje poplatek za transakci.
- **Cena za přenosy dat geografické replikace:** Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.
- **Cena za odchozí přenosy dat**: Odchozí přenosy dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné podle využití šířky pásma sazbou za GB, stejně jako je tomu u účtů úložiště pro obecné účely.
- **Změna úrovně přístupu**: Změna úrovně přístupu k účtu bude mít za následek poplatky za změnu vrstvy pro odvozené objekty blob _vrstvy přístupu_ uložené v účtu, který nemá explicitně nastavenou úroveň. Informace o změně úrovně přístupu pro jeden objekt BLOB najdete v tématu [fakturace vrstvení na úrovni objektů BLOB](#blob-level-tiering-billing).

    Změna úrovně přístupu pro objekt blob, pokud je povolená Správa verzí, nebo pokud má objekt BLOB snímky, může mít za následek další poplatky. Další informace o tom, jak se vám bude účtovat, když je povolená Správa verzí objektů BLOB a explicitně měníte úroveň objektu blob, najdete v tématu [ceny a fakturace](versioning-overview.md#pricing-and-billing) v dokumentaci pro správu verzí objektů BLOB. Další informace o tom, jak se fakturuje, když objekt BLOB má snímky a explicitně měníte úroveň objektu blob, najdete v tématu [ceny a fakturace](snapshots-overview.md#pricing-and-billing) v dokumentaci pro snímky objektů BLOB.

> [!NOTE]
> Další informace o cenách pro objekty blob bloku najdete na stránce s [cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) . Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Časté otázky

**Pokud chci vrstvit svá data, mám použít účet Blob Storage nebo GPv2?**

Pro účely vrstvení doporučujeme místo účtů Blob Storage používat účty GPv2. Účty GPv2 podporují všechny funkce jako účty Blob Storage a navíc řadu dalších. Ceny účtů Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen budou k dispozici pouze v účtech GPv2. Účty GPv1 nepodporují vrstvení.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

**Můžu ve stejném účtu ukládat objekty ve všech třech (horká, studená a archivní) úrovních?**

Ano. Atribut **úroveň přístupu** nastavený na úrovni účtu představuje výchozí úroveň účtu, která se vztahuje na všechny objekty v daném účtu bez explicitní nastavené úrovně. Vrstvení na úrovni objektů BLOB umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaká je nastavení vrstvy přístupu na účtu. V rámci stejného účtu můžou existovat objekty BLOB v některé ze tří úrovní přístupu (horká, studená nebo archivní).

**Můžu změnit výchozí úroveň přístupu účtu úložiště BLOB nebo GPv2?**

Ano, výchozí úroveň účtu můžete změnit nastavením atributu **úroveň přístupu** v účtu úložiště. Změna úrovně účtu se vztahuje na všechny objekty uložené v účtu, které nemají explicitně nastavenou úroveň (například **Hot (odvozeno)** nebo **studená (odvozená)**). Přepínání úrovně účtu z horké na studenou povede k operacím zápisu (za 10 000) pro všechny objekty blob bez nastavené úrovně v účtech GPv2 a přepínání ze studené na horkou se účtují poplatky za operace čtení (za 10 000) i načítání dat (za GB) pro všechny objekty BLOB v účtech BLOB Storage a GPv2.

**Můžu u účtu nastavit výchozí úroveň přístupu na archivní?**

Ne. Jako výchozí úroveň přístupu se dá nastavit jenom horká a studená úroveň přístupu. Archivní úroveň je možné nastavit pouze na úrovni objektu. Při nahrávání objektů BLOB určíte úroveň přístupu podle vaší volby na horkou, studenou nebo archivní, a to bez ohledu na výchozí úroveň účtu. Tato funkce umožňuje zapisovat data přímo do archivní úrovně a ušetřit tak náklady od chvíle, kdy vytvoříte data v úložišti objektů BLOB.

**Ve kterých oblastech jsou horké, studené a archivní úrovně přístupu dostupné v?**

Horká a studená úroveň přístupu spolu s ovládáním datových vrstev na úrovni objektů BLOB jsou dostupná ve všech oblastech. Úložiště archivu bude zpočátku dostupné pouze ve vybraných oblastech. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

**Jaké možnosti redundance jsou podporovány pro horké, studené a archivní úrovně přístupu?**

Horká a studená vrstva podporují všechny možnosti redundance. Úroveň archivu podporuje jenom LRS, GRS a RA-GRS. ZRS, GZRS a RA-GZRS nejsou podporovány pro archivní vrstvu.

**Chovají se objekty blob ve studené úrovni přístupu jinak než ty, které jsou v úrovni Hot Accessu?**

Objekty BLOB v úrovni Hot Access mají stejnou latenci jako objekty BLOB v účtech GPv1, GPv2 a BLOB Storage. Objekty blob ve studené úrovni přístupu mají podobnou latenci (v milisekundách) jako objekty BLOB v účtech GPv1, GPv2 a BLOB Storage. Objekty BLOB v archivní úrovni mají několik hodin latence v účtech GPv1, GPv2 a BLOB Storage.

Objekty blob ve studené úrovni přístupu mají mírně nižší dostupnost služby (SLA) než objekty blob uložené v úrovni Hot Accessu. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

**Jsou operace mezi horkou, studenou a archivní úrovní stejné?**

Všechny operace mezi horkou a studenou úrovní jsou 100% konzistentní. Všechny platné archivní operace, včetně GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier a DeleteBlob, jsou 100% v souladu s horkou a studenou. Data objektu BLOB nelze číst ani upravovat, pokud je v archivní úrovni, dokud není znovu vyměněno. v archivu jsou podporovány pouze operace čtení metadat objektů BLOB. V archivu je ale možné číst, nastavovat nebo upravovat značky indexu objektů BLOB.

**Jak se dozvím o dokončení dosazování objektu blob z archivní úrovně do horké nebo studené úrovně?**

Během dosazování můžete použít operaci získat vlastnosti objektu BLOB k cyklickému dotazování atributu **stav archivu** a potvrdit, kdy se změna vrstvy dokončila. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení se vlastnost stavu archivu odebere a vlastnost objektu BLOB **vrstvy přístupu** odráží novou horkou nebo studenou úroveň. Další informace najdete v tématu [dehydratované data objektů BLOB z archivní úrovně](storage-blob-rehydration.md) .

**Kdy se mi po nastavení úrovně objektu blob začne účtovat podle odpovídající sazby?**

Každý objekt BLOB se vždycky účtuje podle úrovně označené vlastností **úroveň přístupu** objektu BLOB. Když nastavíte novou online úroveň pro objekt blob, vlastnost **úroveň přístupu** okamžitě odráží novou úroveň pro všechny přechody. Nicméně dosazování objektu BLOB z archivní úrovně offline na horkou nebo studenou úroveň může trvat několik hodin. V takovém případě se vám budou účtovat sazby za archivaci, dokud se nedokončí dosazení, a v takovém případě vlastnost **úroveň přístupu** odráží novou úroveň. Po dekomprimaci do online úrovně se vám tento objekt BLOB účtuje za horkou nebo studenou sazbu.

**Návody určit, jestli se při odstraňování nebo přesouvání objektu BLOB ze studené nebo archivní úrovně účtuje poplatek za předčasné odstranění?**

Poplatek za předčasné odstranění se bude účtovat za každý objekt blob, který se odstraní nebo přesune ze studené (pouze účty GPv2) úrovně před uplynutím 30 dnů nebo z archivní úrovně před uplynutím 180 dnů. To, jak dlouho je objekt BLOB ve studené nebo archivní úrovni, můžete zjistit tak, že zkontrolujete vlastnost objektu BLOB s **časem změny úrovně přístupu** , která poskytuje razítko poslední změny vrstvy. Pokud se vrstva objektu BLOB nikdy nezměnila, můžete si ověřit **Poslední upravenou** vlastnost objektu BLOB. Další informace najdete v tématu [studená a archivní předčasné odstranění](#cool-and-archive-early-deletion).

**Které nástroje a sady SDK Azure podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu?**

Ovládání datových vrstev na úrovni objektů blob a úložiště archivu podporují nástroje pro Azure Portal, PowerShell a rozhraní příkazového řádku a klientské knihovny .NET, Java, Python a Node.js.  

**Kolik dat můžu ukládat v horké, studené a archivní úrovni?**

Úložiště dat spolu s dalšími omezeními se nastavuje na úrovni účtu a ne na úrovni přístupu. Můžete zvolit, že se má celý limit používat v jedné vrstvě nebo ve všech třech vrstvách. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Další kroky

Vyhodnotit horkou, studenou a archivní v účtech GPv2 a BLOB Storage

- [Ověření dostupnosti horké, studené a archivní úrovně v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)
- [Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)
- [Přečtěte si informace o prodehydratacích dat objektů BLOB z archivní úrovně.](storage-blob-rehydration.md)
- [Určení, jestli má vaše aplikace nárok na zvýšení úrovně Premium](storage-blob-performance-tiers.md)
- [Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)
- [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
- [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
