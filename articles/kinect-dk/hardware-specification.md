---
title: Specifikace hardwaru Azure Kinect DK
description: Seznamte se s komponentami, specifikacemi a možnostmi zařízení Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specs, hardware, DK, capabilities, depth, color, RGB, IMU, microphone, array, depth
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: dbe4cac64e324c1188ec5a01b4da3935a5d5b479
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371294"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Specifikace hardwaru Azure Kinect DK

Tento článek obsahuje podrobné informace o tom, jak hardware Azure Kinect v jednom příslušenství připojeném přes USB integruje nejnovější technologie Microsoftu v oblasti snímačů.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Výrazy

V rámci tohoto článku se používají následující zkratky.

- NFOV (režim úzkého zorného pole)
- WFOV (režim širokého zorného pole)
- FOV (zorné pole)
- FPS (snímky za sekundu)
- IMU (inerciální měřící jednotka)
- FoI (oblast zájmu)

## <a name="product-dimensions-and-weight"></a>Rozměry a hmotnost produktu

Zařízení Azure Kinect má následující rozměry a hmotnost.

- **Dimenze:** 103 × 39 × 126 mm
- **Hmotnost:** 440 g

![Rozměry Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Provozní prostředí

Zařízení Azure Kinect DK je určené pro vývojáře a komerční firmy pracující v prostředích s následujícími podmínkami:

- **Teplota:** 10–25⁰C
- **Vlhkost:** 8–90% relativní vlhkost (bez kondenzace)

> [!NOTE]
> Používáním mimo tyto podmínky prostředí může dojít k selhání nebo nesprávnému fungování zařízení. Tyto podmínky prostředí platí pro bezprostřední blízkost zařízení za všech provozních podmínek. Při použití s externím krytem doporučujeme používat aktivní regulaci teploty nebo jiná chladicí řešení, aby se zajistilo, že zařízení nepřekročí tyto rozsahy. Součástí návrhu zařízení je chladicí kanál mezi přední a zadní částí obalu. Při implementaci zařízení se ujistěte, že chladicí kanál nic neblokuje.

Přečtěte si další [bezpečnostní informace](https://support.microsoft.com/help/4023454/safety-information) k produktu.

## <a name="depth-camera-supported-operating-modes"></a>Podporované provozní režimy hloubkové kamery

Azure Kinect DK obsahuje integrovanou 1megapixelovou hloubkovou kameru ToF (Time-of-Flight) navrženou Microsoftem, která využívá [obrazový snímač představený na konferenci ISSCC 2018](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). Hloubková kamera podporuje následující režimy:

 | Mode            | Řešení | FoI       | FPS                | Provozní rozsah* | Doba expozice |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV bez rozdělení na intervaly   | 640 × 576    | 75° × 65°   | 0, 5, 15, 30       | 0,5–3,86 m       | 12,8 ms        |
| NFOV 2x2 s rozdělením na intervaly (SW) | 320 × 288    | 75° × 65°   | 0, 5, 15, 30       | 0,5–5,46 m       | 12,8 ms        |
| WFOV 2x2 s rozdělením na intervaly | 512 × 512    | 120° × 120° | 0, 5, 15, 30       | 0,25–2,88 m      | 12,8 ms        |
| WFOV bez rozdělení na intervaly   | 1024 × 1024  | 120° × 120° | 0, 5, 15           | 0,25–2,21 m      | 20,3 ms        |
| Pasivní IR      | 1024 × 1024  | –       | 0, 5, 15, 30       | –              | 1,6 ms         |

\*Odrazivost 15–95 % při 850nm, 2,2 μW/cm<sup>2</sup>/nm, směrodatná odchylka náhodných chyb ≤ 17 mm, typická systematická chyba < 11 mm + 0,1 % vzdálenosti bez rušení způsobeného více cestami. Hloubka mimo uvedený rozsah závisí na odrazivosti objektu.

## <a name="color-camera-supported-operating-modes"></a>Podporované provozní režimy barevné kamery

Azure Kinect DK obsahuje snímač OV12A10 12MP CMOS s pohyblivou závěrkou. Nativní provozní režimy jsou uvedené níže:

|             Rozlišení kamery RGB (HxV)  |          Poměr stran  |          Možnosti formátu   |          Obnovovací frekvence (FPS)  |          Nominální FOV (HxV; po následném zpracování)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840 × 2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° × 59°                              |
|       2560 × 1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° × 59°                              |
|       1920 × 1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° × 59°                              |
|       1280 × 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90° × 59°                              |
|       4096 × 3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90° × 74,3°                            |
|       2048 × 1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90° × 74,3°                            |

Kamera RGB je kompatibilní s video řadičem třídy USB a je možné ji používat bez sady SDK pro snímače. Barevný prostor kamery RGB: Úplný rozsah BT.601 (0–255). 

> [!NOTE]
> Sada SDK pro snímače může poskytovat barevné obrázky ve formátu pixelů BGRA. Zařízení tento režim nativně nepodporuje a jeho použitím se zvýší zatížení procesoru. K převodu z obrázků MJPEG přijatých ze zařízení se používá procesor hostitele.

## <a name="rgb-camera-exposure-time-values"></a>Časové hodnoty expozice kamery RGB

Níže je mapování přijatelných hodnot ruční expozice kamery RGB:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Hrubé časování snímače hloubky

Režim hloubky | IR <br>impulzy | Šířka <br>impulzu  | Období <br>nečinnosti| Doba nečinnosti | Expozice <br> Čas
-|-|-|-|-|-
NFOV bez rozdělení na intervaly <br>  NFOV 2xx s rozdělením na intervaly <br> WFOV 2x2 s rozdělením na intervaly | 9 | 125 μs | 8 | 1450 μs | 12,8 ms 
WFOV bez rozdělení na intervaly                                            | 9 | 125 μs | 8 | 2390 μs | 20,3 ms


## <a name="camera-field-of-view"></a>Zorné pole kamery

Následující obrázek ukazuje zorné pole hloubkové kamery a kamery RGB, neboli úhly viditelné pro snímače. Tento diagram znázorňuje kameru RGB v režimu 4:3.

![Zorné pole kamery](./media/resources/hardware-specs-media/camera-fov.png)

Tento obrázek ukazuje zorné pole kamery, jak je vidět zepředu ze vzdálenosti 2000 mm.

![Zorné pole kamery zepředu](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Když je hloubka v režimu NFOV, kamera RGB má lepší překrývání pixelů v rozlišení 4:3 než v rozlišení 16:9.

## <a name="motion-sensor-imu"></a>Snímač pohybu (IMU)

Tato vestavěná inerciální měřící jednotka (IMU) LSM6DSMUS zahrnuje akcelerometr a gyroskop. Vzorky z akcelerometru a gyroskopu se sbírají současně s frekvencí 1,6 kHz. Vzorky se hlásí hostiteli s frekvencí 208 Hz.

## <a name="microphone-array"></a>Mikrofonní pole

Azure Kinect DK obsahuje vestavěné kruhové pole sedmi vysoce kvalitních mikrofonů, které se považuje za standardní zařízení s audio řadičem třídy USB 2.0. Přístup je možný ke všem 7 kanálům. Výkonové specifikace jsou následující:

- Citlivost: -22 dBFS (94 dB SPL, 1 kHz)
- Odstup signálu od šumu > 65 dB
- Bod akustického přetížení: 116 dB

![Mikrofonní bublina](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK je složené zařízení USB 3, které operačnímu systému zpřístupňuje následující hardwarové koncové body:

ID dodavatele je 0x045E (Microsoft) a tabulka ID produktů je uvedená níže:

|    Rozhraní USB        |    IP adresa PNP    |     Poznámky            |
|-------------------------|--------------|----------------------|
|    Rozbočovač USB 3.1 1. generace    |    0x097A    |    Hlavní rozbočovač    |
|    Rozbočovač USB 2.0         |    0x097B    |    HS USB          |
|    Hloubková kamera       |    0x097C    |    USB 3.0            |
|    Barevná kamera       |    0x097D    |    USB 3.0            |
|    Mikrofony        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Indikátory

Na přední straně zařízení je indikátor streamování kamery, který je možné pomocí sady SDK pro snímače programově vypnout.

Stavová kontrolka LED na zadní straně zařízení indikuje stav zařízení:

| Stav světla     | Význam                                                   |
|-----------------------|------------------------------------------------------------|
| Čistě bílé           | Zařízení je zapnuté a funguje správně.                         |
| Blikající bílé        | Zařízení je zapnuté, ale nemá datové připojení přes USB 3.0.   |
| Blikající žlutohnědé        | Zařízení nemá dostatek energie k provozu.               |
| Blikající žlutohnědé a bílé  | Probíhá aktualizace firmwaru nebo obnovení.                    |

## <a name="power-device"></a>Napájení zařízení

Zařízení je možné napájet dvěma způsoby:

1. Pomocí zdroje napájení, který je součástí balení. K datovému připojení se používá samostatný kabel USB z adaptérem z typu C na typ A.
2. Pomocí kabelu s konektory typu C, který slouží k napájení i datovému připojení.

Kabel s konektory typu C není součástí balení Azure Kinect DK.

> [!NOTE]
> - Napájecí kabel, který je součástí balení, má jeden konektor USB typu A a jeden koaxiální konektor s jedním vnitřním vodičem. S tímto kabelem používejte přiložený síťový zdroj napájení. Zařízení může čerpat více energie, než kolik dokáží poskytnout dva standardní porty USB typu A.
> - Na kabelech USB záleží, proto doporučujeme používat vysoce kvalitní kabely a před vzdáleným nasazením jednotky otestovat jejich funkčnost.

> [!TIP]
> Zásady výběru správného kabelu s konektory typu C:
> - [Certifikovaný kabel USB](https://www.usb.org/products) musí podporovat napájení i datové připojení.
> - Pasivní kabel by neměl být delší než 1,5 m. V případě potřeby delšího kabelu použijte aktivní kabel. 
> - Kabel musí podporovat alespoň 1,5 A. Jinak budete muset připojit externí zdroj napájení.

Ověření kabelu:

- Připojte zařízení kabelem k hostitelskému počítači.
- Ověřte, že se všechna zařízení správně zobrazí ve Správci zařízení Windows. Hloubková kamera a kamera RGB by měly vypadat podobně jako v následujícím příkladu.

  ![Azure Kinect DK ve Správci zařízení](./media/resources/hardware-specs-media/device-manager.png)

- V aplikaci Azure Kinect Viewer ověřte, že kabel dokáže spolehlivě streamovat na všech snímačích s následujícím nastavením:

  - Hloubková kamera: NFOV bez rozdělení na intervaly
  - Kamera RGB: 2160p
  - Povolené mikrofony a IMU

## <a name="what-does-the-light-mean"></a>Jaký je význam světla?

Indikátor napájení je kontrolka LED na zadní straně Azure Kinect DK. Barva této kontrolky LED se mění v závislosti na stavu zařízení.

![Na obrázku je znázorněná zadní strana Azure Kinect DK. Na obrázku jsou tři očíslované popisky: jeden pro indikátor LED a pod ním dva pro kabely.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Na tomto obrázku jsou uvedené následující komponenty:

1. Indikátor napájení
1. Napájecí kabel (připojený ke zdroji napájení)
1. Datový kabel USB-C (připojený k počítači)

Ujistěte se, že jsou kabely připojené jako na obrázku. Pak si projděte následující tabulku s informacemi o tom, co značí různé stavy světla napájení.

|Stav světla: |Význam: |Doporučený postup: |
| ---| --- | --- |
|Čistě bílé |Zařízení je zapnuté a funguje správně. |Zařízení můžete používat. |
|Zhasnuté |Zařízení není připojené k počítači. |Ujistěte se, že je kabel s kulatým napájecím konektorem připojený k zařízení a napájecímu adaptéru USB.<br /><br />Ujistěte se, že je kabel USB-C připojený k zařízení a počítači. |
|Blikající bílé |Zařízení je zapnuté, ale nemá datové připojení přes USB 3.0. |Ujistěte se, že je kabel s kulatým napájecím konektorem připojený k zařízení a napájecímu adaptéru USB.<br /><br />Ujistěte se, že je kabel USB-C připojený k zařízení a portu USB 3.0 na počítači.<br /><br />Připojte zařízení k jinému portu USB 3.0 na počítači.<br /><br />Na počítači otevřete Správce zařízení (**Start** > **Ovládací panely** > **Správce zařízení**) a ověřte, že počítač obsahuje podporovaný hostitelský řadič USB 3.0. |
|Blikající žlutohnědé |Zařízení nemá dostatek energie k provozu. |Ujistěte se, že je kabel s kulatým napájecím konektorem připojený k zařízení a napájecímu adaptéru USB.<br /><br />Ujistěte se, že je kabel USB-C připojený k zařízení a počítači. |
|Žlutohnědé a následně blikající bílé |Zařízení je zapnuté a dochází na něm k aktualizaci firmwaru nebo se zařízení obnovuje do továrního nastavení. |Počkejte, až bude světlo indikátoru napájení čistě bílé. Další informace najdete v tématu [Resetování Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Spotřeba energie

Spotřeba Azure Kinect DK je až 5,9 W, ale skutečná spotřeba energie závisí na konkrétním případu použití.

## <a name="calibration"></a>Kalibrace

Azure Kinect DK se kalibruje v továrně. Kalibrační parametry pro vizuální a inerciální snímače je možné dotazovat programově prostřednictvím sady SDK pro snímače.

## <a name="device-recovery"></a>Obnovení zařízení

Firmware zařízení je možné resetovat na původní firmware pomocí tlačítka pod blokovacím šroubkem.

![Tlačítko pro obnovení Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Při obnově zařízení postupujte podle [těchto pokynů](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Další kroky

- [Použití sady SDK pro snímače Azure Kinect](about-sensor-sdk.md)
- [Nastavení hardwaru](set-up-azure-kinect-dk.md)
