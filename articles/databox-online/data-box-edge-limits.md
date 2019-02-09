---
title: Azure Data Box Edge omezuje | Dokumentace Microsoftu
description: Popisuje omezení systému a doporučené velikosti pro Microsoft Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 30e0c37d3d0c03e77b6dab9c06c0a50bff27e8bc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967172"
---
# <a name="azure-data-box-edge-limits-preview"></a>Omezení pro Azure Data Box Edge (Preview)

Jak nasadit a provozovat řešení Microsoft Azure Data Box Edge vezměte v úvahu tyto limity.

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="data-box-edge-service-limits"></a>Omezení služby data Box Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Omezení datové pole hraniční zařízení

Následující tabulka popisuje omezení pro zařízení Data Box Edge.

| Popis | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 milionů |
|Ne. sdílených složek na zařízení |24 |
|Ne. sdílených složek na kontejner |1 |
|Maximální velikost souboru zapsána do sdílené složky| 5 TB |

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
