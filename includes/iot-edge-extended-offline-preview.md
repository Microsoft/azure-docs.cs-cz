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
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004675"
---
## <a name="enabling-extended-offline-operation-preview"></a>Povolení rozšířené operace vypnutí (Preview)
Počínaje [v1.0.2 vydání](https://aka.ms/edge102) modulu runtime Edge hraniční zařízení a podřízené zařízení připojující se k němu lze nakonfigurovat pro rozšířené operace vypnutí. 

Díky této funkci můžete lokální moduly nebo příjem dat zařízení opakované ověření u hraniční zařízení podle potřeby a komunikovat mezi sebou pomocí zprávy a metody i po odpojení ze služby IoT Hub. Najdete v tomto [blogový příspěvek](https://aka.ms/iot-edge-offline) a [koncept článku](../articles/iot-edge/offline-capabilities.md) podrobné informace a oboru této funkce.

Povolení rozšířené offline v případě brány navázání vztahu nadřazený podřízený mezi hraniční zařízení a podřízené zařízení, která se připojí k němu.

1. Z hraničních okně podrobností o zařízení na portálu služby IoT Hub, klikněte na tlačítko **Spravovat podřízené zařízení (preview)** tlačítko v horním řádku nabídek.

1. Klikněte na tlačítko **+ přidat** tlačítko.

1. V seznamu zařízení vyberte zařízení, podřízené a použijte šipku vpravo a vyberte ty, které chcete přidat jako podřízené objekty.

1. Klikněte na tlačítko **OK** potvrďte.

1. V **nastavit moduly** obrazovka z podrobností o zařízení Edge, klikněte na tlačítko **konfigurovat rozšířená nastavení modulu Runtime Edge**a v části **Centrum Edge** proměnné prostředí přidat záznam  **UpstreamProtocol** s hodnotou **MQTT**. Přidat stejnou proměnnou prostředí a hodnoty pro **agenta Edge** také. 

1. Klikněte na tlačítko **Uložit** a nezapomeňte **odeslat** změny po kliknutí na tlačítko **Další** dvakrát.

Hraniční zařízení a jeho podřízené zařízení jsou teď povolené pro rozšířené operace vypnutí.  