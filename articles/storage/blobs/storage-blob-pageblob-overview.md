---
title: Přehled objektů blob stránky Azure | Microsoft Docs
description: Přehled objektů blob stránky Azure a jejich výhod, včetně případů použití s ukázkovými skripty.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 39c1972eba84f4f1990c87112c5801c386849640
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545954"
---
# <a name="overview-of-azure-page-blobs"></a>Přehled objektů blob stránky Azure

Azure Storage nabízí tři typy úložiště objektů BLOB: objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky. Objekty blob bloku se skládají z bloků a jsou ideální pro ukládání textových nebo binárních souborů a pro efektivní nahrávání velkých souborů. Doplňovací objekty BLOB se také skládají z bloků, ale jsou optimalizované pro operace připojení, které jsou ideální pro scénáře protokolování. Objekty blob stránky se skládají z 512 bajtů stránek o velikosti až 8 TB a jsou navržené pro časté operace náhodného čtení a zápisu. Objekty blob stránky jsou základem disků Azure IaaS. Tento článek se zaměřuje na vysvětlení funkcí a výhod objektů blob stránky.

Objekty blob stránky jsou kolekcí 512 stránek, které poskytují možnost číst a zapisovat libovolné rozsahy bajtů. Objekty blob stránky jsou proto ideální pro ukládání indexových a zhuštěných datových struktur, jako jsou operační systémy a datové disky pro Virtual Machines a databáze. Azure SQL DB například používá objekty blob stránky jako základní trvalé úložiště pro své databáze. Kromě toho se objekty blob stránky často používají také pro soubory s Range-Based aktualizacemi.  

Klíčové funkce objektů blob stránky Azure jsou rozhraní REST, odolnost základního úložiště a možnosti bezproblémové migrace do Azure. Tyto funkce jsou podrobněji popsány v následující části. Kromě toho se objekty blob stránky Azure v současné době podporují na dvou typech úložiště: Premium Storage a úložiště úrovně Standard. Premium Storage je navržená speciálně pro úlohy, které vyžadují konzistentní vysoký výkon a nízkou latenci vytváření objektů blob stránky úrovně Premium, které jsou ideální pro scénáře úložiště s vysokým výkonem. Účty úložiště úrovně Standard jsou cenově efektivnější pro spouštění úloh necitlivých na latenci.

## <a name="restrictions"></a>Omezení

Objekty blob stránky můžou používat jenom vrstvu **Hot** Access, ale nemůžou použít žádnou **z nich** ani jednu z těchto úrovní **archivu** . Další informace o úrovních přístupu najdete v tématu [úrovně přístupu pro Azure Blob Storage – horká, studená a archivní](storage-blob-storage-tiers.md).

## <a name="sample-use-cases"></a>Ukázkové případy použití

Pojďme si projednávat několik případů použití pro objekty blob stránky počínaje IaaS disky Azure. Objekty blob stránky Azure jsou páteřní platformou platformy virtuálních disků pro Azure IaaS. Operační systémy a datové disky Azure se implementují jako virtuální disky, kde se data trvale trvale na platformě Azure Storage a pak se doručí virtuálním počítačům pro maximální výkon. Disky Azure se ukládají ve [formátu virtuálního pevného disku](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10)) Hyper-V a ukládají se jako [objekt blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) v Azure Storage. Kromě používání virtuálních disků pro virtuální počítače Azure s IaaS můžou objekty blob stránky také povolit scénáře PaaS a DBaaS, jako je například služba Azure SQL DB, která aktuálně používá objekty blob stránky k ukládání dat SQL a umožňuje rychlé náhodné operace čtení a zápisu pro databázi. Dalším příkladem může být, že máte službu PaaS pro přístup ke sdílenému médiu pro aplikace pro úpravy videa pro spolupracovníky. objekty blob stránky umožňují rychlý přístup k náhodným umístěním v médiu. Umožňuje také rychlé a efektivní úpravy a sloučení stejného média více uživateli. 

Služby společnosti Microsoft, jako je například Azure Site Recovery, Azure Backup, i řada vývojářů třetích stran implementovala špičkové inovace pomocí rozhraní REST objektu BLOB. Následuje několik jedinečných scénářů implementovaných v Azure: 

* Správa přírůstkových snímků na základě aplikace: aplikace můžou využívat snímky objektů blob stránky a rozhraní REST API pro ukládání kontrolních bodů aplikace bez toho, aby se využívaly nákladné duplicity dat. Azure Storage podporuje místní snímky pro objekty blob stránky, které nevyžadují kopírování celého objektu BLOB. Tato rozhraní API pro veřejné snímky také umožňují přístup k rozdílům mezi snímky a jejich kopírování.
* Migrace aplikací a dat z místního prostředí do cloudu za provozu: Zkopírujte místní data a pomocí rozhraní REST API zapište přímo do objektu blob stránky Azure, zatímco místní virtuální počítač pokračuje v běhu. Jakmile se cíl zachytí, můžete rychle převzít služby při selhání virtuálního počítače Azure pomocí těchto dat. Tímto způsobem můžete migrovat virtuální počítače a virtuální disky z místního prostředí do cloudu s minimálními výpadky, protože migrace dat probíhá na pozadí, zatímco budete nadále používat virtuální počítač a výpadky potřebné pro převzetí služeb při selhání budou krátké (v minutách).
* Sdílený přístup [na základě SAS](../common/storage-sas-overview.md) , který umožňuje scénáře jako více čtenářů a jeden zapisovač s podporou pro řízení souběžnosti.

## <a name="pricing"></a>Ceny

Oba typy úložiště nabízené s objekty blob stránky mají svůj vlastní cenový model. Objekty blob stránky úrovně Premium se řídí cenovým modelem Managed disks, zatímco objekty blob stránky Standard se účtují podle použité velikosti a s každou transakcí. Další informace najdete na stránce s [cenami objektů blob stránky Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

## <a name="page-blob-features"></a>Funkce objektů blob stránky

### <a name="rest-api"></a>REST API

Pokud chcete začít s [vývojem pomocí objektů blob stránky](./storage-quickstart-blobs-dotnet.md), přečtěte si následující dokument. Příklad najdete v tématu Jak získat přístup k objektům blob stránky pomocí klientské knihovny pro úložiště pro .NET. 

Následující diagram popisuje celkové vztahy mezi účtem, kontejnery a objekty blob stránky.

![Snímek obrazovky znázorňující vztahy mezi účtem, kontejnery a objekty blob stránky](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Vytvoření prázdného objektu blob stránky zadané velikosti

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Nejprve získejte odkaz na kontejner. Chcete-li vytvořit objekt blob stránky, zavolejte metodu [GetPageBlobClient](/dotnet/api/azure.storage.blobs.specialized.specializedblobextensions.getpageblobclient) a potom zavolejte metodu [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) . Předejte maximální velikost objektu blob, který se má vytvořit. Tato velikost musí být násobkem 512 bajtů.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Pokud chcete vytvořit objekt blob stránky, nejdřív vytvoříme objekt **CloudBlobClient** se základním identifikátorem URI pro přístup k úložišti objektů BLOB pro váš účet úložiště (*pbaccount* na obrázku 1) společně s objektem **StorageCredentialsAccountAndKey** , jak je znázorněno v následujícím příkladu. Příklad následně ukazuje vytvoření odkazu na objekt **CloudBlobContainer** a pak vytvoření kontejneru (*testvhds*), pokud ještě neexistuje. Pak pomocí objektu **CloudBlobContainer** vytvořte odkaz na objekt **CloudPageBlob** zadáním názvu objektu blob stránky (OS4. VHD) k přístupu. Chcete-li vytvořit objekt blob stránky, zavolejte [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)a předejte maximální velikost objektu blob, který se má vytvořit. *BlobSize* musí být násobkem 512 bajtů.

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

---

#### <a name="resizing-a-page-blob"></a>Změna velikosti objektu blob stránky

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li po vytvoření změnit velikost objektu blob stránky, použijte metodu [Resize](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) . Požadovaná velikost by měla být násobkem 512 bajtů.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li po vytvoření změnit velikost objektu blob stránky, použijte metodu [Resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) . Požadovaná velikost by měla být násobkem 512 bajtů.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Zápis stránek do objektu blob stránky

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li zapisovat stránky, použijte metodu [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li zapisovat stránky, použijte metodu [CloudPageBlob. WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) .  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

To vám umožní napsat sekvenční sadu stránek až do 4MBs. Posunutí do zápisu musí být začínat na hranici 512 bajtů (startingOffset %512 = = 0) a končit na hranici 512-1. 

Jakmile je žádost o zápis pro sekvenční sadu stránek ve službě BLOB Service úspěšná a replikuje se na odolnost a odolnost, zápis se potvrdí a úspěch se vrátí zpátky klientovi.  

V níže uvedeném diagramu vidíte dvě samostatné operace zápisu:

![Diagram znázorňující dvě samostatné možnosti zápisu.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operace zápisu začínající na posunu 0 o délce 1024 bajtů 
2.  Operace zápisu začínající na posunu 4096 o délce 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Čtení stránek z objektu blob stránky

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li číst stránky, použijte metodu [PageBlobClient. download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) pro čtení rozsahu bajtů z objektu blob stránky. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li číst stránky, použijte metodu [CloudPageBlob. DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) pro čtení rozsahu bajtů z objektu blob stránky. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

Díky tomu můžete stáhnout celý objekt BLOB nebo rozsah bajtů od libovolného posunu v objektu BLOB. Při čtení se posun nemusí spouštět na násobku 512. Při čtení bajtů ze stránky NUL vrátí služba nenulové bajty.

Následující obrázek znázorňuje operaci čtení s posunem 256 a velikostí rozsahu 4352. Vrácená data jsou zvýrazněna oranžová. Pro stránky NUL jsou vraceny nuly.

![Diagram znázorňující operaci čtení s posunem 256 a velikostí rozsahu 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Pokud máte zhuštěný objekt blob, můžete chtít jenom stáhnout platné oblasti stránky, abyste se vyhnuli zaplacení na nenulové bajty a aby se snížila latence stahování.  

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li určit, které stránky jsou zálohovány daty, použijte [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges). Pak můžete zobrazit výčet vrácených rozsahů a stáhnout data v jednotlivých oblastech. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Chcete-li určit, které stránky jsou zálohovány daty, použijte [CloudPageBlob. GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). Pak můžete zobrazit výčet vrácených rozsahů a stáhnout data v jednotlivých oblastech. 

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

---

#### <a name="leasing-a-page-blob"></a>Zapůjčení objektu blob stránky

Operace zapůjčení objektu BLOB vytvoří a spravuje zámek objektu BLOB pro operace zápisu a odstranění. Tato operace je užitečná ve scénářích, kde je objekt blob stránky přistupný z více klientů, aby bylo možné současně zapisovat do objektu BLOB pouze jednoho klienta. Disky Azure například využívají tento mechanismus leasingu k tomu, aby se zajistilo, že se disk spravuje jenom pomocí jediného virtuálního počítače. Doba trvání zámku může být 15 až 60 sekund nebo může být nekonečná. Další podrobnosti najdete [v dokumentaci.](/rest/api/storageservices/lease-blob)

Kromě bohatých rozhraní REST API poskytují objekty blob stránky také sdílený přístup, odolnost a zvýšené zabezpečení. Tyto výhody budeme podrobněji pokrývat v dalších odstavcích. 

### <a name="concurrent-access"></a>Souběžný přístup

Objekty blob stránky REST API a její Leasingový mechanismus umožňují aplikacím přístup k objektu blob stránky z více klientů. Řekněme například, že potřebujete vytvořit distribuovanou cloudovou službu, která sdílí objekty úložiště s více uživateli. Může to být webová aplikace obsluhující velkou kolekci imagí několika uživatelům. Jednou z možností implementace této možnosti je použití virtuálního počítače s připojenými disky. Downsides to (i) omezení, že disk lze připojit pouze k jednomu virtuálnímu počítači a tím omezit škálovatelnost, flexibilitu a zvýšení rizik. Pokud dojde k potížím s virtuálním počítačem nebo službou běžící na virtuálním počítači, pak je bitová kopie nepřístupná, dokud nevyprší doba zapůjčení nebo dojde k přerušení. a (II) další náklady na virtuální počítač s IaaS. 

Alternativním parametrem je použití objektů blob stránky přímo přes Azure Storage rozhraní REST API. Tato možnost eliminuje potřebu nákladných virtuálních počítačů s IaaS, nabízí plnou flexibilitu přímého přístupu od více klientů, zjednodušuje model nasazení Classic tím, že eliminuje nutnost připojení a odpojení disků a eliminuje riziko problémů na virtuálním počítači. A poskytuje stejnou úroveň výkonu pro operace náhodného čtení a zápisu jako disk.

### <a name="durability-and-high-availability"></a>Odolnost a vysoká dostupnost

Úložiště úrovně Standard a Premium jsou odolné úložiště, ve kterém jsou data objektů blob stránky vždycky replikovaná, aby se zajistila odolnost a vysoká dostupnost. Další informace o Azure Storage redundanci najdete v této [dokumentaci](../common/storage-redundancy.md). Azure má konzistentně doručovat trvanlivost na podnikové úrovni pro disky IaaS a objekty blob stránky s špičkovou [roční mírou neúspěšného selhání](https://en.wikipedia.org/wiki/Annualized_failure_rate)v oboru.

### <a name="seamless-migration-to-azure"></a>Bezproblémové migrace do Azure

Zákazníkům a vývojářům, kteří mají zájem o implementaci vlastního vlastního řešení zálohování, nabízí Azure také přírůstkové snímky, které podrží jenom rozdíly. Tato funkce vylučuje náklady na počáteční úplnou kopii, která významně snižuje náklady na zálohování. Společně s možností efektivně číst a kopírovat rozdílová data je to další výkonná funkce, která umožňuje ještě více inovací od vývojářů, což vede k nejlepšímu prostředí pro zálohování a zotavení po havárii (DR) v Azure. Můžete nastavit vlastní řešení zálohování nebo zotavení po havárii pro virtuální počítače v Azure pomocí [snímku objektů BLOB](/rest/api/storageservices/snapshot-blob) spolu s rozhraním API [získat rozsahy stránek](/rest/api/storageservices/get-page-ranges) a [přírůstkové kopírování objektů BLOB](/rest/api/storageservices/incremental-copy-blob) , které můžete použít ke snadnému kopírování přírůstkových dat pro Dr. 

Mnoho podniků navíc má důležité úlohy, které už běží v místních datových centrech. Pro migraci zatížení do cloudu by jedno z hlavních otázek představovalo množství prostojů potřebných ke kopírování dat a riziko neočekávaných problémů po přepnutí. V mnoha případech může být výpadkem showstopper migrace do cloudu. Při použití REST API objektů blob stránky Azure tento problém řeší povolením migrace do cloudu s minimálním přerušením pro kritické úlohy. 

Příklady, jak pořídit snímek a jak obnovit objekt blob stránky ze snímku, najdete v článku [nastavení procesu zálohování pomocí přírůstkových snímků](../../virtual-machines/windows/incremental-snapshots.md) .