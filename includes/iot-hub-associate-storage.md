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
ms.openlocfilehash: f88f6aa6daec342b24d165c4c4cc0a89a0805f7a
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558460"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Přidružte účet Azure Storage k IoT Hub

Vzhledem k tomu, že aplikace simulovaného zařízení nahrává soubor do objektu blob, musíte mít účet [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) přidružený k vašemu centru IoT. Když přidružíte účet Azure Storage ke službě IoT Hub, Centrum IoT vygeneruje identifikátor URI SAS. Zařízení může tento identifikátor URI SAS použít k bezpečnému nahrání souboru do kontejneru objektů BLOB. Služba IoT Hub a sady SDK pro zařízení koordinují proces, který generuje identifikátor URI SAS a zpřístupňuje ho zařízení, které se použije k nahrání souboru.

Postupujte podle pokynů v tématu [Konfigurace nahrávání souborů pomocí Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Ujistěte se, že je kontejner objektů BLOB přidružený ke službě IoT Hub a že jsou povolená oznámení o souborech.

![Povolit oznámení souborů na portálu](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
