---
title: Řešení potíží se zvukovými moduly a moduly pro rozpoznávání řeči v Azure Percept
description: Získejte tipy pro řešení problémů s některými častými problémy zjištěnými během připojování.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679300"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Řešení potíží se zvukem a modulem Speech pro Azure Percept

Pomocí níže uvedených pokynů můžete řešit problémy s aplikacemi hlasové asistenty.

## <a name="collecting-speech-module-logs"></a>Shromažďování protokolů modulu řeči

Pokud chcete spustit tyto příkazy, [Připojte se ke službě Azure PERCEPT DK Wi-Fi přístupový bod a připojte se k vývojové sadě přes SSH](./how-to-ssh-into-percept-dk.md) a zadejte příkazy v terminálu SSH.

```console
 iotedge logs azureearspeechclientmodule
```

K přesměrování libovolného výstupu do souboru. txt k další analýze použijte následující syntaxi:

```console
[command] > [file name].txt
```

Po přesměrování výstupu do souboru. txt zkopírujte soubor do hostitelského počítače přes bod připojení služby (SCP):

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[cesta k souboru místního hostitele] odkazuje na umístění na hostitelském počítači, do kterého chcete zkopírovat soubor. txt. [vzdálené uživatelské jméno] je uživatelské jméno SSH zvolené během [instalačního prostředí](./quickstart-percept-dk-set-up.md). Pokud jste během procesu OOBE nevytvořili přihlašovací jméno SSH, je vaše vzdálené uživatelské jméno root.

## <a name="checking-runtime-status-of-the-speech-module"></a>Kontroluje se běhový stav modulu řeči.

Ověřte, zda stav modulu runtime **azureearspeechclientmodule** ukazuje, že je **spuštěn**. Chcete-li najít běhový stav modulů zařízení, otevřete [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) a přejděte do části **všechny prostředky**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknutím na kartu **moduly** zobrazíte stav modulu runtime všech instalovaných modulů.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Stránka hraničního zařízení v Azure Portal.":::

Pokud běhový stav **azureearspeechclientmodule** není uveden jako **spuštěný**, klikněte na **nastavit moduly**  ->  **azureearspeechclientmodule**. Na stránce **nastavení modulu** nastavte **požadovaný stav** na **spuštěno** a klikněte na **aktualizovat**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Obrazovka nastavit moduly v Azure Portal.":::

## <a name="understanding-ear-som-led-indicators"></a>Princip indikátorů LED pro ušní objekty SoM

Pomocí indikátorů LED můžete pochopit, ve kterém stavu je zařízení. Aby se modul plně inicializoval po *Zapnutí*, obvykle trvá přibližně 2 minuty. Jak prochází kroky inicializace, které se zobrazí:

1. 1 levá zelená světla – zařízení je zapnuté. 
2. probíhá 1 zelený zelený světlý a středový LED, který provedl blikání zelených ověřování. 
3. Až se zařízení ověří a bude připravené k použití, všechny tři diody LED se změní na modrou.

|Stav LED                  |Stav ušního SoM            |
|----------------------------|---------------------------|
|1x zelená (indikátor LED vlevo)         |zapnout |
|1x zelená (indikátor LED vlevo) <br> 1x blikající zelenou (indikátor LED na středu) |Probíhá ověřování. |
|3x vypnuto                      |Inicializace dokončena |
|3x modrá                     |připraveno k použití |
|3x blikající modrou            |rozpoznané klíčové slovo |
|3xá závodní modrá              |zpracováván |
|3x červená                      |vypnutí |

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s Azure Percept DK najdete v tématu [Obecná příručka pro odstraňování potíží](./troubleshoot-dev-kit.md) .