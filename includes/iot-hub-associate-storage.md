---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8aa4695ea1175fe9d558e02bae661c9610123299
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087107"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Přidružení účtu služby Azure Storage do služby IoT Hub

Protože aplikace simulovaného zařízení nahraje soubor do objektu blob, musíte mít [služby Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) účet přidružený ke službě IoT Hub. Když přidružíte účet Azure Storage pomocí služby IoT hub, IoT hub vygeneruje identifikátor URI SAS. Zařízení můžete pomocí tohoto identifikátoru URI SAS bezpečně nahrát soubor do kontejneru objektů blob. Služba IoT Hub a sady SDK pro zařízení koordinovat proces, který vygeneruje identifikátor URI SAS a zpřístupňuje je na zařízení používat k nahrání souboru.

Postupujte podle pokynů v [nahrání souborů konfigurace pomocí webu Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) přidružení účtu služby Azure Storage do služby IoT hub. Ujistěte se, že kontejner objektů blob souvisí s centrem IoT a že jsou povolené soubor oznámení.

![Povolit soubor oznámení na portálu](./media/iot-hub-associate-storage/enable-file-notifications.png)