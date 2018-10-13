---
title: Azure Data Box brány omezí | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: 60078845c98f2e241b00e184303dce0c860629e9
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49164429"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Omezení pro Azure Data Box brány (Preview)


Jak nasadit a provozovat řešení Microsoft Azure Data Box brány vezměte v úvahu tyto limity. 

> [!IMPORTANT] 
> Data Box Gateway je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-gateway-service-limits"></a>Omezení služby data Box brány

- V této verzi služba je k dispozici pouze v určitých oblastech v USA, Evropa a Asie a Tichomoří. Další informace najdete v části věnované [dostupnosti v oblastech](#data-box-gateway-overview#region-availability). Účet úložiště by měl být fyzicky nejblíž k oblasti, které se nasadí zařízení (může se lišit od služby geograficky).
- Přesunutí prostředku brány dat pole jiné předplatné nebo skupinu prostředků se nepodporuje. Další podrobnosti najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>Omezení zařízení data Box Gateway

Následující tabulka popisuje omezení pro zařízení Data Box brány.

| Popis | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 milionů <br> Limit je přibližně 25 milionů souborů pro každé 2 TB místa na disku s maximální limit na 100 milionů |
|Ne. sdílených složek na zařízení |24 |
|Maximální velikost souboru zapsána do sdílené složky|Maximální velikost souboru je 2 TB virtuální zařízení, 500 GB. <br> Maximální velikost souboru se zvyšuje s velikost datového disku v předchozím poměr, dokud nedosáhne maximálně 5 TB. |

## <a name="azure-storage-limits"></a>Omezení služby Azure storage

Tato část popisuje omezení pro službu Azure Storage a požadované zásady vytváření názvů pro soubory Azure, objekty BLOB bloku Azure a objekty BLOB stránky Azure, případně ke službě Data Box brány/Data pole Edge. Pečlivě zkontrolujte omezení úložiště a postupujte podle všech doporučení.

Nejnovější informace o omezení služby Azure storage a osvědčené postupy pro zadávání názvů sdílených složek, kontejnery a souborů přejděte na:

- [Pojmenování a odkazování na ně kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Objekty BLOB bloku a vytváření názvů objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud jsou všechny soubory a adresáře, které překračují omezení služby Azure Storage, nebo není v souladu s zásady vytváření názvů souborů a objektů Blob v Azure, pak tyto soubory nebo adresáře se ingestuje do služby Azure Storage pomocí služby Data Box brány/Data pole Edge.

## <a name="data-upload-caveats"></a>Odesílání dat upozornění

Při jejich přesunu do Azure se vztahují následující upozornění na data.

- Doporučujeme vám, že více než jedno zařízení by neměl zapisovat do stejného kontejneru.
- Pokud máte existující objekt Azure (například soubor nebo objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování, bude zařízení přepsat soubor v cloudu. 
- Hierarchii prázdný adresář (bez jakékoli soubory) vytvořené v rámci sdílení složek nebylo odesláno na kontejnery objektů blob.


## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu úložiště Azure a objekt velikosti

Tady jsou omezení velikosti dat, která je zkopírován do účtu úložiště. Ujistěte se, že data, která nahrajete odpovídá tato omezení. Nejaktuálnější informace o těchto omezeních najdete v části [cíle škálování Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) a [soubory Azure škálovat cíle](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Velikost dat zkopírována do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt Blob bloku a stránky objektu blob                                            | 500 TB na jeden účet úložiště|


## <a name="azure-object-size-limits"></a>Omezení velikosti objektu Azure

Tady jsou velikosti Azure objekty, které je možné zapisovat. Ujistěte se, že všechny soubory, které jsou odeslány odpovídají tyto limity.

| Typ objektu Azure | Výchozí omezení                                             |
|-------------------|-----------------------------------------------------------|
| Objekt blob bloku        | ~ 8 TB                                                 |
| Objekt blob stránky         | 1 TB <br> Každý soubor odeslat ve formátu objektů Blob stránky musí být zarovnaná 512 bajtů (integrální více), jinak se odeslání nezdaří. <br> VHD a VHDX jsou 512 bajtů zarovnána. |


## <a name="next-steps"></a>Další postup

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
