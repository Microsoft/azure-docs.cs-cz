---
title: Horké, studené a archivní úrovně přístupu pro objekty BLOB – Azure Storage
description: Horké, studené a archivní úrovně přístupu pro účty úložiště Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255506"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: Horká, studená a archivní úroveň přístupu

Azure storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů objektů objektů blob nákladově nejefektivnějším způsobem. Mezi dostupné úrovně přístupu patří:

- **Hot** – Optimalizováno pro ukládání dat, ke kterým se často přistupuje.
- **Cool** – Optimalizováno pro ukládání dat, ke kterým se zřídka přistupuje a ukládají se alespoň 30 dní.
- **Archiv** – optimalizováno pro ukládání dat, ke kterým se jen zřídka přistupuje a ukládají se alespoň 180 dní s požadavky na flexibilní latenci (v řádu hodin).

Následující aspekty platí pro různé úrovně přístupu:

- Na úrovni účtu lze nastavit pouze horké a studené úrovně přístupu. Úroveň přístupu archivu není k dispozici na úrovni účtu.
- Horké, chladné a archivní úrovně lze nastavit na úrovni objektu blob během nahrávání nebo po nahrání.
- Data v úrovni studenépřístup může tolerovat mírně nižší dostupnost, ale stále vyžaduje vysokou odolnost, načítání latence a propustnost charakteristiky podobné hot data. U skvělých dat jsou přijatelná kompromisy pro nižší dostupnost smlouvy o úrovni služeb (SLA) a vyšší náklady na přístup ve srovnání s horkými daty pro nižší náklady na úložiště.
- Archivní úložiště ukládá data offline a nabízí nejnižší náklady na úložiště, ale také nejvyšší náklady na rehydrataci dat a přístup.

Data uložená v cloudu rostou exponenciálním tempem. Pokud chcete náklady na rozšiřující se úložiště udržet pod kontrolou, je pro optimalizaci nákladů vhodné uspořádat data podle vlastností, jako je četnost přístupu a plánovaná doba uchování. Data uložená v cloudu se mohou lišit v závislosti na tom, jak jsou generována, zpracována a přístupná po celou dobu své životnosti. Některá data se během svojí existence využívají nebo mění často. Některá data se používají často v rané fázi svého životního cyklu, ale s tím jak stárnou, přístup k nim výrazně klesá. Některá data zůstávají nečinná v cloudu a je zřídka, pokud vůbec, přístup po uložení.

Každý z těchto scénářů přístupu k datům těží z jiné vrstvy přístupu, která je optimalizována pro konkrétní přístupový vzor. Díky horkým, chladným a archivním úrovním přístupu řeší úložiště objektů Blob Azure tuto potřebu diferencovaných úrovní přístupu se samostatnými cenovými modely.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Účty úložiště, které podporují vrstvení

Vrstvení dat úložiště objektů mezi aktivními, chladnými a archivačními je podporováno jenom v účtech úložiště objektů Blob a v2 (General Purpose v2). Účty pro obecné účely v1 (GPv1) nepodporují vrstvení. Zákazníci můžou snadno převést své stávající účty úložiště GPv1 nebo Blob na účty GPv2 prostřednictvím portálu Azure. GPv2 poskytuje nové ceny a funkce pro objekty BLOB, soubory a fronty. Některé funkce a snížení cen jsou nabízeny pouze na účtech GPv2. Po komplexní kontrole cen vyhodnoťte pomocí účtů GPv2. Některé úlohy mohou být dražší na GPv2 než GPv1. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

Úložiště objektů blob a účty GPv2 zveřejňují atribut **Úrovně přístupu** na úrovni účtu. Tento atribut umožňuje zadat výchozí úroveň přístupu pro všechny objekty blob, který nemá explicitní nastavit na úrovni objektu. U objektů s vrstvou nastavenou na úrovni objektu se úroveň účtu nepoužije. Vrstvu archivu lze použít pouze na úrovni objektu. Mezi těmito úrovněmi přístupu můžete kdykoli přepínat.

## <a name="hot-access-tier"></a>Horká vrstva přístupu

Úroveň aktivního přístupu má vyšší náklady na úložiště než studené a archivní úrovně, ale nejnižší náklady na přístup. Příklady scénářů použití pro úroveň aktivního přístupu zahrnují:

- Data, která jsou aktivní nebo se očekává, že budou často přístupná (čtena a zapisována)
- Data, která jsou připravena pro zpracování a případnou migraci na úroveň studeného přístupu.

## <a name="cool-access-tier"></a>Studená vrstva přístupu

Úroveň studeného přístupu má nižší náklady na úložiště a vyšší náklady na přístup ve srovnání s horkým úložištěm. Tato úroveň je určená pro data, která zůstanou ve studené vrstvě nejméně 30 dnů. Příklady scénářů použití pro úroveň studeného přístupu zahrnují:

- Krátkodobé zálohování a datové sady pro zotavení po havárii.
- Starší obsah a média, které se již nezobrazují často, ale které by však měly být na vyžádání okamžitě dostupné.
- Velké datové sady, které je potřeba levně uložit, zatímco se shromažďují další data pro budoucí zpracování. (*Příklady:* Dlouhodobé uložení vědeckých dat, nezpracovaná telemetrická data z výrobního závodu.)

## <a name="archive-access-tier"></a>Úroveň přístupu Archive

Úroveň přístupu archivu má nejnižší náklady na úložiště. Ale má vyšší náklady na načítání dat ve srovnání s horkými a chladnými úrovněmi. Načtení dat v archivní vrstvě může trvat několik hodin. Data musí zůstat na úrovni archivu po dobu nejméně 180 dnů nebo musí podléhat poplatku za předčasné vymazání.

Zatímco objekt blob je v úložišti archivu, data objektu blob jsou offline a nelze je číst, přepisovat ani upravovat. Chcete-li číst nebo stahovat objekt blob v archivu, musíte jej nejprve rehydratovat na online úroveň. V archivním úložišti nelze pořizovat snímky objektu blob. Metadata objektu blob však zůstane online a k dispozici, což umožňuje seznam objektu blob a jeho vlastnosti. Pro objekty BLOB v archivu jsou jedinými platnými operacemi GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob a DeleteBlob. Další informace najdete v [tématu Rehydrate data objektu blob z archivní vrstvy.](storage-blob-rehydration.md)

Příklady scénářů použití pro úroveň přístupu archivu zahrnují:

- dlouhodobé zálohování, sekundární zálohování a archivní datové sady
- Původní (hrubá, nezpracovaná) data, která je potřeba zachovat i po jejich zpracování do konečné, použitelné podoby.
- Data pro soulad a archivaci, které je potřeba uchovat po dlouhou dobu a téměř se k nim nepřistupuje.

## <a name="account-level-tiering"></a>Vrstvení na úrovni účtu

Objekty BLOB ve všech třech úrovních přístupu mohou existovat společně v rámci stejného účtu. Jakýkoli objekt blob, který nemá explicitně přiřazenou úroveň, odvodí úroveň z nastavení úrovně přístupu k účtu. Pokud úroveň přístupu pochází z účtu, uvidíte vlastnost objektu blob **inferred přístupu** nastavenou na hodnotu "true" a vlastnost objektu blob **úrovně přístupu** odpovídá úrovni účtu. Na webu Azure Portal se zobrazí vlastnost _odvozená od vrstvy přístupu k objektům_ blob jako **Hot (odvodit)** nebo **Cool (odvodit).**

Změna úrovně přístupu k účtu platí pro všechny _objekty odvozené od vrstvy přístupu_ uložené v účtu, které nemají explicitní úroveň nastavena. Pokud přepnete úroveň účtu z horké na studenou, bude vám účtována za zápis operace (za 10 000) pro všechny objekty BLOB bez nastavené úrovně v účtech GPv2 pouze. Za tuto změnu v účtech úložiště objektů Blob se neplatí žádné poplatky. Pokud přepínáte z cool na horké v úložišti objektů Blob nebo na účty GPv2, bude vám účtovánpoplatek za čtecí operace (za 10 000) i načítání dat (za GB).

## <a name="blob-level-tiering"></a>Ovládání datových vrstev na úrovni objektů blob

Vrstvení na úrovni objektu blob umožňuje nahrát data na úroveň přístupu podle vašeho výběru pomocí put [blob](/rest/api/storageservices/put-blob) nebo [put block list](/rest/api/storageservices/put-block-list) operace a změnit úroveň dat na úrovni objektu pomocí nastavit operace [objektu blob](/rest/api/storageservices/set-blob-tier) nebo funkce [správy životního cyklu.](#blob-lifecycle-management) Data můžete nahrát do požadované úrovně přístupu a pak snadno změnit úroveň přístupu k objektům blob mezi horké, studené nebo archivní vrstvy jako vzorce využití změnit, aniž by bylo nutné přesunout data mezi účty. Všechny požadavky na změnu úrovně se dějí okamžitě a změny úrovně mezi horkou a studenou jsou okamžité. Rehydratace objektu blob z archivu však může trvat několik hodin.

Čas poslední změny úrovně objektu blob je zveřejněný prostřednictvím vlastnosti **Access Tier Change Time** objektu blob. Při přepsání objektu blob v horké nebo studené vrstvy, nově vytvořený objekt blob zdědí vrstvu objektu blob, který byl přepsán, pokud nová úroveň přístupu k objektu blob je explicitně nastavena na vytvoření. Pokud je objekt blob v archivní vrstvě, nelze přepsat, takže nahrávání stejného objektu blob není v tomto scénáři povoleno. 

> [!NOTE]
> Úložiště archivu a ovládání datových vrstev na úrovni objektů blob podporují jenom objekty blob bloku. Můžete také aktuálně změnit úroveň objektu blob bloku, který má snímky.

### <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů blob

Správa životního cyklu úložiště objektů blob nabízí bohaté zásady založené na pravidlech, které můžete použít k přechodu dat na nejlepší úroveň přístupu a vypršení platnosti dat na konci jejího životního cyklu. Další informace najdete [v tématu Správa životního cyklu úložiště objektů Blob Azure.](storage-lifecycle-management-concepts.md)  

> [!NOTE]
> Data uložená v účtu úložiště objektů blob bloku (výkon Premium) nelze aktuálně vrstvené na horké, cool nebo archivovat pomocí [nastavit objekt blob vrstvy](/rest/api/storageservices/set-blob-tier) nebo pomocí azure blob storage správu životního cyklu.
> Chcete-li přesunout data, musíte synchronně kopírovat objekty BLOB z účtu úložiště objektů blob bloku do úrovně horkého přístupu v jiném účtu pomocí [rozhraní URL put block from](/rest/api/storageservices/put-block-from-url) nebo verze AzCopy, která podporuje toto rozhraní API.
> *Rozhraní URL put block from* synchronně kopíruje data na serveru, což znamená, že volání bude dokončeno pouze po přesunutí všech dat z původního umístění serveru do cílového umístění.

### <a name="blob-level-tiering-billing"></a>Fakturace ovládání datových vrstev na úrovni objektů blob

Když je objekt blob nahrán nebo přesunut do horké, studené nebo archivní úrovně, bude se účtovat odpovídající sazbou bezprostředně po změně úrovně.

Při přesunutí objektu blob do úrovně chladiče (hot->cool, hot->archiv nebo archivu >), operace se účtuje jako operace zápisu do cílové vrstvy, kde se vztahuje operace zápisu (za 10 000) a poplatky za zápis dat (za GB) cílové vrstvy.

Při přesunutí objektu blob do teplejší vrstvy (archiv>cool, archiv>horké nebo studené >horké), operace se účtuje jako čtení ze zdrojové vrstvy, kde se použije operace čtení (za 10 000) a načítání dat (za GB) poplatky zdrojové vrstvy. Můžou se účtovat také poplatky za předčasné odstranění objektů blob přesunutých ze studené nebo archivní úrovně. [Rehydratace dat z archivu](storage-blob-rehydration.md) vyžaduje čas a data budou účtovány archivní ceny, dokud se data obnoví online a změny vrstvy objektu blob se změní na horké nebo studené. Následující tabulka shrnuje, jak se účtují změny úrovně:

| | **Poplatky za zápis (operace + přístup)** | **Číst poplatky (operace + přístup)**
| ---- | ----- | ----- |
| **Směr SetBlobTier** | horké >chladné,<br> archiv >horkých,<br> archiv > | archiv->pohodě,<br> archiv->horké,<br> chladné >horké

### <a name="cool-and-archive-early-deletion"></a>Předčasné odstranění ze studené a archivní úrovně

Jakýkoli objekt blob, který se přesune do úrovně cool (pouze účty GPv2) podléhá chladné období předčasného odstranění 30 dnů. Každý objekt blob, který se přesune do vrstvy archivu, podléhá období předčasného odstranění archivu 180 dní. Tento poplatek se účtuje poměrnou částí. Pokud je například objekt blob přesunut do archivace a po 45 dnech odstraněn nebo přesunut do horké vrstvy, bude vám účtován poplatek za předčasné odstranění odpovídající 135 (180 mínus 45) dnům uložení tohoto objektu blob v archivu.

Můžete vypočítat včasné odstranění pomocí vlastnosti objektu blob, **Poslední změněno**, pokud nedošlo k žádné změny úrovně přístupu. V opačném případě můžete použít, když byla úroveň přístupu naposledy upravena tak, aby se ochladila nebo archivovala zobrazením vlastnosti objektu blob: **access-tier-change-time**. Další informace o vlastnostech objektů blob naleznete v [tématu Získání vlastností objektu blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porovnání možností úložiště objektů blob bloku

V následující tabulce je uvedeno porovnání úložiště objektů blob bloku výkonu výkonu a úrovně přístupu pro aktivní, studené a archivní.

|                                           | **Prémiový výkon**   | **Horká vrstva** | **Chladná úroveň**       | **Archivní vrstva**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Dostupnost**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Dostupnost** <br> **(přístupy pro čtení RA-GRS)**  | Není dostupné.                       | 99,99 %       | 99,9 %               | Offline           |
| **Poplatky za využití**                         | Vyšší náklady na úložiště, nižší náklady na přístup a transakce | Vyšší náklady na úložiště, nižší přístup a transakční náklady | Nižší náklady na úložiště, vyšší přístup a transakční náklady | Nejnižší náklady na úložiště, nejvyšší přístup a transakční náklady |
| **Minimální velikost objektu**                   | Není dostupné.                       | Není dostupné.          | Není dostupné.                 | Není dostupné.               |
| **Minimální doba uložení**              | Není dostupné.                       | Není dostupné.          | 30 dní<sup>1</sup> | 180 dnů
| **Latence** <br> **(čas do prvního bajtu)** | Jednociferné milisekundy | milisekundy | milisekundy        | hodin<sup>2</sup> |

<sup>1</sup> Objekty v chladné vrstvě na účtech GPv2 mají minimální dobu uchovávání 30 dnů. Účty úložiště objektů blob nemají minimální dobu uchovávání pro úroveň cool.

<sup>2</sup> Archivní úložiště v současné době podporuje 2 rehydrate priority, vysoká a standardní, která nabízí různé latence načítání. Další informace naleznete v tématu [Rehydrate data objektů blob z vrstvy archivu](storage-blob-rehydration.md).

> [!NOTE]
> Účty úložiště objektů blob podporují stejné cíle výkonu a škálovatelnosti jako účty úložiště pro obecné účely v2. Další informace najdete v tématu [Škálovatelnost a cíle výkonu pro úložiště objektů Blob](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

V této části jsou následující scénáře demonstrované pomocí portálu Azure a powershellu:

- Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage.
- Změna úrovně objektu blob v účtu GPv2 nebo Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu u účtu GPv2 nebo Blob Storage

# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Na webu Azure Portal vyhledejte a vyberte **Všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. V **nastavení**vyberte **Konfigurace,** chcete-li zobrazit a změnit konfiguraci účtu.

1. Vyberte správnou úroveň přístupu pro vaše potřeby: Nastavte **úroveň Přístupu** na **Studenou** nebo **Horkou**.

1. Nahoře klikněte na **Uložit.**

![Změna úrovně účtu úložiště](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Následující skript prostředí PowerShell lze použít ke změně úrovně účtu. Proměnná `$rgName` musí být inicializována s názvem skupiny prostředků. Proměnná `$accountName` musí být inicializována s názvem účtu úložiště. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změna úrovně objektu blob v účtu úložiště GPv2 nebo Blob
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Na webu Azure Portal vyhledejte a vyberte **Všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. Vyberte kontejner a pak vyberte objekt blob.

1. Ve **vlastnostech objektu blob**vyberte **Změnit úroveň**.

1. Vyberte úroveň přístupu **za tepla**, **chladu**nebo **archivace.** Pokud je objekt blob aktuálně archivován a chcete rehydratovat na úroveň online, můžete také vybrat prioritu rehydratace **standardu** nebo **vysoké**.

1. V dolní části vyberte **Uložit.**

![Změna úrovně účtu úložiště](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Následující skript prostředí PowerShell lze změnit vrstvu objektu blob. Proměnná `$rgName` musí být inicializována s názvem skupiny prostředků. Proměnná `$accountName` musí být inicializována s názvem účtu úložiště. Proměnná `$containerName` musí být inicializována s názvem kontejneru. Proměnná `$blobName` musí být inicializována s názvem objektu blob. 
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
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny účty úložiště používají cenový model pro úložiště objektů blob bloku na základě úrovně každého objektu blob. Mějte na paměti následující aspekty fakturace:

- **Cena za uložení**: Vedle uloženého množství dat se ceny za uložení dat liší také podle úrovně přístupu. Pokud je úroveň chladnější, cena za gigabajt se snižuje.
- **Cena za přístup k datům:** Pokud je úroveň chladnější, cena za přístup k datům se zvyšuje. U dat v chladné a archivní přístupové vrstvě se účtuje poplatek za gigabajt za přístup k datům za čtení.
- **Transakční náklady**: Pro všechny úrovně se zvyšuje poplatek za transakci, který se zvyšuje s tím, jak se úroveň ochlazuje.
- **Cena za přenosy dat geografické replikace:** Tento poplatek se vztahuje jen na účty s nastavenou geografickou replikací, jako třeba GRS a RA-GRS. Přenos dat geografické replikace je zpoplatněný podle sazby za GB.
- **Cena za odchozí přenosy dat**: Odchozí přenosy dat (dat přenesených směrem z oblasti Azure) jsou zpoplatněné podle využití šířky pásma sazbou za GB, stejně jako je tomu u účtů úložiště pro obecné účely.
- **Změna úrovně přístupu**: Změna úrovně přístupu k účtu bude mít za následek poplatky za změnu úrovně pro objekty BLOB _odvozené od úrovně přístupu_ uložené v účtu, které nemají explicitní nastavenou úroveň. Informace o změně úrovně přístupu pro jeden objekt blob najdete v části [Účtování vrstvení na úrovni objektů blob](#blob-level-tiering-billing).

> [!NOTE]
> Další informace o cenách pro objekty BLOB bloku najdete na stránce [Ceny úložiště Azure.](https://azure.microsoft.com/pricing/details/storage/blobs/) Další informace o poplatcích za odchozí přenosy dat najdete na stránce [Podrobné informace o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Nejčastější dotazy

**Pokud chci vrstvit svá data, mám použít účet Blob Storage nebo GPv2?**

Pro účely vrstvení doporučujeme místo účtů Blob Storage používat účty GPv2. Účty GPv2 podporují všechny funkce jako účty Blob Storage a navíc řadu dalších. Ceny účtů Blob Storage a GPv2 jsou téměř identické, ale některé nové funkce a snížení cen budou k dispozici pouze v účtech GPv2. Účty GPv1 nepodporují vrstvení.

Cenová struktura se u účtů GPv1 a GPv2 liší a zákazníci by před volbou účtů GPv2 měli pečlivě vyhodnotit obě možnosti. Stávající účet Blob Storage nebo GPv1 můžete snadno převést na účet GPv2 jedním kliknutím. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).

**Je možné ukládat objekty ve všech třech (horké, studené a archivní) úrovně přístupu ve stejném účtu?**

Ano. Atribut **Access Tier** nastavený na úrovni účtu je výchozí úroveň účtu, která se vztahuje na všechny objekty v tomto účtu bez explicitní nastavené úrovně. Vrstvení na úrovni objektu blob umožňuje nastavit úroveň přístupu na úrovni objektu bez ohledu na to, jaké je nastavení úrovně přístupu v účtu. Objekty BLOB v libovolné ze tří úrovní přístupu (horké, studené nebo archivní) mohou existovat v rámci stejného účtu.

**Můžu změnit výchozí úroveň přístupu svého účtu úložiště Blob nebo GPv2?**

Ano, výchozí úroveň účtu můžete změnit nastavením atributu **úrovně Access** v účtu úložiště. Změna úrovně účtu platí pro všechny objekty uložené v účtu, které nemají explicitní sadu úrovní (například **Hot (odvodit)** nebo **Cool (odvodit)**). Přepínání úrovně účtu z horké na studenou vznikne operace zápisu (na 10 000) pro všechny objekty BLOB bez nastavené úrovně pouze v účtech GPv2 a přepínání z chladné na horké, vzniknou jak operace čtení (na 10 000), tak poplatky za načítání dat (za GB) pro všechny objekty BLOB v úložišti objektů BLOB a účty GPv2.

**Můžu u účtu nastavit výchozí úroveň přístupu na archivní?**

Ne. Jako výchozí úroveň přístupu k účtu lze nastavit pouze horké a studené úrovně přístupu. Archivní úroveň je možné nastavit pouze na úrovni objektu. Při nahrávání objektů blob určíte úroveň přístupu podle vašeho výběru, která má být horká, studená nebo archivní bez ohledu na výchozí úroveň účtu. Tato funkce umožňuje zapisovat data přímo do vrstvy archivu a realizovat úspory nákladů od okamžiku, kdy vytvoříte data v úložišti objektů blob.

**Ve kterých oblastech jsou k dispozici horké, chladné a archivní úrovně přístupu?**

Horké a studené úrovně přístupu spolu s vrstvením na úrovni objektů blob jsou k dispozici ve všech oblastech. Úložiště archivu bude zpočátku dostupné pouze ve vybraných oblastech. Úplný seznam najdete v tématu [Dostupné produkty Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

**Chovají se objekty BLOB v úrovni studeného přístupu jinak než objekty na úrovni aktivního přístupu?**

Objekty BLOB na úrovni aktivního přístupu mají stejnou latenci jako objekty BLOB v účtech úložiště GPv1, GPv2 a Blob. Objekty BLOB na úrovni studeného přístupu mají podobnou latenci (v milisekundách) jako objekty BLOB v účtech úložiště GPv1, GPv2 a Blob. Objekty BLOB na úrovni přístupu k archivu mají několik hodin latence v účtech úložiště GPv1, GPv2 a Blob.

Objekty BLOB v úrovni studenépřístup mají o něco nižší úroveň dostupnosti úrovně služby (SLA) než objekty BLOB uložené v úrovni přístupu za provozu. Další informace najdete ve [smlouvě SLA pro vaše úložiště](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Jsou operace mezi horkou, studenou a archivní úrovní stejné?**

Všechny operace mezi horkou a studenou úrovní jsou 100% konzistentní. Všechny platné operace archivace včetně GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier a DeleteBlob jsou 100% konzistentní s horkou a cool. Data objektů blob nelze číst ani upravovat, zatímco v archivní vrstvy, dokud rehydratované; V archivaci jsou podporovány pouze operace čtení metadat objektu blob.

**Jak se dozvím o dokončení dosazování objektu blob z archivní úrovně do horké nebo studené úrovně?**

Během rehydratace můžete použít operaci vlastností objektu blob get k dotazování atributu **Stav archivu** a potvrzení po dokončení změny vrstvy. V závislosti na cílové vrstvě má tento stav hodnotu rehydrate-pending-to-hot nebo rehydrate-pending-to-cool. Po dokončení je vlastnost stavu archivu odebrána a vlastnost objektu blob **úrovně přístupu** odráží novou horkou nebo studenou úroveň. Další informace najdete v [tématu Rehydrate data objektu blob z archivní vrstvy.](storage-blob-rehydration.md)

**Kdy se mi po nastavení úrovně objektu blob začne účtovat podle odpovídající sazby?**

Každý objekt blob se vždy účtuje podle úrovně označené vlastností **Úrovně přístupu objektu** blob. Když nastavíte novou online úroveň pro objekt blob, vlastnost **Access Tier** okamžitě odráží novou úroveň pro všechny přechody. Rehydratace objektu blob z úrovně archivu offline na horkou nebo studenou úroveň však může trvat několik hodin. V takovém případě se vám účtují archivní sazby, dokud není rehydratace dokončena, a v tomto okamžiku vlastnost **Access Tier** odráží novou úroveň. Po rehydrataci na online úroveň se vám za tuto objekt blob účtuje za horkou nebo chladnou rychlost.

**Jak zjistím, jestli mi při odstranění nebo přesunutí objektu blob z chladné nebo archivní vrstvy vznikne poplatek za předčasné odstranění?**

Poplatek za předčasné odstranění se bude účtovat za každý objekt blob, který se odstraní nebo přesune ze studené (pouze účty GPv2) úrovně před uplynutím 30 dnů nebo z archivní úrovně před uplynutím 180 dnů. Můžete určit, jak dlouho objekt blob byl v cool nebo archivní vrstvy kontrolou objektu blob **třídy přístupu,** který poskytuje razítko poslední změny vrstvy. Pokud se úroveň objektu blob nikdy nezměnila, můžete zkontrolovat vlastnost objektu blob **Poslední změna.** Další informace naleznete v tématu [Cool and archive early deletion](#cool-and-archive-early-deletion).

**Které nástroje a sady SDK Azure podporují ovládání datových vrstev na úrovni objektů blob a úložiště archivu?**

Ovládání datových vrstev na úrovni objektů blob a úložiště archivu podporují nástroje pro Azure Portal, PowerShell a rozhraní příkazového řádku a klientské knihovny .NET, Java, Python a Node.js.  

**Kolik dat můžu ukládat v horké, studené a archivní úrovni?**

Úložiště dat spolu s dalšími omezeními jsou nastaveny na úrovni účtu a ne na úroveň přístupu. Můžete použít všechny své limity v jedné vrstvě nebo ve všech třech úrovních. Další informace naleznete v tématu [Škálovatelnost a cíle výkonu pro účty standardního úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Další kroky

Vyhodnocení horké, cool a archivace v účtech úložiště GPv2 a Blob

- [Ověření dostupnosti horké, studené a archivní úrovně v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)
- [Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)
- [Další informace o rehydrataci dat objektů blob z archivní vrstvy](storage-blob-rehydration.md)
- [Zjistěte, zda by výkon premium prospěl vaší aplikaci](storage-blob-performance-tiers.md)
- [Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)
- [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
- [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
