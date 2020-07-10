---
title: Omezení Azure Data Box | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti pro součásti Microsoft Azure Data Box a připojení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7d699fc47fa0a0cb57d103ff42ff17bdc0f3a82b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202666"
---
# <a name="azure-data-box-limits"></a>Omezení Azure Data Box

Tato omezení zvažte při nasazení a provozu Data Box Microsoft Azure. Následující tabulka popisuje tato omezení pro Data Box.

## <a name="data-box-service-limits"></a>Omezení služby Data Box

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Omezení Data Box

- Data Box může ukládat maximálně 500 000 000 souborů pro import i export.
- Data Box podporuje maximálně 512 kontejnerů nebo sdílených složek v cloudu. Adresáře nejvyšší úrovně v rámci sdílené složky uživatele se stanou kontejnery nebo sdílené složky Azure v cloudu. 
- Kapacita využití Data Box může být menší než 80 TB z důvodu využití místa metadat ReFS.

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat


### <a name="for-import-order"></a>Pro pořadí importu

Mezi omezeními Data Box v případě importu patří:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>Pro export objednávky

K Data Boxm aspektům pro exportované objednávky patří:

- Data Box je zařízení se systémem Windows, které nepodporuje názvy souborů citlivých na velká a malá písmena. V Azure můžete mít například dva různé soubory s názvy, které se v různých velikostech liší. Nepoužívejte data box k exportu těchto souborů, protože se přepíší na zařízení.
- Pokud máte ve vstupních souborech duplicitní značky nebo značky odkazující na stejná data, Data Box export může soubory přeskočit nebo přepsat. Počet souborů a velikost dat zobrazených na Azure Portal se mohou lišit od skutečné velikosti dat, která jsou v zařízení k dispozici. 
- Data Box exportuje data do systému Windows přes protokol SMB a omezí omezení SMB pro soubory a složky. Soubory a složky s nepodporovanými názvy nejsou exportovány.
- Existuje mapování 1:1 z předpony na kontejner.
- Maximální velikost souboru je 1024 znaků, názvy souborů, které překračují tuto délku, se neexportují.
- V souboru *XML* jsou exportovány duplicitní předpony (nahrány během vytváření objednávky). Duplicitní předpony nejsou ignorovány.
- U objektů blob stránky a názvů kontejnerů se rozlišují velká a malá písmena, takže pokud se velká a malá písmena neshodují, objekt BLOB nebo kontejner se nenalezne.
 

## <a name="azure-storage-account-size-limits"></a>Omezení velikosti účtu Azure Storage

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Zásady pro pojmenovávání objektů blob bloku Azure, objektů blob stránky a názvů souborů

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
