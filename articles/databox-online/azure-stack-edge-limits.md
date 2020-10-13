---
title: Limity Azure Stack Edge pro | Microsoft Docs
description: Přečtěte si o omezeních a doporučených velikostech při nasazení a provozu Azure Stack Edge pro, včetně omezení služeb, omezení zařízení a omezení úložiště.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992752"
---
# <a name="azure-stack-edge-pro-limits"></a>Omezení pro Azure Stack Edge Pro

Vezměte v úvahu tato omezení při nasazení a provozu řešení Microsoft Azure Stack Edge pro. 

## <a name="azure-stack-edge-service-limits"></a>Omezení služby Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Omezení Azure Stack hraničních zařízení

Následující tabulka popisuje omezení pro zařízení Azure Stack Edge pro. 

V následující tabulce jsou popsány limity Azure Stack hraničního zařízení.

| Description | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 000 000 |
|Ne. sdílených složek na kontejner |1 |
|Maximální počet. koncových bodů sdílení a koncových bodů REST na zařízení| 24 |
|Maximální počet. účtů vrstveného úložiště na zařízení| 24|
|Maximální velikost souboru zapsaná do sdílené složky| 5 TB |
|Maximální počet skupin prostředků na zařízení| 800 |

## <a name="azure-storage-limits"></a>Omezení úložiště Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Výhody a rizika nahrávání dat

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Omezení velikosti účtu a velikosti objektu Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Omezení velikosti objektů Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge pro](azure-stack-edge-deploy-prep.md)
