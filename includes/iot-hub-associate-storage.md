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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76021133"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Přidružte účet Azure Storage k IoT Hub

Vzhledem k tomu, že aplikace simulovaného zařízení nahrává soubor do objektu blob, musíte mít účet [Azure Storage](../articles/storage/common/storage-account-create.md) přidružený k vašemu centru IoT. Když přidružíte účet Azure Storage ke službě IoT Hub, Centrum IoT vygeneruje identifikátor URI SAS. Zařízení může tento identifikátor URI SAS použít k bezpečnému nahrání souboru do kontejneru objektů BLOB. Služba IoT Hub a sady SDK pro zařízení koordinují proces, který generuje identifikátor URI SAS a zpřístupňuje ho zařízení, které se použije k nahrání souboru.

Postupujte podle pokynů v tématu [Konfigurace nahrávání souborů pomocí Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Ujistěte se, že je kontejner objektů BLOB přidružený ke službě IoT Hub a že jsou povolená oznámení o souborech.

![Povolit oznámení souborů na portálu](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
