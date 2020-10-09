---
title: Azure Kinect Viewer
description: Pochopte, jak vizualizovat všechny datové proudy zařízení pomocí prohlížeče Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, snímač, Viewer, vizualizace, Hloubka, RGB, barva, imu, zvuk, mikrofon, Point Cloud
ms.openlocfilehash: 57cf7df831e97da4143a7f196b69a3a10609a017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277149"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect Viewer

Prohlížeč Azure Kinect, který se nachází v adresáři nainstalovaných nástrojů jako `k4aviewer.exe` (například `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` , kde `X.Y.Z` je nainstalovaná verze sady SDK), se dá použít k vizualizaci všech datových proudů zařízení:

* Ověřte, že senzory fungují správně.
* Snadnější umístění zařízení
* Experimentujte s nastavením kamery.
* Čtení konfigurace zařízení.
* Nahrávání záznamů pomocí služby [Azure Kinect](azure-kinect-recorder.md)Recorder.

Další informace o Azure Kinect Vieweru najdete v tématu [Jak používat Azure Kinect video](https://www.microsoft.com/videoplayer/embed/RE3hNwG).

Azure Kinect Viewer je [Open Source](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) a je možné ho použít jako příklad používání rozhraní API.

## <a name="use-viewer"></a>Použití prohlížeče

Prohlížeč může pracovat ve dvou režimech: pomocí živých dat ze senzoru nebo zaznamenaných dat ([Azure Kinect](azure-kinect-recorder.md)Recording).

### <a name="start-application"></a>Spustit aplikaci

Spusťte aplikaci spuštěním `k4aviewer.exe` .

![Kontrola verze firmwaru zařízení pomocí prohlížeče](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Použití prohlížeče s živými daty

1. V části **otevřít zařízení** vyberte **sériové číslo** zařízení, které chcete otevřít. Pak vyberte **aktualizovat**, pokud chybí zařízení.
2. Vyberte tlačítko **otevřít zařízení** .
3. Vyberte **začít** a začněte streamovat data s výchozím nastavením.

### <a name="use-the-viewer-with-recorded-data"></a>Použití prohlížeče se zaznamenanými daty

V části **Otevřít záznam** přejděte na zaznamenaný soubor a vyberte jej.

## <a name="check-device-firmware-version"></a>Ověřit verzi firmwaru zařízení

V okně Konfigurace získáte přístup ke verzi firmwaru zařízení, jak je znázorněno na následujícím obrázku.

![Kontrola verze firmwaru zařízení pomocí prohlížeče](./media/how-to-guides/check-firmware-update.png)

Například v tomto případě je u poskytovatele internetových fotoaparátů spuštěná aplikace FW 1.5.63.

## <a name="depth-camera"></a>Hloubková kamera

Prohlížeč hloubkové kamery zobrazí dvě okna:

* Jedna se nazývá *aktivní jas* , což je obrázek ve stupních šedi, který znázorňuje infračervený jas.
* Druhá se nazývá *Hloubka*, která má barevnou reprezentaci dat hloubky.

Pokud chcete zobrazit hodnotu senzoru hloubky, jak je znázorněno níže, podržte ukazatel myši v Obrazovém okně hloubky.

![Zobrazení hloubky](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>Kamera RGB

Následující obrázek znázorňuje zobrazení barevné kamery.

![Zobrazení RGB](./media/how-to-guides/viewer-rgb-camera.png)

Nastavení kamery RGB můžete ovládat z okna konfigurace během streamování.

![Ovládací prvky kamery RGB](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Jednotka měření Inertial (IMU)

Okno IMU má dvě komponenty, akcelerometr a vybavený gyroskopem.

Horní polovina je akcelerometr a ukazuje lineární akceleraci v metrech za sekundu<sup>2</sup>.  Zahrnuje akceleraci z hustoty, takže pokud je v tabulce plochá, osa Z se pravděpodobně zobrazí přibližně – 9,8 m/s<sup>2</sup>.

Spodní polovina je vybavený gyroskopem část a zobrazuje rotaci pohybů v radiánech za sekundu.

![Zobrazení snímače pohybu](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Vstup mikrofonu

Zobrazení mikrofonu znázorňuje znázornění zvuku na každém mikrofonu. Pokud není zvuk, graf se zobrazí jako prázdný, jinak se v opačném případě zobrazí tmavá modrá křivka s světle modrým signálem.

Tmavá vlna představuje minimální a maximální hodnoty zjištěné mikrofonem v daném časovém intervalu. Světlá vlna představuje kořenovou střední hodnotu hodnot pozorovaných mikrofonem v daném časovém intervalu.

![Vstupní zobrazení mikrofonu](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Ukázat cloudová vizualizace

Hloubkové vizuální zobrazení v 3D vám umožní přesouvat se v obraze pomocí pokynných klíčů.

![Cloud bodu hloubky](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Řízení synchronizace

Ke konfiguraci zařízení jako samostatného (výchozího), hlavního nebo podřízeného režimu při konfiguraci synchronizace s více zařízeními můžete použít prohlížeč.
Při změně konfigurace nebo vložení nebo odebrání synchronizačního kabelu vyberte **aktualizovat** , aby se aktualizace aktualizovala.

![Řízení externích synchronizací](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Průvodce nastavením externí synchronizace](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
