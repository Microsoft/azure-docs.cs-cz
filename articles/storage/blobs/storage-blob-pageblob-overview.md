---
title: Přehled objektů BLOB stránky Azure | Dokumentace Microsoftu
description: Přehled o objekty BLOB stránky Azure a jejich výhod, včetně případů použití se skripty.
services: storage
author: anasouma
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: wielriac
ms.component: blobs
ms.openlocfilehash: 6d1c443cfe3454d1b1e50a7270bd78598f69f6de
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063926"
---
# <a name="overview-of-azure-page-blobs"></a>Přehled objektů BLOB stránky Azure

Azure Storage nabízí tři typy úložiště objektů blob: Objekty BLOB bloku a doplňovacích objektů BLOB stránky objekty BLOB. Objekty BLOB bloku se skládají z bloků a jsou ideální pro ukládání textových nebo binárních souborů a pro nahrávání velkých souborů efektivně. Doplňovací objekty BLOB jsou také tvoří bloky, ale jsou optimalizované pro doplňovací operace. Díky tomu je ideální pro scénáře protokolování. Objekty BLOB stránky jsou tvořené 512 bajtů stránky do 8 TB, v celkové velikosti a jsou navržené pro operace časté náhodné čtení a zápisu. Objekty BLOB stránky jsou základem pro disky Azure IaaS. Tento článek se týká s vysvětlením, funkce a výhody objekty BLOB stránky.

Objekty BLOB stránky jsou kolekce stránek 512 bajtů, které poskytují možnost čtení/zápis libovolný rozsah bajtů. Objekty BLOB stránky jsou proto ideální pro ukládání indexu a zhuštěné datové struktury, jako je operační systém a datové disky pro virtuální počítače a databáze. Databáze SQL Azure například používá objekty BLOB stránky jako základní trvalého úložiště pro své databáze. Kromě toho objekty BLOB stránky se také často používají pro soubory s aktualizacemi založený na rozsahu.  

Klíčové funkce objektů BLOB stránky Azure jsou jeho rozhraní REST, odolnosti základního úložiště a možností zajistí tak bezproblémovou migraci do Azure. Tyto funkce jsou podrobněji popsány v následující části. Kromě toho jsou objekty BLOB stránky Azure aktuálně podporovány na dva typy úložiště: Storage úrovně Standard a Premium Storage. Storage úrovně Premium je určený speciálně pro úlohy vyžadující konzistentní vysoký výkon a nízkou latencí. Díky tomu objekty BLOB stránky úrovně premium ideální pro vysoce výkonné datové úložiště databáze.  Storage úrovně Standard je nákladově efektivní pro spouštění úloh v kterému latence nevadí.

## <a name="sample-use-cases"></a>Ukázka případy použití

Pojďme se podívat do ní několik případů použití pro objekty BLOB stránky, počínaje disky Azure IaaS. Objekty BLOB stránky Azure je páteří zajištění provozu virtuálních disků platform pro Azure IaaS. Operační systém Azure a datové disky jsou implementovány jako virtuální disky se data trvale zachována na platformě Azure Storage a pak doručí na virtuální počítače pro maximální výkon. Disky Azure jsou zachované v Hyper-V [Formát virtuálního pevného disku](https://technet.microsoft.com/library/dd979539.aspx) a uložené jako [objektů blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) ve službě Azure Storage. Kromě používání virtuální disky pro virtuální počítače Azure IaaS, objekty BLOB stránky také povolit PaaS a DBaaS scénářů, jako je služba Azure SQL DB, která momentálně používá objekty BLOB stránky pro ukládání dat SQL, povolení vysoká rychlost náhodného čtení a zápis operace pro databázi. Dalším příkladem může být, pokud máte službu PaaS sdílená média přístup pro úpravy aplikace pro spolupráci videa, objekty BLOB stránky Dal rychle získat přístup do náhodných umístění na médiu. Umožňuje také rychlé a efektivní úpravy a slučování stejná média víc uživatelů. 

První strany službám Microsoftu, jako je Azure Site Recovery, Azure Backup, stejně jako mnoho vývojáři třetích stran jste implementovali špičkové inovace pomocí rozhraní REST pro objekt blob stránky. Zde jsou některé jedinečné scénáře implementovat v Azure: 
* Správa aplikace řízené přírůstkový snímek: Aplikace můžou využívat snímky objektů blob stránky a rozhraní REST API pro ukládání kontrolní body aplikace bez dalších nákladů na nákladné duplikaci dat. Azure Storage podporuje místních snímků pro objekty BLOB stránky, které nevyžadují celý objekt blob kopírování. Tyto veřejné snímku rozhraní API také umožňují přístup a kopírování rozdíly mezi snímky.
* Migrace za provozu aplikace a data z místního cloudu: Kopírování dat v místním prostředí a pomocí REST API můžete zapisovat přímo do objektu blob stránky Azure, zatímco místního virtuálního počítače stále běží. Jakmile cíl zachytila, můžete rychle převzetí služeb při selhání virtuálního počítače Azure pomocí tato data. Tímto způsobem můžete migrovat virtuální počítače a virtuální disky z místního do cloudu s minimálními výpadky, protože migrace dat probíhá na pozadí, zatímco budete nadále používat tento virtuální počítač a výpadků potřebné pro převzetí služeb při selhání bude krátký (v minutách).
* [Na základě SAS](../common/storage-dotnet-shared-access-signature-part-1.md) sdílený přístup, která umožňuje scénáře, jako je několik čtenářů a jednoho zapisovače s podporou pro řízení souběžnosti.

## <a name="page-blob-features"></a>Funkce objektů blob stránky

### <a name="rest-api"></a>REST API
Si přečtěte následující dokument, abyste mohli začít s [vývoj pomocí objekty BLOB stránky](storage-dotnet-how-to-use-blobs.md). Jako příklad podívejte se na tom, jak přístup k objektům BLOB stránky pomocí klientské knihovny pro úložiště pro .NET. 

Následující diagram popisuje celkový vztahy mezi účtem, kontejnery a objekty BLOB stránky.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Vytvoření objektu blob prázdná stránka zadané velikosti
Chcete-li vytvořit objekt blob stránky, nejdřív vytvoříme **CloudBlobClient** objekt s základní identifikátor URI pro přístup k úložišti objektů blob pro účet úložiště (*pbaccount* obrázek 1) spolu s  **StorageCredentialsAccountAndKey** objektu, jak je znázorněno v následujícím příkladu. Příklad poté ukazuje vytvoření odkazu na **CloudBlobContainer** objektu a následného vytvoření kontejneru (*testvhds*) Pokud ještě neexistuje. Pak pomocí **CloudBlobContainer** objektu, vytvořit odkaz na **CloudPageBlob** objektu tak, že zadáte název objektu blob stránky (os4.vhd) přístup. Chcete-li vytvořit objekt blob stránky, zavolejte [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) předávajícího maximální velikost pro tento objekt blob k vytvoření. *BlobSize* musí být násobkem 512 bajtů.

```csharp
using Microsoft.WindowsAzure.StorageClient;
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

#### <a name="resizing-a-page-blob"></a>Změna velikosti objekt blob stránky
Chcete-li po vytvoření změnit velikost objektů blob stránky, použijte [změnit velikost](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) rozhraní API. Požadovaná velikost by měla být násobkem 512 bajtů.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Vytvoření stránek pro objekt blob stránky
Chcete-li zapsat stránky, použijte [CloudPageBlob.WritePages](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.beginwritepages?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_BeginWritePages_System_IO_Stream_System_Int64_System_String_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_System_AsyncCallback_System_Object_) metody.  To umožňuje zapisovat sekvenční sadu stránek až 4MBs. Posun do musí začínat na hranici 512 bajtů (startingOffset % 512 == 0) a ukončí se na hranici 512 - 1.  Následující příklad kódu ukazuje, jak volat **WritePages** pro objekt blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Jakmile požadavek na zápis pro sekvenční sadu stránek úspěšný ve službě blob service a je odolnost proti chybám a odolnosti replikuje, byla potvrzena zápisu a úspěchu se vrátí zpět do klienta.  

Následující diagram ukazuje 2 samostatné operace zápisu:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operace zápisu začínající na posunu 0 délky 1024 bajtů 
2.  Operace zápisu začínající na posunu 4096 délky 1 024 

#### <a name="reading-pages-from-a-page-blob"></a>Čtení stránek z objektů blob stránky
Chcete-li číst stránky, použijte [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) metodu za účelem čtení rozsah bajtů z objektů blob stránky. To umožňuje stáhnout úplnou objektu blob nebo velikost v bajtech od posunu v objektu blob. Při čtení, není potřeba spustit na více 512 posun. Při čtení bajtů ze stránky NUL, služba vrátí nula bajtů.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
Následující obrázek znázorňuje operaci čtení s BlobOffSet 256 a rangeSize 4352. Data vrácená je zvýrazněn oranžově. Nuly jsou vráceny na stránkách NUL

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Pokud máte řídce naplněných objektů blob můžete stáhnout pouze oblasti platný stránky, abyste neplatili za egressing nula bajtů a snižuje latenci stahování.  Chcete-li zjistit, které stránky se zálohují na data, použijte [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Potom můžete vytvořit výčet vrácené rozsahy a stáhnout data v každé oblasti. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Zapůjčení objektu blob stránky
Operace zapůjčení objektu Blob vytváří a spravuje zámek na objekt blob pro zápis a operace odstranění. Tato operace je užitečná v situacích, kde objekt blob stránky je přistupováno z několika klientů k zajištění, že pouze jednoho klienta může zapisovat do objektu blob v čase. Disky Azure, například využívá to zapůjčení mechanismus Ujistěte se, že disk spravuje jenom jeden virtuální počítač. Doba trvání uzamknutí může být 15 až 60 sekund, nebo může být nekonečné. Naleznete v dokumentaci k [tady](/rest/api/storageservices/lease-blob) další podrobnosti.

Kromě bohatá rozhraní API REST objekty BLOB stránky také nabízejí sdílený přístup, odolnost a zvýšené zabezpečení. Obsahuje tyto výhody podrobněji další odstavcích. 

### <a name="concurrent-access"></a>Souběžný přístup
Na stránce objekty BLOB rozhraní REST API a jeho koordinovala mechanismus umožňuje aplikacím přistupovat ke objekty blob stránky z několika klientů. Řekněme například, že budete muset sestavit distribuované cloudové služby, který sdílí úložiště objektů s více uživateli. Může to být webová aplikace obsluhující velkou kolekci imagí několika uživatelům. Jednou z možností pro implementaci to je použití virtuálního počítače s připojenými disky. Nevýhody tohoto stavu (i) omezení, že disk může být připojen pouze na jeden virtuální počítač proto omezit škálovatelnost, flexibilitu a zvýšení rizika. Pokud dojde k potížím s virtuálního počítače nebo služby spuštěné na virtuálním počítači, pak z důvodu zapůjčení, bitová kopie je nedostupné až do vypršení platnosti zapůjčení nebo dojde k přerušení; a (ii) další náklady s Virtuálním počítači IaaS. 

Alternativní možností je použít objekty BLOB stránky přímo prostřednictvím REST API služby Azure Storage. Tato možnost eliminuje potřebu nákladných virtuálních počítačů IaaS, nabízí úplnou flexibilitu přímý přístup z více klientů, zjednodušuje modelu nasazení classic tím, že tím eliminuje nutnost připojení a odpojení disků a odstraňuje riziko problémů ve virtuálním počítači. A nabízí stejnou úroveň výkonu pro operace náhodného čtení a zápisu jako disk

### <a name="durability-and-high-availability"></a>Odolnost a vysoká dostupnost
Standard a premium storage jsou odolných úložišť, ve kterém se vždy replikují zajistila stálost a vysoká dostupnost dat objektů blob stránky. Další informace o redundance úložiště Azure, najdete v tomto [dokumentaci](../common/storage-redundancy.md). Azure má konzistentně doručit odolnost na podnikové úrovni pro disky IaaS a stránce objekty BLOB, se špičkovou NULOVOU % [s hodinovou chybovost](https://en.wikipedia.org/wiki/Annualized_failure_rate). To znamená, že Azure ztratil nikdy data objektů blob stránky zákazníka. 

### <a name="seamless-migration-to-azure"></a>Bezproblémová migrace do azure
Pro zákazníky a vývojáře, kteří mají zájem o implementaci vlastní přizpůsobené řešení pro zálohování Azure také nabízí přírůstkových snímků, které obsahují pouze rozdíly. Tato funkce se vyhnete náklady na počáteční úplné kopie, což značně snižuje náklady na zálohování. Spolu se schopností efektivní čtení a zkopírujte rozdílová data Toto je další výkonné funkce, která umožňuje ještě větší inovace od vývojářů, což vede do řešení ve své třídě nejlepší zálohování a po havárii (DR) pro obnovení v Azure. Můžete nastavit vlastní zálohování a řešení zotavení po Havárii pro virtuální počítače v Azure s využitím [snímek objektu Blob](/rest/api/storageservices/snapshot-blob) spolu s [získání rozsahů stránek](/rest/api/storageservices/get-page-ranges) rozhraní API a [přírůstkové kopírování objektů Blob](/rest/api/storageservices/incremental-copy-blob) rozhraní API, které je možné pomocí snadno kopírovat přírůstkových dat pro zotavení po Havárii. 

Navíc mnoho podniků má důležité úlohy už běží v místních datových centrech. Pro úlohy migrace do cloudu, jeden z hlavních cílů by doba výpadku potřebné pro kopírování dat a rizika neočekávaných problémů po přepnutí. V mnoha případech může být výpadek showstopper pro migraci do cloudu. Na stránce rozhraní REST API pro objekty BLOB, Azure tento problém řeší tím, že migrace do cloudu s minimálním dopadem na nejdůležitější úlohy. 

Příklady na pořízení snímku a ze snímku obnovit objekt blob stránky, najdete [nastavení procesu zálohování pomocí přírůstkových snímků](../../virtual-machines/windows/incremental-snapshots.md) článku.
