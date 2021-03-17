---
title: Řešení potíží s Azure Data Box, Azure Data Box Heavy
description: Popisuje řešení potíží, které se zobrazují v Azure Data Box a Azure Data Box Heavy při kopírování dat do těchto zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124670"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Řešení potíží souvisejících se Azure Data Box a Azure Data Box Heavy

Tento článek podrobně popisuje, jak řešit problémy, které se mohou zobrazit při použití Azure Data Box nebo Azure Data Box Heavy pro import objednávek. Článek obsahuje seznam možných chyb, ke kterým dochází, když se data zkopírují do Data Box nebo když se data nahrají z Data Box pro pořadí importu.

Informace v tomto článku se nevztahují na objednávky exportu vytvořené pro Data Box.

## <a name="error-classes"></a>Třídy chyb

Chyby v Data Box a Data Box Heavy jsou shrnuty takto:

| Kategorie chyby *        | Popis        | Doporučená akce    |
|----------------------------------------------|---------|--------------------------------------|
| Názvy kontejnerů nebo sdílených složek | Názvy kontejnerů nebo sdílených složek nedodržují pravidla pro pojmenování Azure.  |Stažení seznamů chyb. <br> Přejmenujte kontejnery nebo sdílené složky. [Další informace](#container-or-share-name-errors).  |
| Omezení velikosti kontejneru nebo sdílení | Celkový objem dat v kontejnerech nebo sdílených složkách překračuje limit Azure.   |Stažení seznamů chyb. <br> Snižte celkový objem dat v kontejneru nebo sdílené složce. [Další informace](#container-or-share-size-limit-errors).|
| Omezení velikosti objektu nebo souboru | Objekt nebo soubory v kontejnerech nebo sdílených složkách přesahují limit Azure.|Stažení seznamů chyb. <br> Zmenšete velikost souboru v kontejneru nebo sdílené složce. [Další informace](#object-or-file-size-limit-errors). |    
| Data nebo typ souboru | Formát dat nebo typ souboru se nepodporuje. |Stažení seznamů chyb. <br> U objektů blob stránky nebo spravovaných disků zajistěte, aby byla data zarovnaná 512 bajtů a zkopírována do předem vytvořených složek. [Další informace](#data-or-file-type-errors). |
| Nekritické chyby objektů BLOB nebo souborů  | Názvy objektů BLOB nebo souborů nedodržují pravidla pro pojmenování Azure nebo tento typ souboru se nepodporuje. | Tyto objekty blob nebo soubory se nemusí kopírovat nebo se můžou změnit názvy. [Přečtěte si, jak tyto chyby opravit](#non-critical-blob-or-file-errors). |

\* První čtyři kategorie chyb jsou kritické chyby a je nutné je opravit předtím, než budete moci pokračovat v přípravě na odeslání.


## <a name="container-or-share-name-errors"></a>Chyby názvu kontejneru nebo sdílené složky

Jedná se o chyby týkající se kontejneru a názvů sdílených složek.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Popis chyby:** Název kontejneru nebo sdílené složky musí být dlouhý 3 až 63 znaků. 

**Navrhované řešení:** Složka pod Data Box nebo Data Box Heavy sdílené složky (SMB/NFS), do které jste zkopírovali data, se ve vašem účtu úložiště stávají kontejnerem Azure. 

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní pro zařízení si stáhněte a zkontrolujte soubory s chybami a Identifikujte názvy složek s problémy.
- Změňte název složky pod Data Box nebo Data Box Heavy sdílení, abyste se ujistili, že:

    - Název obsahuje 3 až 63 znaků.
    - Názvy můžou obsahovat jenom písmena, číslice a spojovníky.
    - Názvy nesmí začínat ani končit pomlčkami.
    - Názvy nemůžou mít po sobě jdoucí spojovníky.
    - Příklady platných názvů: `my-folder-1` , `my-really-extra-long-folder-111`
    - Příklady názvů, které nejsou platné: `my-folder_1` , `my` , `--myfolder` , `myfolder--` , `myfolder!`

    Další informace najdete v tématu konvence pojmenování Azure pro [názvy kontejnerů](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [názvy sdílených složek](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Popis chyby:** Název kontejneru nebo sdílené složky musí obsahovat jenom písmena, číslice nebo spojovníky.

**Navrhované řešení:** Složka pod Data Box nebo Data Box Heavy sdílené složky (SMB/NFS), do které jste zkopírovali data, se ve vašem účtu úložiště stávají kontejnerem Azure. 

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní pro zařízení si stáhněte a zkontrolujte soubory s chybami a Identifikujte názvy složek s problémy.
- Změňte název složky pod Data Box nebo Data Box Heavy sdílení, abyste se ujistili, že:

    - Název obsahuje 3 až 63 znaků.
    - Názvy můžou obsahovat jenom písmena, číslice a spojovníky.
    - Názvy nesmí začínat ani končit pomlčkami.
    - Názvy nemůžou mít po sobě jdoucí spojovníky.
    - Příklady platných názvů: `my-folder-1` , `my-really-extra-long-folder-111`
    - Příklady názvů, které nejsou platné: `my-folder_1` , `my` , `--myfolder` , `myfolder--` , `myfolder!`

    Další informace najdete v tématu konvence pojmenování Azure pro [názvy kontejnerů](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [názvy sdílených složek](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Popis chyby:** Názvy kontejnerů a názvy sdílených složek nemůžou začínat ani končit pomlčkami a nesmí mít po sobě jdoucí spojovníky.

**Navrhované řešení:** Složka pod Data Box nebo Data Box Heavy sdílené složky (SMB/NFS), do které jste zkopírovali data, se ve vašem účtu úložiště stávají kontejnerem Azure. 

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní pro zařízení si stáhněte a zkontrolujte soubory s chybami a Identifikujte názvy složek s problémy.
- Změňte název složky pod Data Box nebo Data Box Heavy sdílení, abyste se ujistili, že:

    - Název obsahuje 3 až 63 znaků.
    - Názvy můžou obsahovat jenom písmena, číslice a spojovníky.
    - Názvy nesmí začínat ani končit pomlčkami.
    - Názvy nemůžou mít po sobě jdoucí spojovníky.
    - Příklady platných názvů: `my-folder-1` , `my-really-extra-long-folder-111`
    - Příklady názvů, které nejsou platné: `my-folder_1` , `my` , `--myfolder` , `myfolder--` , `myfolder!`

    Další informace najdete v tématu konvence pojmenování Azure pro [názvy kontejnerů](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [názvy sdílených složek](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Popis chyby**: adresář nebo název kontejneru obsahují neplatné znaky.

**Navrhované řešení**: adresář nebo názvy kontejnerů, které jste zkopírovali, obsahují nepodporované znaky.

- Na stránce připojit a kopírovat místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory s chybami a Identifikujte názvy složek s problémy. 
- Přejmenujte adresář nebo kontejnery, abyste měli jistotu, že jsou kompatibilní se zásadami vytváření názvů Azure.

Další informace najdete v tématu Zásady vytváření názvů Azure pro [adresáře](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)   a [kontejnery](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names).

## <a name="container-or-share-size-limit-errors"></a>Počet chyb omezení velikosti kontejneru nebo sdílení

Jedná se o chyby související s daty, která přesahují velikost povolených dat v kontejneru nebo sdílené složce.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Popis chyby:** Sdílená složka Azure omezuje sdílení na 5 TiB dat a v účtu úložiště nejsou povolené velké sdílené složky. Tento limit byl pro některé sdílené složky překročen.

**Navrhované řešení:** Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.

- Identifikujte složky, které tento problém mají, z protokolů chyb a ujistěte se, že jsou soubory v této složce pod 5 TiB.
- Limit 5 TiB se nevztahuje na účet úložiště, který povoluje velké sdílené složky. Při umísťování vaší objednávky ale musíte mít nakonfigurované velké sdílené složky. 
  - Kontaktujte [Podpora Microsoftu](data-box-disk-contact-microsoft-support.md) a požádejte o nový expediční štítek.
  - [Povolte v účtu úložiště velké sdílené složky.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Rozbalte sdílené složky v účtu úložiště](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) a nastavte kvótu na 100 TIB.
  
  
## <a name="object-or-file-size-limit-errors"></a>Počet chyb omezení velikosti objektu nebo souboru

Jedná se o chyby související s daty překračujícími maximální velikost objektu nebo souboru, který je povolený v Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Popis chyby:** Velikost souboru překračuje maximální velikost souboru pro nahrání.

**Navrhované řešení:** Velikost objektu BLOB nebo souborů překračuje maximální povolený limit pro nahrání.

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
- Ujistěte se, že velikost objektů BLOB a souborů nepřekračuje limity velikosti objektů Azure.

## <a name="data-or-file-type-errors"></a>Chyby typu dat nebo souboru

Jedná se o chyby související s nepodporovaným typem souboru nebo datovým typem, který se nachází v kontejneru nebo sdílené složce. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Popis chyby:** Objekt BLOB nebo soubor je nesprávně zarovnán.

**Navrhované řešení:** Sdílená položka objektu blob stránky v Data Box nebo Data Box Heavy podporuje pouze soubory, které jsou zarovnané 512 bajtů (například VHD/VHDX). Všechna data zkopírovaná do sdílené složky objektů blob stránky se nahrají do Azure jako objekty blob stránky.

Odeberte všechna data, která nejsou typu VHD/VHDX, ze sdílené složky objektů blob stránky. Pro obecná data můžete použít sdílené složky pro objekty blob bloku nebo soubory Azure.

Další informace najdete v tématu [Přehled objektů blob stránky](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Popis chyby:** Ve sdílené složce spravovaného disku se nachází nepodporovaný typ souboru. Jsou povoleny pouze pevné virtuální pevné disky.

**Navrhované řešení:**

- Ujistěte se, že jste k vytváření spravovaných disků nahráli pouze pevné virtuální pevné disky.
- Soubory VHDX nebo **dynamické** a **rozdílové** virtuální pevné disky nejsou podporovány.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Popis chyby:** Adresář není povolený v žádné z již existujících složek pro spravované disky. V těchto složkách jsou povoleny pouze pevné virtuální pevné disky.

**Navrhované řešení:** U spravovaných disků se v každé sdílené složce vytvoří následující tři složky, které odpovídají kontejnerům v účtu úložiště: SSD úrovně Premium, HDD úrovně Standard a SSD úrovně Standard. Tyto složky odpovídají úrovni výkonu pro spravovaný disk.

- Ujistěte se, že jste zkopírovali data objektů blob stránky (VHD) do jedné z těchto existujících složek.
- Složka nebo adresář nejsou v těchto existujících složkách povoleny. Odeberte všechny složky, které jste vytvořili v existujících složkách.

Další informace najdete v tématu [kopírování na spravované disky](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Popis chyby:** V systému Linux nejsou povoleny symbolické odkazy. 

**Navrhované řešení:** Symbolické odkazy jsou obvykle odkazy, kanály a další takové soubory. Buď odeberte odkazy, nebo vyřešte odkazy a zkopírujte data.


## <a name="non-critical-blob-or-file-errors"></a>Nekritické chyby objektů BLOB nebo souborů

Všechny nekritické chyby týkající se názvů objektů blob, souborů nebo kontejnerů, které se zobrazují během kopírování dat, jsou shrnuty v následující části. Pokud jsou tyto chyby k dispozici, názvy se upraví tak, aby odpovídaly konvencím pojmenování Azure. Odpovídající stav objednávky pro nahrání dat bude **dokončen s upozorněními**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Popis chyby:** Názvy objektů BLOB nebo souborů obsahují nepodporované řídicí znaky.

**Navrhované řešení:** Objekty blob nebo soubory, které jste zkopírovali, obsahují názvy s nepodporovanými znaky.

Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
Odeberte nebo přejmenujte soubory, aby se odebraly nepodporované znaky.

Další informace najdete v tématu konvence pojmenování Azure pro [názvy objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Popis chyby:** Názvy objektů BLOB nebo souborů obsahují neplatné znaky.

**Navrhované řešení:** Objekty blob nebo soubory, které jste zkopírovali, obsahují názvy s nepodporovanými znaky.

Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
Odeberte nebo přejmenujte soubory, aby se odebraly nepodporované znaky.

Další informace najdete v tématu konvence pojmenování Azure pro [názvy objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Popis chyby:** Názvy objektů BLOB nebo souborů končí chybnými znaky.

**Navrhované řešení:** Objekty blob nebo soubory, které jste zkopírovali, obsahují názvy s nepodporovanými znaky.

Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
Odeberte nebo přejmenujte soubory, aby se odebraly nepodporované znaky.

Další informace najdete v tématu konvence pojmenování Azure pro [názvy objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Popis chyby:** Název objektu BLOB nebo souboru obsahuje příliš mnoho segmentů cesty.

**Navrhované řešení:** Objekty blob nebo soubory, které jste zkopírovali, překračují maximální počet segmentů cesty. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače, například lomítkem nebo.

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
- Ujistěte se, že [názvy objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) odpovídají konvencím názvů Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Popis chyby:** Název objektu BLOB nebo souboru je příliš dlouhý.

**Navrhované řešení:** Názvy objektů BLOB nebo souborů překračují maximální délku.

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
- Název objektu BLOB nesmí překročit 1 024 znaků.
- Odeberte nebo přejmenujte objekt BLOB nebo soubory tak, aby názvy nepřesáhly 1024 znaků.

Další informace najdete v tématu konvence pojmenování Azure pro názvy objektů BLOB a názvy souborů.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Popis chyby:** Jeden z segmentů v názvu objektu BLOB nebo souboru je příliš dlouhý.

**Navrhované řešení:** Jeden z segmentů cesty v objektu BLOB nebo názvu souboru překračuje maximální počet znaků. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače, například lomítkem nebo.

- Na stránce **připojit a kopírovat** místního webového uživatelského rozhraní si stáhněte a zkontrolujte soubory chyb.
- Ujistěte se, že [názvy objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) odpovídají konvencím názvů Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Popis chyby:** Pro sdílené složky spravovaného disku jsou zadané nesprávné názvy kontejnerů.

**Navrhované řešení:** U spravovaných disků se v každé sdílené složce vytvoří následující složky, které odpovídají kontejnerům ve vašem účtu úložiště: SSD úrovně Premium, HDD úrovně Standard a SSD úrovně Standard. Tyto složky odpovídají úrovni výkonu pro spravovaný disk.

- Ujistěte se, že jste zkopírovali data objektů blob stránky (VHD) do jedné z těchto existujících složek. Do Azure se nahrají jenom data z těchto existujících kontejnerů.
- Všechny ostatní složky, které jsou vytvořeny na stejné úrovni jako SSD úrovně Premium, HDD úrovně Standard a SSD úrovně Standard, neodpovídají platné úrovni výkonu a nelze je použít.
- Odeberte soubory nebo složky vytvořené mimo úrovně výkonu.

Další informace najdete v tématu [kopírování na spravované disky](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Další kroky

- Seznamte se s [požadavky na systém data box BLOB Storage](data-box-system-requirements-rest.md).