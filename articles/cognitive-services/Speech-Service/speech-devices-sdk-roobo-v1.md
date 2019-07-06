---
title: Rozpoznávání řeči zařízení SDK Roobo Smart zvuku Dev Kit v1 – hlasové služby
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení práce s zařízení sadou SDK pro řeč, Roobo inteligentní zvuk Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e4aba26238a0d87c8e708ae27c7b2dbdb73f16ab
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604837"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Zařízení: Roobo Smart zvuku Dev Kit

Tento článek obsahuje konkrétní informace o zařízení pro Roobo inteligentní zvuk Dev Kit.

## <a name="set-up-the-development-kit"></a>Nastavení sady development kit

1. Vývojová sada obsahuje dva konektory micro USB. Je vlevo konektor k sadě pro vývoj a je zvýrazněn jako napájení na následujícím obrázku. Vpravo je ovládat ji a je označen ladění na obrázku.

    ![připojení dev kit](media/speech-devices-sdk/qsg-1.png)

1. Vývojová sada Power pomocí kabelu USB micro pro připojení k počítači port, který power nebo napájení adaptér. Indikátor zelené power bude zprovoznění v horním panelu.

1. K řízení development kit, připojení k počítači pomocí druhého micro USB kabelu portu ladění. Je nutné pomocí kabelu vysoce kvalitní zajistit spolehlivou komunikaci.

1. Orientovat vývojové sady pro buď zacyklená nebo lineární konfigurace.

    |Konfigurace Development kit|Orientace|
    |-----------------------------|------------|
    |Cyklické|Sloupku s mikrofonů, kterým čelí horní mez|
    |Lineární|Na své straně s mikrofonů směrem k vám (viz následující obrázek)|

    ![orientace lineární dev kit](media/speech-devices-sdk/qsg-2.png)

1. Instalace certifikátů a nastavení oprávnění zvukové zařízení. V okně příkazového řádku zadejte následující příkazy:

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
    > Pokud se zobrazí chyba `no devices/emulators found` zkontrolujte USB kabelu je připojený a vysoce kvalitní kabel. Můžete použít `adb devices` ke kontrole, že počítač může kontaktovat pracovníka development Kit jako vrátí seznam zařízení.
    >
    > [!TIP]
    > Ztlumte mikrofonu a reproduktorů Ujistěte se, že pracujete s mikrofonů development kit v počítači. Díky tomu nebude aktivujete omylem zařízení se zvukem z počítače.

1. Pokud se chcete připojit mluvčího dev Kit, můžete ho připojíte k zvukového výstupu. Měli byste zvolit kvalitní mluvčího s analogové moduly 3,5 mm.

    ![Vysor zvuku](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informace o vývoj

Další informace o vývoj, naleznete v tématu [Příručka pro vývojáře v Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Zvuk

Roobo poskytuje nástroj, který zachycuje všechny zvuk na flash paměti. Občas může pomoci při odstraňování problémů zvuku. Verze nástroje se poskytuje pro každou konfiguraci development kit. Na [Roobo lokality](https://ddk.roobo.com/), vyberte zařízení a pak vyberte **Roobo nástroje** odkaz v dolní části stránky.

## <a name="next-steps"></a>Další postup

* [Spuštění aplikace ukázka pro Android](speech-devices-sdk-android-quickstart.md)
