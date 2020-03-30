---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021133"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Přidružení účtu úložiště Azure k centru IoT Hub

Vzhledem k tomu, že aplikace simulované zařízení nahraje soubor do objektu blob, musíte mít účet [Azure Storage](../articles/storage/common/storage-account-create.md) přidružený k vašemu centru IoT. Když přidružíte účet Úložiště Azure k centru IoT, služba IoT hub vygeneruje identifikátor URI SAS. Zařízení může pomocí tohoto identifikátoru URI SAS bezpečně odeslat soubor do kontejneru objektů blob. Služba IoT Hub a sady SDK zařízení koordinují proces, který generuje identifikátor URI SAS, a zpřístupní jej zařízení k nahrání souboru.

Postupujte podle pokynů v [části Konfigurace nahrávání souborů pomocí portálu Azure](../articles/iot-hub/iot-hub-configure-file-upload.md). Ujistěte se, že kontejner objektů blob je přidružený k centru IoT a že jsou povolena oznámení o souborech.

![Povolení oznámení souborů na portálu](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
