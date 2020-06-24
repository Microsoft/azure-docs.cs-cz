---
title: Rychlý Start – záznam datových proudů senzorů Azure Kinect do souboru
description: V tomto rychlém startu se dozvíte, jak zaznamenat datové proudy ze sady senzorů do souboru.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Azure, Kinect, Recording, Play back, Reader, Matroska, MKV, Streams, Depth, RGB, fotoaparát, Color, imu, audio, snímač
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277940"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Rychlý Start: nahrání datových proudů senzorů Azure Kinect do souboru

V tomto rychlém startu najdete informace o tom, jak můžete pomocí nástroje pro [nahrávání v Azure Kinect](azure-kinect-recorder.md) zaznamenávat datové proudy ze sady senzorů do souboru.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

Tento rychlý Start předpokládá:

- Máte službu Azure Kinect DK připojenou k hostitelskému počítači a pracujete správně.
- Dokončili jste [Nastavení](set-up-azure-kinect-dk.md) hardwaru.

## <a name="create-recording"></a>Vytvořit záznam

1. Otevřete příkazový řádek a zadejte cestu k [záznamu Azure Kinect](azure-kinect-recorder.md), který se nachází v adresáři nainstalovaných nástrojů jako `k4arecorder.exe` . Příklad: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Záznam 5 sekund.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Ve výchozím nastavení používá záznam hloubkový režim NFOV Unbinned a výstupy 1080p RGB do 30 fps včetně dat IMU. Úplný přehled možností a tipů pro nahrávání najdete v tématu [Azure Kinect](azure-kinect-recorder.md)Recording.

## <a name="play-back-recording"></a>Přehrání záznamu

K přehrání záznamu můžete použít [prohlížeč Azure Kinect Viewer](azure-kinect-viewer.md) .

1. Předběžné[`k4aviewer.exe`](azure-kinect-viewer.md)
2. Odložte kartu **otevřené nahrávání** a otevřete záznam.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nahrávat streamy senzorů do souboru, je čas

> [!div class="nextstepaction"]
> [Vytvoření první aplikace Azure Kinect](build-first-app.md)
