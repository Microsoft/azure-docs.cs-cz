---
title: Omezení Azure Stackch Mini R na hraničních zařízeních | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti Azure Stack hraničního konektoru R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466879"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Omezení Azure Stackch Mini R na hraničních zařízeních


Vezměte v úvahu tato omezení při nasazování a provozování Azure Stackch Mini R řešení.

## <a name="azure-stack-edge-service-limits"></a>Omezení služby Azure Stack Edge

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Azure Stack omezení pro zařízení s minimálním okrajem R

V následující tabulce jsou popsány limity Azure Stackho konektoru R pro zařízení v hraničním prostředí.

| Description | Omezení|
|---|---:|
|No. souborů na zařízení | 100 000 000 <!--check with devs-->|
|No. sdílených složek na kontejner | 1|
|Maximální počet. koncových bodů sdílení a koncových bodů REST na zařízení| 24 |
|Maximální počet. účtů vrstveného úložiště na zařízení| 24|
|Maximální velikost souboru zapsaná do sdílené složky| 500 GB|
|Maximální počet skupin prostředků na zařízení| 800|

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu a velikosti objektu Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
