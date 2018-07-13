---
title: Omezení disku Azure Data Box | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro Microsoft Azure Data Box Disk.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/12/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 4db70fa93914ba0544d9beb8e523241513a2e5ce
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009298"
---
# <a name="azure-data-box-disk-limits-preview"></a>Omezení pro Azure Data Box Disk (Preview)


Jak nasadit a provozovat řešení Microsoft Azure Data Box Disk vezměte v úvahu tyto limity. 

> [!IMPORTANT] 
> Azure Data Box Disk je ve verzi Preview. Zkontrolujte [podmínky použití pro verze preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) před nasazením tohoto řešení. 


## <a name="data-box-service-limits"></a>Omezení služby data Box

 - Služba data Box je dostupná jenom v USA a EU ve všech oblastech Azure pro veřejný cloud Azure.
 - Data Box Disk podporuje jeden účet úložiště.

## <a name="data-box-disk-performance"></a>Data Box Disk výkonu

Při testování s disky připojené přes USB 3.0, se výkon disku až 430 MB/s. Aktuální počet se liší v závislosti na velikosti souboru použít. U menších souborů může se zobrazit nižší výkon.

## <a name="azure-storage-limits"></a>Omezení služby Azure storage

Tato část popisuje omezení pro službu Azure Storage a požadované zásady vytváření názvů pro soubory Azure, objekty BLOB bloku Azure a objekty BLOB stránky Azure, případně ke službě Data Box. Pečlivě zkontrolujte omezení úložiště a postupujte podle všech doporučení.

Nejnovější informace o omezení služby Azure storage a osvědčené postupy pro zadávání názvů sdílených složek, kontejnery a souborů přejděte na:

- [Pojmenování a odkazování na ně kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování a odkazování na sdílené složky](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Objekty BLOB bloku a vytváření názvů objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud jsou všechny soubory a adresáře, které překračují omezení služby Azure Storage, nebo není v souladu s zásady vytváření názvů souborů a objektů Blob v Azure, pak tyto soubory nebo adresáře se ingestuje do služby Azure Storage pomocí služby Data Box.

## <a name="data-upload-caveats"></a>Odesílání dat upozornění

- Nekopírovat data přímo do disky. Kopírování dat do předem vytvořené *BlockBlob* a *PageBlob* složek.
- Složku ve složce *BlockBlob* a *PageBlob* je kontejner. Například kontejnery jsou vytvořeny jako *BlockBlob/kontejneru* a *PageBlob/kontejneru*.
- Pokud máte existující objekt Azure (třeba jako objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování disku Data Box přepíše soubor v cloudu.
- Každý soubor zapsán do *BlockBlob* a *PageBlob* sdílené složky se nahraje jako objekt blob bloku a objektů blob stránky.
- Všechny prázdné hierarchii adresářů (bez jakékoli soubory) vytvořené v rámci *BlockBlob* a *PageBlob* složky nebylo odesláno.
- Pokud nejsou žádné chyby při odesílání dat do Azure, vytvoří se protokol chyb v účtu cílového úložiště. Cesta k tento protokol chyb je k dispozici na portálu, když se nahrávání dokončí, a vy můžete zkontrolovat protokol k provedení nápravné akce. Neodstraňujte data ze zdroje bez ověření odesílaná data.

## <a name="azure-storage-account-size-limits"></a>Omezení velikosti účtu úložiště Azure

Tady jsou omezení velikosti dat, která je zkopírován do účtu úložiště. Ujistěte se, že data, která nahrajete odpovídá tato omezení. Nejaktuálnější informace o těchto omezeních najdete v části [cíle škálování Azure blob storage](https://docs.microsoft.com/en-us/azure/storage/cstorage-scalability-targets#azure-blob-storage-scale-targets) a [soubory Azure škálovat cíle](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Velikost dat zkopírována do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt Blob bloku a stránky objektu blob                                            | 500 TB na jeden účet úložiště. <br> To zahrnuje data ze všech zdrojů, včetně disku Data Box.|


## <a name="azure-object-size-limits"></a>Omezení velikosti objektu Azure

Tady jsou velikosti Azure objekty, které je možné zapisovat. Ujistěte se, že všechny soubory, které jsou odeslány odpovídají tyto limity.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt Blob bloku        | ~ 8 TB                                                 |
| Objekt Blob stránky         | 1 TB <br> (Každý soubor odeslat ve formátu objektů Blob stránky musí být zarovnaná 512 bajtů (integrální více), jinak se odeslání nezdaří. <br> VHD a VHDX jsou 512 bajtů zarovnána.) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Objekt blob bloku Azure a zásady vytváření názvů objektů blob stránky

| Entita                                       | Konvence                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Názvy kontejnerů objektů blob bloku a objektů blob stránky | Musí být platný název DNS, který je dlouhý 3 až 63 znaků. <br>  Musí začínat písmenem nebo číslicí. <br> Může obsahovat jenom malá písmena, číslice a pomlčky (-). <br> Každé pomlčky (-) musí být bezprostředně před a následované písmenem nebo číslicí. <br> Po sobě jdoucí pomlčky nejsou povolené v názvech. |
| Názvy objektů blob pro objekt blob bloku a objektů blob stránky      | Názvy objektů BLOB jsou malá a velká písmena a může obsahovat libovolnou kombinaci znaků. <br> Název objektu blob musí být dlouhý 1 až 1024 znaků. <br> Znaky vyhrazené v adresách URL musí být správně uvozené. <br>Počet segmentů cesty obsahující název objektu blob může mít maximálně 254. Segment cesty je řetězec oddělovače po sobě jdoucích znaků (například lomítka "/"), které odpovídají názvu virtuálního adresáře. |
