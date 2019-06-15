---
title: Řešení potíží na vaše zařízení Azure Data Box, Azure Data Box náročné | Dokumentace Microsoftu
description: Popisuje postup řešení potíží v Azure Data Box a Azure Data Box náročné viděli při kopírování dat do těchto zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 0c454c5f19ebefc7f91df62511448dbedb93dfc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257291"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Řešit problémy spojené s Azure Data Box a Azure Data Box Heavy

Tento článek podrobně popisuje informace o řešení potíží se může zobrazit při použití Azure Data Boxn nebo Azure Data Box náročné.

## <a name="errors-during-data-copy"></a>Chyby během kopírování dat.

Všechny chyby, které se můžou vyskytnout během kopírování dat, které jsou shrnuty v následující části.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Popis chyby:** Název kontejneru nebo sdílené složky musí být dlouhý 3 až 63 znaků. 

**Navrhované řešení:** Složku v zařízení Data Box nebo Data Box náročné share(SMB/NFS), do kterého jste zkopírovali data se změní na kontejner služby Azure ve vašem účtu úložiště. 

- Na **připojit a Kopírovat** stránky zařízení místního webového uživatelského rozhraní, stahování a kontrola názvů chybovým souborům pro identifikaci složku s problémy.
- Změňte název složky ve sdílené zařízení Data Box nebo Data Box náročné na Ujistěte se, že:

    - Název může mít délku 3 až 63 znaků.
    - Názvy obsahovat pouze písmena, číslice a pomlčky.
    - Názvy nesmí začínat ani končit pomlčkou.
    - Název nemůže obsahovat po sobě jdoucí pomlčky.
    - Příklady platných názvů: `my-folder-1`, `my-really-extra-long-folder-111`
    - Příklady názvy, které nejsou platné: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Další informace najdete v tématu Azure zásady vytváření názvů pro [názvy kontejnerů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [sdílet názvy](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Popis chyby:** Název kontejneru nebo sdílené složky se musí skládat pouze z písmen, číslic a spojovníků.

**Navrhované řešení:** Složku v zařízení Data Box nebo Data Box náročné share(SMB/NFS), do kterého jste zkopírovali data se změní na kontejner služby Azure ve vašem účtu úložiště. 

- Na **připojit a Kopírovat** stránky zařízení místního webového uživatelského rozhraní, stahování a kontrola názvů chybovým souborům pro identifikaci složku s problémy.
- Změňte název složky ve sdílené zařízení Data Box nebo Data Box náročné na Ujistěte se, že:

    - Název může mít délku 3 až 63 znaků.
    - Názvy obsahovat pouze písmena, číslice a pomlčky.
    - Názvy nesmí začínat ani končit pomlčkou.
    - Název nemůže obsahovat po sobě jdoucí pomlčky.
    - Příklady platných názvů: `my-folder-1`, `my-really-extra-long-folder-111`
    - Příklady názvy, které nejsou platné: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Další informace najdete v tématu Azure zásady vytváření názvů pro [názvy kontejnerů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [sdílet názvy](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Popis chyby:** Názvy kontejnerů a názvy sdílených složek nelze spustit nebo končit pomlčkou a nemůže obsahovat po sobě jdoucí pomlčky.

**Navrhované řešení:** Složku v zařízení Data Box nebo Data Box náročné share(SMB/NFS), do kterého jste zkopírovali data se změní na kontejner služby Azure ve vašem účtu úložiště. 

- Na **připojit a Kopírovat** stránky zařízení místního webového uživatelského rozhraní, stahování a kontrola názvů chybovým souborům pro identifikaci složku s problémy.
- Změňte název složky ve sdílené zařízení Data Box nebo Data Box náročné na Ujistěte se, že:

    - Název může mít délku 3 až 63 znaků.
    - Názvy obsahovat pouze písmena, číslice a pomlčky.
    - Názvy nesmí začínat ani končit pomlčkou.
    - Název nemůže obsahovat po sobě jdoucí pomlčky.
    - Příklady platných názvů: `my-folder-1`, `my-really-extra-long-folder-111`
    - Příklady názvy, které nejsou platné: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    Další informace najdete v tématu Azure zásady vytváření názvů pro [názvy kontejnerů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) a [sdílet názvy](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Popis chyby:** Nesprávné kontejneru názvy jsou určené pro sdílené složky spravovaného disku.

**Navrhované řešení:** Za spravované disky v rámci každé sdílené složky jsou vytvořeny následující složky, které odpovídají kontejnery v účtu úložiště: Premium SSD, HDD standardní a SSD na úrovni Standard. Tyto složky odpovídají úroveň výkonu pro spravovaný disk.

- Ujistěte se, že zkopírujete data objektů blob stránky (VHD) do jedné z těchto existující složky. Jenom data z těchto existující kontejnery se nahraje do Azure.
- Ostatní složku, která je vytvořena na stejné úrovni jako Premium SSD a standardní HDD, SSD na úrovni Standard neodpovídá na úroveň výkonu platný a nelze jej použít.
- Odeberte soubory nebo složky, které vytvořil mimo úrovně výkonu.

Další informace najdete v tématu [kopírování na managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Popis chyby:** Sdílené složky Azure omezuje sdílenou složku a 5 TB dat. Tento limit překročil pro některé sdílené složky.

**Navrhované řešení:** Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.

Identifikujte složky, které mají tento problém z protokoly chyb a ujistěte se, že soubory v této složce jsou v části 5 TB.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Popis chyby:** Názvy objektů blob nebo soubor obsahuje nepodporovaný řídicí znaky.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali obsahují názvy s nepodporované znaky.

Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
Neodeberete nebo nepřejmenujete soubory, které chcete odebrat nepodporované znaky.

Další informace najdete v tématu Azure zásady vytváření názvů pro [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Popis chyby:** Názvy objektů blob nebo souboru obsahuje neplatné znaky.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali obsahují názvy s nepodporované znaky.

Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
Neodeberete nebo nepřejmenujete soubory, které chcete odebrat nepodporované znaky.

Další informace najdete v tématu Azure zásady vytváření názvů pro [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Popis chyby:** Názvy objektů blob nebo souboru jsou končí chybné znaky.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali obsahují názvy s nepodporované znaky.

Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
Neodeberete nebo nepřejmenujete soubory, které chcete odebrat nepodporované znaky.

Další informace najdete v tématu Azure zásady vytváření názvů pro [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Popis chyby:** Název objektu blob nebo soubor obsahuje příliš mnoho segmentů cesty.

**Navrhované řešení:** Objekty BLOB nebo soubory, které jste zkopírovali překračuje maximální počet segmentů cesty. Segment cesty je řetězec oddělovače po sobě jdoucích znaků, například lomítkem /.

- Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
- Ujistěte se, že [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) v souladu s Azure zásady vytváření názvů.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Popis chyby:** Název objektu blob nebo souboru je příliš dlouhý.

**Navrhované řešení:** Objekt blob nebo názvy souborů překračují maximální délku.

- Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
- Název objektu blob nesmí být delší než 1 024 znaků.
- Odebrat nebo přejmenovat objekt blob nebo soubory tak, aby názvy nepoužívejte více než 1024 znaků.

Další informace najdete v tématu Azure zásady vytváření názvů pro názvy objektů blob a názvy souborů.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Popis chyby:** Jeden ze segmentů v názvu objektu blob nebo souboru je příliš dlouhý.

**Navrhované řešení:** Jedna segmentů cesty v názvu objektu blob nebo souboru překračuje maximální počet znaků. Segment cesty je řetězec oddělovače po sobě jdoucích znaků, například lomítkem /.

- Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
- Ujistěte se, že [názvy objektů blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) a [názvy souborů](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) v souladu s Azure zásady vytváření názvů.

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Popis chyby:** Velikost souboru překračuje maximální velikost souboru pro nahrání.

**Navrhované řešení:** Objekt blob nebo velikost souborů překročí maximální limit pro nahrávání.

- Na **připojit a Kopírovat** stránky z místního webového uživatelského rozhraní, stáhnout a revidovat chybovým souborům.
- Ujistěte se, že velikost objektu blob a souboru nepřekračují omezení velikosti objektu Azure.

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Popis chyby:** Objekt blob nebo soubor je nesprávně zarovnán.

**Navrhované řešení:** Sdílené složky objektů blob stránky v zařízení Data Box nebo Data Box náročné pouze podporuje soubory, které jsou 512 bajtů zarovnána (třeba VHD/VHDX). Žádná data zkopírován do sdílené složky objektů blob stránky je nahráli do Azure jako objekty BLOB stránky.

Odeberte všechna data – na VHD/VHDX ze sdílené složky objektů blob stránky. Sdílené složky můžete použít pro objekty blob bloku nebo Azure files pro obecná data.

Další informace najdete v tématu [objekty BLOB stránky přehled](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Popis chyby:** Nepodporovaný typ souboru se nachází ve sdílené složce spravovaného disku. Jsou povoleny pouze pevné virtuální pevné disky.

**Navrhované řešení:**

- Ujistěte se, že pouze nahrát pevné virtuální pevné disky pro vytvoření spravovaných disků.
- Soubory VHDX nebo **dynamické** a **rozdílové** virtuální pevné disky nejsou podporovány.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Popis chyby:** Adresář není povolen v některém z existující složky pro spravované disky. V těchto složek jsou povoleny pouze pevné virtuální pevné disky.

**Navrhované řešení:** Za spravované disky v rámci každé sdílené složky se vytvoří následující tři složky, které odpovídají kontejnery v účtu úložiště: Premium SSD, HDD standardní a SSD na úrovni Standard. Tyto složky odpovídají úroveň výkonu pro spravovaný disk.

- Ujistěte se, že zkopírujete data objektů blob stránky (VHD) do jedné z těchto existující složky.
- Složku nebo adresář není povolen v těchto existující složky. Odeberte všechny složky, které jste vytvořili v již existující složky.

Další informace najdete v tématu [kopírování na managed disks](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Další postup

- Další informace o [požadavky na systém pole datový objekt Blob úložiště](data-box-system-requirements-rest.md).
