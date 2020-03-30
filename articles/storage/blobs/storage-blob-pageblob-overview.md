---
title: Přehled objektů BLOB stránek Azure | Dokumenty společnosti Microsoft
description: Přehled objektů BLOB stránky Azure a jejich výhody, včetně případů použití s ukázkovými skripty.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/13/2019
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 060e1d01e5f078bad9852ae35d0af9142192a7b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985621"
---
# <a name="overview-of-azure-page-blobs"></a>Přehled objektů BLOB stránky Azure

Azure Storage nabízí tři typy úložiště objektů blob: objekty BLOB bloku, objekty BLOB apaa a objekty BLOB stránky. Objekty BLOB bloku se skládají z bloků a jsou ideální pro ukládání textu nebo binárních souborů a pro efektivní nahrávání velkých souborů. Připojit objekty BLOB se také stočímy z bloků, ale jsou optimalizované pro operace připojení, takže jsou ideální pro protokolování scénářů. Objekty BLOB stránky se sčítají z 512bajtových stránek o celkové velikosti až 8 TB a jsou určeny pro časté náhodné operace čtení a zápisu. Objekty BLOB stránky jsou základem disků Azure IaaS. Tento článek se zaměřuje na vysvětlení funkcí a výhod objektů BLOB stránky.

Objekty BLOB stránky jsou kolekce stránek o velikosti 512 bajtů, které umožňují čtení a zápis libovolné rozsahy bajtů. Proto jsou objekty BLOB stránky ideální pro ukládání indexových a řídkých datových struktur, jako je operační systém a datové disky pro virtuální počítače a databáze. Azure SQL DB například používá objekty BLOB stránky jako základní trvalé úložiště pro své databáze. Objekty BLOB stránky se navíc často používají také pro soubory s aktualizacemi založenými na rozsahu.  

Klíčové vlastnosti objektů BLOB stránky Azure jsou jeho rozhraní REST, odolnost základního úložiště a bezproblémové možnosti migrace do Azure. Tyto funkce jsou podrobněji popsány v další části. Kromě toho jsou objekty BLOB stránky Azure aktuálně podporované ve dvou typech úložiště: Úložiště Premium a Standardní úložiště. Úložiště Premium storage je navrženo speciálně pro úlohy vyžadující konzistentní vysoký výkon a nízkou latenci, takže objekty BLOB stránky premium jsou ideální pro scénáře vysoce výkonného úložiště. Účty standardního úložiště jsou nákladově efektivnější pro spouštění úloh necitlivých na latenci.

## <a name="sample-use-cases"></a>Případy použití vzorku

Pojďme diskutovat o několik případů použití pro objekty BLOB stránky počínaje Disky Azure IaaS. Objekty BLOB stránky Azure jsou páteří platformy virtuálních disků pro Azure IaaS. Operační systém Azure i datové disky jsou implementovány jako virtuální disky, kde jsou data trvale trvalá v platformě Azure Storage a pak doručena do virtuálních počítačů pro maximální výkon. Disky Azure jsou trvalé ve [formátu Hyper-VHD](https://technet.microsoft.com/library/dd979539.aspx) a uloženy jako [objekt blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) v Azure Storage. Kromě použití virtuálních disků pro virtuální počítače Azure IaaS umožňují objekty BLOB stránky také scénáře PaaS a DBaaS, jako je služba Azure SQL DB, která aktuálně používá objekty BLOB stránky pro ukládání dat SQL, což umožňuje rychlé náhodné operace čtení a zápisu pro databázi. Dalším příkladem by bylo, pokud máte službu PaaS pro sdílený přístup k médiím pro kolaborativní aplikace pro úpravu videa, objekty BLOB stránky umožňují rychlý přístup k náhodným umístěním v médiích. Umožňuje také rychlé a efektivní úpravy a slučování stejných médií více uživateli. 

Služby Microsoftu od první strany, jako je Azure Site Recovery, Azure Backup, stejně jako mnoho vývojářů třetích stran implementovaly špičkové inovace pomocí rozhraní REST objektu BLOB stránky. Následují některé z jedinečných scénářů implementovaných v Azure: 

* Správa přírůstkových snímků řízená aplikací: Aplikace můžou využít snímky objektů blob stránky a rest api pro ukládání kontrolních bodů aplikace, aniž by došlo k nákladnému duplikaci dat. Azure Storage podporuje místní snímky pro objekty BLOB stránky, které nevyžadují kopírování celého objektu blob. Tato veřejná snímek API také umožňují přístup a kopírování rozdílů mezi snímky.
* Migrace aplikací za provozu aplikací a dat z místního do cloudu: Zkopírujte místní data a pomocí rest api zapis zapisovat přímo do objektu blob stránky Azure, zatímco místní virtuální počítač nadále běží. Jakmile cíl dojedou, můžete rychle převzetí služeb při selhání na virtuální počítač Azure pomocí tohoto data. Tímto způsobem můžete migrovat virtuální počítače a virtuální disky z místního do cloudu s minimálními prostoji, protože migrace dat probíhá na pozadí, zatímco budete pokračovat v používání virtuálního počítače a prostoje potřebné pro převzetí služeb při selhání bude krátká (v minutách).
* Sdílený přístup [založený na SAS,](../common/storage-sas-overview.md) který umožňuje scénáře, jako je více čtecích zařízení a jeden zapisovač s podporou řízení souběžnosti.

## <a name="page-blob-features"></a>Funkce objektů blob stránky

### <a name="rest-api"></a>REST API

V následujícím dokumentu můžete začít s [vývojem objektů BLOB stránky](storage-dotnet-how-to-use-blobs.md). Jako příklad můžete například získat přístup k objektům BLOB stránky pomocí knihovny klienta úložiště pro rozhraní .NET. 

Následující diagram popisuje celkové vztahy mezi objekty BLOB účtu, kontejnerů a stránky.

![Snímek obrazovky zobrazující vztahy mezi účty, kontejnery a objekty BLOB stránky](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Vytvoření prázdného objektu blob stránky zadané velikosti

Chcete-li vytvořit objekt blob stránky, nejprve vytvoříme objekt **CloudBlobClient** se základním identifikátorem URI pro přístup k úložišti objektů blob pro váš účet úložiště *(pbaccount* na obrázku 1) spolu s objektem **StorageCredentialsAccountAndKey,** jak je znázorněno v následujícím příkladu. Příklad pak ukazuje vytvoření odkazu na **objekt CloudBlobContainer** a potom vytvoření kontejneru (*testvhds*), pokud ještě neexistuje. Potom pomocí objektu **CloudBlobContainer** vytvořte odkaz na objekt **CloudPageBlob** zadáním názvu objektu blob stránky (os4.vhd) pro přístup. Chcete-li vytvořit objekt blob stránky, volejte [CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), předávání maximální velikost pro objekt blob vytvořit. *BlobSize* musí být násobkem 512 bajtů.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Změna velikosti objektu blob stránky

Chcete-li změnit velikost objektu blob stránky po vytvoření, použijte metodu [Změnit velikost.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) Požadovaná velikost by měla být násobkem 512 bajtů.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

#### <a name="writing-pages-to-a-page-blob"></a>Zápis stránek do objektu blob stránky

Chcete-li psát stránky, použijte metodu [CloudPageBlob.WritePages.](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages)  To vám umožní psát sekvenční sadu stránek až do 4MBs. Posun zapisované do musí začínat na hranici 512 bajtů (startingOffset % 512 == 0) a končit na hranici 512 - 1.  Následující příklad kódu ukazuje, jak volat **WritePages** pro objekt blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Jakmile požadavek na zápis pro sekvenční sadu stránek proběhne ve službě objektů blob a je replikován z hlediska odolnosti a odolnosti proti chybám, byl potvrzen zápis a úspěch je vrácen zpět klientovi.  

Níže uvedený diagram znázorňuje 2 samostatné operace zápisu:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operace zápisu začínající posunem 0 délky 1024 bajtů 
2.  Operace zápisu začínající posunem 4096 o délce 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Čtení stránek z objektu blob stránky

Chcete-li číst stránky, použijte [metodu CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) ke čtení rozsahu bajtů z objektu blob stránky. To umožňuje stáhnout úplný objekt blob nebo rozsah bajtů počínaje libovolným posunem v objektu blob. Při čtení posun nemusí začínat na násobku 512. Při čtení bajtů ze stránky NUL vrátí služba nula bajtů.

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

Následující obrázek znázorňuje operaci čtení s posunem 256 a velikostí rozsahu 4352. Vrácená data jsou zvýrazněna oranžově. Nuly jsou vráceny pro stránky NUL.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Pokud máte řídce osídlený objekt blob, můžete jen stáhnout platné oblasti stránky, abyste se vyhnuli placení za odchozí platnost nulových bajtů a snížili latenci stahování.  Chcete-li zjistit, které stránky jsou zálohovány daty, použijte [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Potom můžete vytvořit výčet vrácené oblasti a stáhnout data v každé oblasti. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

#### <a name="leasing-a-page-blob"></a>Zapůjčení objektu blob stránky

Operace objektu blob zapůjčení vytvoří a spravuje zámek na objekt blob pro operace zápisu a odstranění. Tato operace je užitečná ve scénářích, kde je objekt blob stránky přistupovat z více klientů zajistit pouze jeden klient může zapisovat do objektu blob najednou. Disky Azure například využívá tento mechanismus leasingu k zajištění, že disk je pouze spravovaný jedním virtuálním počítačem. Doba trvání zámku může být 15 až 60 sekund nebo může být nekonečná. Další podrobnosti naleznete v dokumentaci [zde.](/rest/api/storageservices/lease-blob)

Kromě bohatých api REST poskytují objekty BLOB stránky také sdílený přístup, odolnost a rozšířené zabezpečení. Tyto výhody budeme podrobněji hradit v dalších odstavcích. 

### <a name="concurrent-access"></a>Souběžný přístup

Objekty BLOB stránky REST API a jeho mechanismus zapůjčení umožňuje aplikacím přístup k objektu blob stránky z více klientů. Řekněme například, že potřebujete vytvořit distribuovanou cloudovou službu, která sdílí objekty úložiště s více uživateli. Mohlo by to být webová aplikace sloužící velkou sbírku obrázků pro několik uživatelů. Jednou z možností pro implementaci tohoto je použití virtuálního počítače s připojenými disky. Nevýhody tohoto patří (i) omezení, že disk lze připojit pouze k jednomu virtuálnímu počítače, čímž se omezí škálovatelnost, flexibilita a rostoucí rizika. Pokud je problém s virtuálním počítači nebo služby spuštěné na virtuálním počítači, pak z důvodu zapůjčení, image je nepřístupná, dokud vyprší zapůjčení nebo je přerušeno; a (ii) Dodatečné náklady na virtuální zařízení IaaS. 

Alternativní možností je použití objektů BLOB stránky přímo prostřednictvím api AZURE Storage REST. Tato možnost eliminuje potřebu nákladných virtuálních počítačů IaaS, nabízí plnou flexibilitu přímého přístupu od více klientů, zjednodušuje klasický model nasazení tím, že eliminuje potřebu připojení nebo odpojení disků a eliminuje riziko problémů na virtuálním počítači. A poskytuje stejnou úroveň výkonu pro náhodné operace čtení a zápisu jako disk

### <a name="durability-and-high-availability"></a>Odolnost a vysoká dostupnost

Standardní i prémiové úložiště jsou trvalé úložiště, kde jsou data objektu blob stránky vždy replikována, aby byla zajištěna odolnost a vysoká dostupnost. Další informace o redundanci úložiště Azure najdete v této [dokumentaci](../common/storage-redundancy.md). Azure konzistentně dodává odolnost na podnikové úrovni pro disky IaaS a objekty BLOB stránky s špičkovou nulovou roční [mírou selhání](https://en.wikipedia.org/wiki/Annualized_failure_rate).

### <a name="seamless-migration-to-azure"></a>Bezproblémová migrace do Azure

Pro zákazníky a vývojáře, kteří mají zájem o implementaci vlastního řešení zálohování, Azure také nabízí přírůstkové snímky, které mají pouze rozdíly. Tato funkce se vyhýbá nákladům na počáteční úplnou kopii, což výrazně snižuje náklady na zálohování. Spolu se schopností efektivně číst a kopírovat rozdílová data je to další výkonná funkce, která umožňuje ještě více inovací od vývojářů, což vede k nejlepšímu zálohování a zotavení po havárii (DR) v Azure. Můžete nastavit vlastní zálohování nebo řešení zotavení po havárii pro vaše virtuální počítače v Azure pomocí [snímek blob](/rest/api/storageservices/snapshot-blob) spolu s [rozhraním API získat stránky a](/rest/api/storageservices/get-page-ranges) [přírůstkové kopírování blob](/rest/api/storageservices/incremental-copy-blob) API, které můžete použít pro snadné kopírování přírůstkových dat pro ZOTAVENÍ po havárii. 

Kromě toho mnoho podniků má kritické úlohy, které jsou již spuštěny v místních datových centrech. Pro migraci úlohy do cloudu, jedním z hlavních problémů by množství prostojů potřebných pro kopírování dat a riziko nepředvídaných problémů po přechodu. V mnoha případech může být prostoje showstopper pro migraci do cloudu. Pomocí objektů BLOB stránky REST API Azure řeší tento problém povolením migrace do cloudu s minimálním narušením kritických úloh. 

Příklady o tom, jak pořídit snímek a jak obnovit objekt blob stránky ze snímku, naleznete [v nastavení procesu zálohování pomocí článku přírůstkové snímky.](../../virtual-machines/windows/incremental-snapshots.md)
