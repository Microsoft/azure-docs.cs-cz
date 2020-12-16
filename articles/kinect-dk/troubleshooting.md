---
title: Známé problémy s Azure Kinect a řešení potíží
description: Přečtěte si o některých známých problémech a tipůch k odstraňování potíží při používání sady snímač SDK se službou Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: řešení potíží, aktualizace, chyba, Kinect, zpětná vazba, obnovení, protokolování, tipy
ms.openlocfilehash: a6e00b6c5e9e4f82bb668769aade8311896bef32
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587277"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Známé problémy s Azure Kinect a řešení potíží

Tato stránka obsahuje známé problémy a tipy pro řešení potíží při používání sady snímač SDK se službou Azure Kinect DK. Problémy specifické pro produktovou hardwarovou podporu viz také [stránky podpory produktu](./index.yml) .

## <a name="known-issues"></a>Známé problémy

- Problémy s kompatibilitou s ASMedia hostitelskými řadiči USB (například ASM1142 chipset)
  - V některých případech použití ovladače Microsoft USB může odblokovat
  - Mnoho počítačů má také alternativní hostitelské řadiče a změnu portu USB3 může pomáhat

Další problémy související s sadou snímač SDK najdete v [otázkách GitHubu](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues) .

## <a name="collecting-logs"></a>Shromažďování protokolů

Protokolování pro K4A.dll je povoleno prostřednictvím proměnných prostředí. Ve výchozím nastavení je protokolování odesláno do stdout a jsou generovány pouze chyby a kritické zprávy. Tato nastavení je možné změnit, aby protokolování do souboru přešel. Podrobnosti je také možné podle potřeby upravit. Níže je příklad, jak v systému Windows povolit protokolování do souboru s názvem k4a. log a zachytí upozornění a zprávy vyšší úrovně.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Scénář spuštění z příkazového řádku (například spuštění prohlížeče)
4. Přejděte do souboru k4a. log a sdílejte soubor.

Další informace najdete v části Klipart ze souboru hlaviček:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

Protokolování K4ABT.dll sady SDK pro sledování textu je podobné s tím rozdílem, že uživatelé by měli upravit jinou sadu názvů proměnných prostředí:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Zařízení nezobrazuje výčet ve Správci zařízení.

- Ověřte, že je stav LED za zařízením, pokud je blikající žlutě, ke které máte problémy s připojením USB, a nezíská dostatek energie. Kabel napájecího napájení by měl být připojen k poskytovanému adaptéru napájení. I když je napájecí kabel připojen k síti typu USB, vyžaduje, aby zařízení poskytovalo vyšší výkon, než může port USB počítače. Nepřipojujte se k němu na portu počítače nebo na rozbočovači USB.
- Ověřte, zda je připojen napájecí kabel a zda je k dispozici použití USB3 portu pro data.
- Zkuste změnit port USB3 pro datové připojení (doporučení pro použití portu USB blízko do základní desky, například na zadní straně počítače).
- Ověřte, jestli váš kabel, poškozené nebo nižší kabely kvality můžou způsobit nespolehlivý výčet (zařízení ve Správci zařízení udržuje blikání).
- Pokud jste se připojili k přenosnému počítači a běží na baterii, může to omezovat napájení portu.
- Restartujte hostitelský počítač.
- Pokud potíže potrvají, může dojít k potížím s kompatibilitou.
- Pokud při aktualizaci firmwaru došlo k chybě a zařízení se neobnovilo sám sebou, proveďte [obnovení továrního nastavení](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Nepodařilo se otevřít Azure Kinect Viewer

- Nejdřív ověřte, že se vaše zařízení zobrazuje ve Windows Device Manager.

    ![Kamery Azure Kinect ve Správci zařízení ve Windows](./media/resources/viewer-fails.png)

- Ověřte, zda se v zařízení nepoužívá žádná jiná aplikace (například aplikace kamery Windows). Přístup k zařízení má jenom jedna aplikace současně.
- V protokolu k4aviewer. err vyhledejte chybové zprávy.
- Otevřete aplikaci Windows kamera a ověřte, že funguje.
- Zařízení Power Cycle před použitím zařízení vyčkejte, než se vypíná.
- Restartujte hostitelský počítač.
- Ujistěte se, že na svém počítači používáte nejnovější grafické ovladače.
- Pokud používáte vlastní sestavení sady SDK, zkuste použít oficiální vydanou verzi, pokud to problém vyřeší.
- Pokud potíže potrvají, [Shromážděte protokoly](troubleshooting.md#collecting-logs) a názory na soubor.

## <a name="cannot-find-microphone"></a>Nejde najít mikrofon.

- Nejprve si nejdříve zajistěte, aby se v Device Manager vyčíslení pole mikrofonu.
- Pokud je zařízení ve výčtu a funguje jinak v systému Windows, může to být tím, že po aktualizaci firmwaru Windows přiřadí jiné ID kontejneru k hloubce kamery.
- Můžete ji zkusit resetovat tak, že přejdete na Device Manager, kliknete pravým tlačítkem na "Azure Kinect Microphone Array" a vyberte odinstalovat zařízení. Až to bude hotové, odpojte a znovu připojte senzor.

    ![Pole Kinect pro mikrofon Azure](./media/resources/mic-not-found.png)

- Po restartování Azure Kinect Viewer a zkuste to znovu.

## <a name="device-firmware-update-issues"></a>Problémy aktualizace firmwaru zařízení

- Pokud po aktualizaci není hlášeno správné číslo verze, může být nutné zařízení vypínat z elektrického proudu.
- Pokud dojde k přerušení aktualizace firmwaru, může dojít k chybnému stavu a výčet se nezdařil. Odpojte zařízení a znovu ho připojte a počkejte 60 sekund, abyste viděli, jestli se dá obnovit.
Pokud to neprovede, [resetování továrního nastavení](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Problémy s kvalitou obrazu

- Spusťte [Azure Kinect Viewer](azure-kinect-viewer.md) a ověřte, jestli je zařízení zablokované, nebo jestli je senzor zablokovaný nebo je objektiv nečistý.
- Pokud se problém děje v určitém režimu, zkuste použít jiné operační režimy.
- Pokud chcete sdílet problémy s kvalitou obrazu s týmem, můžete:

1) Zobrazení pozastavení v [Azure Kinect Vieweru](azure-kinect-viewer.md) a pořídit snímek obrazovky nebo
2) Nahlaste se pomocí nahrávání [Azure Kinect](azure-kinect-recorder.md), třeba `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Nekonzistentní nebo neočekávaná časová razítka zařízení

Volání ```k4a_device_set_color_control``` může dočasně vyvolat změny časování v zařízení, které může trvat několik zachycení. Vyhněte se volání rozhraní API ve smyčce zachycení imagí, abyste se vyhnuli obnovení interního výpočtu časování u každého nového obrázku. Místo toho volejte rozhraní API před spuštěním kamery nebo pouze v případě, že potřebujete změnit hodnotu v rámci smyčky zachycení obrázku. Zejména Vyhněte se volání ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>Kompatibilita s řadičem hostitele USB3

Pokud zařízení ve Správci zařízení nevytváří výčet, může být důvodem to, že je připojený k nepodporovanému USB3 řadiči. 

Pro Azure Kinect DK v **systému Windows** jsou technologie Intel, **Texas Instrumentation (ČŘ)** a **reneseas** *jedinými hostitelskými řadiči, které jsou podporovány*. Sada Azure Kinect SDK na platformách Windows spoléhá na jednotné ID kontejneru a musí zahrnovat zařízení USB 2,0 a 3,0, aby sada SDK mohla najít hloubkovou, barevnou a zvukovou zařízení, která jsou fyzicky umístěná na stejném zařízení. V systému Linux může být podporováno více řadičů hostitele, protože tato platforma spoléhá na ID kontejneru a další informace o sériových číslech zařízení. 

Téma hostitelských řadičů USB získá ještě složitější, když má počítač nainstalovaný víc než jeden hostitelský řadič. Pokud jsou řadiče hostitele smíšené, může uživatel zaznamenat problémy, když některé porty fungují správně a jiné nefungují vůbec. V závislosti na tom, jak jsou porty kabelové, se můžou zobrazit všechny klientské porty s problémy s Azure Kinect.

**Windows:** Chcete-li zjistit, jaký hostitelský řadič máte otevřený Device Manager

1. Zobrazení – > zařízení podle typu 
2. S připojenými fotoaparáty k Azure Kinect a vybranými kamerami – >Azure Kinect 4K
3. Zobrazit – > zařízení podle připojení

![Odstraňování potíží s porty USB](./media/resources/usb-troubleshooting.png)

Chcete-li lépe pochopit, který port USB je připojen v počítači, opakujte tento postup pro každý port USB při připojení Azure Kinect DK k různým portům USB v počítači.

## <a name="depth-camera-auto-powers-down"></a>Automatická pravomoc kamery hloubkové kamery

Laser používaný hloubkovou kamerou k výpočtu dat hloubky obrazu má omezené životnost. Aby se maximalizoval život Lasers, hloubka kamery detekuje, že se data hloubky nespotřebovávají. Hloubka kamery se vypne, když se zařízení streamuje několik minut, ale hostitelský počítač data nečte. Má také vliv na synchronizaci více zařízení, kde se podřízená zařízení spouštějí ve stavu, ve kterém je hloubka kamery streamování, a hloubkové snímky aktivně pomůžou počkat, až hlavní zařízení spustí synchronizaci zachycení. Aby se zabránilo tomuto problému ve scénářích zachycení více zařízení, zajistěte, aby se hlavní zařízení spouštělo do minuty prvního podřízeného. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Použití sady SDK pro sledování těla pomocí Unreal

Chcete-li použít sadu SDK pro sledování textu s Unreal, ujistěte se, že jste přidali `<SDK Installation Path>\tools` proměnnou prostředí `PATH` a zkopírovali `dnn_model_2_0.onnx` `cudnn64_7.dll` do `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Použití Azure Kinect na bezobslužném systému Linux

Modul pro hloubku Azure Kinect v systému Linux používá OpenGL. OpenGL vyžaduje instanci okna, která vyžaduje, aby monitorování bylo připojené k systému. Alternativní řešení pro tento problém:

1. Povolte automatické přihlašování pro uživatelský účet, který plánujete použít. Pokyny, jak povolit automatické přihlašování, najdete v [tomto](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) článku.
2. Vypněte systém, odpojte monitor a zapněte systém. Automatické přihlašování vynutí vytvoření relace x-serveru.
2. Připojení přes SSH a nastavení proměnné pro zobrazení ENV `export DISPLAY=:0`
3. Spusťte aplikaci Azure Kinect.

## <a name="missing-c-documentation"></a>Chybějící dokumentace jazyka C#

Dokumentace k sadě senzor SDK jazyka C# se nachází [zde](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html).

Dokumentace pro sledování těla sady SDK jazyka C# se nachází [zde](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html).

## <a name="next-steps"></a>Další kroky

[Další informace o podpoře](support.md)