---
title: Speech Devices SDK Roobo Smart audio dev Kit v1 – Speech Service
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení práce se sadou Speech Devices SDK a Roobo Smart audio dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015286"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Zařízení: Roobo Smart audio dev Kit

Tento článek popisuje informace specifické pro zařízení pro sadu Roobo Smart audio dev Kit.

## <a name="set-up-the-development-kit"></a>Nastavení vývojové sady

1. Vývojová sada má dva konektory USB. Levý konektor je Power Kit a na obrázku níže se zvýrazní jako napájení. Napravo je jeho řízení a je označeno jako ladění v obrázku.

    ![Připojení k sadě dev SDK](media/speech-devices-sdk/qsg-1.png)

1. Pokud chcete připojit port napájení k počítači nebo adaptéru napájení, zapněte vývojovou sadu pomocí kabelu mikroUSB. Zelený indikátor napájení se na horní desce rozsvítí.

1. K řízení vývojové sady připojte port ladění k počítači pomocí druhého kabelu USB. Je nutné používat vysoce kvalitní kabel k zajištění spolehlivé komunikace.

1. Naorientujte svou vývojovou sadu buď pro kruhovou, nebo lineární konfiguraci.

    |Konfigurace sady Development Kit|Orientace|
    |-----------------------------|------------|
    |Kruh|Vzhůru, s mikrofony, které čelí stropu|
    |Lineární|Na své straně s mikrofony, na které jste byli připojeni (viz následující obrázek)|

    ![Orientace lineární sady dev Kit](media/speech-devices-sdk/qsg-2.png)

1. Nainstalujte certifikáty a nastavte oprávnění pro zvukové zařízení. V okně příkazového řádku zadejte následující příkazy:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Tyto příkazy používají Android Debug Bridge, `adb.exe` , který je součástí instalace Android Studio. Tento nástroj je umístěný v umístění C:\Users \[ User name] \AppData\Local\Android\Sdk\platform-Tools. Tento adresář můžete přidat do své cesty, abyste ho mohli lépe vyvolávat `adb` . V opačném případě je nutné zadat úplnou cestu k instalaci adb.exe v každém příkazu, který vyvolá `adb` .
    >
    > Pokud se zobrazí chyba, `no devices/emulators found` Zkontrolujte, zda je kabel USB připojen a je kabelem vysoké kvality. Můžete použít `adb devices` ke kontrole, jestli počítač může komunikovat s vývojovou sadou, protože vrátí seznam zařízení.
    >
    > [!TIP]
    > Ztlumení mikrofonu a mluvčího počítače, abyste měli jistotu, že pracujete s mikrotelefony na vývojové sadě. Tímto způsobem nebudete náhodně aktivovat zařízení pomocí zvuku z počítače.

1. Pokud chcete připojit mluvčího k sadě pro vývojáře, můžete ho připojit ke zvukovému řádku. Měli byste zvolit kvalitní mluvčí s analogovým konektorem 3,5 mm.

    ![Vysor zvuk](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informace o vývoji

Další informace o vývoji najdete v [příručce pro vývoj Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Zvuk

Roobo poskytuje nástroj, který zachycuje všechny zvuky do paměti Flash. Může pomoct při odstraňování problémů se zvukem. K dispozici je verze nástroje pro každou konfiguraci sady Development Kit. Na  [webu Roobo](http://ddk.roobo.com/)vyberte své zařízení a potom v dolní části stránky vyberte odkaz **nástroje Roobo** .

## <a name="next-steps"></a>Další kroky

* [Spuštění ukázkové aplikace pro Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)