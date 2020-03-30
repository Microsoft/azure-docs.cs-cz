---
title: Řešení problémů s Azure Data Box, Azure Data Box Heavy
description: Popisuje, jak řešit problémy, které se při kopírování dat do těchto zařízení zobrazí v Azure Data Box a Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560061"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Řešení problémů souvisejících s Azure Data Box a Azure Data Box Heavy

Tento článek podrobně popisuje, jak řešit problémy, které se můžou zobrazit při používání Azure Data Box nebo Azure Data Box Heavy. Článek obsahuje seznam možných chyb, které se zkopírují do Datové schránky nebo kdy jsou data z Datové schránky nahrána.

## <a name="error-classes"></a>Třídy chyb

Chyby v datových schránce a datové schránce Heavy jsou shrnuty takto:

| Kategorie chyb*        | Popis        | Doporučená akce    |
|----------------------------------------------|---------|--------------------------------------|
| Názvy kontejnerů nebo sdílení | Názvy kontejnerů nebo sdílení nedodržují pravidla pojmenování Azure.  |Stáhněte si seznamy chyb. <br> Přejmenujte kontejnery nebo sdílené složky. [Další informace](#container-or-share-name-errors).  |
| Omezení velikosti kontejneru nebo sdílené složky | Celkový počet dat v kontejnerech nebo sdílených složek překračuje limit Azure.   |Stáhněte si seznamy chyb. <br> Snižte celková data v kontejneru nebo sdílené složce. [Další informace](#container-or-share-size-limit-errors).|
| Omezení velikosti objektu nebo souboru | Objekt nebo soubory v kontejnerech nebo sdílených složek překračuje limit Azure.|Stáhněte si seznamy chyb. <br> Zmenšete velikost souboru v kontejneru nebo sdílené složce. [Další informace](#object-or-file-size-limit-errors). |    
| Datový nebo typ souboru | Formát dat nebo typ souboru není podporován. |Stáhněte si seznamy chyb. <br> U objektů BLOB stránky nebo spravovaných disků zajistěte, aby data byla zarovnána a zkopírována do předem vytvořených složek. [Další informace](#data-or-file-type-errors). |
| Nekritické chyby objektu blob nebo souboru  | Názvy objektů blob nebo souborů nedodržují pravidla pojmenování Azure nebo typ souboru není podporován. | Tyto objekty blob nebo soubory nemusí být zkopírovány nebo názvy mohou být změněny. [Přečtěte si, jak tyto chyby opravit](#non-critical-blob-or-file-errors). |

\*První čtyři kategorie chyb jsou kritické chyby a musí být opraveny, než budete moci přistoupit k přípravě na odeslání.


## <a name="container-or-share-name-errors"></a>Chyby názvů kontejnerů nebo sdílené položky

Jedná se o chyby týkající se kontejneru a názvy sdílení.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Popis chyby:** Název kontejneru nebo sdílené položky musí být mezi 3 a 63 znaky. 

**Navrhované řešení:** Složka pod sdílenou složkou Data Box nebo Data Box Heavy (SMB/NFS), do které jste zkopírovali data, se stane kontejnerem Azure ve vašem účtu úložiště. 

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní zařízení stáhněte a zkontrolujte soubory chyb, abyste identifikovali názvy složek s problémy.
- Změňte název složky pod sdílenou složkou Data Box nebo Data Box Heavy a ujistěte se, že:

    - Název má mezi 3 a 63 znaky.
    - Názvy mohou mít pouze písmena, čísla a pomlčky.
    - Názvy nemohou začínat ani končit spojovníky.
    - Názvy nemohou mít po sobě jdoucí spojovníky.
    - Příklady platných `my-folder-1`názvů: ,`my-really-extra-long-folder-111`
    - Příklady `my-folder_1`neplatných názvů: , `my` `--myfolder`, `myfolder--`, .`myfolder!`

    Další informace naleznete v konvencích pojmenování Azure pro [názvy kontejnerů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [názvy sdílení](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Popis chyby:** Název kontejneru nebo sdílené položky se musí skládat pouze z písmen, čísel nebo spojovníků.

**Navrhované řešení:** Složka pod sdílenou složkou Data Box nebo Data Box Heavy (SMB/NFS), do které jste zkopírovali data, se stane kontejnerem Azure ve vašem účtu úložiště. 

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní zařízení stáhněte a zkontrolujte soubory chyb, abyste identifikovali názvy složek s problémy.
- Změňte název složky pod sdílenou složkou Data Box nebo Data Box Heavy a ujistěte se, že:

    - Název má mezi 3 a 63 znaky.
    - Názvy mohou mít pouze písmena, čísla a pomlčky.
    - Názvy nemohou začínat ani končit spojovníky.
    - Názvy nemohou mít po sobě jdoucí spojovníky.
    - Příklady platných `my-folder-1`názvů: ,`my-really-extra-long-folder-111`
    - Příklady `my-folder_1`neplatných názvů: , `my` `--myfolder`, `myfolder--`, .`myfolder!`

    Další informace naleznete v konvencích pojmenování Azure pro [názvy kontejnerů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [názvy sdílení](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Popis chyby:** Názvy kontejnerů a názvy sdílení nemohou začínat nebo končit spojovníky a nemohou mít po sobě jdoucí spojovníky.

**Navrhované řešení:** Složka pod sdílenou složkou Data Box nebo Data Box Heavy (SMB/NFS), do které jste zkopírovali data, se stane kontejnerem Azure ve vašem účtu úložiště. 

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní zařízení stáhněte a zkontrolujte soubory chyb, abyste identifikovali názvy složek s problémy.
- Změňte název složky pod sdílenou složkou Data Box nebo Data Box Heavy a ujistěte se, že:

    - Název má mezi 3 a 63 znaky.
    - Názvy mohou mít pouze písmena, čísla a pomlčky.
    - Názvy nemohou začínat ani končit spojovníky.
    - Názvy nemohou mít po sobě jdoucí spojovníky.
    - Příklady platných `my-folder-1`názvů: ,`my-really-extra-long-folder-111`
    - Příklady `my-folder_1`neplatných názvů: , `my` `--myfolder`, `myfolder--`, .`myfolder!`

    Další informace naleznete v konvencích pojmenování Azure pro [názvy kontejnerů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [názvy sdílení](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Chyby omezení velikosti kontejneru nebo sdílené složky

Jedná se o chyby související s daty překračujícími velikost dat povolených v kontejneru nebo sdílené složce.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Popis chyby:** Sdílená složka Azure omezuje sdílenou složku na 5 TB dat. Tento limit byl u některých akcií překročen.

**Navrhované řešení:** Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.

Identifikujte složky, které mají tento problém z protokolů chyb a ujistěte se, že soubory v této složce jsou pod 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Chyby omezení velikosti objektu nebo souboru

Jedná se o chyby související s daty překračujícími maximální velikost objektu nebo souboru, který je povolen v Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Popis chyby:** Velikost souboru překračuje maximální velikost souboru pro nahrávání.

**Navrhované řešení:** Objekt blob nebo velikostsouboru překračují maximální limit povolený pro nahrávání.

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
- Ujistěte se, že velikost objektu blob a souborů nepřekročí omezení velikosti objektu Azure.

## <a name="data-or-file-type-errors"></a>Chyby typu dat nebo souboru

Jedná se o chyby související s nepodporovaným typem souboru nebo datovým typem nalezeným v kontejneru nebo sdílené složce. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Popis chyby:** Objekt blob nebo soubor je nesprávně zarovnán.

**Navrhované řešení:** Sdílená složka objektu blob stránky v datové schránce nebo datové schránce Heavy podporuje pouze soubory, které jsou zarovnané o 512 bajtů (například VHD/VHDX). Všechna data zkopírovaná do sdílené složky objektů blob stránky se nahrají do Azure jako objekty blob stránky.

Odeberte všechna data mimo virtuální pevné disky/VHDX ze sdílené položky objektu blob stránky. Sdílené složky můžete použít pro objekt blob bloku nebo soubory Azure pro obecná data.

Další informace naleznete v [tématu Přehled objektů BLOB stránky](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Popis chyby:** Ve spravované sdílené složce disku je přítomen nepodporovaný typ souboru. Povoleny jsou pouze pevné VHD.

**Navrhované řešení:**

- Ujistěte se, že jste nahráli pouze pevné virtuální disky k vytvoření spravovaných disků.
- VHDX soubory nebo **dynamické** a **differencing** VHDnejsou podporovány.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Popis chyby:** Adresář není povolen v žádné z již existujících složek pro spravované disky. V těchto složkách jsou povoleny pouze pevné visuty.

**Navrhované řešení:** Pro spravované disky jsou v rámci každé sdílené složky vytvořeny následující tři složky, které odpovídají kontejnerům ve vašem účtu úložiště: Premium SSD, Standard HDD a Standard SSD. Tyto složky odpovídají úrovni výkonu spravovaného disku.

- Ujistěte se, že zkopírujete data objektů blob stránky (VD) do jedné z těchto existujících složek.
- Složka nebo adresář není v těchto existujících složkách povolen. Odeberte všechny složky, které jste vytvořili v již existujících složkách.

Další informace naleznete v [tématu Kopírování na spravované disky](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Popis chyby:** Symbolické odkazy nejsou v Linuxu povoleny. 

**Navrhované řešení:** Symbolické odkazy jsou obvykle odkazy, kanály a další takové soubory. Odeberte odkazy nebo je vyřešte a zkopírujte data.


## <a name="non-critical-blob-or-file-errors"></a>Nekritické chyby objektu blob nebo souboru

Všechny nekritické chyby související s názvy objektů BLOB, souborů nebo kontejnerů, které se zoátřují během kopírování dat, jsou shrnuty v následující části. Pokud jsou k dispozici tyto chyby, názvy budou upraveny tak, aby odpovídaly konvencím pojmenování Azure. Odpovídající stav objednávky pro nahrání dat bude **dokončen s upozorněními**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Popis chyby:** Názvy objektů blob nebo souborů obsahují nepodporované řídicí znaky.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali, obsahují názvy s nepodporovanými znaky.

Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
Odeberte nebo přejmenujte soubory, abyste odstranili nepodporované znaky.

Další informace najdete v tématu Azure konvence pojmenování pro [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Popis chyby:** Názvy objektů blob nebo souborů obsahují neplatné znaky.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali, obsahují názvy s nepodporovanými znaky.

Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
Odeberte nebo přejmenujte soubory, abyste odstranili nepodporované znaky.

Další informace najdete v tématu Azure konvence pojmenování pro [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Popis chyby:** Názvy objektů blob nebo souborů končí chybnými znaky.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali, obsahují názvy s nepodporovanými znaky.

Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
Odeberte nebo přejmenujte soubory, abyste odstranili nepodporované znaky.

Další informace najdete v tématu Azure konvence pojmenování pro [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Popis chyby:** Název objektu blob nebo souborobsahuje příliš mnoho segmentů cesty.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali, překračují maximální počet segmentů cesty. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače, například lomítkem /.

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
- Ujistěte se, že [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) odpovídají konvencím pojmenování Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Popis chyby:** Název objektu blob nebo souboru je příliš dlouhý.

**Navrhované řešení:** Objekt blob nebo názvy souborů překračují maximální délku.

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
- Název objektu blob nesmí překročit 1 024 znaků.
- Odeberte nebo přejmenujte objekt blob nebo soubory tak, aby názvy nepřekročily 1024 znaků.

Další informace najdete v tématu Azure konvence pojmenování pro názvy objektů blob a názvy souborů.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Popis chyby:** Jeden ze segmentů v objektu blob nebo názvu souboru je příliš dlouhý.

**Navrhované řešení:** Jeden ze segmentů cesty v objektu blob nebo názvu souboru překračuje maximální počet znaků. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače, například lomítkem /.

- Na stránce **Připojit a zkopírovat** místní webové uživatelské rozhraní stáhněte a zkontrolujte chybové soubory.
- Ujistěte se, že [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) odpovídají konvencím pojmenování Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Popis chyby:** Pro spravované sdílené složky disků jsou určeny nesprávné názvy kontejnerů.

**Navrhované řešení:** Pro spravované disky jsou v rámci každé sdílené složky vytvořeny následující složky, které odpovídají kontejnerům ve vašem účtu úložiště: Premium SSD, Standard HDD a Standard SSD. Tyto složky odpovídají úrovni výkonu spravovaného disku.

- Ujistěte se, že zkopírujete data objektů blob stránky (VD) do jedné z těchto existujících složek. Do Azure se nahrají jenom data z těchto existujících kontejnerů.
- Jakákoli jiná složka vytvořená na stejné úrovni jako Premium SSD, Standard HDD a Standard SSD neodpovídá platné úrovni výkonu a nelze ji použít.
- Odeberte soubory nebo složky vytvořené mimo úrovně výkonu.

Další informace naleznete v [tématu Kopírování na spravované disky](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Další kroky

- Přečtěte si o [požadavcích na systém úložiště datové schránky blob](data-box-system-requirements-rest.md).
