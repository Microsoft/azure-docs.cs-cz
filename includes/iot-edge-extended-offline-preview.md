---
title: zahrnout soubor
description: IOT edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264362"
---
## <a name="enabling-extended-offline-operation-preview"></a>Povolení rozšířené operace vypnutí (Preview)
S [vydání verzi v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) modulu runtime Edge hraniční zařízení a podřízené zařízení připojující se k němu lze nakonfigurovat pro rozšířené operace vypnutí. 

Díky této funkci můžete lokální moduly nebo příjem dat zařízení opakované ověření u hraniční zařízení podle potřeby a komunikovat mezi sebou pomocí zprávy a metody i po odpojení ze služby IoT Hub. Najdete v tomto [blogový příspěvek](https://aka.ms/iot-edge-offline) a [koncept článku](../articles/iot-edge/offline-capabilities.md) podrobné informace a oboru této funkce.

Povolení rozšířené offline v případě brány navázání vztahu nadřazený podřízený mezi hraniční zařízení a podřízené zařízení, která se připojí k němu.

1. Z hraničních okně podrobností o zařízení na portálu služby IoT Hub, klikněte na tlačítko **Spravovat podřízené zařízení (preview)** tlačítko v horním řádku nabídek.

1. Klikněte na tlačítko **+ přidat** tlačítko.

1. V seznamu zařízení vyberte zařízení, podřízené a použijte šipku vpravo a vyberte ty, které chcete přidat jako podřízené objekty.

1. Klikněte na tlačítko **OK** potvrďte.

Hraniční zařízení a jeho podřízené zařízení jsou teď povolené pro rozšířené operace vypnutí.  