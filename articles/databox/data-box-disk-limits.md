---
title: Limity disku datové schránky Azure | Dokumenty společnosti Microsoft
description: Popisuje systémová omezení a doporučené velikosti pro disk datové schránky Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 1bb8300f1e54cf03563704cf00549ce9e09a3916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260160"
---
# <a name="azure-data-box-disk-limits"></a>Limity disku datové schránky Azure


Zvažte tato omezení při nasazování a provozu řešení Microsoft Azure Data Box Disk.

## <a name="data-box-service-limits"></a>Limity služby Datové schránky

 - Služba Data Box je dostupná v oblastech Azure uvedených v [části Dostupnost oblasti](data-box-disk-overview.md#region-availability).
 - Jeden účet úložiště je podporován s diskem datové schránky.

## <a name="data-box-disk-performance"></a>Výkon disku datové schránky

Při testování s disky připojenými přes USB 3.0 byl výkon disku až 430 MB/s. Skutečné hodnoty se liší v závislosti na velikosti použitých souborů. U menších souborů může být výkon nižší.

## <a name="azure-storage-limits"></a>Limity úložiště Azure

Tato část popisuje omezení pro službu Azure Storage a požadované konvence pojmenování pro soubory Azure, objekty BLOB bloků Azure a objekty BLOB stránky Azure, podle služby Data Box. Pečlivě si zkontrolujte limity úložiště a dodržujte všechna doporučení.

Nejnovější informace o limitech služeb úložiště Azure a doporučených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete na adrese:

- [Pojmenování a odkazování na kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokovat objekty BLOB a konvence objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud existují nějaké soubory nebo adresáře, které překračují limity služby Azure Storage nebo neodpovídají konvencím pojmenování souborů/objektů blob Azure, pak se tyto soubory nebo adresáře nedevolují do úložiště Azure prostřednictvím služby Data Box.

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

- Nekopírujte data přímo na disky. Zkopírujte data do předem vytvořených složek *BlockBlob*,*PageBlob*a *AzureFile.*
- Složka pod *BlockBlob* a *PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *BlockBlob/container* a *PageBlob/container*.
- Pokud máte existující objekt Azure (například objekt blob) v cloudu se stejným názvem jako objekt, který se kopíruje, disk datové schránky přejmenuje soubor jako soubor(1) v cloudu.
- Každý soubor napsaný do sdílených složek *BlockBlob* a *PageBlob* se nahraje jako objekt blob bloku a objekt blob stránky.
- Žádná prázdná hierarchie adresářů (bez souborů) vytvořená ve složkách *BlockBlob* a *PageBlob* se nenahraje.
- Pokud se při nahrávání dat do Azure zobrazí nějaké chyby, vytvoří se v cílovém účtu úložiště protokol chyb. Cesta k tomuto protokolu chyb je k dispozici na portálu po dokončení nahrávání a můžete zkontrolovat protokol provést nápravná opatření. Neodstraňujte data ze zdroje bez ověření nahraných dat.
- Metadata souborů a oprávnění ntfs nejsou zachovány při odeslání dat do souborů Azure. Například *poslední změněný* atribut souborů nebude při kopírování dat zachován.
- Pokud jste v pořadí zadali spravované disky, přečtěte si následující další aspekty:

    - Ve skupině prostředků napříč všemi předem vytvořenými složkami a všemi Data Box Disky můžete mít pouze jeden spravovaný disk s daným názvem. To znamená, že virtuální pevné disky nahrané do předem vytvořených složek musí mít jedinečné názvy. Ujistěte se, že daný název neodpovídá žádnému již existujícímu spravovanému disku ve skupině prostředků. Pokud mají virtuální pevné disky stejný název, na spravovaný disk se převede pouze jeden virtuální pevný disk s tímto názvem. Ostatní virtuální pevné disky se nahrají do přípravného účtu úložiště jako objekty blob stránky.
    - Virtuální pevné disky vždy kopírujte do některé z předem vytvořených složek. Pokud virtuální pevné disky zkopírujete mimo tyto složky nebo do složky, kterou jste vytvořili, nahrají se do účtu Azure Storage jako objekty blob stránky, a ne jako spravované disky.
    - K vytvoření spravovaných disků je možné nahrát pouze pevné virtuální pevné disky. Dynamické virtuální pevné disky, rozdílové virtuální pevné disky ani soubory VHDX se nepodporují.

## <a name="azure-storage-account-size-limits"></a>Omezení velikosti účtu úložiště Azure

Tady jsou omezení velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahrajete, odpovídají těmto limitům. Nejaktuálnější informace o těchto limitech najdete na [cíle škálování úložiště objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage) a [cíle škálování souborů Azure](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account#scale-targets-for-standard-storage-accounts).

| Velikost dat zkopírovaných do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | 500 TB na účet úložiště. <br> To zahrnuje data ze všech zdrojů, včetně datové schránky disku.|


## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

Tady jsou velikosti objektů Azure, které se dá zapsat. Ujistěte se, že všechny soubory, které jsou odeslány, odpovídají těmto limitům.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TiB                                                 |
| Objekt blob stránky         | 8 TiB <br> (Každý soubor nahraný ve formátu objektu Blob stránky musí mít zarovnaných 512 bajtů, jinak se nahrávání nezdaří. <br> VHD i VHDX jsou zarovnány o 512 bajtů.) |
|Soubory Azure        | 1 TiB <br> Max. velikost podílu je 5 TiB     |
| Spravované disky     |4 TiB <br> Další informace o velikosti a omezeních naleznete v těchto tématech: <li>[Cíle škálovatelnosti pro spravované disky](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Objekt blob bloku Azure, objekt blob stránky a konvence pojmenování souborů

| Entita                                       | Zásady                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Názvy kontejnerů pro objekt blob bloku a objekt blob stránky <br> Názvy sdílení souborů pro soubory Azure | Musí se jednat o platný název DNS dlouhý na 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat pouze malá písmena, číslice a pomlčku (-). <br> Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. <br> Názvy nesmí obsahovat po sobě jdoucí pomlčky. |
| Názvy adresářů a souborů pro soubory Azure     |<li> Zachování velkých a malých písmen, bez rozlišování velkých a malých písmen nesmí přesáhnout délku 255 znaků. </li><li> Nelze končit lomítkem (/). </li><li>Pokud je k dispozici, bude automaticky odebrán. </li><li> Následující znaky nejsou povoleny:<code>" \\ / : \| < > * ?</code></li><li> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li> Neplatné znaky cesty URL nejsou povoleny. Body kódu \\jako uE000 nejsou platné znaky Unicode. Některé znaky ASCII nebo Unicode, například řídicí znaky (0x00 až 0x1F, \\u0081 atd.), také nejsou povoleny. Pravidla, kterými se řídí řetězce Unicode v protokolu HTTP/1.1, viz RFC 2616, Oddíl 2.2: Základní pravidla a RFC 3987. </li><li> Následující názvy souborů nejsou povoleny: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, tečka znak (.).</li>|
| Názvy objektů blob bloku a objektů blob stránky      | Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. <br> Název objektu blob musí mít délku 1 až 1024 znaků. <br> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. <br>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře. |

## <a name="managed-disk-naming-conventions"></a>Konvence pojmenování spravovaného disku

| Entita | Zásady                                             |
|-------------------|-----------------------------------------------------------|
| Názvy spravovaných disků       | <li> Název musí mít dlouhý 1 až 80 znaků. </li><li> Název musí začínat písmenem nebo číslem, končit písmenem, číslem nebo podtržítkem. </li><li> Název může obsahovat pouze písmena, čísla, podtržítka, tečky nebo pomlčky. </li><li>   Název by neměl mít `/`mezery nebo .                                              |

## <a name="next-steps"></a>Další kroky

- Kontrola [požadavků na systém datové schránky](data-box-disk-system-requirements.md)
