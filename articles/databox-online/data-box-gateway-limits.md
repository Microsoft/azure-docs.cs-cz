---
title: Azure Data Box brány omezí | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 34aac9a589516ace080906095ef3c14b34469bbd
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311184"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Omezení pro Azure Data Box brány (Preview)


Jak nasadit a provozovat řešení Microsoft Azure Data Box brány vezměte v úvahu tyto limity. 

> [!IMPORTANT] 
> Data Box Gateway je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-gateway-service-limits"></a>Omezení služby data Box brány

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Omezení zařízení data Box Gateway

Následující tabulka popisuje omezení pro zařízení Data Box brány.

| Popis | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 milionů <br> Limit je přibližně 25 milionů souborů pro každé 2 TB místa na disku s maximální limit na 100 milionů |
|Ne. sdílených složek na zařízení |24 |
|Ne. sdílených složek za kontejner úložiště Azure |1 |
|Maximální velikost souboru zapsána do sdílené složky|Maximální velikost souboru je 2 TB virtuální zařízení, 500 GB. <br> Maximální velikost souboru se zvyšuje s velikost datového disku v předchozím poměr, dokud nedosáhne maximálně 5 TB. |

## <a name="azure-storage-limits"></a>Omezení služby Azure storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Odesílání dat upozornění

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu úložiště Azure a objekt velikosti

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Omezení velikosti objektu Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další postup

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
