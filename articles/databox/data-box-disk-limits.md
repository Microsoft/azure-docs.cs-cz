---
title: Omezení disku Azure Data Box | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/19/2019
ms.author: alkohli
ms.openlocfilehash: 9cad48eeadc06c84e326cbc5f19f1c97e151a795
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880445"
---
# <a name="azure-data-box-disk-limits"></a>Omezení disku Azure Data Box


Jak nasadit a provozovat řešení Microsoft Azure Data Box Disk vezměte v úvahu tyto limity.

## <a name="data-box-service-limits"></a>Omezení služby data Box

 - Služba data Box je k dispozici v oblastech Azure podle [dostupnost v oblastech](data-box-disk-overview.md#region-availability).
 - Data Box Disk podporuje jeden účet úložiště.

## <a name="data-box-disk-performance"></a>Data Box Disk výkonu

Při testování s disky připojenými přes USB 3.0 byl výkon disku až 430 MB/s. Skutečné hodnoty se liší v závislosti na velikosti použitých souborů. U menších souborů může být výkon nižší.

## <a name="azure-storage-limits"></a>Omezení služby Azure storage

Tato část popisuje omezení pro službu Azure Storage a požadované zásady vytváření názvů pro soubory Azure, objekty BLOB bloku Azure a objekty BLOB stránky Azure, případně ke službě Data Box. Pečlivě zkontrolujte omezení úložiště a postupujte podle všech doporučení.

Nejnovější informace o omezení služby Azure storage a osvědčené postupy pro zadávání názvů sdílených složek, kontejnery a souborů přejděte na:

- [Pojmenování a odkazování na ně kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Objekty BLOB bloku a vytváření názvů objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud jsou všechny soubory a adresáře, které překračují omezení služby Azure Storage, nebo není v souladu s zásady vytváření názvů souborů a objektů Blob v Azure, pak tyto soubory nebo adresáře se ingestuje do služby Azure Storage pomocí služby Data Box.

## <a name="data-upload-caveats"></a>Odesílání dat upozornění

- Nekopírovat data přímo do disky. Kopírování dat do předem vytvořené *BlockBlob*,*PageBlob*, a *AzureFile* složek.
- Složku ve složce *BlockBlob* a *PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *BlockBlob/kontejneru* a *PageBlob/kontejneru*.
- Pokud máte existující objekt Azure (třeba jako objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování disku Data Box přejmenuje soubor jako file(1) v cloudu.
- Každý soubor zapsán do *BlockBlob* a *PageBlob* sdílené složky se nahraje jako objekt blob bloku a objektů blob stránky.
- Všechny prázdné hierarchii adresářů (bez jakékoli soubory) vytvořené v rámci *BlockBlob* a *PageBlob* složky nebylo odesláno.
- Pokud nejsou žádné chyby při odesílání dat do Azure, vytvoří se protokol chyb v účtu cílového úložiště. Cesta k tento protokol chyb je k dispozici na portálu, když se nahrávání dokončí, a vy můžete zkontrolovat protokol k provedení nápravné akce. Neodstraňujte data ze zdroje bez ověření odesílaná data.
- Pokud jste zadali spravované disky v pořadí, projděte si následující další aspekty:

    - Do všech vytvořených složek a po celém všechny disku Data Box, může mít jenom jeden spravovaný disk se zadaným názvem ve skupině prostředků. Z toho vyplývá, že virtuální pevné disky nahráli do složek vytvořených by měly mít jedinečné názvy. Ujistěte se, že zadaný název neodpovídá již existujícího spravovaného disku do skupiny prostředků. Pokud virtuální pevné disky mají stejné názvy, pouze jeden virtuální pevný disk je převeden na spravovaný disk s tímto názvem. Další virtuální pevné disky jsou odeslány jako objekty BLOB stránky do přípravného účtu úložiště.
    - Vždy kopírovat virtuální pevné disky k jednomu z vytvořených složky. Pokud kopírujete virtuální pevné disky mimo tyto složky nebo do složky, kterou jste vytvořili, virtuální pevné disky se nahrají do účtu služby Azure Storage jako objekty BLOB stránky a nespravovaných disků.
    - Pouze pevné virtuální pevné disky můžete nahrát do vytvoření spravovaných disků. Dynamické virtuální pevné disky, rozdílové virtuální pevné disky nebo VHDX soubory nejsou podporovány.

## <a name="azure-storage-account-size-limits"></a>Omezení velikosti účtu úložiště Azure

Tady jsou omezení velikosti dat, která je zkopírován do účtu úložiště. Ujistěte se, že data, která nahrajete odpovídá tato omezení. Nejaktuálnější informace o těchto omezeních najdete v části [cíle škálování Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) a [soubory Azure škálovat cíle](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Velikost dat zkopírována do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt Blob bloku a stránky objektu blob                                            | 500 TB na jeden účet úložiště. <br> To zahrnuje data ze všech zdrojů, včetně disku Data Box.|


## <a name="azure-object-size-limits"></a>Omezení velikosti objektu Azure

Tady jsou velikosti Azure objekty, které je možné zapisovat. Ujistěte se, že všechny soubory, které jsou odeslány odpovídají tyto limity.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TB                                                 |
| Objekt blob stránky         | 8 TiB <br> (Každý soubor odeslat ve formátu objektů Blob stránky musí být 512 bajtů zarovnána, jinak se odeslání nezdaří. <br> VHD i VHDX jsou 512 bajtů zarovnána.) |
|Soubory Azure        | 1 TiB <br> Max. velikost sdílené složky je 5 TiB     |
| Managed Disks     |4 TiB <br> Další informace o velikosti a omezení naleznete v tématu: <li>[Cíle škálovatelnosti pro spravované disky](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Objekt blob bloku Azure, objekty blob stránky a konvence pojmenování souboru

| Entita                                       | Zásady                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Názvy kontejnerů objektů blob bloku a objektů blob stránky <br> Názvy sdílení souborů pro soubory Azure | Musí být platný název DNS, který je dlouhý 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat jenom malá písmena, číslice a pomlčky (-). <br> Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. <br> Názvy nesmí obsahovat po sobě jdoucí pomlčky. |
| Názvy souborů a adresář pro soubory Azure     |<li> Zachování případu, velkých a malých písmen a nesmí být delší než 255 znaků. </li><li> Nemůže končit lomítkem (/). </li><li>Pokud je zadán, bude automaticky odebrán. </li><li> Nejsou povolené následující znaky: <code>" \\ / : \| < > * ?</code></li><li> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li> Neplatné znaky cesty adresy URL nejsou povoleny. Kód body jako \\uE000 nejsou platné znaky Unicode. Řídicí znaky, jako jsou některé znaky ASCII a Unicode (0x00 0x1F \\u0081, atd.), nejsou povoleny. Pravidla kódování Unicode řetězců v protokolu HTTP/1.1 naleznete v dokumentu RFC 2616 2.2 části: Základní pravidla a v dokumentu RFC 3987. </li><li> Tyto názvy nejsou povoleny: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, tečka (.) a dvě tečky znaků (.).</li>|
| Názvy objektů blob bloku a objektů blob stránky      | Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. <br> Název objektu blob musí mít délku 1 až 1024 znaků. <br> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. <br>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře. |

## <a name="managed-disk-naming-conventions"></a>Spravovat zásady vytváření názvů disku

| Entita | Zásady                                             |
|-------------------|-----------------------------------------------------------|
| Spravované názvy disku       | <li> Název musí být dlouhý 1 až 80 znaků. </li><li> Název musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem. </li><li> Název může obsahovat jenom písmena, čísla, podtržítka, tečky nebo spojovníky. </li><li>   Název nesmí obsahovat mezery nebo `/`.                                              |

## <a name="next-steps"></a>Další postup

- Kontrola [požadavky na systém zařízení Data Box](data-box-system-requirements.md)
