---
title: Limity Azure Stack Edge pro | Microsoft Docs
description: Přečtěte si o omezeních a doporučených velikostech při nasazení a provozu Azure Stack Edge pro, včetně omezení služeb, omezení zařízení a omezení úložiště.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5f3c39ce7334145d3ffc1d54badb1f7b766da70f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904460"
---
# <a name="azure-stack-edge-pro-limits"></a>Omezení pro Azure Stack Edge Pro

Vezměte v úvahu tato omezení při nasazení a provozu řešení Microsoft Azure Stack Edge pro. 

## <a name="azure-stack-edge-service-limits"></a>Omezení služby Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Omezení Azure Stack hraničních zařízení

Následující tabulka popisuje omezení pro zařízení Azure Stack Edge pro. 

| Description | Hodnota |
|---|---|
|Ne. souborů na zařízení |100 000 000 |
|Ne. sdílených složek na zařízení |24 |
|Ne. sdílených složek na kontejner |1 |
|Maximální velikost souboru zapsaná do sdílené složky| 5 TB |

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
