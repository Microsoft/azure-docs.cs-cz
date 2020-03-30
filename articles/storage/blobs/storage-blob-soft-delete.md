---
title: Obnovitelné odstranění pro objekty BLOB úložiště Azure | Dokumenty společnosti Microsoft
description: Azure Storage now offers soft delete for blob objects so that you can more easily recover your data when it is erroneously modified or deleted by an application or other storage account user.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4deae235ed15d02874ab5cb3470c62e934324364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234291"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Obnovitelné odstranění objektů blob služby Azure Storage

Azure Storage now offers soft delete for blob objects so that you can more easily recover your data when it is erroneously modified or deleted by an application or other storage account user.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Jak funguje obnovitelné odstranění

Pokud je povoleno, měkké odstranění umožňuje uložit a obnovit data při odstranění objektů blob nebo snímků objektů blob. Tato ochrana se vztahuje na data objektu blob, která se vymaže v důsledku přepsání.

Když jsou data odstraněna, přejde do obnovitelného odstraněného stavu namísto trvalého vymazání. Když je zapnuto měkké odstranění a přepíšete data, je generován měkký odstraněný snímek, který uloží stav přepsaných dat. Obnovitelné odstraněné objekty jsou neviditelné, pokud nejsou výslovně uvedeny. Můžete nakonfigurovat dobu, po kterou bude možné obnovitelně odstraněná data obnovit, než jejich platnost trvale vyprší.

Obnovitelné odstranění je zpětně kompatibilní, takže nemusíte provádět žádné změny v aplikacích, abyste mohli využít ochrany, kterou tato funkce poskytuje. Obnovení [dat](#recovery) však zavádí nové **rozhraní API undelete blob.**

### <a name="configuration-settings"></a>Nastavení konfigurace

Při vytváření nového účtu je obnovitelné odstranění ve výchozím nastavení vypnuto. Obnovitelné odstranění je ve výchozím nastavení vypnuto také pro existující účty úložiště. Funkci můžete kdykoli během životnosti účtu úložiště zapnout a vypnout.

Stále budete mít přístup a obnovit obnovitelná odstraněná data, když je funkce vypnutá, za předpokladu, že měkká odstraněná data byla uložena, když byla tato funkce dříve zapnuta. Když zapnete měkké odstranění, musíte také nakonfigurovat dobu uchování.

Doba uchování označuje dobu, po kterou jsou data smazaná softwarem uložena a k dispozici pro obnovení. Pro objekty BLOB a snímky objektů blob, které jsou explicitně odstraněny, doba uchovávání hodin se spustí při odstranění dat. Pro obnovitelné odstraněné snímky generované funkcí obnovitelného odstranění při přepsání dat se hodiny spustí při generování snímku. V současné době můžete uchovávat smazaná data pro obnovitelné smazané údaje po dobu 1 až 365 dnů.

Dobu uchovávání měkkých odstranění můžete kdykoli změnit. Aktualizované období uchovávání se bude vztahovat pouze na nově smazané údaje. Dříve odstraněná data vyprší na základě doby uchování, která byla nakonfigurována při odstranění dat. Pokus o odstranění obnovitelného odstraněného objektu neovlivní jeho dobu vypršení platnosti.

### <a name="saving-deleted-data"></a>Ukládání odstraněných dat

Obnovitelné odstranění zachová vaše data v mnoha případech, kdy jsou odstraněny nebo přepsány snímky objektů blob nebo objektů blob.

Při přepsání objektu blob pomocí **Objektu blob Put**, **Put Block**, Put **Block List**nebo Kopírovat **objekt blob** je automaticky generován snímek stavu objektu blob před operací zápisu. Tento snímek je snímek obnovitelné odstraněné; je neviditelný, pokud nejsou explicitně uvedeny obnovitelné odstraněné objekty. Informace o tom, jak zobrazit seznam nematických odstraněných objektů, najdete v části [Obnovení.](#recovery)

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Měkká odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Novější písemná data se zobrazují pod staršími daty. Při přepsání B0 s B1, je generován měkký odstraněný snímek B0. Při přepsání B1 s B2, je generován měkký odstraněný snímek B1.*

> [!NOTE]  
> Obnovitelné odstranění poskytuje pouze ochranu proti přepsání operací kopírování, pokud je zapnuto pro účet cílového objektu blob.

> [!NOTE]  
> Obnovitelné odstranění neposkytuje ochranu proti přepsání objektů BLOB v archivní vrstvě. Pokud je objekt blob v archivu přepsán novým objektem blob v libovolné vrstvě, přepsaný objekt blob trvale vyprší.

Při **odstranění objektu blob** je volána na snímek, tento snímek je označen jako obnovitelné odstraněn. Nový snímek není generován.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Měkká odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Novější písemná data se zobrazují pod staršími daty. Při **volání objektu blob snímek** B0 se stane snímek a B1 je aktivní stav objektu blob. Při odstranění snímku B0 je označen jako obnovitelné odstranění.*

Při **odstranění objektu blob** se nazývá na základní objekt blob (jakýkoli objekt blob, který není sám snímek), tento objekt blob je označen jako obnovitelné odstraněn. V souladu s předchozím chováním volání **odstranit objekt blob** na objekt blob, který má aktivní snímky vrátí chybu. Volání **odstranit objekt blob** na objekt blob s obnovitelné odstraněné snímky nevrátí chybu. Stále můžete odstranit objekt blob a všechny jeho snímky v jedné operaci, když je zapnuto měkké odstranění. Tím označíte základní objekt blob a snímky jako obnovitelné odstranění.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Měkká odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Novější písemná data se zobrazují pod staršími daty. Zde odstranit volání **objektu blob** se provádí k odstranění B2 a všechny přidružené snímky. Aktivní objekt blob, B2 a všechny přidružené snímky jsou označeny jako obnovitelné odstraněné.*

> [!NOTE]  
> Při přepsání obnovitelného odstraněného objektu blob se automaticky vygeneruje neodstraněný odstraněný snímek stavu objektu blob před operací zápisu. Nový objekt blob zdědí vrstvu přepsaného objektu blob.

Obnovitelné odstranění neukládá data v případě odstranění kontejneru nebo účtu, ani při přepsání metadat objektu blob a vlastností objektů blob. Chcete-li chránit účet úložiště před chybným odstraněním, můžete nakonfigurovat zámek pomocí Správce prostředků Azure. Další informace najdete v článku Správce prostředků Azure [Uzamknout prostředky, abyste zabránili neočekávaným změnám.](../../azure-resource-manager/management/lock-resources.md)

Následující tabulka podrobně popisuje očekávané chování při zapnutém softwarovém odstranění:

| Operace rozhraní REST API | Typ prostředku | Popis | Změna chování |
|--------------------|---------------|-------------|--------------------|
| [Odstranit](/rest/api/storagerp/StorageAccounts/Delete) | Účet | Odstraní účet úložiště, včetně všech kontejnerů a objektů BLOB, které obsahuje.                           | Žádná změna. Kontejnery a objekty BLOB v odstraněném účtu nelze obnovit. |
| [Odstranění kontejneru](/rest/api/storageservices/delete-container) | Kontejner | Odstraní kontejner, včetně všech objektů BLOB, které obsahuje. | Žádná změna. Objekty BLOB v odstraněném kontejneru nelze obnovit. |
| [Vložení objektu blob](/rest/api/storageservices/put-blob) | Objekty BLOB pro blokování, připojení a stránku | Vytvoří nový objekt blob nebo nahradí existující objekt blob v kontejneru | Pokud se používá k nahrazení existující objekt blob, snímek stavu objektu blob před voláním se automaticky vygeneruje. To platí také pro dříve obnovitelné odstraněné objekt blob, pokud a pouze v případě, že je nahrazen objektem blob stejného typu (Blok, Připojit nebo Page). Pokud je nahrazen objektem blob jiného typu, všechna existující obnovitelné odstraněná data trvale vyprší. |
| [Odstranění objektu blob](/rest/api/storageservices/delete-blob) | Objekty BLOB pro blokování, připojení a stránku | Označí snímek objektu blob nebo objektu blob k odstranění. Objekt blob nebo snímek je později odstraněn během uvolňování paměti | Pokud se používá k odstranění snímku objektu blob, tento snímek je označen jako obnovitelné odstraněné. Pokud se používá k odstranění objektu blob, tento objekt blob je označen jako obnovitelné odstranění. |
| [Zkopírování objektu blob](/rest/api/storageservices/copy-blob) | Objekty BLOB pro blokování, připojení a stránku | Zkopíruje zdrojový objekt blob do cílového objektu blob ve stejném účtu úložiště nebo v jiném účtu úložiště. | Pokud se používá k nahrazení existující objekt blob, snímek stavu objektu blob před voláním se automaticky vygeneruje. To platí také pro dříve obnovitelné odstraněné objekt blob, pokud a pouze v případě, že je nahrazen objektem blob stejného typu (Blok, Připojit nebo Page). Pokud je nahrazen objektem blob jiného typu, všechna existující obnovitelné odstraněná data trvale vyprší. |
| [Umístit blok](/rest/api/storageservices/put-block) | Objekty blob bloku | Vytvoří nový blok, který má být potvrzen jako součást objektu blob bloku. | Pokud se používá k potvrzení bloku do objektu blob, který je aktivní, neexistuje žádná změna. Pokud se používá k potvrzení bloku do objektu blob, který je obnovitelné odstraněn, vytvoří se nový objekt blob a snímek se automaticky vygeneruje, aby zachytil stav obnovitelného odstraněného objektu blob. |
| [Seznam blokovaných položek](/rest/api/storageservices/put-block-list) | Objekty blob bloku | Potvrdí objekt blob zadáním sady ID bloků, které tvoří objekt blob bloku. | Pokud se používá k nahrazení existující objekt blob, snímek stavu objektu blob před voláním se automaticky vygeneruje. To platí také pro dříve obnovitelné odstraněné objekt blob, pokud a pouze v případě, že je objekt blob bloku. Pokud je nahrazen objektem blob jiného typu, všechna existující obnovitelné odstraněná data trvale vyprší. |
| [Umístit stránku](/rest/api/storageservices/put-page) | Objekty blob stránky | Zapíše rozsah stránek do objektu blob stránky. | Žádná změna. Data objektu blob stránky, která jsou přepsána nebo vymazána pomocí této operace, nejsou uložena a nelze je obnovit. |
| [Připojit blok](/rest/api/storageservices/append-block) | Doplňovací objekty blob | Zapíše blok dat na konec objektu blob připojit | Žádná změna. |
| [Nastavení vlastností objektu blob](/rest/api/storageservices/set-blob-properties) | Objekty BLOB pro blokování, připojení a stránku | Nastaví hodnoty pro vlastnosti systému definované pro objekt blob. | Žádná změna. Přepsané vlastnosti objektu blob nelze obnovit. |
| [Nastavení metadat objektu blob](/rest/api/storageservices/set-blob-metadata) | Objekty BLOB pro blokování, připojení a stránku | Nastaví uživatelem definovaná metadata pro zadaný objekt blob jako jeden nebo více párů název-hodnota. | Žádná změna. Přepsání metadat blob nelze obnovit. |

Je důležité si všimnout, že volání "Put Page" přepsat nebo vymazat rozsahy objektu blob stránky nebude automaticky generovat snímky. Disky virtuálních počítačů jsou zálohovány objekty BLOB stránky a k zápisu dat použijte **put page.**

### <a name="recovery"></a>Obnovení

Volání operace [Undelete Blob](/rest/api/storageservices/undelete-blob) na obnovitelné mablob odstraněné obnoví ji a všechny přidružené obnovitelné odstraněné snímky jako aktivní. Volání `Undelete Blob` operace na aktivní základní objekt blob obnoví všechny přidružené obnovitelné odstraněné snímky jako aktivní. Když jsou snímky obnoveny jako aktivní, vypadají jako snímky generované uživatelem; nepřepíší základní objekt blob.

Chcete-li obnovit objekt blob na konkrétní `Undelete Blob` měkký odstraněný snímek, můžete volat na základní objekt blob. Potom můžete zkopírovat snímek přes objekt blob nyní aktivní. Snímek můžete také zkopírovat do nového objektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Měkká odstraněná data jsou šedá, zatímco aktivní data jsou modrá. Novější písemná data se zobrazují pod staršími daty. Zde **undelete blob** se nazývá na objekt blob B, čímž se obnoví základní objekt blob, B1 a všechny přidružené snímky, zde pouze B0, jako aktivní. Ve druhém kroku b0 je zkopírován přes základní objekt blob. Tato operace kopírování generuje obnovitelné odstraněný snímek B1.*

Chcete-li zobrazit obnovitelné odstraněné objekty BLOB a snímky objektů BLOB, můžete zahrnout odstraněná data do **objektů BLOB seznamu**. Můžete zobrazit pouze obnovitelné odstraněné základní objekty BLOB nebo zahrnout také obnovitelné odstraněné snímky objektů blob. U všech dat smazaných z měkkého hesla můžete zobrazit čas, kdy byla data odstraněna, a také počet dní před trvalým vypršením platnosti dat.

### <a name="example"></a>Příklad

Následuje výstup konzoly skriptu .NET, který nahrává, přepisuje, snímky, odstraňuje a obnovuje objekt blob s názvem *HelloWorld,* když je zapnuto měkké odstranění:

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

Viz [další kroky](#next-steps) části ukazatel na aplikaci, která vyrábí tento výstup.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechna data smazaná z měkkého úložiště se účtují stejnou rychlostí jako aktivní data. Za data, která jsou trvale odstraněna po nakonfigurované době uchovávání, vám nebudou účtovány poplatky. Pro hlubší prokliky a jak se jim účtují poplatky, viz [Principy, jak snímky časově rozrůstají poplatky](storage-blob-snapshots.md).

Transakce související s automatickým generováním snímků se vám nebudou účtovat. Transakce **undelete blob** se vám budou účtovat ve sazbě pro operace zápisu.

Další podrobnosti o cenách azure blob storage obecně, podívejte se na [stránku ceny úložiště objektů blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Když zpočátku zapnete měkké odstranění, doporučujeme použít malou dobu uchovávání, abyste lépe pochopili, jak tato funkce ovlivní vaši fakturu.

## <a name="get-started"></a>Začínáme

Následující kroky ukazují, jak začít pomocí obnovitelného odstranění.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Povolte obnovitelné odstranění objektů BLOB na vašem účtu úložiště pomocí portálu Azure:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte svůj účet úložiště. 

2. Přejděte na možnost **Ochrana dat** v části Služba **objektů blob**.

3. V části **Blob soft delete** klikněte na **Povoleno.**

4. Zadejte počet dní, které si chcete *zachovat v* části **Zásady uchovávání informací.**

5. Zvolte tlačítko **Uložit,** chcete-li potvrdit nastavení ochrany dat.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Chcete-li zobrazit obnovitelné odstraněné objekty BLOB, zaškrtněte políčko **Zobrazit odstraněné objekty BLOB.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Chcete-li zobrazit obnovitelné odstraněné snímky pro daný objekt blob, vyberte objekt blob a klepněte na tlačítko **Zobrazit snímky**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Ujistěte se, že je zaškrtnuté políčko **Zobrazit odstraněné snímky.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Po kliknutí na obnovitelné odstraněné objekt blob nebo snímek, všimněte si nové vlastnosti objektu blob. Označují, kdy byl objekt odstraněn a kolik dní zbývá, dokud trvale vyprší platnost snímku objektu blob nebo objektu blob. Pokud měkký odstraněný objekt není snímek, budete mít také možnost jej obnovit.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Nezapomeňte, že zrušení odstranění objektu blob také odevní všechny přidružené snímky. Chcete-li obnovit obnovitelné odstraněné snímky pro aktivní objekt blob, klikněte na objekt blob a vyberte **Zrušit odstranění všech snímků**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Po zrušení snímky objektu blob, můžete klepnout na **tlačítko Zvýšit** zkopírovat snímek přes objekt blob kořenového adresáře, a tím obnovení objektu blob na snímek.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektu blob. Následující příklad umožňuje obnovitelné odstranění pro podmnožinu účtů v předplatném:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Pomocí následujícího příkazu můžete ověřit, zda bylo měkké odstranění zapnuto:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Chcete-li obnovit objekty BLOB, které byly omylem odstraněny, volejte Undelete na těchto objektech BLOB. Nezapomeňte, že volání **undelete blob**, na aktivní i obnovitelné odstraněné objekty BLOB, obnoví všechny přidružené obnovitelné odstraněné snímky jako aktivní. Následující příklad volá Undelete na všechny obnovitelné odstraněné a aktivní objekty BLOB v kontejneru:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Chcete-li najít aktuální zásady uchovávání informací s měkkým odstraněním, použijte následující příkaz:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[Cli](#tab/azure-CLI)

Chcete-li povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektu blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Chcete-li ověřit, zda je měkké odstranění zapnuto, použijte následující příkaz: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Chcete-li povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektu blob:

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

Chcete-li povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektu blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Chcete-li obnovit objekty BLOB, které byly omylem odstraněny, volejte Undelete na těchto objektech BLOB. Nezapomeňte, že volání **undelete blob**, na aktivní i obnovitelné odstraněné objekty BLOB, obnoví všechny přidružené obnovitelné odstraněné snímky jako aktivní. Následující příklad volá Undelete na všechny obnovitelné odstraněné a aktivní objekty BLOB v kontejneru:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Chcete-li obnovit konkrétní verzi objektu blob, nejprve volání Undelete na objekt blob, pak zkopírujte požadovaný snímek přes objekt blob. Následující příklad obnoví objekt blob bloku na jeho naposledy generovaný snímek:

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

## <a name="special-considerations"></a>Zvláštní aspekty

Pokud existuje možnost, že vaše data omylem změní nebo odstraní aplikace nebo jiný uživatel účtu úložiště, doporučujeme zapnout obnovitelné odstranění. Povolení obnovitelného odstranění pro často přepsaná data může mít za následek zvýšení poplatků za kapacitu úložiště a zvýšenou latenci při výpisu objektů BLOB. Tyto dodatečné náklady a latenci můžete zmírnit uložením často přepsaných dat v samostatném účtu úložiště, kde je zakázáno odstranění pomocí programu Soft Delete. 

## <a name="faq"></a>Nejčastější dotazy

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Pro které služby úložiště mohu použít obnovitelné odstranění?

V současné době je obnovitelné odstranění k dispozici jenom pro úložiště objektů blob (objekt).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Je obnovitelné odstranění k dispozici pro všechny typy účtů úložiště?

Ano, obnovitelné odstranění je k dispozici pro účty úložiště objektů Blob i pro objekty BLOB v účtech úložiště pro obecné účely (GPv1 i GPv2). Podporovány jsou standardní i prémiové typy účtů. Obnovitelné odstranění je k dispozici pro nespravované disky, což jsou objekty BLOB stránky pod kryty. Obnovitelné odstranění není k dispozici pro spravované disky.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Je obnovitelné odstranění k dispozici pro všechny vrstvy úložiště?

Ano, obnovitelné odstranění je k dispozici pro všechny úrovně úložiště, včetně horkých, chladných a archivních. Obnovitelné odstranění však neposkytuje ochranu proti přepsání objektů BLOB v archivní vrstvě.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Můžu použít rozhraní API úrovně objektů blob nastavit k objektům BLOB s měkkými odstraněnými snímky?

Ano. Obnovitelné odstraněné snímky zůstanou v původní vrstvě, ale základní objekt blob se přesune do nové vrstvy. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Účty úložiště Premium mají limit na snímek objektu blob 100. Započítávají se do tohoto limitu obnovitelné odstraněné snímky?

Ne, obnovitelné odstraněné snímky se do tohoto limitu nezapočítávají.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Můžu pro existující účty úložiště zapnout obnovitelné odstranění?

Ano, obnovitelné odstranění lze konfigurovat pro existující i nové účty úložiště.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Pokud odstraním celý účet nebo kontejner se zapnutým měkkým odstraněním, uloží se všechny přidružené objekty BLOB?

Ne, pokud odstraníte celý účet nebo kontejner, všechny přidružené objekty BLOB budou trvale odstraněny. Další informace o ochraně účtu úložiště před náhodným odstraněním naleznete v [tématu Uzamčení prostředků, které mají zabránit neočekávaným změnám](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Mohu zobrazit metriky kapacity pro odstraněná data?

Měkká odstraněná data jsou součástí celkové kapacity účtu úložiště. Další informace o sledování a sledování kapacity úložiště najdete v [tématu Storage Analytics](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Pokud soft delete vypnu, budu mít stále přístup k smazaným smazaným datům?

Ano, budete mít stále přístup a obnovit neprošlá obnovitelná odstraněná data, pokud je vypnuto obnovitelné odstranění.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Můžu číst a kopírovat obnovitelné smazané snímky objektu blob?  

Ano, ale nejprve musíte zavolat Undelete na objekt blob.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Je obnovitelné odstranění dostupné pro všechny typy objektů blob?

Ano, obnovitelné odstranění je k dispozici pro objekty BLOB bloku, objekty BLOB a objekty BLOB stránky.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Je pro disky virtuálních počítačů k dispozici obnovitelné odstranění?  

Obnovitelné odstranění je k dispozici pro prémiové i standardní nespravované disky, což jsou objekty BLOB stránky pod kryty. Obnovitelné odstranění vám pomůže obnovit pouze data odstraněná operacemi **Odstranění objektu blob**, **Objekt blob ,** Put Block **List**, **Blok put** a Kopírování **objektů blob.** Data přepsána voláním put **page** nelze obnovit.

Virtuální počítač Azure zapisuje na nespravovaný disk pomocí volání **put page**, takže použití funkce obnovitelnéodstranění k zrušení zápisu na nespravovaný disk z virtuálního počítače Azure není podporovaný scénář.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Musím změnit stávající aplikace, aby bylo použití obnovitelného odstranění?

Je možné využít obnovitelné odstranění bez ohledu na verzi rozhraní API, kterou používáte. Chcete-li však vypsat a obnovit obnovitelné odstraněné objekty BLOB a snímky objektů BLOB, budete muset použít verzi 2017-07-29 [rozhraní REST API služby storage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) nebo vyšší. Společnost Microsoft doporučuje vždy používat nejnovější verzi rozhraní Azure Storage API.

## <a name="next-steps"></a>Další kroky

* [Ukázkový kód rozhraní .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Rozhraní REST API služby Blob Service](/rest/api/storageservices/blob-service-rest-api)
* [Replikace úložiště Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Navrhování vysoce dostupných aplikací pomocí RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Převzetí služeb při selhání účtu zotavení po havárii a úložiště (preview) ve službě Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
