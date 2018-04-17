---
title: Jedinečné funkce objektů BLOB stránky Azure | Microsoft Docs
description: Přehled o objekty BLOB stránky Azure a jejich výhod, jako třeba případy použití s ukázkové skripty.
services: storage
author: anasouma
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: wielriac
ms.openlocfilehash: 5d1ad1555cb1e01e363456af5c50ecd090ce7147
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="unique-features-of-azure-page-blobs"></a>Jedinečné funkce objektů BLOB stránky Azure

Azure Storage nabízí tři typy úložiště objektů blob: objekty BLOB bloku a doplňovacích objektů BLOB a objekty BLOB stránky. Objekty BLOB bloku se skládá z bloků a jsou ideální pro ukládání textu nebo binárních souborů a efektivně nahrávání velkých souborů. Append – objekty BLOB jsou také tvoří bloky, ale jsou optimalizované pro doplňovací operace, což je ideální pro scénáře protokolování. Objekty BLOB stránky jsou tvořeny stránky o velikosti 512 bajtů 8 TB celkové velikosti a jsou určené pro operace časté náhodné čtení a zápisu. Objekty BLOB stránky jsou základem Azure IaaS disků. Tento článek se zaměřuje na vysvětlením funkce a výhody objekty BLOB stránky.

Objekty BLOB stránky jsou kolekce stránek 512 bajtů, které poskytují možnost čtení/zápisu libovolný rozsah bajtů. Proto jsou ideální pro ukládání dat na základě indexu a zhuštěné struktury jako operačního systému a datové disky pro virtuální počítače a databáze objekty BLOB stránky. Například Azure SQL DB používá objekty BLOB stránek jako základní trvalé úložiště pro jeho databáze. Kromě toho objekty BLOB stránky jsou také často používají pro soubory s aktualizacemi, na základě rozsahu.  

Klíčové funkce objektů BLOB stránky Azure jsou jeho rozhraní REST, odolnost základní úložiště a možnostech bezproblémové migrace do Azure. Tyto funkce jsou podrobněji popsány v následující části. Kromě toho jsou aktuálně podporovány objekty BLOB stránky Azure na dva typy úložiště: Storage úrovně Premium a Standard Storage. Storage úrovně Premium je určený speciálně pro procesy vyžadující konzistentní vysoký výkon a nízkou latencí díky objekty BLOB stránky premium ideální pro databáze vysoké původce datového úložiště.  Standardní úložiště je nákladově efektivnější pro spuštění úlohy kterému latence nevadí.

> [!WARNING]
> Objekty BLOB stránky v Storage úrovně Premium jsou navrženy pro použití jako virtuální pevné disky pouze. Společnost Microsoft nedoporučuje ukládání dalších typů dat v objekty BLOB stránky v Storage úrovně Premium, jak může být výrazně větší náklady. Používejte objekty BLOB bloku pro ukládání dat, která není na disku VHD.

## <a name="sample-use-cases"></a>Ukázka případy použití

Probereme několik případů použití pro objekty BLOB stránky počínaje disků Azure IaaS. Objekty BLOB stránky Azure jsou základem virtuální disky platformy pro Azure IaaS. Azure operačního systému a datové disky jsou implementované jako virtuální disky, kde data spolehlivě trvalá v platformě Azure Storage a pak doručí virtuálních počítačů pro maximální výkon. Disky systému Azure zůstávají v Hyper-V [formátu virtuálního pevného disku](https://technet.microsoft.com/library/dd979539.aspx) a uložené jako [objekt blob stránky](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) ve službě Azure Storage. Kromě používání virtuální disky pro virtuální počítače Azure IaaS, objekty BLOB stránky také povolit PaaS a DBaaS scénáře, jako je služba Azure SQL DB, která k ukládání dat SQL, povolení vysoká rychlost náhodného operace čtení a zápis pro databázi v současné době používá objekty BLOB stránky. Dalším příkladem může být, pokud máte PaaS služba pro přístup sdílená média pro spolupráci video aplikace pro úpravy, objekty BLOB stránky povolit přístup k náhodné umístění na médiu. Taky umožňuje rychlé a efektivní úpravy a slučování stejná média více uživatelů. 

První strany služby společnosti Microsoft, jako je Azure Site Recovery, Azure Backup, jakož i celá řada vývojářů třetích stran byla implementována špičkový inovace pomocí rozhraní REST objekt blob stránky. Zde jsou některé jedinečné scénáře implementovat v Azure: 
* Správu aplikací směrované přírůstkový snímek: aplikace můžou využívat snímky objekt blob stránky a rozhraní REST API pro ukládání aplikací kontrolní body, aniž by docházelo k nákladná duplicitních dat. Úložiště Azure podporuje místní snímky pro objekty BLOB stránky, které nevyžadují kopírování celý objekt blob. Tyto veřejné snímek rozhraní API také umožní přístup k informacím a kopírování rozdílů mezi snímky.
* Migrace aplikací a dat z místního do cloudu za provozu: místní data kopírovat a používat rozhraní REST API pro zápis přímo na objekt blob stránky Azure, zatímco místní virtuální počítač stále běží. Po cíl zachytila, můžete rychle převzetí služeb při selhání virtuálního počítače Azure pomocí tato data. Tímto způsobem můžete migrovat virtuální počítače a virtuální disky z místního do cloudu s minimálními výpadky, protože migrace dat probíhá na pozadí, když budete pokračovat v používání virtuálního počítače a výpadek potřebné pro převzetí služeb při selhání bude krátký (v minutách).
* [Na základě SAS](../common/storage-dotnet-shared-access-signature-part-1.md) sdíleného přístupu, která umožňuje různé scénáře jako více čtečky a jedním zapisovače s podporou pro řízení souběžnosti.

## <a name="page-blob-features"></a>Funkce objektů blob stránky

### <a name="rest-api"></a>REST API
V následujícím dokumentu začít pracovat s [vývoj pomocí objekty BLOB stránky](storage-dotnet-how-to-use-blobs.md). Jako příklad podívejte se na informace o přístupu k objekty BLOB stránky pomocí klientské knihovny pro úložiště pro .NET. 

Následující diagram popisuje celkové vztahy mezi účet, kontejnery a objekty BLOB stránky.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Vytvoření objektu blob prázdná stránka zadané velikosti
Chcete-li vytvořit objekt blob stránky, jsme nejprve vytvořit **CloudBlobClient** objekt s základní identifikátor URI pro přístup k úložišti objektů blob pro váš účet úložiště (*pbaccount* na obrázku 1) spolu s  **StorageCredentialsAccountAndKey** objektu, jak je znázorněno v následujícím příkladu. V příkladu poté zobrazí odkaz na vytváření **CloudBlobContainer** objektu a pak vytvořit kontejner (*testvhds*) Pokud ještě neexistuje. Pak pomocí **CloudBlobContainer** objektu, vytvořit odkaz na **CloudPageBlob** objekt zadáním názvu objektu blob stránky (os4.vhd) k přístupu. Chcete-li vytvořit objekt blob stránky, volejte [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) předávání v maximální velikosti pro vytvoření objektu blob. *BlobSize* musí být násobkem 512 bajtů.

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
Chcete-li po vytvoření změnit velikost objekt blob stránky, použijte [změnit velikost](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) rozhraní API. Požadovaná velikost musí být násobkem 512 bajtů.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Vytvoření stránek pro objekt blob stránky
K vytvoření stránky, použijte [CloudPageBlob.WritePages](/library/microsoft.windowsazure.storageclient.cloudpageblob.writepages.aspx) metoda.  To umožňuje zapisovat sekvenční sadu stránek až 4MBs. Posun zapisuje do musí začínat na hranici 512 bajtů (startingOffset % 512 == 0) a ukončí se na hranici 512 - 1.  Následující příklad kódu ukazuje, jak volat **WritePages** pro objekt blob:

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Jakmile žádost o zápis pro sekvenční sada stránek ve službě blob je úspěšné a se replikují pro odolnost a odolnost proti chybám, byla potvrzena zápis a úspěch se vrátí zpět do klienta.  

Následující diagram ukazuje 2 samostatné operací zápisu:

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Operace zápisu začínající na posunu 0 délky 1024 bajtů 
2.  Operace zápisu začínající na posunu 4096 délky 1024 

#### <a name="reading-pages-from-a-page-blob"></a>Čtení stránek z objekt blob stránky
Chcete-li číst stránky, použijte [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) metoda čtení rozsah bajtů z objektů blob stránky. To umožňuje stáhnout úplný objektů blob nebo rozsah bajtů od všech posun v objektu blob. Při čtení, není nutné spustit v násobkem 512 posun. Při čtení bajtů z stránky NUL, vrátí službu 0 bajtů.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
Následující obrázek znázorňuje operace čtení s BlobOffSet 256 a rangeSize 4352. Data vrácená je označený na oranžová. Pro stránky NUL jsou vráceny nulami

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Pokud máte řídce vyplněná objektů blob, můžete stáhnout pouze oblasti platný stránky předejdete platícího pro egressing nula bajtů a snižování latence stahování.  Chcete-li zjistit, které stránky jsou založenou na datech, použijte [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Můžete vytvořit výčet vrácený rozsahy a stahování dat v každém rozsahu. 
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

#### <a name="leasing-a-page-blob"></a>Leasing objekt blob stránky
Objekt Blob zapůjčení operaci vytváří a spravuje zámku na objektu blob pro zápis a operace odstranění. Tato operace je užitečný ve scénářích, kde se objekt blob stránky přistupuje od více klientů k zajištění, že pouze jednoho klienta může zapsat do objektu blob v čase. Disky systému Azure, například využívá to leasing mechanismus pro Ujistěte se, že disk je jenom jeden virtuální počítač spravuje. Doba trvání uzamčení může být 15 až 60 sekund, nebo může být nekonečná. Najdete v dokumentaci [sem](/rest/api/storageservices/lease-blob) další podrobnosti.

> Pomocí následujícího odkazu zobrazíte [ukázky kódu](/resources/samples/?service=storage&term=blob&sort=0 ) pro mnoho scénářů jiné aplikace. 

Objekty BLOB stránky kromě bohatá rozhraní API REST, zadejte také sdíleného přístupu, odolnost a rozšířené zabezpečení. Obsahuje tyto výhody podrobněji v další odstavcích. 

### <a name="concurrent-access"></a>Souběžný přístup
Na stránku objekty BLOB REST API a jeho mechanismus "pronájmu" umožňuje aplikacím pro přístup z více klientů objekt blob stránky. Řekněme například, že potřebujete k vytváření distribuované cloudové služby, který sdílí úložiště objektů s více uživateli. Může to být webové aplikace slouží velké kolekce obrázků na několik uživatelů. Jednou z možností pro implementaci to je pro použití s připojené disky virtuálního počítače. Downsides tento zahrnout (i), disk může být připojen pouze pro jeden virtuální počítač a omezení škálovatelnost, flexibilitu tak zvýšit rizika omezení. Pokud dojde k potížím s virtuální počítač nebo služba spuštěna na virtuálním počítači, z důvodu zapůjčení, bitovou kopii je nepřístupný dokud zapůjčení vyprší platnost, nebo je poškozená; a (ii) další náklady na použití virtuálních počítačů IaaS. 

Alternativní možností je použít objekty BLOB stránky přímo prostřednictvím rozhraní API REST úložiště Azure. Tato možnost se eliminuje potřeba nákladná virtuální počítače IaaS, nabízí úplnou flexibilitu přímý přístup z více klientů, zjednodušuje modelu nasazení classic tím, takže není nutné připojit a odpojit disky a eliminuje riziko problémů ve virtuálním počítači. A poskytuje stejnou úroveň výkonu pro náhodné čtení/zápisu operace jako disk

### <a name="durability-and-high-availability"></a>Odolnost a vysokou dostupnost
Úložiště Standard a premium jsou odolná úložiště, kde se vždy replikují data objektů blob stránky zajistit odolnost a vysokou dostupnost. Další informace o redundance úložiště Azure najdete [dokumentaci](../common/storage-redundancy.md). Azure má konzistentně doručit odolnost podnikové úrovni pro IaaS disky a stránka objektů BLOB s špičkový NULOVÉ % [rozpočítat na roční období míra selhání](https://en.wikipedia.org/wiki/Annualized_failure_rate). To znamená Azure ztratil nikdy data objektů blob stránky zákazníka. 

### <a name="seamless-migration-to-azure"></a>Bezproblémovou migraci na azure
Pro zákazníky a vývojáře, kteří mají zájem o implementaci vlastní přizpůsobená řešení pro zálohování Azure nabízí i přírůstkové snímky, které obsahují pouze se rozdíly. Tato funkce zabraňuje náklady na počáteční úplná kopie, což značně snižuje náklady na zálohování. Spolu se schopností efektivně pro čtení a zkopírujte rozdílová data je to jiné výkonné funkci, která umožňuje i další inovace od vývojářů, což v rozhraní třídy nejvhodnější zálohování a po havárii (DR) obnovení v Azure. Můžete nastavit vlastní zálohování nebo řešení pro zotavení po Havárii pro virtuální počítače v Azure pomocí [Blob snímku](/rest/api/storageservices/snapshot-blob) spolu s [rozsahů stránek Get](/rest/api/storageservices/get-page-ranges) rozhraní API a [přírůstkové kopie Blob](/rest/api/storageservices/incremental-copy-blob) rozhraní API, které můžete používejte pro snadno kopírování přírůstkových dat pro zotavení po Havárii. 

Kromě toho mnoho podniků má kritickým úlohám, které už běží v místních datových center. Pro migraci úloh do cloudu, jedním z hlavních otázky by doba potřebná pro kopírování dat a riziko neočekávaných problémů po přepnutí výpadku. V mnoha případech může být výpadek showstopper pro migraci do cloudu. Pomocí stránky objekty BLOB REST API, Azure tento problém řeší tím, že umožňuje migrace do cloudu s minimálním dopadem na kritické úlohy. 

Příklady na postup pořízení snímku a jak obnovit objekt blob stránky ze snímku, naleznete [nastavení záložní proces, který používá přírůstkové snímky](../../virtual-machines/windows/incremental-snapshots.md) článku.
