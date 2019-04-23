---
title: Azure Data Box omezuje | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro připojení a komponenty Microsoft Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 147cf61dcd36edc75a936cf9b467fd89c8d8a965
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326594"
---
# <a name="azure-data-box-limits"></a>Omezení služby Azure Data Box

Tato omezení zvažte, jak nasadit a provozovat váš Microsoft Azure Data Box. Následující tabulka popisuje tato omezení pro zařízení Data Box.


## <a name="data-box-service-limits"></a>Omezení služby data Box

 - Pokud používáte několik účtů úložiště ve službě Data Box, všechny účty úložiště by měly patřit do stejné oblasti Azure.
 - Doporučujeme použít více než 3 účty úložiště. Použití více účtů úložiště může potenciálně ovlivnit výkon.

## <a name="data-box-limits"></a>Omezení zařízení data Box

- Data Box můžete uložit maximálně 500 milionů souborů.

## <a name="azure-storage-limits"></a>Omezení služby Azure storage

Tato část popisuje omezení pro službu Azure Storage a požadované zásady vytváření názvů pro soubory Azure, objekty BLOB bloku Azure a objekty BLOB stránky Azure, případně ke službě Data Box. Pečlivě zkontrolujte omezení úložiště a postupujte podle všech doporučení.

Nejnovější informace o omezení služby Azure storage a osvědčené postupy pro zadávání názvů sdílených složek, kontejnery a souborů přejděte na:

- [Pojmenování a odkazování na ně kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Objekty BLOB bloku a vytváření názvů objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud jsou všechny soubory a adresáře, které překračují omezení služby Azure Storage, nebo není v souladu s zásady vytváření názvů souborů a objektů Blob v Azure, pak tyto soubory nebo adresáře se ingestuje do služby Azure Storage pomocí služby Data Box.

## <a name="data-upload-caveats"></a>Odesílání dat upozornění

- Nemusíte kopírovat soubory přímo do všech vytvořených sdílené složky. Budete muset vytvořit složku ve sdílené složce a zkopírujte soubory do této složky.
- Složku ve složce *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *StorageAccount_BlockBlob/kontejneru* a *StorageAccount_PageBlob/kontejneru*.
- Všechny složky, vytvořené přímo v rámci *StorageAccount_AzureFiles* přeloženy do sdílené složky Azure.
- Pokud máte existující objekt Azure (například soubor nebo objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování, bude zařízení Data Box přepsat soubor v cloudu.
- Každý soubor zapsán do *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* sdílené složky se nahraje jako objekt blob bloku a objektů blob stránky.
- Úložiště objektů blob v Azure nepodporuje adresáře. Pokud vytváříte složku ve složce *StorageAccount_BlockBlob* složku a pak virtuální složky se vytvoří v názvu objektu blob. Pro soubory Azure je udržován skutečné adresářovou strukturu.
- Všechny prázdné hierarchii adresářů (bez jakékoli soubory) vytvořené v rámci *StorageAccount_BlockBlob* a *StorageAccount_PageBlob* složky nebylo odesláno.
- Pokud nejsou žádné chyby při odesílání dat do Azure, vytvoří se protokol chyb v účtu cílového úložiště. Cesta k této chybě protokolu je k dispozici, když se nahrávání dokončí, a vy můžete zkontrolovat protokol k provedení nápravné akce. Neodstraňovat data ze zdroje bez ověření odesílaná data.

## <a name="azure-storage-account-size-limits"></a>Omezení velikosti účtu úložiště Azure

Tady jsou omezení velikosti dat, která je zkopírován do účtu úložiště. Ujistěte se, že data, která nahrajete odpovídá tato omezení. Nejaktuálnější informace o těchto omezeních najdete v části [cíle škálování Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) a [soubory Azure škálovat cíle](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Velikost dat zkopírována do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objektů blob stránky                                            | 500 TB na jeden účet úložiště. <br> To zahrnuje data ze všech zdrojů, včetně zařízení Data Box.|
| Azure File                                                          | 5 TiB jednotlivou sdílenou složku.<br> Všechny složky s *StorageAccount_AzureFiles* musí postupovat podle tohoto limitu.       |

## <a name="azure-object-size-limits"></a>Omezení velikosti objektu Azure

Tady jsou velikosti Azure objekty, které je možné zapisovat. Ujistěte se, že všechny soubory, které jsou odeslány odpovídají tyto limity.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 4,75 TB                                                 |
| Objekt blob stránky         | 8 TiB <br> Každý soubor odeslat ve formátu objektů blob stránky musí být zarovnaná 512 bajtů (integrální více), jinak se odeslání nezdaří. <br> VHD a VHDX jsou 512 bajtů zarovnána. |
| Soubory Azure        | 1 TiB                                                      |
| Spravované disky     | 4 TiB <br> Další informace o velikosti a omezení naleznete v tématu: <li>[Cíle škálovatelnosti standardních disků SSD](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Cíle škálovatelnosti disků Premium SSD](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Cíle škálovatelnosti standardní HDD](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Ceny a fakturace spravovaných disků](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Objekt blob bloku Azure, objekty blob stránky a konvence pojmenování souboru

| Entita                                       | Zásady                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Názvy kontejnerů objektů blob bloku a objektů blob stránky | Musí být platný název DNS, který je dlouhý 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat jenom malá písmena, číslice a pomlčky (-). <br> Každé pomlčce (-) musí bezprostředně předcházet číslice (0–9) nebo malé písmeno (a–z) a také po ní musí následovat. <br> Po sobě jdoucí pomlčky nejsou povolené v názvech. |
| Názvy sdílených složek pro soubory Azure                  | Stejný jako výše uvedený                                                                                                                                                                                                                                                                                                             |
| Názvy souborů a adresář pro soubory Azure     |<li> Zachování případu, velkých a malých písmen a nesmí být delší než 255 znaků. </li><li> Nemůže končit lomítkem (/). </li><li>Pokud je zadán, bude automaticky odebrán. </li><li> Nejsou povolené následující znaky: <code>" \\ / : \| < > * ?</code></li><li> Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li> Neplatné znaky cesty adresy URL nejsou povoleny. Kód body jako \\uE000 nejsou platné znaky Unicode. Řídicí znaky, jako jsou některé znaky ASCII a Unicode (0x00 0x1F \\u0081, atd.), nejsou povoleny. Pravidla kódování Unicode řetězců v protokolu HTTP/1.1 naleznete v dokumentu RFC 2616 2.2 části: Základní pravidla a v dokumentu RFC 3987. </li><li> Tyto názvy nejsou povoleny: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, tečka (.) a dvě tečky znaků (.).</li>|
| Názvy objektů blob bloku a objektů blob stránky      | </li><li>Názvy objektů blob rozlišují velká a malá písmena a smí obsahovat libovolnou kombinaci znaků. </li><li>Název objektu blob musí mít délku 1 až 1024 znaků. </li><li>Vyhrazené znaky v adresách URL musí být správně uzavřené do uvozovek. </li><li>Počet segmentů cesty, ze kterých se název objektu blob skládá, nesmí překročit 254. Segment cesty je řetězec mezi po sobě jdoucími znaky oddělovače (třeba lomítko „/“), který odpovídá názvu virtuálního adresáře.</li> |
