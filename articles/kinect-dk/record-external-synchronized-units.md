---
title: Použití zapisovače Azure Kinect s externími synchronizovanými zařízeními
description: Naučte se nahrávat data ze zařízení nakonfigurovaných pro externí synchronizaci pomocí zapisovače Azure Kinect.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, senzor, prohlížeč, externí synchronizace, zpoždění fáze, Hloubka, RGB, fotoaparát, zvukový kabel, záznam
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277144"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Použití zapisovače Azure Kinect s externími synchronizovanými zařízeními

V tomto článku najdete pokyny k tomu, jak může [zapisovač Azure Kinect](azure-kinect-recorder.md) nahrávat zařízení konfigurovaná externí synchronizací dat.

## <a name="prerequisites"></a>Předpoklady

- [Nastavte několik jednotek Azure Kinect DK pro externí synchronizaci](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Omezení externí synchronizace

- Hlavní zařízení nemůže mít připojenu synchronizaci kabelem.
- Aby bylo možné synchronizaci povolit, musí hlavní zařízení streamovat kameru RGB.
- Všechny jednotky musí používat stejnou konfiguraci kamery (snímkování a rozlišení).
- Všechny jednotky musí spouštět stejný firmware zařízení (pokyny k[aktualizaci firmwaru](update-device-firmware.md) ).
- Všechna podřízená zařízení musí být spuštěná před hlavním zařízením.
- Stejná hodnota expozice by měla být nastavená na všech zařízeních.
- *Hlavní nastavení zpoždění* každého podřízeného objektu je relativní vzhledem k hlavnímu zařízení.

## <a name="record-when-each-unit-has-a-host-pc"></a>Záznam, pokud má každá jednotka hostitelský počítač

V následujícím příkladu má každé zařízení vlastní vyhrazený hostitelský počítač.
Doporučujeme připojit zařízení k vyhrazeným počítačům, aby se zabránilo problémům s využitím šířky pásma USB a CPU nebo GPU.

### <a name="subordinate-1"></a>Podřízená-1

1. Nastavte si zapisovač pro první jednotku.

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Zařízení začíná čekat

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Podřízená 2

1. Nastavit záznam pro druhou jednotku

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Zařízení začíná čekat

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Hlavní

1. Spustit nahrávání pro hlavní uzel

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Počkejte na dokončení nahrávání.

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Záznam, pokud je více jednotek připojeno k jednomu hostitelskému počítači

K jednomu hostitelskému počítači je možné připojit několik DKs služby Azure Kinect. To však může být náročné na šířku pásma USB a na výpočetní výkon. Snížení poptávky:

- Připojte každé zařízení k vlastnímu řadiči hostitele USB.
- Máte výkonný grafický procesor, který dokáže zvládnout hloubku pro každé zařízení.
- Zaznamená jenom senzory potřebné k použití nižšího snímkového kmitočtu.

Vždy nejprve spustit podřízená zařízení a hlavní poslední.

## <a name="subordinate-1"></a>Podřízená-1

1. Spustit záznam na podřízeném

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Zařízení přejde do stavu čekání.

## <a name="master"></a>Hlavní

1. Spustit hlavní zařízení

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Počkat na dokončení nahrávání

## <a name="playing-recording"></a>Přehrávání záznamu

K přehrání záznamu můžete použít [prohlížeč Azure Kinect Viewer](azure-kinect-viewer.md) .



## <a name="tips"></a>Tipy

- Používejte ruční expozici pro zaznamenávání synchronizovaných fotoaparátů. Automatické vystavení kamery RGB může mít dopad na synchronizaci času.
- Restartování podřízeného zařízení způsobí ztrátu synchronizace.
- Některé [režimy kamery](hardware-specification.md#depth-camera-supported-operating-modes) podporují maximálně 15 snímků. Doporučujeme, abyste mezi zařízeními nekombinované režimy a frekvence snímků.
- Připojení několika jednotek k jednomu počítači může snadno nasycenou šířku pásma USB zvážit použití samostatného hostitelského počítače na zařízení. Věnujte pozornost také výpočetním PROCESORům a GPU.
- Pokud není potřeba zlepšit spolehlivost, zakažte mikrofon a IMU.

Případné problémy najdete v tématu [řešení potíží](troubleshooting.md) .

## <a name="see-also"></a>Viz také

- [Nastavit externí synchronizaci](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Záznam Azure Kinect](azure-kinect-recorder.md) Recorder pro nastavení zapisovače a další informace.
- [Azure Kinect Viewer](azure-kinect-viewer.md) pro přehrávání nahrávek nebo nastavení vlastností kamery RGB, které nejsou k dispozici prostřednictvím záznamu Recording.
- [Nástroj pro firmware Azure Kinect](azure-kinect-firmware-tool.md) pro aktualizaci firmwaru zařízení.
