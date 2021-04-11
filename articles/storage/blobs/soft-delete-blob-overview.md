---
title: Obnovitelné odstranění pro objekty blob
titleSuffix: Azure Storage
description: Obnovitelné odstranění pro objekty blob chrání vaše data, abyste mohli snadněji obnovit data v případě, že je omylem změnil nebo odstranila aplikace nebo jiný uživatel účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 29d9dd7757319e59fc12b42d89c2ce16dec71b8b
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551063"
---
# <a name="soft-delete-for-blobs"></a>Obnovitelné odstranění pro objekty blob

Částečný odstranění objektu BLOB chrání jednotlivé objekty blob, snímky nebo verze před náhodným odstraněním nebo přepsáním tím, že v systému v určeném časovém období udržuje Odstraněná data. Během doby uchování můžete objekt s příslušným odstraněním obnovit do jeho stavu v době, kdy byl odstraněn. Po vypršení doby uchování se objekt trvale odstraní.

## <a name="recommended-data-protection-configuration"></a>Doporučená konfigurace ochrany dat

Obnovitelné odstranění objektu BLOB je součástí komplexní strategie ochrany dat pro data objektů BLOB. Pro zajištění optimální ochrany dat objektů BLOB doporučuje Microsoft povolit všechny následující funkce ochrany dat:

- Dočasná odstranění kontejneru pro obnovení kontejneru, který byl odstraněn. Pokud chcete zjistit, jak povolit obnovitelné odstranění kontejneru, přečtěte si téma [povolení a Správa obnovitelného odstranění kontejnerů](soft-delete-container-enable.md).
- Správa verzí objektů BLOB pro automatické udržování předchozích verzí objektu BLOB. Pokud je povolená Správa verzí objektů blob, můžete obnovit předchozí verzi objektu blob, aby se data obnovila v případě, že se omylem změnila nebo odstranila. Informace o tom, jak povolit správu verzí objektů blob, najdete v tématu [povolení a správa verzí objektů BLOB](versioning-enable.md).
- Obnovitelné odstranění objektu BLOB pro obnovení objektu blob, snímku nebo verze, který byl odstraněn. Informace o tom, jak povolit obnovitelné odstranění objektů blob, najdete v tématu [povolení a Správa obnovitelného odstranění pro objekty blob](soft-delete-blob-enable.md).

Další informace o doporučeních Microsoftu pro ochranu dat najdete v tématu [Přehled ochrany dat](data-protection-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-soft-delete-works"></a>Jak funguje obnovitelné odstranění objektů BLOB

Když pro účet úložiště povolíte obnovitelné odstranění objektů blob, zadáte dobu uchování pro odstraněné objekty mezi 1 a 365 dny. Doba uchování indikuje, jak dlouho budou data po odstranění nebo přepsání k dispozici. Hodiny začnou dobu uchování, jakmile je objekt odstraněn nebo přepsán.

I když je doba uchování aktivní, můžete obnovit odstraněný objekt BLOB spolu s jeho snímky nebo odstraněnou verzí voláním operace [obnovit objekt BLOB](/rest/api/storageservices/undelete-blob) . Následující diagram znázorňuje, jak se odstraněné objekty mají obnovit, když je povolené obnovitelné odstranění objektu BLOB:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagram znázorňující, jak se dá obnovit odstraněný objekt BLOB":::

Dobu uchování obnovitelného odstranění můžete kdykoli změnit. Aktualizovaná doba uchování se vztahuje jenom na data, která se odstranila po změně doby uchování. Všechna data, která byla odstraněna před změnou doby uchování, se vztahují na dobu uchování, která byla v platnosti, kdy byla odstraněna.

Pokus o odstranění objektu, který je odstraněn, nemá vliv na čas jeho vypršení platnosti.

Pokud zakážete obnovitelné odstranění objektu blob, můžete v účtu úložiště dál přistupovat a obnovovat dočasně odstraněné objekty, dokud neuplyne doba uchování obnovitelného odstranění.

Správa verzí objektů BLOB je k dispozici pro obecné účely v2, objekty blob bloku a účty BLOB Storage. Účty úložiště s hierarchickým oborem názvů povoleným pro použití s Azure Data Lake Storage Gen2 nejsou aktuálně podporovány.

Verze 2017-07-29 a vyšší Azure Storage REST API podporuje obnovitelné odstranění objektu BLOB.

> [!IMPORTANT]
> Pro obnovení jednotlivého objektu blob, snímku nebo verze můžete použít jenom obnovitelné odstranění objektu BLOB. Chcete-li obnovit kontejner a jeho obsah, musí být pro účet úložiště také povoleno podmíněné odstranění kontejneru. Microsoft doporučuje povolit kompletní ochranu dat objektů BLOB pomocí provizorního odstranění a správy verzí objektů BLOB v kontejneru. Další informace najdete v tématu [Přehled ochrany dat](data-protection-overview.md).
>
> Obnovitelné odstranění objektu BLOB nechrání před odstraněním účtu úložiště. Pokud chcete chránit účet úložiště před odstraněním, nakonfigurujte zámek u prostředku účtu úložiště. Další informace o uzamykání účtu úložiště najdete v tématu [použití zámku Azure Resource Manager k účtu úložiště](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Způsob zpracování odstranění, když je povolené obnovitelné odstranění

Když je povolené obnovitelné odstraňování objektů blob, odstraní se objekty blob, které jsou odstraněné jako obnovitelné. Není vytvořen žádný snímek. Po uplynutí doby uchování dojde k trvalému odstranění odstraněného objektu BLOB.

Pokud objekt BLOB obsahuje snímky, objekt BLOB nejde odstranit, pokud se snímky taky neodstraní. Při odstranění objektu BLOB a jeho snímků jsou objekty blob i snímky označeny příznakem Soft-deleteded. Nevytvoří se žádné nové snímky.

Můžete také odstranit jeden nebo více aktivních snímků bez odstranění základního objektu BLOB. V takovém případě se snímek vymaže.

Odstraněné objekty jsou neviditelné, pokud nejsou explicitně zobrazeny nebo uvedeny. Další informace o tom, jak vypsat objekty odstraněné s příčtením, najdete v tématu [Správa a obnovení objektů BLOB s odstraněnou možností](soft-delete-blob-manage.md)

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Jak se zpracovávají přepsání, když je povolené obnovitelné odstranění

Voláním operace, jako je například [Put BLOB](/rest/api/storageservices/put-blob), [seznam blokovaných](/rest/api/storageservices/put-block-list) [objektů nebo objekt BLOB kopírování](/rest/api/storageservices/copy-blob) , Přepisuje data v objektu BLOB. Když je povolené obnovitelné odstraňování objektů blob, přepsání objektu BLOB automaticky vytvoří před operací zápisu částečný odstraněný snímek stavu objektu BLOB. Po vypršení doby uchování se snímek s odstraněným odstraněnou trvale odstraní.

Obnovitelné odstraněné snímky jsou neviditelné, pokud nejsou explicitně odstraněné objekty výslovně zobrazeny nebo uvedeny. Další informace o tom, jak vypsat objekty odstraněné s příčtením, najdete v tématu [Správa a obnovení objektů BLOB s odstraněnou možností](soft-delete-blob-manage.md)

Pro ochranu operace kopírování musí být pro cílový účet úložiště povolený částečný Delete objektu BLOB.

Obnovitelné odstranění objektu BLOB nechrání proti operacím zápis metadat nebo vlastností objektu BLOB. Při aktualizaci metadat nebo vlastností objektu BLOB se nevytvoří žádný snímek s odstraněným jemným odstraněním.

Obnovitelné odstranění objektu BLOB neumožňuje přepsat ochranu objektů BLOB v archivní úrovni. Pokud je objekt BLOB v archivní úrovni přepsaný novým objektem blob na libovolné úrovni, přepsaný objekt BLOB se trvale odstraní.

Pro účty Premium Storage se nevyřízené snímky nepočítají směrem k limitu pro objekty blob 100 snímků.

### <a name="restoring-soft-deleted-objects"></a>Obnovení odstraněných objektů

Dočasně odstraněné objekty blob můžete obnovit voláním operace [obnovení objektu BLOB](/rest/api/storageservices/undelete-blob) v rámci doby uchování. Operace obnovení **objektu BLOB** obnoví objekt BLOB a všechny místně odstraněné snímky, které jsou k němu přidružené. Všechny snímky, které byly odstraněny během doby uchování, budou obnoveny.

Volání příkazového obnovení **objektu BLOB** u objektu blob, který není jemným odstraněním, obnoví všechny obnovitelné snímky odstraněné, které jsou přidruženy k objektu BLOB. Pokud objekt BLOB nemá žádné snímky a není měkký a neodstraní se, pak volání metody **Undelete BLOB** nemá žádný vliv.

Pokud chcete povýšit odstraněný snímek na základní objekt blob, nejdřív nahlaste **objekt BLOB** v základním objektu BLOB a obnovte objekt BLOB a jeho snímky. Potom zkopírujte požadovaný snímek přes základní objekt BLOB. Snímek můžete také zkopírovat do nového objektu BLOB.

Data v dočasném objektovém objektu BLOB nebo snímku nelze číst, dokud nebude objekt obnoven.

Další informace o tom, jak obnovit odstraněné objekty, najdete v tématu [Správa a obnovení objektů BLOB s odstraněnou možností](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Měkké odstranění a správa verzí objektu BLOB

Pokud pro účet úložiště povolíte správu verzí objektů BLOB a obnovitelné odstranění objektů blob, přepsání objektu BLOB automaticky vytvoří novou verzi. Nová verze není Odstraněná a při vypršení doby uchování dočasného odstranění se neodebere. Nevytvořily se žádné snímky odstraněné. Když odstraníte objekt blob, bude aktuální verze objektu BLOB předchozí verzí a aktuální verze se odstraní. Nevytvoří se žádná nová verze a nevytvoří se žádné snímky odstraněné.

Povolení obnovitelného odstranění a správy verzí společně chrání verze objektů BLOB před odstraněním. Když je povolené obnovitelné odstranění, vytvoří se verze s odstraněnou verzí. Pomocí operace obnovení **objektu BLOB** můžete obnovit odstraněnou verzi, pokud existuje aktuální verze objektu BLOB. Pokud není k dispozici žádná aktuální verze, je nutné před voláním operace **Undelete objektu BLOB** zkopírovat předchozí verzi do aktuální verze.

> [!NOTE]
> Volání operace **obnovení objektu BLOB** u odstraněného objektu blob, když je povolená Správa verzí, obnoví všechny odstraněné verze nebo snímky, ale neobnoví základní objekt BLOB. Chcete-li obnovit základní objekt blob, zvyšte úroveň předchozí verze tak, že ji zkopírujete do základního objektu BLOB.

Microsoft doporučuje, aby se pro vaše účty úložiště povolila jak Správa verzí, tak i předběžné odstranění objektů BLOB pro optimální ochranu dat. Další informace o použití správy verzí objektů BLOB a obnovitelného odstranění najdete v tématu [Správa verzí objektů BLOB a obnovitelné odstranění](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Dočasná odstranění ochrany objektu BLOB podle operace

Následující tabulka popisuje očekávané chování operací odstranění a zápisu v případě, že je povoleno podmíněné odstranění objektů blob, a to buď s verzí objektu blob, nebo bez něj:

| REST API operace | Obnovitelné odstranění povoleno | Obnovitelné odstranění a správa verzí povolena |
|--|--|--|
| [Odstranit účet úložiště](/rest/api/storagerp/storageaccounts/delete) | Ve výstupu nedošlo k žádné změně. Kontejnery a objekty BLOB v odstraněném účtu nejde obnovit. | Ve výstupu nedošlo k žádné změně. Kontejnery a objekty BLOB v odstraněném účtu nejde obnovit. |
| [Odstranění kontejneru](/rest/api/storageservices/delete-container) | Ve výstupu nedošlo k žádné změně. Objekty BLOB v odstraněném kontejneru nejsou obnovitelné. | Ve výstupu nedošlo k žádné změně. Objekty BLOB v odstraněném kontejneru nejsou obnovitelné. |
| [Odstranění objektu blob](/rest/api/storageservices/delete-blob) | Při použití k odstranění objektu BLOB je tento objekt BLOB označený jako měkký odstraněný. <br /><br /> Při použití k odstranění snímku objektu BLOB je snímek označený jako měkký odstraněný. | Při použití k odstranění objektu BLOB se aktuální verze stala předchozí verzí a aktuální verze se odstraní. Nevytvoří se žádná nová verze a nevytvoří se žádné snímky odstraněné.<br /><br /> Pokud se používá k odstranění verze objektu blob, verze je označená jako měkké Odstraněná. |
| [Obnovit objekt BLOB](/rest/api/storageservices/delete-blob) | Obnoví objekt BLOB a všechny snímky, které se odstranily v rámci doby uchování. | Obnoví objekt BLOB a všechny verze, které byly odstraněny v rámci doby uchování. |
| [Vložení objektu blob](/rest/api/storageservices/put-blob)<br />[Seznam blokovaných umístění](/rest/api/storageservices/put-block-list)<br />[Zkopírování objektu blob](/rest/api/storageservices/copy-blob)<br />[Kopírovat objekt BLOB z adresy URL](/rest/api/storageservices/copy-blob) | Pokud je metoda volána na aktivním objektu blob, bude automaticky vygenerován snímek stavu objektu BLOB před operací. <br /><br /> Je-li tato metoda volána u objektu BLOB s odstraněným podmnožinou, je snímek předchozího stavu objektu BLOB vytvořen pouze v případě, že je nahrazen objektem BLOB stejného typu. Pokud je objekt BLOB jiného typu, všechna existující dočasná Odstraněná data se trvale odstraní. | Nová verze, která zachycuje stav objektu BLOB před operací, se vygeneruje automaticky. |
| [Blok vložení](/rest/api/storageservices/put-block) | Pokud se použije k potvrzení bloku na aktivní objekt blob, nedojde k žádné změně.<br /><br />Pokud se použije k potvrzení bloku do objektu blob, který je jemný odstraněný, vytvoří se nový objekt BLOB a automaticky se vygeneruje snímek, který zachytí stav objektu BLOB s příčárkou odstraněnou. | Ve výstupu nedošlo k žádné změně. |
| [Vložit stránku](/rest/api/storageservices/put-page)<br />[Vložit stránku z adresy URL](/rest/api/storageservices/put-page-from-url) | Ve výstupu nedošlo k žádné změně. Data objektu blob stránky, která jsou přepsána nebo vymazána pomocí této operace, nejsou uložena a nelze je obnovit. | Ve výstupu nedošlo k žádné změně. Data objektu blob stránky, která jsou přepsána nebo vymazána pomocí této operace, nejsou uložena a nelze je obnovit. |
| [Připojit blok](/rest/api/storageservices/append-block)<br />[Připojit blok z adresy URL](/rest/api/storageservices/append-block-from-url) | Ve výstupu nedošlo k žádné změně. | Ve výstupu nedošlo k žádné změně. |
| [Nastavení vlastností objektu BLOB](/rest/api/storageservices/set-blob-properties) | Ve výstupu nedošlo k žádné změně. Přepsané vlastnosti objektu BLOB nelze obnovit. | Ve výstupu nedošlo k žádné změně. Přepsané vlastnosti objektu BLOB nelze obnovit. |
| [Nastavení metadat objektu BLOB](/rest/api/storageservices/set-blob-metadata) | Ve výstupu nedošlo k žádné změně. Přepsaná metadata objektu BLOB nelze obnovit. | Nová verze, která zachycuje stav objektu BLOB před operací, se vygeneruje automaticky. |
| [Nastavení úrovně objektu blob](/rest/api/storageservices/set-blob-tier) | Základní objekt BLOB se přesune na novou úroveň. Všechny aktivní nebo měkké odstraněné snímky zůstanou v původní úrovni. Nevytvořil se žádný snímek s odstraněnou odstraněnou. | Základní objekt BLOB se přesune na novou úroveň. Všechny aktivní nebo dočasně odstraněné verze zůstanou v původní úrovni. Není vytvořena žádná nová verze. |

## <a name="pricing-and-billing"></a>Ceny a fakturace

Všechna tichá Odstraněná data se účtují stejnou sazbou jako aktivní data. Neúčtují se vám poplatky za data, která se trvale odstraní po uplynutí doby uchování.

Pokud povolíte obnovitelné odstranění, společnost Microsoft doporučuje používat krátkou dobu uchovávání, aby lépe porozuměla tomu, jak bude tato funkce mít na vyúčtování vliv. Minimální doporučená doba uchování je sedm dní.

Povolení obnovitelného odstranění často přepsaných dat může mít za následek zvýšené poplatky za kapacitu úložiště a vyšší latenci při výpisu objektů BLOB. Tyto dodatečné náklady a latence můžete zmírnit uložením často přepsaných dat do samostatného účtu úložiště, kde je deaktivované obnovitelné odstranění.

Při přepisování nebo odstraňování objektů BLOB se vám neúčtují transakce související s automatickým generováním snímků nebo verzí. Účtují se za volání operace **Undelete objektu BLOB** v transakční sazbě pro operace zápisu.

Další informace o cenách pro Blob Storage najdete na stránce s [cenami BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) .

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Obnovitelné odstranění objektů BLOB a disky virtuálních počítačů  

Obnovitelné odstranění objektu BLOB je k dispozici pro nespravované disky úrovně Premium i Standard, které jsou objekty blob stránky v rámci pokrývání. Obnovitelné odstranění vám může pomáhat obnovit data Odstraněná nebo přepsaná pomocí seznamu **Odstranit objekt BLOB**, **Vložit objekt BLOB**, **seznam blokovaných bloků** a kopírovat pouze operace **objektů BLOB** .

Data, která jsou přepsána voláním **Vložení stránky** , nelze obnovit. Virtuální počítač Azure se zapisuje na nespravovaný disk pomocí volání na **stránku Put**, takže pomocí obnovitelného odstranění vrátíte zpátky zápisy na nespravovaný disk z virtuálního počítače Azure, není podporovaný scénář.

## <a name="next-steps"></a>Další kroky

- [Povolení obnovitelného odstranění pro objekty blob](./soft-delete-blob-enable.md)
- [Správa a obnovení objektů BLOB s odstraněnou přípouštějící](soft-delete-blob-manage.md)
- [Správa verzí objektů BLOB](versioning-overview.md)