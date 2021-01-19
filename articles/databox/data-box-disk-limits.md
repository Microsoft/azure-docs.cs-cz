---
title: Omezení Azure Data Box Disk | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti pro Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: e003d0121721838bd5ae038a3a8b4d1b8cd9d1eb
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573186"
---
# <a name="azure-data-box-disk-limits"></a>Omezení Azure Data Box Disk


Tato omezení zvažte při nasazení a provozu Data Box Disk řešení Microsoft Azure.

## <a name="data-box-service-limits"></a>Omezení služby Data Box

 - Služba Data Box je dostupná v oblastech Azure, které jsou uvedené v [oblasti dostupnost](data-box-disk-overview.md#region-availability).
 - Data Box Disk podporuje jeden účet úložiště.

## <a name="data-box-disk-performance"></a>Data Box Disk výkon

Při testování s disky připojenými přes USB 3.0 byl výkon disku až 430 MB/s. Skutečné hodnoty se liší v závislosti na velikosti použitých souborů. U menších souborů může být výkon nižší.

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

Tato část popisuje omezení pro službu Azure Storage Service a požadované zásady vytváření názvů pro soubory Azure, objekty blob bloku Azure a objekty blob stránky Azure, které se vztahují na službu Data Box. Pečlivě si prohlédněte omezení úložiště a sledujte všechna doporučení.

Nejnovější informace o omezeních služby Azure Storage a osvědčených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete tady:

- [Pojmenování kontejnerů a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zásady objektů blob bloku a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud existují nějaké soubory nebo adresáře, které překračují omezení služby Azure Storage, nebo nevyhovuje zásadám vytváření názvů souborů Azure nebo objektů blob, pak se tyto soubory nebo adresáře ingestují do Azure Storage prostřednictvím služby data box.

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

- Nekopírujte data přímo do disků. Zkopírujte data na předem vytvořené složky *BlockBlob*,*PageBlob* a *AzureFile* .
- Složka pod *BlockBlob* a *PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *BlockBlob/Container* a *PageBlob/Container*.
- Pokud máte v cloudu existující objekt Azure (například objekt BLOB) se stejným názvem, jako má kopírovaný objekt, Data Box Disk soubor přejmenuje jako soubor (1) v cloudu.
- Každý soubor zapsaný do sdílených složek *BlockBlob* a *PageBlob* se nahraje jako objekt blob bloku a objekt blob stránky v uvedeném pořadí.
- Žádná prázdná hierarchie adresářů (bez jakýchkoli souborů) vytvořená v rámci složek *BlockBlob* a *PageBlob* není nahraná.
- Pokud při nahrávání dat do Azure dojde k nějakým chybám, v cílovém účtu úložiště se vytvoří protokol chyb. Cesta k tomuto protokolu chyb je k dispozici na portálu po dokončení nahrávání a můžete si prohlédnout protokol a provést nápravná opatření. Neodstraňujte data ze zdroje bez ověřování nahraných dat.
- Metadata souboru a oprávnění systému souborů NTFS se nezachovají při nahrávání dat do souborů Azure. Například atribut *naposledy upravený* soubory se nebude uchovávat při kopírování dat.
- Pokud jste v uvedeném pořadí zadali spravované disky, přečtěte si následující pokyny:

    - Ve skupině prostředků napříč všemi předem vytvořenými složkami a všemi Data Box Disky můžete mít pouze jeden spravovaný disk s daným názvem. To znamená, že virtuální pevné disky nahrané do předem vytvořených složek musí mít jedinečné názvy. Ujistěte se, že daný název neodpovídá žádnému již existujícímu spravovanému disku ve skupině prostředků. Pokud mají virtuální pevné disky stejný název, na spravovaný disk se převede pouze jeden virtuální pevný disk s tímto názvem. Ostatní virtuální pevné disky se nahrají do přípravného účtu úložiště jako objekty blob stránky.
    - Virtuální pevné disky vždy kopírujte do některé z předem vytvořených složek. Pokud virtuální pevné disky zkopírujete mimo tyto složky nebo do složky, kterou jste vytvořili, nahrají se do účtu Azure Storage jako objekty blob stránky, a ne jako spravované disky.
    - K vytvoření spravovaných disků je možné nahrát pouze pevné virtuální pevné disky. Dynamické virtuální pevné disky, rozdílové virtuální pevné disky ani soubory VHDX se nepodporují.
    - Soubory, které nejsou zkopírované na virtuálním pevném disku, se zkopírují do složek spravovaného disku, které se nedají převést na spravovaný disk.

## <a name="azure-storage-account-size-limits"></a>Omezení velikosti účtu Azure Storage

Tady jsou omezení velikosti dat, která se dají zkopírovat do účtu úložiště. Ujistěte se, že data, která nahráváte, jsou v souladu s těmito omezeními. 

| Typ dat             | Výchozí omezení          |
|--------------------------|------------------------|
| objekt blob bloku, objekt blob stránky    | Aktuální informace o těchto omezeních najdete v tématu [cíle škálování služby Azure Blob Storage](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [cíle škálování Azure úrovně Standard](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)a [cíl škálování souborů Azure](../storage/files/storage-files-scale-targets.md#file-share-and-file-scale-targets). <br /><br /> Omezení zahrnují data ze všech zdrojů, včetně Data Box Disk.|


## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

Tady jsou velikosti objektů Azure, které se dají zapsat. Zajistěte, aby všechny nahrané soubory splňovaly tato omezení.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TiB                                                 |
| Objekt blob stránky         | 8 TiB <br> (Každý soubor nahraný ve formátu objektu blob stránky musí být 512 bajtů, jinak se nahrávání nepovede. <br> VHD i VHDX jsou zarovnaný 512 bajtů.) |
|Azure Files        | 1 TiB <br> Max. Velikost sdílené složky je 5 TiB.     |
| Spravované disky     |4 TiB <br> Další informace o velikosti a omezeních najdete v těchto tématech: <li>[Cíle škálovatelnosti pro spravované disky](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Zásady pro pojmenovávání objektů blob bloku Azure, objektů blob stránky a názvů souborů

| Entita                                       | Konvence                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Názvy kontejnerů pro objekt blob bloku a objekt blob stránky <br> Názvy sdílených složek pro soubory Azure | Musí se jednat o platný název DNS, který má délku 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat jenom malá písmena, číslice a spojovník (-). <br> Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. <br> Názvy nesmí obsahovat po sobě jdoucí pomlčky. |
| Názvy adresářů a souborů pro soubory Azure     |<li> Bez rozlišování velkých a malých písmen a nesmí překročit 255 znaků. </li><li> Nemůže končit lomítkem (/). </li><li>Pokud je tato akce k dispozici, bude automaticky odebrána. </li><li> Nejsou povoleny následující znaky: <code>" \\ / : \| < > * ?</code></li><li> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li> Neplatné znaky cesty URL nejsou povoleny. Kódové body, jako \\ je uE000, nejsou platné znaky Unicode. Některé znaky ASCII nebo Unicode, například řídicí znaky (0x00 až 0x1F, \\ u0081 atd.), nejsou také povoleny. Pravidla upravující řetězce Unicode v HTTP/1.1 najdete v dokumentu RFC 2616, oddíl 2,2: základní pravidla a RFC 3987. </li><li> Následující názvy souborů nejsou povoleny: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, tečka (.) a dva tečky (..).</li>|
| Názvy objektů blob bloku a objektů blob stránky      | Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. <br> Název objektu blob musí mít délku 1 až 1024 znaků. <br> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. <br>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře. |

## <a name="managed-disk-naming-conventions"></a>Zásady vytváření názvů spravovaných disků

| Entita | Konvence                                             |
|-------------------|-----------------------------------------------------------|
| Názvy spravovaných disků       | <li> Název musí mít délku 1 až 80 znaků. </li><li> Název musí začínat písmenem nebo číslicí a končit písmenem, číslicí nebo podtržítkem. </li><li> Název může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. </li><li>   Název nesmí obsahovat mezery nebo `/` .                                              |

## <a name="next-steps"></a>Další kroky

- Kontrola [data box disk systémových požadavků](data-box-disk-system-requirements.md)