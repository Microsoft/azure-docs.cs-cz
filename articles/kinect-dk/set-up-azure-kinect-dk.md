---
title: Rychlý start – Nastavení Azure Kinect DK
description: Tento rychlý start obsahuje pokyny k nastavení hardwaru Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, set up, hardware, quick, usb, power, viewer, sensor, streaming, setup, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211274"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Rychlý start: Nastavení Azure Kinect DK

Tento rychlý start obsahuje pokyny k nastavení Azure Kinect DK. Ukážeme si, jak otestovat vizualizaci datových proudů ze snímačů a jak používat aplikaci [Azure Kinect Viewer](azure-kinect-viewer.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="system-requirements"></a>Systémové požadavky

Projděte si [systémové požadavky](system-requirements.md) a ověřte, jestli konfigurace hostitelského počítače splňuje všechny minimální požadavky Azure Kinect DK.

## <a name="set-up-hardware"></a>Nastavení hardwaru

> [!NOTE]
> Před použitím zařízení nezapomeňte z kamery odstranit ochrannou fólii.

1. Připojte napájecí konektor ke vstupu napájení na zadní straně zařízení. Druhý konec kabelu připojte k napájecímu adaptéru USB a pak adaptér zapojte do zásuvky.
2. Připojte datový kabel USB k zařízení a pak k portu USB 3.0 na počítači.
   >[!NOTE]
   >Nejlepších výsledků dosáhnete, když kabel připojíte přímo k zařízení a počítači. Nepoužívejte k připojení rozšíření ani další adaptéry.

3. Zkontrolujte, jestli kontrolka LED indikátoru napájení (vedle kabelu USB) svítí čistě bíle.
  
   Zapnutí zařízení několik sekund trvá. Zařízení je připravené k použití, jakmile se přední kontrolka LED indikátoru streamování vypne.  

   Další informace o kontrolce LED indikátoru napájení najdete v části [Jaký je význam světla?](hardware-specification.md#what-does-the-light-mean).

    ![Všechny funkce zařízení](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Stažení sady SDK

1. Vyberte odkaz na [stažení sady SDK](sensor-sdk-download.md).
2. Nainstalujte sadu SDK na počítač.

## <a name="update-firmware"></a>Aktualizace firmwaru

Aby vše správně fungovalo, sada SDK vyžaduje nejnovější verzi firmwaru zařízení. Pokud chcete zkontrolovat a aktualizovat verzi firmwaru, postupujte podle kroků v tématu [Aktualizace firmwaru Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Ověření streamování dat ze zařízení

1. Spusťte aplikaci [Azure Kinect Viewer](azure-kinect-viewer.md). Tento nástroj můžete spustit jedním z následujících způsobů:
   - Aplikaci Viewer můžete spustit z příkazového řádku nebo dvojím kliknutím na spustitelný soubor. Tento soubor `k4aviewer.exe` se nachází v adresáři nástrojů sady SDK (například `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, kde `X.Y.Z` je nainstalovaná verze sady SDK).
   - Aplikaci Azure Kinect Viewer můžete spustit z nabídky **Start** zařízení.
2. V aplikaci Azure Kinect Viewer vyberte **Open Device** > **Start** (Otevřít zařízení -> Spustit).

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Ověřte, že nástroj vizualizuje datové proudy jednotlivých snímačů:
   - Hloubková kamera
   - Barevná kamera
   - Infračervená kamera
   - IMU
   - Mikrofony

    ![Vizualizační nástroj](./media/quickstarts/visualization-tool.png)

Právě jste dokončili nastavení Azure Kinect DK. Teď můžete začít s vývojem aplikací nebo integrací služeb.

V případě jakýchkoli problémů si projděte téma [Řešení potíží](troubleshooting.md).

## <a name="see-also"></a>Viz také

- [Informace o hardwaru Azure Kinect DK](hardware-specification.md)
- [Aktualizace firmwaru zařízení](update-device-firmware.md)
- Další informace o aplikaci [Azure Kinect Viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Další kroky

Jakmile bude zařízení Azure Kinect DK připravené a funkční, můžete se seznámit také s následujícím postupem:
> [!div class="nextstepaction"]
> [Záznam datových proudů ze snímačů do souboru](record-sensor-streams-file.md)
