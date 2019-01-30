---
title: Obnovitelné odstranění pro objekty BLOB služby Azure Storage | Dokumentace Microsoftu
description: Azure Storage teď nabízí obnovitelného odstranění pro objekty blob, takže můžete snadno obnovit data, když je chybně změněného nebo odstraněného aplikaci nebo jiný uživatel účet úložiště.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 07/15/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: e78b1f94338f177601b1e93fcc7974a228be062b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239982"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Obnovitelné odstranění pro objekty BLOB služby Azure Storage
Azure Storage teď nabízí obnovitelného odstranění pro objekty blob, takže můžete snadno obnovit data, když je chybně změněného nebo odstraněného aplikaci nebo jiný uživatel účet úložiště.

## <a name="how-does-it-work"></a>Jak to funguje?
Pokud je zapnutá, obnovitelné odstranění umožňuje uložení a obnovení dat při odstranění objektů BLOB nebo snímky objektů blob. Tato ochrana se vztahuje k datům objektu blob, který se vymažou v důsledku přepsání.

Když se odstraní data, přejde do stavu obnovitelného odstranění místo se trvale smazána. Pokud zadáte obnovitelného odstranění a přepisovat data, obnovitelně odstraněného snímku se vygeneruje pro uložení stavu přepsaná data. Obnovitelně odstraněné objekty nejsou viditelná, pokud výslovně uvedené. Můžete nakonfigurovat množství času, které obnovitelně odstraněná data obnovitelná než trvale vypršela.

Obnovitelné odstranění je zpětně kompatibilní; nemusíte dělat žádné změny aplikací výhod ochrany, které tuto funkci poskytuje. Ale [obnovení dat](#recovery) zavádí nový **obnovit objekt Blob** rozhraní API.

### <a name="configuration-settings"></a>Nastavení konfigurace
Když vytvoříte nový účet, obnovitelné odstranění je vypnuto ve výchozím nastavení. Obnovitelné odstranění je vypnuto ve výchozím nastavení pro existující účty úložiště také. Tuto funkci zapnout a vypnout můžete přepínat kdykoli během životnosti účtu úložiště.

Budete stále moct používat a obnovit obnovitelně odstraněná data, pokud je vypnuta funkce, za předpokladu, že obnovitelně odstraněná data se neuloží, když tato funkce byla dříve zapnutá. Když zapnete obnovitelné odstranění, musíte také nakonfigurovat dobu uchování.

Doba uchování Určuje dobu, kterou obnovitelně odstraněná data uloží jsou k dispozici pro obnovení. Pro objekty BLOB a snímky objektů blob, které jsou explicitně odstranit se začíná účtovat období uchovávání dat při odstranění. Pro obnovitelně odstraněné snímky generované funkce obnovitelného odstranění, když data se přepíší hodin začne, když je generován snímek. Aktuálně můžete uchovat obnovitelně odstraněná data pro mezi 1 a 365 dny.

Můžete změnit dobu uchování obnovitelné odstranění kdykoli. Na dobu se aktualizovala doba uchování bude vztahovat jenom na nově odstraněným datům. Dřív odstraněná data skončí založené na dobu uchování, která je nakonfigurovaná, když tato data byla odstraněna. Pokus o odstranění obnovitelně odstraněný objekt nebude mít vliv na jeho čas vypršení platnosti.

### <a name="saving-deleted-data"></a>Ukládání odstraněných dat.
Obnovitelné odstranění chrání vaše data v mnoha případech, ve kterém jsou objekty BLOB nebo jejich snímky odstraní nebo přepíše.

Pokud objekt blob se přepíše pomocí **Put Blob**, **Vložit blok**, **vložit seznam blokovaných** nebo **objekt Blob kopírování** snímek objektu blob stavu před verzí zápisu operace není automaticky vygenerován. Tento snímek se obnovitelného se odstranil snímek; To je neviditelné, pokud obnovitelně odstraněné objekty jsou výslovně uvedené. Zobrazit [obnovení](#recovery) části a zjistěte, jak zobrazit seznam obnovitelně odstraněné objekty.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Obnovitelně odstraněná data je šedá, zatímco data služby active je modrá. Více nedávno zapsaná data se zobrazí pod starší data. Pokud dojde k přepsání B0 B1, obnovitelně odstraněného snímku B0 se vygeneruje. Pokud zadáte přepíše B1 a B2, obnovitelně odstraněného snímku B1 se vygeneruje.*

> [!NOTE]  
> Obnovitelné odstranění pouze poskytuje ochranu pro operace kopírování přepsat, když je zapnutý pro cílový objekt blob účtu.

> [!NOTE]  
> Obnovitelné odstranění neposkytuje ochranu pro objekty BLOB v archivní úrovni přepsat. Pokud dojde k přepsání objektů blob v archivní úrovni nový objekt blob ve všech úrovních, platnost trvale přepsána objektů blob.

Když **odstranění objektů Blob** je volána na snímek, je označena jako obnovitelně tomuto snímku odstraněn. Nový snímek se nevygeneroval.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Obnovitelně odstraněná data je šedá, zatímco data služby active je modrá. Více nedávno zapsaná data se zobrazí pod starší data. Když **pořízení snímku objektu Blob** je volána, stane B0 snímku a B1 je aktivní stav objektu blob. Při odstranění snímku B0 je označen jako obnovitelné odstranění.*

Při **odstranit objekt Blob** je volán na základní objekt blob (žádný objekt blob, který je sám není snímek), tohoto objektu blob je označen jako obnovitelné odstranění. Konzistentní s předchozí chování, volání **odstranit objekt Blob** na objekt blob má aktivní snímky vrátí chybu. Volání **odstranit objekt Blob** na objekt blob se obnovitelného se odstranily snímky nevrací chybu. Můžete odstranit objekt blob a jeho snímky v jediné operaci po zapnutí obnovitelné odstranění. To označuje základní objekt blob a snímků jako obnovitelné odstranění.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Obnovitelně odstraněná data je šedá, zatímco data služby active je modrá. Více nedávno zapsaná data se zobrazí pod starší data. Tady **odstranit objekt Blob** je provedeno volání B2 a všechny přidružené snímky odstranit. Aktivní objekt blob, B2 a všechny přidružené snímky jsou označeny jako obnovitelné odstranění.*

> [!NOTE]  
> Když je přepsán obnovitelně odstraněných objektů blob, obnovitelně odstraněný snímek objektu blob stavu před operace zápisu není automaticky vygenerován. Nový objekt blob zdědí úroveň objektu blob přepsána.

Obnovitelné odstranění není zachraňte vaše data v případech, kontejneru nebo účtu odstraní, ani když metadata objektů blob a vlastnosti objektu blob se přepíše. K ochraně před chybné odstranění účtu úložiště, můžete nakonfigurovat zámek pomocí Azure Resource Manageru. Podrobnosti najdete v článku Azure Resource Manageru [uzamčení prostředků k zabránit neočekávaným změnám](../../azure-resource-manager/resource-group-lock-resources.md) Další informace.

Následující tabulka obsahuje podrobnosti o očekávané chování při zapnutí se obnovitelného odstranění:

| Operace rozhraní REST API | Typ prostředku | Popis | Změna chování |
|--------------------|---------------|-------------|--------------------|
| [Odstranění](/rest/api/storagerp/StorageAccounts/Delete) | Účet | Odstraní účet úložiště, včetně všech kontejnerů a objektů BLOB, které obsahuje.                           | Žádná změna. Kontejnery a objekty BLOB v rámci odstraněného účtu nejsou zotavit. |
| [Delete Container](/rest/api/storageservices/delete-container) | Kontejner | Odstraní kontejner, včetně všech objektů BLOB, které obsahuje. | Žádná změna. Objekty BLOB v kontejneru odstraněné nejsou zotavit. |
| [Vložení objektu Blob](/rest/api/storageservices/put-blob) | Přidat blok, a objekty BLOB stránky | Vytvoří nový objekt blob nebo nahradí existující objekt blob v kontejneru | Pokud se používá k nahrazení existujícímu objektu blob, je automaticky vygenerován snímek objektu blob stavu před voláním. To platí i pro dříve obnovitelně odstraněných objektů blob a pouze v případě nahrazuje objekt blob (bloku, doplňovací nebo stránky) stejného typu. Pokud je nahrazen jiného typu objektu blob, všechny existující obnovitelně odstraněná data trvale vypršela. |
| [Odstranit objekt Blob](/rest/api/storageservices/delete-blob) | Přidat blok, a objekty BLOB stránky | Označí objekt blob nebo snímek objektu blob k odstranění. Objekt blob nebo snímek je později odstranit během uvolňování paměti | Pokud se používá k odstranit snímek objektu blob, tento snímek je označen jako obnovitelné odstranění. Pokud chcete odstranit objekt blob, tohoto objektu blob je označen jako obnovitelné odstranění. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Přidat blok, a objekty BLOB stránky | Zkopíruje zdrojový objekt blob do cílové objektů blob ve stejném účtu úložiště nebo do jiného účtu úložiště. | Pokud se používá k nahrazení existujícímu objektu blob, je automaticky vygenerován snímek objektu blob stavu před voláním. To platí i pro dříve obnovitelně odstraněných objektů blob a pouze v případě nahrazuje objekt blob (bloku, doplňovací nebo stránky) stejného typu. Pokud je nahrazen jiného typu objektu blob, všechny existující obnovitelně odstraněná data trvale vypršela. |
| [Vložit blok](/rest/api/storageservices/put-block) | Objekty blob bloku | Vytvoří nový blok pro potvrzení jako součást objektu blob bloku. | Pokud se používá k potvrzení do objektu blob, která je aktivní blok není žádná změna. Pokud se používá k potvrzení bloku do objektu blob, který se obnovitelného odstranění, se vytvoří nový objekt blob a k zaznamenání stavu obnovitelně odstraněného objektu blob je automaticky vygenerován snímek. |
| [Vložit blokovaných webů.](/rest/api/storageservices/put-block-list) | Objekty blob bloku | Potvrzení objektu blob tak, že zadáte sadu bloku ID, které tvoří objekt blob bloku. | Pokud se používá k nahrazení existujícímu objektu blob, je automaticky vygenerován snímek objektu blob stavu před voláním. To platí i pro dříve obnovitelně odstraněných objektů blob a pouze v případě je objekt Blob bloku. Pokud je nahrazen jiného typu objektu blob, všechny existující obnovitelně odstraněná data trvale vypršela. |
| [Vložit stránky](/rest/api/storageservices/put-page) | Objekty blob stránky | Zapíše rozsah stránek do objektů Blob stránky. | Žádná změna. Data objektů Blob stránky, která se přepíšou nebo vymazání pomocí této operace se neuloží a nepůjde obnovit. |
| [Přidat blok](/rest/api/storageservices/append-block) | Doplňovací objekty BLOB | Zapíše datového bloku na konec objektu Blob připojit | Žádná změna. |
| [Nastavit vlastnosti objektu Blob](/rest/api/storageservices/set-blob-properties) | Přidat blok, a objekty BLOB stránky | Nastaví hodnoty pro vlastnosti systému, které jsou definovány pro objekt blob. | Žádná změna. Vlastnosti objektu blob přepsána nejsou zotavit. |
| [Nastavte Metadata objektu Blob](/rest/api/storageservices/set-blob-metadata) | Přidat blok, a objekty BLOB stránky | Nastaví metadata definovaná uživatelem pro zadaný objekt blob jako jeden nebo více dvojic název hodnota. | Žádná změna. Metadata přepsána objektu blob se nedá vrátit zpátky. |

Je důležité si všimněte, že volání "Umístit stránka" přepsat nebo zrušte rozsahy objekt Blob stránky nebudou automaticky generovat snímky. Disky virtuálních počítačů jsou založená na objekty BLOB stránky a použijte **umístit stránku** zapsat data.

### <a name="recovery"></a>Obnovení
Aby bylo snazší pro obnovení odstraněných dat, zavedli jsme nové rozhraní API "Obnovit objekt Blob". Volání rozhraní API obnovit obnovitelně odstraněný základní BLOB obnoví a všech souvisejících obnovitelně odstraněné snímky jako aktivní. Volání rozhraní API obnovit na aktivní základní objekt blob obnoví všechny související obnovitelně odstraněné snímky jako aktivní. Když se snímky obnoví jako aktivní, vypadají uživatelem generovaný snímků Nepřepisovat základní objekt blob.

Chcete-li obnovit objekt blob konkrétní obnovitelně odstraněného snímku lze volat **obnovit objekt Blob** na základní objekt blob. Potom můžete zkopírovat snímek objektu BLOB nyní aktivní. Můžete také zkopírování snímku do nového objektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Obnovitelně odstraněná data je šedá, zatímco data služby active je modrá. Více nedávno zapsaná data se zobrazí pod starší data. Tady **obnovit objekt Blob** je volána v objektu blob B, a tím obnovení základní objekt blob, B1 a všechny přidružené snímky zde právě B0 jako aktivní. V druhém kroku B0 zkopíruje základní objekt blob. Tato operace kopírování generuje obnovitelně odstraněného snímku B1.*

Zobrazit obnovitelně odstraněné objekty BLOB a snímků objektů blob, můžete zahrnout odstraněným datům v **výpis objektů blob**. Můžete zobrazit pouze obnovitelně odstraněné základní objekty BLOB nebo zahrnují i snímky obnovitelně odstraněných objektů blob. Pro všechny obnovitelně odstraněná data můžete zobrazit čas, kdy byla odstraněna data a také počet dní, než se data budou trvale vypršela.

### <a name="example"></a>Příklad:
Následuje výstupu konzoly .NET skript, který se odešle, přepíše, snímky, odstranění a obnovení objektu blob s názvem "HelloWorld" při obnovitelné odstranění zapnutý:

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

Zobrazit [další kroky](#next-steps) části ukazatele na aplikaci, která vytváří tento výstup.

## <a name="pricing-and-billing"></a>Ceny a fakturace
Všechny obnovitelně odstraněná data se účtuje stejná sazba jako za aktivní data. Pro data, která se trvale odstraní po uplynutí doby uchovávání dat nakonfigurovaných nebude účtovat. Prozkoumejte podrobněji snímky a jak se nechat nabíhat poplatky, najdete v tématu [pochopení, jak snímky nechat nabíhat poplatky](storage-blob-snapshots.md).

Nebudou se vám účtovat transakce související s automatické generování snímků. Bude se vám účtovat **obnovit objekt Blob** transakce rychlostí "Operace zápisu".

Další podrobnosti o cenách pro službu Azure Blob Storage v obecné, podívejte se [Azure Blob Storage stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

Když zapnete zpočátku obnovitelné odstranění, doporučujeme použít malé uchovávají pro lepší pochopení, jak tato funkce bude mít vliv na váš účet.

## <a name="quickstart"></a>Rychlý start
### <a name="azure-portal"></a>portál Azure
Povolit obnovitelné odstranění, přejděte **obnovitelné odstranění** v části **služby Blob Service**. Pak klikněte na tlačítko **povoleno** a zadejte počet dní, které chcete uchovat obnovitelně odstraněná data.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Chcete-li zobrazit obnovitelně odstraněné objekty BLOB, vyberte **Zobrazit odstraněné objekty BLOB** zaškrtávací políčko.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Chcete-li zobrazit obnovitelně odstraněné snímky pro daný objekt blob, vyberte objekt blob klikněte **zobrazit snímky**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Ujistěte se, **Zobrazit odstraněné snímky** zaškrtávací políčko zaškrtnuto.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Když kliknete na obnovitelně odstraněného objektu blob nebo snímek, Všimněte si, že nové vlastnosti objektu blob. Označují, kdy objekt byl odstraněn a počet dnů do objektu blob nebo snímek objektu blob je trvale vypršela platnost. Pokud obnovitelně odstraněný objekt není snímek, bude také mít možnost obnovit ji.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Mějte na paměti, že zrušení odstranění objektu blob se také obnovit všechny přidružené snímky. Chcete-li obnovit obnovitelně odstraněné snímky pro aktivní objekt blob, klikněte na objekt blob a vyberte **obnovit všechny snímky**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Jakmile jste se obnovit snímky objekt blob, můžete kliknout na **povýšit** ke zkopírování snímku objektu BLOB root, a tím obnovení snímku objektu blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete povolit obnovitelné odstranění, aktualizujte vlastnosti služby klienta objektů blob. Následující příklad povolí obnovitelného odstranění pro podmnožinu účtů v předplatném:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Můžete ověřit, že tento obnovitelné odstranění zapnutý pomocí následujícího příkazu:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Pokud chcete obnovit objekty BLOB, které se neúmyslně odstranily, volání zrušení odstranění u těchto objektů BLOB. Mějte na paměti, že volání **obnovit objekt Blob**, obnoví na aktivní a obnovitelně odstraněné objekty BLOB, všechny přidružené obnovitelně odstraněné snímky jako aktivní. Následující příklad volá obnovit na všech obnovitelně odstraněný a aktivní objektů BLOB v kontejneru:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Pokud chcete najít aktuální zásady uchovávání informací obnovitelné odstranění, použijte následující příkaz:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>Azure CLI 
Pokud chcete povolit obnovitelné odstranění, aktualizace vlastnosti služby klienta objektů blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Chcete-li ověřit obnovitelné odstranění zapnutý, použijte následující příkaz: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Klientská knihovna Python
Pokud chcete povolit obnovitelné odstranění, aktualizace vlastnosti služby klienta objektů blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Klientská knihovna pro .NET
Pokud chcete povolit obnovitelné odstranění, aktualizace vlastnosti služby klienta objektů blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Pokud chcete obnovit objekty BLOB, které se neúmyslně odstranily, volání zrušení odstranění u těchto objektů BLOB. Mějte na paměti, že volání **obnovit objekt Blob**, obnoví na aktivní a obnovitelně odstraněné objekty BLOB, všechny přidružené obnovitelně odstraněné snímky jako aktivní. Následující příklad volá obnovit na všech obnovitelně odstraněný a aktivní objektů BLOB v kontejneru:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Obnovení do objektů blob v konkrétní verzi, první volání zrušení odstranění objektu BLOB, zkopírujte požadované snímku objektu BLOB. Následující příklad obnoví na jeho naposledy generovaný snímek objektu blob bloku:

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

## <a name="should-i-use-soft-delete"></a>Použít obnovitelné odstranění?
Může se stát, že vaše data náhodně změněného nebo odstraněného aplikaci nebo jiný uživatelský účet úložiště, doporučujeme zapnout obnovitelné odstranění. Obnovitelné odstranění je jednou ze součástí strategie ochrany dat a pomáhá zabránit nechtěnému úniku.

## <a name="faq"></a>Nejčastější dotazy
**Jaké typy úložiště je možné použít obnovitelné odstranění?**  
V současné době obnovitelné odstranění je k dispozici pouze pro úložiště objektů blob (objekt).

**Se obnovitelného odstranění pro všechny typy účtů úložiště k dispozici?**  
Ano, je dostupná pro účty blob storage také jako objekty BLOB v pro obecné účely obnovitelné odstranění účtů úložiště (GPv1 a GPv2). To platí pro účty standard i premium. Obnovitelné odstranění není k dispozici pro spravované disky.

**Je k dispozici pro všechny úrovně úložiště obnovitelné odstranění?**  
Ano, je k dispozici pro všechny vrstvy úložiště, včetně horké, studené a archivní obnovitelné odstranění. Ale obnovitelné odstranění neposkytuje ochranu pro objekty BLOB v archivní úrovni přepsat.

**Můžete použít rozhraní API nastavit úroveň objektu Blob na úrovni objektů BLOB pomocí obnovitelně odstraněné snímky**  
Ano. Obnovitelně odstraněné snímky zůstanou v původní úroveň, ale základní objekt blob přesune do nové úrovni. 

**Mají účty služby Premium storage za omezení počtu snímků objektů blob 100. Toto omezení počítají obnovitelně odstraněné snímky?**  
Ne, obnovitelné odstranění snímků se nepočítají do tohoto limitu.

**Můžete zapnout obnovitelného odstranění pro existující účty úložiště?**  
Ano, obnovitelné odstranění je konfigurovatelné pro stávající i nové účty úložiště.

**Když odstraním celý účet nebo kontejner se obnovitelného odstranění zapnutá, budou všechny související objekty BLOB uložit?**  
Ne, pokud odstraníte celý účet nebo kontejner, všechny související objekty BLOB se trvale odstraní. Zjistěte, jak zabránit náhodného odstranění účtu úložiště, najdete v tématu Azure Resource Manageru článku [uzamčení prostředků k zabránit neočekávaným změnám](../../azure-resource-manager/resource-group-lock-resources.md).

**Můžete zobrazit metriky kapacity pro odstraněná data?**  
Obnovitelně odstraněná data je zahrnutý jako součást vaší celková kapacita účtu úložiště. Další informace o sledování a monitorování kapacity úložiště, najdete v tématu [Storage Analytics](../common/storage-analytics.md) článku.

**Pokud vypnu obnovitelné odstranění, můžu stále budou mít přístup k obnovitelně odstraněným datům?**  
Ano, stále budete moct používat a obnovit nevypršela obnovitelně odstraněná data, když je vypnutý obnovitelné odstranění.

**Může číst a zkopírovat obnovitelně odstraněné snímky Moje objektů blob?**  
Ano, ale je nutné volat zrušení odstranění nejprve u objektu blob.

**Pro všechny typy objektů blob se obnovitelného odstranění je k dispozici?**  
Ano, je dostupná pro objekty BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky obnovitelné odstranění.

**Obnovitelné odstranění je k dispozici pro disky virtuálních počítačů?**  
Obnovitelné odstranění je k dispozici pro nespravované disky premium a standard. Obnovitelné odstranění pouze můžete obnovit data odstraněna **odstranit objekt Blob**, **Put Blob**, **vložit seznam blokovaných**, **Vložit blok** a **Kopírovat objekt Blob**. Data přepsána volání **umístit stránku** se nedá vrátit zpátky.

**Budu muset změnit svoje existující aplikace, které chcete použít obnovitelné odstranění?**  
Je možné využít výhod obnovitelné odstranění bez ohledu na verzi rozhraní API, které používáte. Ale na seznamu a obnovovat obnovitelně odstraněné objekty BLOB a snímky objektů blob, budete muset použít verze 2017-07-29 [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) nebo vyšší. Obecně platí vždy doporučujeme používat nejnovější verzi bez ohledu na to, jestli používáte tuto funkci.

## <a name="next-steps"></a>Další postup
* [Vzorový kód .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [BLOB Service REST API](/rest/api/storageservices/blob-service-rest-api)
* [Replikace Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Navrhování aplikací s vysokou dostupností pomocí RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Co dělat, když dojde k výpadku služby Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
