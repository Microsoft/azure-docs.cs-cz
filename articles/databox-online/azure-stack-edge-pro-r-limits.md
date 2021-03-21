---
title: Omezení Azure Stack Edge pro R | Microsoft Docs
description: Popisuje omezení systému a Doporučené velikosti pro Azure Stack Edge pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466767"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Omezení Azure Stack Edge pro R

Vezměte v úvahu tato omezení při nasazení a provozu řešení Azure Stack Edge pro R.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Omezení služby Azure Stack Edge pro R

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack omezení pro zařízení Edge pro R

Následující tabulka popisuje omezení pro zařízení Azure Stack Edge pro R.

| Description | Hodnota |
|---|---|
|No. souborů na zařízení |100 000 000 |
|No. sdílených složek na kontejner |1 |
|Maximální počet. koncových bodů sdílení a koncových bodů REST na zařízení| 24 |
|Maximální počet. účtů vrstveného úložiště na zařízení| 24|
|Maximální velikost souboru zapsaná do sdílené složky| 5 TB |
|Maximální počet skupin prostředků na zařízení| 800 |

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu a velikosti objektu Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md)
