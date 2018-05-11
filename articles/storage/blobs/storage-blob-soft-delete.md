---
title: Soft odstranění objektů BLOB služby Azure Storage (Preview) | Microsoft Docs
description: Azure Storage teď nabízí obnovitelného odstranění (Preview) pro objekty blob, takže můžete snadno obnovit data, pokud je chybně upravit nebo odstranit, aplikaci nebo jiný uživatel účet úložiště.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: mihauss
ms.openlocfilehash: 0e728f9f9754d76d893b12309bb52201d772efbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="soft-delete-for-azure-storage-blobs-preview"></a>Obnovitelného odstranění pro objekty BLOB Azure Storage (Preview)

## <a name="overview"></a>Přehled

Azure Storage teď nabízí obnovitelného odstranění (Preview) pro objekty blob, takže můžete snadno obnovit data, pokud je chybně upravit nebo odstranit, aplikaci nebo jiný uživatel účet úložiště.

## <a name="how-does-it-work"></a>Jak to funguje?

Pokud je zapnutá, obnovitelného odstranění umožňuje uložení a obnovení dat v případě odstranění objektů BLOB nebo snímky objektů blob. Tato ochrana rozšiřuje k datům objektu blob, který je v důsledku přepsání vymazat.

Při odstranění dat přechází do stavu obnovitelného odstranění místo trvale vymazání. Když obnovitelného odstranění je na a přepsala data, byl vygenerován logicky odstraněné snímek pro uložení stavu přepsána data. Logicky odstraněné objekty jsou neviditelná, pokud není výslovně uveden.
Můžete nakonfigurovat množství času, které je logicky odstraněná data obnovitelná trvale platnosti.

Obnovitelného odstranění je zpětně kompatibilní; Nemáte žádné změny pro vaše aplikace využívat výhod ochrany, které tato funkce poskytuje. Ale [obnovení dat](#recovery) představuje novou **zrušení odstranění objektů Blob** rozhraní API.

> [!NOTE]
> Chcete-li verzi Public Preview je zakázaná volání nastavit úroveň objektů Blob na objekt blob se snímky.
Obnovitelného odstranění generuje snímky chránit vaše data, kdy je přepsán. Aktivně pracujeme na řešení umožňující vrstvení objektů BLOB s snímky.

### <a name="configuration-settings"></a>Nastavení konfigurace

Když vytvoříte nový účet, obnovitelného odstranění vypnuté ve výchozím nastavení. Obnovitelného odstranění vypnuté také ve výchozím nastavení pro existující účty úložiště. Tuto funkci zapnout a vypnout můžete přepínat kdykoli během doby platnosti účtu úložiště.

Budou mít pořád povolený přístup a obnovit logicky odstraněná data, pokud je vypnutý funkci, za předpokladu, že logicky odstraněná data byla uložena, pokud je dříve zapnutá funkce. Když zapnete obnovitelného odstranění, musíte také nakonfigurovat dobu uchování.

Doba uchování určuje množství času, které je logicky odstraněná data uložená a k dispozici pro obnovení. Pro objekty BLOB a snímky objektů blob, které jsou explicitně odstranit hodiny doby uchování spustí, když data se odstraní. Hodiny pro logicky odstraněné snímky vytvářené funkci obnovitelného odstranění, když se přepíšou data, spustí při vygenerování snímku. Nyní můžete zachovat logicky odstraněná data pro rozmezí 1 až 365 dnů.

Doba uchování obnovitelného odstranění kdykoli, můžete změnit. Po dobu uchování aktualizované se uplatní jenom na nově odstraněná data. Dřív odstraněná data vypršení platnosti na základě na dobu uchování, která se nakonfigurovala při dat byla odstraněna.

### <a name="saving-deleted-data"></a>Ukládání odstraněná data

Obnovitelného odstranění chrání vaše data v mnoha případech, kde jsou objekty BLOB nebo objekt blob snímky odstraní nebo přepíše.

Tím, že objekt blob je přepsat pomocí **Put objektů Blob**, **Put bloku**, **uvést seznam blokovaných** nebo **kopírovat objekt Blob** snímek objektu blob stavu před verzí zápisu operace se automaticky vygeneroval. Tento snímek je logicky odstraněného snímku; neviditelná je pokud logicky odstraněné objekty, které jsou explicitně uvedené. Najdete v článku [obnovení](#recovery) části se dozvíte, jak seznam logicky odstraněných objektů.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Logicky odstraněná data je šedý symbol, při blue aktivní data. Více nedávno zapsaná data se zobrazí pod starší data. Když dojde k přepsání B0 B1, generuje se logicky odstraněného snímku B0. Když dojde k přepsání B1 B2, generuje se logicky odstraněného snímku B1.*

> [!NOTE]
> Obnovitelného odstranění pouze poskytuje ochranu pro operace kopírování přepsat, když je zapnutý pro cílový objekt blob účet.

> [!NOTE]
> Obnovitelného odstranění neposkytuje ochranu pro objekty BLOB ve vrstvě archivu přepsat. Pokud dojde k přepsání objektů blob v archivu nový objekt blob v libovolné úrovně, vypršela trvale přepsána objektu blob.

Když **odstranit objekt Blob** nazývá na snímku, tento snímek je označen jako logicky odstranit. Nový snímek nevygenerovala.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Logicky odstraněná data je šedý symbol, při blue aktivní data. Více nedávno zapsaná data se zobrazí pod starší data. Když **snímku Blob** je volána, stane B0 snímek a B1 je aktivním stavu objektu blob. Při odstranění snímku B0 je označen jako logicky odstraněna.*

Když **odstranit objekt Blob** se volá na základní objekt blob (žádné blob tedy ne samotný snímek), tento objekt blob je označen jako logicky odstranit. Konzistentní s předchozí chování volání **odstranit objekt Blob** u objektu blob, který obsahuje aktivní snímky vrátí chybu. Volání metody **odstranit objekt Blob** na objekt blob se logicky odstraněné snímky nevrací chybu. Přesto můžete odstranit objekt blob a všechny jeho snímky v jedné operace po zapnutí obnovitelného odstranění. Díky tomu označí základní objekt blob a snímků jako logicky odstranit.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Logicky odstraněná data je šedý symbol, při blue aktivní data. Více nedávno zapsaná data se zobrazí pod starší data. Tady **odstranit objekt Blob** Přišla žádost o B2 a všechny přidružené snímky odstranit. Aktivní blob, B2 a všechny přidružené snímky jsou označeny jako logicky odstraněna.*

> [!NOTE]
> Když se přepíše logicky odstraněných objektů blob, se automaticky vygeneroval snímku logicky odstraněného objektu blob stavu před operace zápisu. Nový objekt blob dědí vrstvě přepsána objektu blob.

Obnovitelného odstranění není uložit vaše data v případě kontejner nebo odstraní účet, ani když blob metadata a vlastnosti objektu blob budou přepsána. Chcete-li chránit chybné odstranění účtu úložiště, můžete nakonfigurovat zámek pomocí Azure Resource Manager. Naleznete v článku Azure Resource Manager [prostředky zámku na zabránit neočekávané změny](../../azure-resource-manager/resource-group-lock-resources.md) Další informace.

V následující tabulce jsou očekávané chování, pokud je zapnuta obnovitelného odstranění:

| Operace REST API                                                                                                | Typ prostředku                 | Popis                                                                                                 | Změna v chování                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Odstranění](/rest/api/storagerp/StorageAccounts/Delete)              | Účet                       | Odstraní účet úložiště, včetně všech kontejnery a objekty BLOB, které obsahuje.                           | Žádná změna. Kontejnery a objekty BLOB v odstraněného účtu nejsou použitelná pro obnovení.                                                                                                                                                                                                                                                                                          |
| [Odstranit kontejner](/rest/api/storageservices/fileservices/delete-container)       | Kontejner                     | Odstraní kontejner, včetně všech objektů BLOB, které obsahuje.                                                | Žádná změna. Objekty BLOB v kontejneru odstraněné nejsou použitelná pro obnovení.                                                                                                                                                                                                                                                                                                       |
| [Uveďte objektů Blob](/rest/api/storageservices/fileservices/put-blob)                       | Přidat blok, a objekty BLOB stránek | Vytvoří nový objekt blob nebo nahradí existující objekt blob do kontejneru                                          | Pokud se používá k nahrazení existující objekt blob, je automaticky vygenerován snímek objektu blob stavu před volání. To platí také pro dříve logicky odstraněných objektů blob jenom v případě je nahrazena objekt blob stejného typu (bloku, doplňovací nebo stránky). Pokud ho nahrazuje objekt blob jiného typu, všechny existující logicky odstraněná data trvale vypršela. |
| [Odstranit objekt Blob](/rest/api/storageservices/fileservices/delete-blob)                 | Přidat blok, a objekty BLOB stránek | Označí objektů blob nebo objekt blob snímku pro odstranění. Objekt blob nebo snímek je později odstranit během uvolňování paměti | Pokud se používá k odstranění objektů blob snímek, tento snímek je označen jako logicky odstranit. Pokud se používá k odstranění objektu blob, že objektu blob je označen jako logicky odstraněna.                                                                                                                                                                                                                           |
| [Zkopírování objektu Blob](/rest/api/storageservices/fileservices/copy-blob)                     | Přidat blok, a objekty BLOB stránek | Zdrojový objekt blob se zkopíruje na cílový objekt blob ve stejném účtu úložiště nebo jiný účet úložiště.       | Pokud se používá k nahrazení existující objekt blob, je automaticky vygenerován snímek objektu blob stavu před volání. To platí také pro dříve logicky odstraněných objektů blob jenom v případě je nahrazena objekt blob stejného typu (bloku, doplňovací nebo stránky). Pokud ho nahrazuje objekt blob jiného typu, všechny existující logicky odstraněná data trvale vypršela. |
| [Uveďte bloku](/rest/api/storageservices/put-block)                                  | Objekty blob bloku                   | Vytvoří nový blok se svěřit jako součást objekt blob bloku.                                                | Pokud se používá k potvrzení blok do objektu blob, který je aktivní se nezměnila. Pokud se používá k potvrzení blok do objektu blob, které je logicky odstranit, se vytvoří nový objekt blob a k zaznamenání stavu obnovitelného odstraněného objektu blob je automaticky vygenerován snímek.                                                                                                                      |
| [Uveďte seznam blokovaných položek](/rest/api/storageservices/fileservices/put-block-list)           | Objekty blob bloku                   | Objekt blob potvrdí zadáním sadu ID, které tvoří objekt blob bloku bloku.                             | Pokud se používá k nahrazení existující objekt blob, je automaticky vygenerován snímek objektu blob stavu před volání. To platí také pro dříve logicky odstraněných objektů blob jenom v případě je objekt Blob bloku. Pokud ho nahrazuje objekt blob jiného typu, všechny existující logicky odstraněná data trvale vypršela.                                                |
| [Uveďte stránky](/rest/api/storageservices/fileservices/put-page)                       | Objekty blob stránky                    | Zapíše rozsahu stránek pro objekt Blob stránky.                                                                     | Žádná změna. Objekt Blob stránky dat se přepíše nebo vymazat pomocí této operace se neuloží a není použitelná pro obnovení.                                                                                                                                                                                                                                                   |
| [Append – blok](/rest/api/storageservices/fileservices/append-block)               | Doplňovací objekty BLOB                  | Zapíše datového bloku na konec objektu Blob připojit                                                         | Žádná změna.                                                                                                                                                                                                                                                                                                                                                           |
| [Nastavit vlastnosti objektů Blob](/rest/api/storageservices/fileservices/set-blob-properties) | Přidat blok, a objekty BLOB stránek | Nastaví hodnoty pro vlastnosti systému, které jsou definované pro objekt blob.                                                       | Žádná změna. Vlastnosti objektu blob přepsána nejsou použitelná pro obnovení.                                                                                                                                                                                                                                                                                                          |
| [Nastavit Metadata objektu Blob](/rest/api/storageservices/fileservices/set-blob-metadata)     | Přidat blok, a objekty BLOB stránek | Nastaví metadata definovaná uživatelem pro zadaný objekt blob jako jeden nebo více dvojic název hodnota.                          | Žádná změna. Metadata objektu blob přepsána není použitelná pro obnovení.                                                                                                                                                                                                                                                                                                             |

Je důležité si všimněte si, že volání "Put stránka" přepsat nebo zrušte rozsahy objekt Blob stránky nebude automaticky generovat snímky. Disky virtuálního počítače jsou zajišťované objekty BLOB stránky a použijte **Put stránky** zapsat data.

### <a name="recovery"></a>Obnovení

Aby bylo snazší obnovit odstraněná data, jste zavedli jsme nové rozhraní API "Zrušení odstranění objektů Blob". Volání rozhraní API zrušení odstranění na logicky odstraněné základní objektu blob obnoví a všechny související logicky odstraněné snímky jako aktivní. Volání rozhraní API zrušení odstranění na aktivní základní objekt blob obnovení všechny související logicky odstraněné snímky jako aktivní. Když jsou snímky obnoví jako aktivní, zobrazují se jako uživatelem generovaný snímky; Nepřepisovat základní objekt blob.

Chcete-li obnovit objekt blob konkrétní logicky odstraněného snímku můžete volat **zrušení odstranění objektů Blob** na základní objekt blob. Potom můžete zkopírovat snímku přes objekt blob nyní aktivní. Můžete také zkopírovat snímku do nového objektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Logicky odstraněná data je šedý symbol, při blue aktivní data. Více nedávno zapsaná data se zobrazí pod starší data. Zde **zrušení odstranění objektů Blob** nazývá u objektu blob B, a tak obnovit základní objekt blob, B1 a všechny přidružené snímky, zde právě B0 jako aktivní. V druhém kroku se zkopíruje B0 přes základní objekt blob. Tato operace kopírování generuje softwarové odstraněného snímku B1.*

Pokud chcete zobrazit doporučené odstraněné objekty BLOB a objektů blob snímky, můžete zahrnout odstraněná data v **seznamu objektů blob**. Můžete zobrazit jenom logicky odstraněné základní objektů BLOB nebo zahrnují také snímky logicky odstraněných objektů blob. Pro všechny logicky odstraněná data můžete zobrazit čas, kdy byla odstraněna data a také počet dní, než bude trvale platnost data.

### <a name="example"></a>Příklad

Následuje výstup konzoly .NET skript, který odešle, přepíše, snímky, odstranění a obnovení odstranit objekt blob s názvem "HelloWorld", když logicky zapnutá:

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

Najdete v článku [další kroky](#Next steps) části ukazatel k aplikaci, která vytváří tento výstup.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechny logicky odstraněná data se fakturuje stejnou rychlostí jako aktivní data. Vám nebude nic účtováno pro data, která je trvale odstraněny po dobu uchování nakonfigurované. Podrobnější prohlídku do snímky a jak se nabíhat poplatky, najdete v tématu [pochopení, jak snímky nabíhat poplatky](storage-blob-snapshots.md).

Nebude platit pro transakce související s automatické generování snímků. Poplatky vám budou účtovány pro **zrušení odstranění objektů Blob** transakce rychlostí "Operací zápisu".

Další informace o cenách pro Azure Blob Storage v obecné, podívejte se [Azure Blob Storage stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

Když zapnete původně obnovitelného odstranění, doporučujeme používat doby uchování malé lépe pochopit, jak funkce ovlivní vaše faktura.

## <a name="quickstart"></a>Rychlý start

### <a name="azure-portal"></a>Azure Portal
Chcete-li povolit obnovitelného odstranění, přejděte na **obnovitelného odstranění** možnost pod **služby objektů Blob**. Pak klikněte na tlačítko **povoleno** a zadejte počet dní, které chcete uchovávat logicky odstraněná data.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Chcete-li zobrazit logicky odstraněné objekty BLOB, vyberte **Zobrazit odstraněné objekty BLOB** zaškrtávací políčko.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Chcete-li zobrazit logicky odstraněné snímky pro daný objekt blob, vyberte objekt blob a klikněte na **zobrazit snímky**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Zajistěte, aby **zobrazit odstranit snímky** je zaškrtnuto políčko.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Když kliknete na logicky odstraněných objektů blob nebo snímek, Všimněte si nové vlastnosti objektů blob. Indikují, když objekt byl odstraněn, a jak dlouho jsou ponechána až do objektů blob nebo objekt blob snímek trvale vypršela. Pokud softwarové odstraněného objektu není snímku, bude mít i možnost zrušení jeho odstranění.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Mějte na paměti, že zrušení odstranění objektu blob bude také obnovit všechny přidružené snímky. Chcete-li zrušit logicky odstraněné snímky pro aktivní objekt blob, klikněte na objekt blob a vyberte **zrušení odstranění všechny snímky**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Jakmile jste zrušení odstranění snímků objekt blob, můžete kliknout na **povýšit** kopírování snímku přes objekt blob kořenové, a tak obnovit objekt blob do snímku.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>Prostředí Power Shell
Pokud chcete povolit obnovitelného odstranění, aktualizujte vlastnosti služby klienta objektů blob. Následující příklad povolí obnovitelného odstranění pro podmnožinu účtů v předplatném:

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Pokud chcete obnovit objekty BLOB, které byly odstraněny omylem, volání obnovit na tyto objekty BLOB. Mějte na paměti, že volání **zrušení odstranění objektů Blob**, i na aktivní a logicky odstraněné objekty BLOB, obnoví všechny přidružené snímky logicky odstraněné jako aktivní. V následujícím příkladu volání obnovit na všech logicky odstraněné a aktivní objektů BLOB v kontejneru:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="python-client-library"></a>Klientská knihovna pro Python

Pokud chcete povolit obnovitelného odstranění, aktualizujte vlastnosti služby klienta objektů blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Klientská knihovna pro .net

Pokud chcete povolit obnovitelného odstranění, aktualizujte vlastnosti služby klienta objektů blob:

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Pokud chcete obnovit objekty BLOB, které byly odstraněny omylem, volání obnovit na tyto objekty BLOB.
Mějte na paměti, že volání **zrušení odstranění objektů Blob**, i na aktivní a logicky odstraněné objekty BLOB, obnoví všechny přidružené snímky logicky odstraněné jako aktivní. V následujícím příkladu volání obnovit na všech logicky odstraněné a aktivní objektů BLOB v kontejneru:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Obnovit na verzi konkrétní objekt blob, nejdřív voláním obnovit na objekt blob a potom zkopírujte požadované snímku prostřednictvím objektu blob. Následující příklad obnoví objekt blob bloku na jeho nejnovější vygenerovaný snímek:

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

## <a name="should-i-use-soft-delete"></a>Použít obnovitelného odstranění?

Pokud je pravděpodobné, že vaše data omylem změněného nebo odstraněného aplikaci nebo jiný uživatelský účet úložiště, doporučujeme zapnout obnovitelného odstranění.
Obnovitelného odstranění je jednou ze součástí strategie ochrany dat a pomáhá zabránit nechtěnému únikem.

## <a name="faq"></a>Nejčastější dotazy

**Jaké typy úložiště lze použít obnovitelného odstranění?**

V současné době obnovitelného odstranění je k dispozici pouze pro úložiště objektů blob (objekt).

**Je k dispozici pro všechny typy účtů úložiště obnovitelného odstranění?**

Ano, je k dispozici pro účty úložiště blob také jako objekty BLOB v účtech úložiště pro obecné účely obnovitelného odstranění. To platí pro účty standard a premium. Není k dispozici pro spravované disky obnovitelného odstranění.

**Je k dispozici pro všechny vrstvy úložiště obnovitelného odstranění?**

Ano, je k dispozici pro všechny vrstvy úložiště, včetně aktivní, nástrojů a archivu obnovitelného odstranění. Ale obnovitelného odstranění neposkytuje ochranu pro objekty BLOB ve vrstvě archivu přepsat.

**Mít prémiové účty úložiště na omezení počtu snímků blob 100. Do tohoto limitu počítají logicky odstraněné snímky?**

Ne, soft odstranit snímky nepočítá toto omezení.

**Můžete zapnout obnovitelného odstranění pro existující účty úložiště?**

Ano, je možné konfigurovat pro stávající a nové účty úložiště obnovitelného odstranění.

**Pokud odstranit celý klienta nebo kontejner s obnovitelným odstraněním zapnutá, bude všech přidružených objektů BLOB uložit?**

Ne, pokud odstraníte celý klienta nebo kontejner, všechny související objekty BLOB se trvale odstraní. Informace o tom, k ochraně před náhodného odstranění účtu úložiště, naleznete v článku Azure Resource Manager [prostředky zámku na zabránit neočekávané změny](../../azure-resource-manager/resource-group-lock-resources.md).

**Můžete zobrazit metriky kapacity pro odstraněná data?**

Logicky odstraněná data je jako součást kapacity účtu celkové úložiště. Další informace o sledování a monitorování kapacity úložiště, najdete v tématu [Storage Analytics](../common/storage-analytics.md) článku.

**Pokud vypnout obnovitelného odstranění I přesto bude mít přístup k logicky odstraněná data?**

Ano, které budou mít pořád povolený přístup a neprošlé logicky odstraněná data obnovit, když je vypnutý obnovitelného odstranění.

**Může číst a zkopírování logicky odstraněné snímky Moje blob?**

Ano, ale je třeba volat obnovit nejprve u objektu blob.

**Je obnovitelným odstraněním k dispozici pro všechny typy blob?**

Ano, je k dispozici pro objekty BLOB bloku a doplňovacích objektů BLOB a objekty BLOB stránky obnovitelného odstranění.

**Je obnovitelným odstraněním k dispozici pro disky virtuálního počítače?**

Je k dispozici pro premium a standard nespravované disky obnovitelného odstranění. Obnovitelného odstranění pouze můžete obnovit data odstraněna **odstranit objekt Blob**, **Put Blob**, **Put blokovaných**, **Put bloku** a **Kopírovat objekt Blob**. Data přepsány volání **Put stránky** není použitelná pro obnovení.

**Je potřeba změnit svoje existující aplikace používat obnovitelného odstranění?**

Je možné využít výhod obnovitelného odstranění bez ohledu na verzi rozhraní API, který používáte. Ale na seznamu a obnovit logicky odstraněné objekty BLOB a objektů blob snímky, budete muset použít verzi 2017-07-29 [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) nebo vyšší. Obecně platí vždy doporučujeme používat nejnovější verzi bez ohledu na to, jestli používáte tuto funkci.

## <a name="next-steps"></a>Další postup

* [Ukázkový kód rozhraní .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [Rozhraní API REST služby objektů BLOB](/rest/api/storageservices/fileservices/blob-service-rest-api)

* [Replikace Azure Storage](../common/storage-redundancy.md)

* [Navrhování pomocí RA-GRS vysoce dostupných aplikací.](../common/storage-designing-ha-apps-with-ragrs.md)

* [Co dělat, když dojde k výpadku Azure Storage](../common/storage-disaster-recovery-guidance.md)
