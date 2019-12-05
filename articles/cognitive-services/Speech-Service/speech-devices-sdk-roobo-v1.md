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
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815589"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Zařízení: Roobo Smart audio dev Kit

Tento článek popisuje informace specifické pro zařízení pro sadu Roobo Smart audio dev Kit.

## <a name="set-up-the-development-kit"></a>Nastavení sady development kit

1. Vývojová sada má dva konektory USB. Levý konektor je Power Kit a na obrázku níže se zvýrazní jako napájení. Napravo je jeho řízení a je označeno jako ladění v obrázku.

    ![připojení dev kit](media/speech-devices-sdk/qsg-1.png)

1. Pokud chcete připojit port napájení k počítači nebo adaptéru napájení, zapněte vývojovou sadu pomocí kabelu mikroUSB. Zelený indikátor napájení se na horní desce rozsvítí.

1. K řízení vývojové sady připojte port ladění k počítači pomocí druhého kabelu USB. Je nutné používat vysoce kvalitní kabel k zajištění spolehlivé komunikace.

1. Orientovat vývojové sady pro buď zacyklená nebo lineární konfigurace.

    |Konfigurace Development kit|Orientace|
    |-----------------------------|------------|
    |Cyklické|Sloupku s mikrofonů, kterým čelí horní mez|
    |Lineární|Na své straně s mikrofonů směrem k vám (viz následující obrázek)|

    ![orientace lineární dev kit](media/speech-devices-sdk/qsg-2.png)

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
    > Tyto příkazy používají Android Debug Bridge, `adb.exe`, který je součástí instalace sady Android Studio. Tento nástroj se nachází v C:\Users\[uživatelské jméno] \AppData\Local\Android\Sdk\platform nástroje. Tento adresář můžete přidat do cesty k němu pohodlnější, který má být vyvolán `adb`. V opačném případě musíte zadat úplnou cestu k instalaci adb.exe v každý příkaz, který vyvolá `adb`.
    >
    > Pokud se zobrazí chyba `no devices/emulators found` pak zkontrolujte, zda je kabel USB připojen a je kabelem s vysokým kvalitou. Pomocí `adb devices` můžete ověřit, že váš počítač může komunikovat s vývojovou sadou, protože vrátí seznam zařízení.
    >
    > [!TIP]
    > Ztlumte mikrofonu a reproduktorů Ujistěte se, že pracujete s mikrofonů development kit v počítači. Díky tomu nebude aktivujete omylem zařízení se zvukem z počítače.

1. Pokud chcete připojit mluvčího k sadě pro vývojáře, můžete ho připojit ke zvukovému řádku. Měli byste zvolit kvalitní mluvčí s analogovým konektorem 3,5 mm.

    ![Vysor zvuku](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informace o vývoji

Další informace o vývoji najdete v [příručce pro vývoj Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Zvuk

Roobo poskytuje nástroj, který zachycuje všechny zvuky do paměti Flash. Občas může pomoci při odstraňování problémů zvuku. Verze nástroje se poskytuje pro každou konfiguraci development kit. Na [webu Roobo](https://ddk.roobo.com/)vyberte své zařízení a potom v dolní části stránky vyberte odkaz **nástroje Roobo** .

## <a name="next-steps"></a>Další kroky

* [Spuštění ukázkové aplikace pro Android](speech-devices-sdk-android-quickstart.md)
