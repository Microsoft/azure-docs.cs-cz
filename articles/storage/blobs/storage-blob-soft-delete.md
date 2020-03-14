---
title: Obnovitelné odstranění pro objekty blob Azure Storage | Microsoft Docs
description: Azure Storage teď nabízí obnovitelné odstranění objektů blob, abyste mohli snadněji obnovit data, když je omylem změnil nebo odstranila aplikace nebo jiný uživatel účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 360930b708d6358692de2af7325701b73d5cf9c9
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370555"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Obnovitelné odstranění pro objekty blob Azure Storage

Azure Storage teď nabízí obnovitelné odstranění objektů blob, abyste mohli snadněji obnovit data, když je omylem změnil nebo odstranila aplikace nebo jiný uživatel účtu úložiště.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Jak funguje obnovitelné odstranění

Když se tato možnost povolí, obnovitelné odstranění vám umožní ukládat a obnovovat data při odstraňování objektů BLOB nebo snímků objektů BLOB. Tato ochrana se rozšiřuje na data objektů blob, která se vymažou jako výsledek přepsání.

Při odstranění dat se místo trvalého vymazání přepne na stav neodstraněno. Když je obnovitelné odstranění zapnuté a přepíšete data, vygeneruje se měkký odstraněný snímek, který uloží stav přepsaných dat. Měkké odstraněné objekty jsou neviditelné, pokud nejsou výslovně uvedeny. Můžete nakonfigurovat dobu, po kterou bude možné obnovitelně odstraněná data obnovit, než jejich platnost trvale vyprší.

Obnovitelné odstranění je zpětně kompatibilní, takže nemusíte dělat žádné změny v aplikacích, abyste mohli využít výhod ochrany, kterou tato funkce nabízí. [Obnovení dat](#recovery) ale zavádí nové **neodstranitelné rozhraní API objektů BLOB** .

### <a name="configuration-settings"></a>Nastavení konfigurace

Při vytváření nového účtu je měkké odstranění ve výchozím nastavení vypnuté. U stávajících účtů úložiště je ve výchozím nastavení vypnuté i obnovitelné odstranění. Tuto funkci můžete kdykoli kdykoli během životnosti účtu úložiště zapnout nebo vypnout.

I když je tato funkce vypnutá, budete mít přístup k odstraněným datům a budete je moct obnovit. za předpokladu, že se při dříve zapnuté funkci uložila dočasná Odstraněná data. Když zapnete obnovitelné odstranění, musíte také nakonfigurovat dobu uchovávání.

Doba uchování označuje dobu, po kterou jsou data Odstraněná po uložení a dostupná pro obnovení. Pro objekty BLOB a snímky objektů blob, které se explicitně odstraní, se po odstranění dat začnou spouštět hodiny doby uchovávání. U neřízených odstraněných snímků generovaných funkcí tichého odstranění při přepisování dat se čas spustí při vygenerování snímku. V současné době můžete Odstraněná data uchovávat po dobu 1 až 365 dnů.

Dobu uchování obnovitelného odstranění můžete kdykoli změnit. Aktualizovaná doba uchovávání se bude vztahovat jenom na nově Odstraněná data. Vyprší platnost dříve odstraněných dat na základě doby uchování, která byla konfigurována při odstranění těchto dat. Pokus o odstranění nepodmíněného odstraněného objektu nebude mít vliv na jeho čas vypršení platnosti.

### <a name="saving-deleted-data"></a>Ukládání odstraněných dat

Obnovitelné odstranění zachová data v mnoha případech, kdy jsou objekty blob nebo snímky objektů BLOB odstraněny nebo přepsány.

Když je objekt BLOB přepsaný pomocí funkce **Put BLOB**, **Put**, **Put list**nebo **copy objektu** blob, je snímek stavu objektu BLOB před operací zápisu automaticky vygenerován. Tento snímek je neměkký odstraněný snímek; není viditelná, pokud nejsou explicitně odstraněné objekty výslovně uvedeny. V části věnované [obnovení](#recovery) se dozvíte, jak zobrazit seznam neodstraněných objektů.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. Když se B0 přepíše pomocí B1, vygeneruje se měkký odstraněný snímek B0. Když je B1 přepsána v B2, je vygenerována měkký odstraněný snímek B1.*

> [!NOTE]  
> Obnovitelné odstranění nabízí pouze přepis ochrany při operacích kopírování, pokud je zapnutý pro účet cílového objektu BLOB.

> [!NOTE]  
> Obnovitelné odstranění neumožňuje přepsat ochranu objektů BLOB v archivní úrovni. Pokud je objekt BLOB v archivu přepsaný novým objektem blob na jakékoli úrovni, přepsanému objektu BLOB se trvale vyprší platnost.

Při volání metody **Delete BLOB** pro snímek je tento snímek označený jako měkký odstraněný. Nový snímek se nevygeneruje.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. Když se zavolá **objekt BLOB snímku** , B0 se vytvoří jako snímek a B1 je aktivním stavem objektu BLOB. Po odstranění snímku B0 je označen jako měkký odstraněný.*

Když se **objekt BLOB pro odstranění** volá u základního objektu BLOB (jakýkoli objekt blob, který není sám snímkem), tento objekt BLOB je označený jako měkký odstraněný. V souladu s předchozím chováním volání funkce **Odstranit objekt BLOB** u objektu blob, který má aktivní snímky, vrátí chybu. Volání metody **Delete BLOB** u objektu BLOB s nejemnými odstraněnou snímků nevrátí chybu. Můžete přesto odstranit objekt BLOB a všechny jeho snímky v rámci jedné operace, když je zapnuté obnovitelné odstranění. Tím se vyznačuje základní objekt BLOB a snímky jako obnovitelné.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. Zde je provedeno volání **objektu BLOB Delete** pro odstranění B2 a všech přidružených snímků. Aktivní objekt blob, B2 a všechny přidružené snímky jsou označené jako měkké odstraněné.*

> [!NOTE]  
> Po přepsání nepodmíněného odstraněného objektu BLOB se automaticky vygeneruje měkký odstraněný snímek stavu objektu BLOB před operací zápisu. Nový objekt BLOB zdědí úroveň přepsaného objektu BLOB.

Obnovitelné odstranění neukládá vaše data v případě, že dojde k odstranění kontejneru nebo účtu, ani když jsou metadata objektů BLOB a vlastnosti objektů BLOB přepsána. K ochraně účtu úložiště před chybným odstraněním můžete nakonfigurovat zámek pomocí Azure Resource Manager. Další informace o tom, [Jak zabránit neočekávaným změnám](../../azure-resource-manager/management/lock-resources.md) , najdete v článku o Azure Resource Manager uzamčení prostředků.

Následující tabulka podrobně popisuje očekávané chování při zapnutí obnovitelného odstranění:

| Operace REST API | Typ prostředku | Popis | Změna chování |
|--------------------|---------------|-------------|--------------------|
| [Odstranění](/rest/api/storagerp/StorageAccounts/Delete) | Účet | Odstraní účet úložiště, včetně všech kontejnerů a objektů blob, které obsahuje.                           | Žádná změna. Kontejnery a objekty BLOB v odstraněném účtu nejde obnovit. |
| [Odstranit kontejner](/rest/api/storageservices/delete-container) | Kontejner | Odstraní kontejner včetně všech objektů blob, které obsahuje. | Žádná změna. Objekty BLOB v odstraněném kontejneru nejsou obnovitelné. |
| [Vložení objektu BLOB](/rest/api/storageservices/put-blob) | Blokování, připojení a objekty blob stránky | Vytvoří nový objekt BLOB nebo nahradí existující objekt BLOB v rámci kontejneru. | Pokud se použije k nahrazení existujícího objektu blob, automaticky se vygeneruje snímek stavu objektu BLOB před voláním. To platí také pro dříve odstraněné objekty blob, pokud jsou nahrazeny objektem BLOB stejného typu (blok, připojit nebo stránka). Pokud je nahrazen objektem BLOB jiného typu, všechna existující dočasná Odstraněná data budou trvale vypršet. |
| [Odstranit objekt BLOB](/rest/api/storageservices/delete-blob) | Blokování, připojení a objekty blob stránky | Označí objekt BLOB nebo snímek objektu BLOB pro odstranění. Objekt BLOB nebo snímek se později odstraní během uvolňování paměti. | Pokud se použije k odstranění snímku objektu blob, bude tento snímek označený jako měkký odstraněný. Při použití k odstranění objektu BLOB je tento objekt BLOB označený jako měkký odstraněný. |
| [Kopírovat objekt BLOB](/rest/api/storageservices/copy-blob) | Blokování, připojení a objekty blob stránky | Zkopíruje zdrojový objekt blob do cílového objektu BLOB ve stejném účtu úložiště nebo v jiném účtu úložiště. | Pokud se použije k nahrazení existujícího objektu blob, automaticky se vygeneruje snímek stavu objektu BLOB před voláním. To platí také pro dříve odstraněné objekty blob, pokud jsou nahrazeny objektem BLOB stejného typu (blok, připojit nebo stránka). Pokud je nahrazen objektem BLOB jiného typu, všechna existující dočasná Odstraněná data budou trvale vypršet. |
| [Blok vložení](/rest/api/storageservices/put-block) | Objekty blob bloku | Vytvoří nový blok, který bude potvrzen jako součást objektu blob bloku. | Pokud se použije k potvrzení bloku do objektu blob, který je aktivní, nedošlo k žádné změně. Pokud se použije k potvrzení bloku do objektu blob, který se dočasná odstraní, vytvoří se nový objekt BLOB a automaticky se vygeneruje snímek, který zachytí stav podmíněného odstraněného objektu BLOB. |
| [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list) | Objekty blob bloku | Potvrdí objekt BLOB zadáním sady identifikátorů ID bloků, které tvoří objekt blob bloku. | Pokud se použije k nahrazení existujícího objektu blob, automaticky se vygeneruje snímek stavu objektu BLOB před voláním. To platí i pro dřív odstraněný objekt blob, pokud je a jenom v případě, že se jedná o objekt blob bloku. Pokud je nahrazen objektem BLOB jiného typu, všechna existující dočasná Odstraněná data budou trvale vypršet. |
| [Vložit stránku](/rest/api/storageservices/put-page) | Objekty blob stránky | Zapisuje rozsah stránek do objektu blob stránky. | Žádná změna. Data objektu blob stránky, která jsou přepsána nebo vymazána pomocí této operace, nejsou uložena a nelze je obnovit. |
| [Připojit blok](/rest/api/storageservices/append-block) | Připojit objekty blob | Zapisuje blok dat na konec doplňovacího objektu BLOB. | Žádná změna. |
| [Nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties) | Blokování, připojení a objekty blob stránky | Nastaví hodnoty vlastností systému definované pro objekt BLOB. | Žádná změna. Přepsané vlastnosti objektu BLOB nelze obnovit. |
| [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata) | Blokování, připojení a objekty blob stránky | Nastaví uživatelsky definovaná metadata pro zadaný objekt BLOB jako jednu nebo více párů název-hodnota. | Žádná změna. Přepsaná metadata objektu BLOB nelze obnovit. |

Je důležité si všimnout, že volání "Put Page" na přepsání nebo vymazání rozsahů objektu blob stránky negeneruje automaticky snímky. Disky virtuálních počítačů se zálohují objekty blob stránky a používají **stránku Put** k zápisu dat.

### <a name="recovery"></a>Obnovení

Volání operace obnovení [objektu BLOB](/rest/api/storageservices/undelete-blob) na neodstraněném základním objektu BLOB obnoví a všechny přidružené měkké odstraněné snímky jako aktivní. Volání operace `Undelete Blob` na aktivním základním objektu BLOB obnoví všechny přidružené měkké odstraněné snímky jako aktivní. Když se snímky obnovují jako aktivní, vypadají jako uživatelem generované snímky; nepřepisují základní objekt BLOB.

Chcete-li obnovit objekt blob do konkrétního neodstraněného snímku, můžete volat `Undelete Blob` na základním objektu BLOB. Pak můžete snímek zkopírovat přes objekt BLOB nyní – aktivní. Snímek můžete také zkopírovat do nového objektu BLOB.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Měkké Odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Další data napsaná v poslední době se zobrazí pod staršími daty. V tomto případě se v objektu BLOB B volá příkaz **Undelete BLOB** , který obnovuje základní objekt blob, B1 a všechny přidružené snímky, a to jenom B0 jako aktivní. V druhém kroku se B0 zkopíruje přes základní objekt BLOB. Tato operace kopírování vygeneruje měkký odstraněný snímek B1.*

Pokud chcete zobrazit nepodmíněné odstraněné objekty BLOB a snímky objektů blob, můžete se rozhodnout zahrnout Odstraněná data do **seznamu objektů BLOB**. Můžete si vybrat, že se mají zobrazovat jenom obnovitelné odstraněné základní objekty blob, nebo taky zahrnout obnovitelné odstraněné snímky objektů BLOB. Pro všechna tichá Odstraněná data můžete zobrazit čas odstranění dat a počet dní, než budou data trvale vypršet.

### <a name="example"></a>Příklad

Následuje výstup konzoly skriptu .NET, který nahrává, Přepisuje, snímuje, odstraňuje a obnovuje objekt BLOB s názvem *Hello* , pokud je obnovitelné odstranění zapnuté:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

V části [Další kroky](#next-steps) najdete ukazatel na aplikaci, která vytvořila tento výstup.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechna tichá Odstraněná data se účtují stejnou sazbou jako aktivní data. Neúčtují se vám poplatky za data, která se po nakonfigurované době uchování trvale odstraní. Pro hlubší podrobně na snímky a jejich navýšení poplatků vám [pomůžou pochopit, jak se na snímcích účtují poplatky](storage-blob-snapshots.md).

Neúčtují se vám transakce týkající se automatického generování snímků. V případě operací zápisu se vám budou účtovat transakce **neodstraněného objektu BLOB** .

Další podrobnosti o cenách pro Azure Blob Storage obecně najdete na stránce s cenami za [azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Při prvotním zapnutí obnovitelného odstranění doporučujeme, abyste lépe porozuměli tomu, jak bude tato funkce mít na vyúčtování vliv.

## <a name="get-started"></a>Začínáme

Následující kroky ukazují, jak začít s obnovitelné odstraněním.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Povolit obnovitelné odstranění pro objekty BLOB v účtu úložiště pomocí Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte do možnosti **Ochrana dat** v části **BLOB Service**.

3. Klikněte na **Povolit** v části **BLOB – obnovitelné odstranění** .

4. Zadejte počet dní, po které chcete *zachovat* v části **zásady uchovávání informací** .

5. Kliknutím na tlačítko **Uložit** potvrďte nastavení ochrany dat.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Chcete-li zobrazit obnovitelné odstraněné objekty blob, zaškrtněte políčko **Zobrazit odstraněné objekty blob** .

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Pokud chcete pro daný objekt BLOB zobrazit obnovitelné odstraněné snímky, vyberte objekt BLOB a pak klikněte na **Zobrazit snímky**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Ujistěte se, že je zaškrtnuté políčko **Zobrazit odstraněné snímky** .

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Když kliknete na měkký odstraněný objekt BLOB nebo snímek, Všimněte si, že se zobrazí nové vlastnosti objektu BLOB. Označují, že se objekt odstranil, a kolik dní zbývá, dokud platnost snímku objektu BLOB nebo objektu BLOB neproběhne trvale. Pokud neměkký odstraněný objekt není snímkem, budete mít také možnost ho zrušit.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Pamatujte, že zrušení odstranění objektu BLOB také zruší odstranění všech přidružených snímků. Chcete-li obnovit odstraněné obnovitelné snímky pro aktivní objekt blob, klikněte na objekt BLOB a vyberte možnost zrušit **odstranění všech snímků**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po obnovení snímků objektu blob můžete kliknutím na **zvýšit úroveň** zkopírovat snímek přes kořenový objekt blob, čímž se obnoví objekt blob do snímku.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB. Následující příklad povoluje obnovitelné odstranění pro podmnožinu účtů v rámci předplatného:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Pomocí následujícího příkazu můžete ověřit, zda bylo toto obnovitelné odstranění zapnuté:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz redelete. Nezapomeňte, že volání **Undelete BLOB**, jak u aktivních, tak i u objektů BLOB s odstraněnými objekty, obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá Undelete u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Chcete-li najít aktuální zásady uchovávání informací o tichém odstranění, použijte následující příkaz:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-CLI)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Chcete-li ověřit, zda je funkce obnovitelné odstranění zapnutá, použijte následující příkaz: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů BLOB:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Chcete-li obnovit objekty blob, které byly omylem odstraněny, zavolejte u těchto objektů BLOB příkaz redelete. Nezapomeňte, že volání **Undelete BLOB**, jak u aktivních, tak i u objektů BLOB s odstraněnými objekty, obnoví všechny přidružené obnovitelné snímky odstraněné jako aktivní. Následující příklad volá Undelete u všech provizorních odstraněných a aktivních objektů BLOB v kontejneru:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Chcete-li provést obnovení na konkrétní verzi objektu blob, nejdříve zavolejte příkaz Undelete u objektu BLOB a potom zkopírujte požadovaný snímek do objektu BLOB. Následující příklad obnoví objekt blob bloku na jeho naposledy vygenerovaný snímek:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>Zvláštní upozornění

Pokud existuje možnost, že vaše data budou omylem upravována nebo odstraněna aplikací nebo jiným uživatelem účtu úložiště, doporučuje se zapnutí obnovitelného odstranění. Povolení obnovitelného odstranění často přepsaných dat může mít za následek zvýšené poplatky za kapacitu úložiště a vyšší latenci při výpisu objektů BLOB. Tyto dodatečné náklady a latence můžete zmírnit uložením často přepsaných dat do samostatného účtu úložiště, kde je deaktivované obnovitelné odstranění. 

## <a name="faq"></a>Nejčastější dotazy

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Pro které služby úložiště je možné použít obnovitelné odstranění?

V současné době je obnovitelné odstranění k dispozici pouze pro úložiště objektů BLOB (Object).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Je obnovitelné odstranění k dispozici pro všechny typy účtů úložiště?

Ano, obnovitelné odstranění je k dispozici pro účty úložiště BLOB a také pro objekty BLOB v účtech úložiště pro obecné účely (GPv1 a GPv2). Podporují se účty typu Standard a Premium. Obnovitelné odstranění je k dispozici pro nespravované disky, které jsou objekty blob stránky v rámci pokrývání. Obnovitelné odstranění není pro spravované disky k dispozici.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Je obnovitelné odstranění k dispozici pro všechny úrovně úložiště?

Ano, obnovitelné odstranění je k dispozici pro všechny úrovně úložiště, včetně horké, studené a archivní. Obnovitelné odstranění ale neumožňuje přepsat ochranu objektů BLOB v archivní úrovni.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Můžu použít nastavení rozhraní API vrstev objektů BLOB pro objekty blob vrstvy se měkkými odstraněnou snímků?

Ano. Měkké odstraněné snímky zůstanou v původní úrovni, ale základní objekt BLOB se přesune na novou úroveň. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Účty úložiště úrovně Premium mají omezení počtu snímků objektů blob na 100. Počítá se z tohoto limitu měkké odstraněné snímky?

Ne, měkké odstraněné snímky se nepočítají směrem k tomuto limitu.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Můžu u stávajících účtů úložiště zapnout obnovitelné odstranění?

Ano, obnovitelné odstranění lze konfigurovat pro stávající i nové účty úložiště.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Když odstraním celý účet nebo kontejner se zapnutým obnovitelném odstraněním, budou se všechny přidružené objekty blob ukládat?

Ne. Pokud odstraníte celý účet nebo kontejner, všechny přidružené objekty BLOB se trvale odstraní. Další informace o ochraně účtu úložiště před náhodným odstraněním najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávaným změnám](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Můžu zobrazit metriky kapacity pro Odstraněná data?

Dočasná Odstraněná data jsou součástí celkové kapacity účtu úložiště. Další informace o sledování a sledování kapacity úložiště najdete v tématu [Analýza úložiště](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Když odstraním obnovitelné odstranění, budu mít pořád přístup k Tichě odstraněným datům?

Ano, budete mít i nadále přístup k neplatným odstraněným datům a jejich obnovení, pokud je obnovitelné odstranění vypnuto.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Můžu číst a kopírovat obnovitelné odstraněné snímky objektu BLOB?  

Ano, ale v objektu BLOB musíte nejdřív zavolat příkaz Undelete.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Je obnovitelné odstranění k dispozici pro všechny typy objektů BLOB?

Ano, obnovitelné odstranění je k dispozici pro objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Je obnovitelné odstranění k dispozici pro disky virtuálních počítačů?  

Obnovitelné odstranění je k dispozici pro nespravované disky úrovně Premium i Standard, což jsou objekty blob stránky v rámci pokrývání. Obnovitelné odstranění vám pomůže jenom obnovit data odstraněná z operací **Odstranit objekt BLOB**, **Vložit objekt BLOB**, **umístit seznam blokovaných**objektů, **Vložit blok** a **Kopírovat objekt BLOB** . Data přepsaná voláním **stránky Put** nelze obnovit.

Virtuální počítač Azure se zapisuje na nespravovaný disk pomocí volání na **stránku Put**, takže pomocí obnovitelného odstranění vrátíte zpátky zápisy na nespravovaný disk z virtuálního počítače Azure, není podporovaný scénář.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Potřebuji změnit moje existující aplikace, aby používaly obnovitelné odstranění?

Je možné využít obnovitelné odstranění bez ohledu na verzi rozhraní API, kterou používáte. Pokud ale chcete vypsat a obnovit obnovitelné odstraněné objekty BLOB a snímky objektů blob, budete muset použít REST API nebo novější verze 2017-07-29 [služby Storage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) . Microsoft doporučuje vždy používat nejnovější verzi rozhraní Azure Storage API.

## <a name="next-steps"></a>Další kroky

* [Vzorový kód .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [REST API služby BLOB Service](/rest/api/storageservices/blob-service-rest-api)
* [Replikace Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Návrh aplikací s vysokou dostupností pomocí RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Zotavení po havárii a převzetí služeb při selhání účtu úložiště (Preview) v Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
