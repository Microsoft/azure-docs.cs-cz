---
title: Řešení potíží se službou Azure Percept audio a modulem Speech
description: Získejte tipy pro řešení potíží pro Azure Percept audio a azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f34013bdb14481bfe872a9b3c4234d603bc2d7ec
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635565"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Řešení potíží se zvukem a modulem Speech pro Azure Percept

Pomocí níže uvedených pokynů můžete řešit problémy s aplikacemi hlasové asistenty.

## <a name="collecting-speech-module-logs"></a>Shromažďování protokolů modulu řeči

Pokud chcete spustit tyto příkazy, [Připojte se ke službě Azure PERCEPT DK Wi-Fi přístupový bod a připojte se k vývojové sadě přes SSH](./how-to-ssh-into-percept-dk.md) a zadejte příkazy v terminálu SSH.

```console
sudo iotedge logs azureearspeechclientmodule
```

K přesměrování libovolného výstupu do souboru. txt k další analýze použijte následující syntaxi:

```console
sudo [command] > [file name].txt
```

Po přesměrování výstupu do souboru. txt zkopírujte soubor do hostitelského počítače přes bod připojení služby (SCP):

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[cesta k souboru místního hostitele] odkazuje na umístění na hostitelském počítači, do kterého chcete zkopírovat soubor. txt. [vzdálené uživatelské jméno] je uživatelské jméno SSH zvolené během [připojování](./quickstart-percept-dk-set-up.md). Pokud jste nevytvořili přihlašovací jméno SSH během prostředí inPerceptování ve službě Azure pro Dánsko, je vaše vzdálené uživatelské jméno root.

## <a name="checking-runtime-status-of-the-speech-module"></a>Kontroluje se běhový stav modulu řeči.

Ověřte, zda stav modulu runtime **azureearspeechclientmodule** ukazuje, že je **spuštěn**. Chcete-li najít běhový stav modulů zařízení, otevřete [Azure Portal](https://portal.azure.com/) a přejděte do části **všechny prostředky**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknutím na kartu **moduly** zobrazíte stav modulu runtime všech instalovaných modulů.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Stránka hraničního zařízení v Azure Portal.":::

Pokud běhový stav **azureearspeechclientmodule** není uveden jako **spuštěný**, klikněte na **nastavit moduly**  ->  **azureearspeechclientmodule**. Na stránce **nastavení modulu** nastavte **požadovaný stav** na **spuštěno** a klikněte na **aktualizovat**.

## <a name="understanding-ear-som-led-indicators"></a>Princip indikátorů LED pro ušní objekty SoM

Pomocí indikátorů LED můžete pochopit, ve kterém stavu je zařízení. Aby se modul plně inicializoval po *Zapnutí*, obvykle trvá přibližně 2 minuty. Jak prochází kroky inicializace, které se zobrazí:

1. INDIKÁTORy bílé na střed – zařízení je zapnuté.
2. 1 blikání INDIKÁTORu na pozadí – probíhá ověřování.
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
