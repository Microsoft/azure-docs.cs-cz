---
title: Omezení Azure Data Box Gateway | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti pro Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581761"
---
# <a name="azure-data-box-gateway-limits"></a>Omezení Azure Data Box Gateway

Tato omezení zvažte při nasazení a provozu Data Box Gateway řešení Microsoft Azure.

## <a name="data-box-gateway-service-limits"></a>Omezení služby Data Box Gateway

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Omezení Data Box Gateway zařízení

Následující tabulka popisuje omezení pro Data Box Gateway zařízení.

| Description | Hodnota |
|---|---|
|No. souborů na zařízení |100 000 000 <br> Pro každý 25 000 000 souborů, které se přidávají (s maximálním limitem na 100 000 000), byste měli přidat 2 TB místa na disku, 8 GB paměti RAM a 4 jádra procesoru. |
|No. sdílených složek na zařízení |24 |
|No. sdílených složek na kontejner úložiště Azure |1 |
|Maximální velikost souboru zapsaná do sdílené složky|Pro virtuální zařízení o velikosti 2 TB je maximální velikost souboru 500 GB. <br> Maximální velikost souboru se zvětšuje s velikostí datových disků v předchozím poměru, dokud nedosáhne maximálního počtu 5 TB. |

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu a velikosti objektu Azure Storage

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další kroky

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
