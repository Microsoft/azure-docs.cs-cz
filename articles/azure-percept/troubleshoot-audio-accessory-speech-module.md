---
title: Řešení potíží se službou Azure Percept audio a modulem Speech
description: Získejte tipy pro řešení potíží pro Azure Percept audio a azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605650"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Řešení potíží se zvukem a modulem Speech pro Azure Percept

Pomocí níže uvedených pokynů můžete řešit problémy s aplikacemi hlasové asistenty.

## <a name="collecting-speech-module-logs"></a>Shromažďování protokolů modulu řeči

Chcete-li spustit tyto příkazy, spusťte [SSH do sady dev Kit](./how-to-ssh-into-percept-dk.md) a zadejte příkazy do výzvy klienta ssh.

Shromažďovat protokoly modulů řeči:

```console
sudo iotedge logs azureearspeechclientmodule
```

Chcete-li přesměrovat výstup do souboru. txt pro další analýzu, použijte následující syntaxi:

```console
sudo [command] > [file name].txt
```

Změňte oprávnění souboru. txt tak, aby bylo možné ho zkopírovat:

```console
sudo chmod 666 [file name].txt
```

Po přesměrování výstupu do souboru. txt zkopírujte soubor do hostitelského počítače přes bod připojení služby (SCP):

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[cesta k souboru místního hostitele] odkazuje na umístění na hostitelském počítači, do kterého chcete zkopírovat soubor. txt. [vzdálené uživatelské jméno] je uživatelské jméno SSH zvolené během [instalačního prostředí](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Kontroluje se běhový stav modulu řeči.

Ověřte, zda stav modulu runtime **azureearspeechclientmodule** ukazuje, že je **spuštěn**. Pokud chcete najít běhový stav modulů zařízení, otevřete [Azure Portal](https://portal.azure.com/) a přejděte do části **všechny prostředky**  ->  **[vaše centrum IoT]**  ->  **IoT Edge**  ->  **[ID zařízení]**. Kliknutím na kartu **moduly** zobrazíte stav modulu runtime všech instalovaných modulů.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Stránka hraničního zařízení v Azure Portal.":::

Pokud běhový stav **azureearspeechclientmodule** není uveden jako **spuštěný**, klikněte na **nastavit moduly**  ->  **azureearspeechclientmodule**. Na stránce **nastavení modulu** nastavte **požadovaný stav** na **spuštěno** a klikněte na **aktualizovat**.

## <a name="understanding-ear-som-led-indicators"></a>Princip indikátorů LED pro ušní objekty SoM

Pomocí indikátorů LED můžete pochopit, ve kterém stavu je zařízení. Obvykle trvá dokončení inicializace modulu v případě, že dojde k úplné inicializaci modulu po 2 minutách. Jak prochází kroky inicializace, zobrazí se:

1. Indikátor bílé na střed (statický): zařízení je zapnuté.
2. Indikátor bílé na střed (blikající): probíhá ověřování.
3. Až se zařízení ověří a bude připravené k použití, všechny tři diody LED se změní na modrou.

|POD|Stav LED|Stav ušního SoM|
|---|---------|--------------|
|L02|na1x bílé, statické na|Zapnout |
|L02|na1x bílá, 0,5 Hz – bliknutí|Probíhá ověřování. |
|L01 & L02 & L03|3x Blue, static on|Čekání na klíčové slovo|
|L01 & L02 & L03|Blikání pole LED, 20fps |Naslouchat nebo mluvit|
|L01 & L02 & L03|Pole LED, 20fps|Uvažujete|
|L01 & L02 & L03|3x Red, static on |Vypnutí|

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Azure Percept DK najdete v tématu [Obecná příručka pro odstraňování potíží](./troubleshoot-dev-kit.md) .
