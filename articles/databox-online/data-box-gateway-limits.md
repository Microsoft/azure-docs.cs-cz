---
title: Limity brány Azure Data Box Gateway | Dokumenty společnosti Microsoft
description: Popisuje systémová omezení a doporučené velikosti brány datové schránky Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e80b03f696a78887676e9f16750055a4dcfac230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60755194"
---
# <a name="azure-data-box-gateway-limits"></a>Limity brány datové schránky Azure

Zvažte tato omezení při nasazování a provozu řešení brány Microsoft Azure Data Box Gateway. 


## <a name="data-box-gateway-service-limits"></a>Limity služeb brány datové schránky

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limity zařízení brány datové schránky

Následující tabulka popisuje omezení pro zařízení Brány datové schránky.

| Popis | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 milionů <br> Limit je ~ 25 milionů souborů za každé 2 TB místa na disku s maximálním limitem na 100 milionů |
|Ne. sdílených složek na zařízení |24 |
|Ne. sdílených složek na kontejner úložiště Azure |1 |
|Maximální velikost souboru zapsaná do sdílené složky|U virtuálního zařízení o velikosti 2 TB je maximální velikost souboru 500 GB. <br> Maximální velikost souboru se zvětší s velikostí datového disku v předchozím poměru, dokud nedosáhne maximálně 5 TB. |

## <a name="azure-storage-limits"></a>Limity úložiště Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu úložiště Azure a omezení velikosti objektů

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další kroky

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
