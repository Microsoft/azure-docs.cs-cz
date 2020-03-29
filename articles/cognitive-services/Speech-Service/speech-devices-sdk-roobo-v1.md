---
title: Řečová zařízení SDK Roobo Smart Audio Dev Kit v1 - Řečová služba
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení s sadou Speech Devices SDK, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815589"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Zařízení: Roobo Smart Audio Dev Kit

Tento článek obsahuje specifické informace pro roobo Smart Audio Dev Kit.

## <a name="set-up-the-development-kit"></a>Nastavení vývojové sady

1. Vývojová sada má dva micro USB konektory. Levý konektor je pro napájení vývojové sady a je zvýrazněn jako Napájení na obrázku níže. Ten správný je ovládat, a je označen ladění v obraze.

    ![Připojení sady pro dev](media/speech-devices-sdk/qsg-1.png)

1. Napájet vývojovou soupravu pomocí kabelu micro USB pro připojení napájecího portu k počítači nebo napájecímu adaptéru. Pod horní deskou se rozsvítí zelený indikátor napájení.

1. Chcete-li ovládat vývojovou sadu, připojte ladicí port k počítači pomocí druhého kabelu micro USB. Pro zajištění spolehlivé komunikace je nezbytné použít vysoce kvalitní kabel.

1. Orientujte svou vývojovou soupravu pro kruhovou nebo lineární konfiguraci.

    |Konfigurace vývojové sady|Orientace|
    |-----------------------------|------------|
    |Kruhové|Vzpřímené poloze, s mikrofony směřujícími ke stropu|
    |Lineární|Na boku, s mikrofony směřujícími k vám (zobrazeno na následujícím obrázku)|

    ![Lineární orientace sady pro dev](media/speech-devices-sdk/qsg-2.png)

1. Nainstalujte certifikáty a nastavte oprávnění zvukového zařízení. Do okna příkazového řádku zadejte následující příkazy:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Tyto příkazy používají most ladění `adb.exe`androida , který je součástí instalace sady Android Studio. Tento nástroj je umístěn v\[c:\Users uživatelské jméno]\AppData\Local\Android\Sdk\platform-tools. Tento adresář můžete přidat do cesty, aby `adb`bylo vhodnější vyvolat . V opačném případě je nutné zadat úplnou cestu k instalaci programu `adb`adb.exe v každém příkazu, který vyvolá .
    >
    > Pokud se zobrazí `no devices/emulators found` chyba, zkontrolujte, zda je kabel USB připojen a je vysoce kvalitní kabel. Můžete zkontrolovat, `adb devices` zda váš počítač může mluvit s vývojovou sadou, protože vrátí seznam zařízení.
    >
    > [!TIP]
    > Ztlumit mikrofon a reproduktor počítače, abyste měli jistotu, že pracujete s mikrofony vývojové sady. Tímto způsobem nebudete náhodně spouštět zařízení se zvukem z počítače.

1. Chcete-li k dev kitu připojit reproduktor, můžete jej připojit k odchozí muzice zvuku. Měli byste si vybrat kvalitní reproduktor s 3,5mm analogovým konektorem.

    ![Zvuk Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informace o vývoji

Další informace o vývoji naleznete v [průvodci vývojem Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Zvuk

Roobo poskytuje nástroj, který zachycuje veškerý zvuk pro flash paměti. Mohlo by vám to pomoci při řešení problémů se zvukem. Pro každou konfiguraci vývojové sady je k dispozici verze nástroje. Na [webu Roobo](https://ddk.roobo.com/)vyberte zařízení a pak vyberte odkaz **Roobo Tools** v dolní části stránky.

## <a name="next-steps"></a>Další kroky

* [Spuštění ukázkové aplikace pro Android](speech-devices-sdk-android-quickstart.md)
