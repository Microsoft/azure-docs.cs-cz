---
title: Resetování Azure Kinect DK
description: Popisuje postup při resetování zařízení Azure Kinect DK do továrního nastavení.
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reset
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201894"
---
# <a name="reset-azure-kinect-dk"></a>Resetování Azure Kinect DK

Může nastat situace, kdy bude potřeba resetovat Azure Kinect DK zpět do továrního nastavení (například v případě nesprávné instalace aktualizace firmwaru).

1. Vypněte Azure Kinect DK. Uděláte to tak, že odpojíte kabel USB a napájecí kabel.
  ![Diagram znázorňující umístění šroubku, který zakrývá tlačítko pro resetování](media/reset-azure-kinect-dk-diagram.png)
1. Tlačítko pro resetování najdete tak, že odšroubujete šroubek, který se nachází v zámku pro připojení stativu.
1. Znovu připojte napájecí kabel.
1. Vložte konec rozložené kancelářské sponky do prázdného otvoru po šroubku v zámku pro připojení stativu.
1. Pomocí kancelářské sponky jemně stiskněte a podržte tlačítko pro resetování.
1. Zatímco budete držet tlačítko pro resetování, znovu připojte kabel USB.
1. Přibližně za 3 sekundy se světlo indikátoru napájení změní na žlutohnědé. Jakmile se světlo změní, uvolněte tlačítko pro resetování.  
   
   Po uvolnění tlačítka pro resetování světlo indikátoru napájení bude blikat bíle a žlutohnědě, zatímco se zařízení bude resetovat. 
1. Počkejte, až bude světlo indikátoru napájení čistě bílé.
1. Vraťte šroubek nad tlačítko pro resetování v zámku pro připojení stativu.
1. Pomocí aplikace Azure Kinect Viewer ověřte, že se firmware resetoval. Provedete to tak, že spustíte aplikaci [Azure Kinect Viewer](azure-kinect-viewer.md) a vyberete **Device firmware version info** (Informace o verzi firmwaru zařízení). Zobrazí se verze firmwaru nainstalovaná na vašem zařízení Azure Kinect DK.

Vždy se ujistěte, že na zařízení máte nainstalovaný nejnovější firmware. Nejnovější verzi firmwaru můžete získat pomocí nástroje Azure Kinect Firmware Tool. Další informace o tom, jak zkontrolovat stav firmwaru, najdete v části [Kontrola verze firmwaru zařízení](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Související témata

- [Informace o Azure Kinect DK](about-azure-kinect-dk.md)
- [Nastavení Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Specifikace hardwaru Azure Kinect DK: Provozní prostředí](hardware-specification.md#operating-environment)
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Synchronizace mezi několika zařízeními Azure Kinect DK](multi-camera-sync.md)
