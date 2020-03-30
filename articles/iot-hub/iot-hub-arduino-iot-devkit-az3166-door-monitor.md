---
title: Odeslání e-mailu při otevření dveří pomocí funkce Azure
description: Sledujte magnetický senzor, abyste zjistili, kdy se dveře otevřou, a pomocí funkce Azure odešlete e-mailové oznámení.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6bebe8ac6b9869466938600d6267fd0062c84477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977298"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitorování dveří – pomocí funkcí Azure a SendGrid, odeslat e-mail při otevření dveří           

MXChip IoT DevKit obsahuje vestavěný magnetický senzor. V tomto projektu zjistíte přítomnost nebo nepřítomnost blízkého silného magnetického pole - v tomto případě pocházejícího z malého, permanentního magnetu.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se dozvíte:
- Jak používat magnetický senzor MXChip IoT DevKit k detekci pohybu blízkého magnetu.
- Jak pomocí služby SendGrid odeslat oznámení na vaši e-mailovou adresu.

> [!NOTE]
> Pro praktické využití tohoto projektu proveďte následující úkoly:
> - Namontujte magnet na okraj dveří.
> - DevKit namontujte na zárubně dveří v blízkosti magnetu. Otevřením nebo zavřením dveří se spustí senzor, což vede k tomu, že obdržíte e-mailové oznámení o události.

## <a name="what-you-need"></a>Co potřebujete

Dokončete [příručku Začínáme](iot-hub-arduino-iot-devkit-az3166-get-started.md) s tímto účelem:

* Připojení devKitu k Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud ho nemáte, můžete se zaregistrovat jednou z těchto metod:

* Aktivujte [bezplatnou 30denní zkušební verzi účtu Microsoft Azure](https://azure.microsoft.com/free/).
* Nárokujte svůj [kredit Azure,](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) pokud jste předplatitelem MSDN nebo Visual Studia.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Nasazení služby SendGrid v Azure

[SendGrid](https://sendgrid.com/) je cloudová platforma pro doručování e-mailů. Tato služba bude použita k odesílání e-mailových oznámení.

> [!NOTE]
> Pokud jste už nasadili službu SendGrid, můžete přejít přímo do [nasazení služby IoT Hub v Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Nasazení sendgridu

Chcete-li zřídit služby Azure, použijte tlačítko **Nasadit do Azure.** Toto tlačítko umožňuje rychlé a snadné nasazení open source projektů do Microsoft Azure.

Klikněte na tlačítko **Nasadit do Azure** níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Pokud ještě nejste přihlášení ke svému účtu Azure, přihlaste se teď. 

Nyní se zobrazí registrační formulář SendGrid.

![Nasazení sendgridu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Vyplňte registrační formulář:

   * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid nebo použijte existující. Viz [Správa prostředků Azure pomocí skupin prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Název**: Název služby SendGrid. Zvolte jedinečný název, který se liší od ostatních služeb, které můžete mít.

   * **Heslo**: Služba vyžaduje heslo, které nebude použito pro nic v tomto projektu.

   * **E-mail**: Služba SendGrid odešle ověření na tuto e-mailovou adresu.

Zaškrtněte možnost **Připnout na řídicí panel,** abyste tuto aplikaci v budoucnu snadněji našli, a kliknutím na **Koupit** odešlete přihlašovací formulář.
 
### <a name="sendgrid-api-key-creation"></a>Vytvoření klíče rozhraní API SendGrid

Po dokončení nasazení na něj klikněte a potom klikněte na tlačítko **Spravovat.** Zobrazí se stránka účtu SendGrid, kde je třeba ověřit svou e-mailovou adresu.

![Správa sítě SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stránce SendGrid klikněte na **Settings** > **API Keys** > **Create API Key**.

![SendGrid vytvořit rozhraní API jako první](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na stránce **Vytvořit klíč rozhraní API** zadejte název klíče rozhraní **API** a klepněte na tlačítko Vytvořit **& zobrazení**.

![SendGrid Vytvořit rozhraní API druhé](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klíč rozhraní API se zobrazí pouze jednou. Ujistěte se, že jej bezpečně zkopírujete a uložíte, protože se používá v dalším kroku.

## <a name="deploy-iot-hub-in-azure"></a>Nasazení služby IoT Hub v Azure

Následující kroky zřídí další služby související s Azure IoT a nasadí funkce Azure pro tento projekt.

Klikněte na tlačítko **Nasadit do Azure** níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Zobrazí se registrační formulář.

![Nasazení IoTHubu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Vyplňte pole v registračním formuláři.

   * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid nebo použijte existující. Viz [Správa prostředků Azure pomocí skupin prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Název aplikace Iot Hub:** Název vašeho centra IoT hub. Zvolte jedinečný název, který se liší od ostatních služeb, které můžete mít.

   * **Iot Hub Sku**: F1 (omezeno na jedno předplatné) je zdarma. Další informace o cenách naleznete na [stránce s cenami](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Z e-mailu**: Toto pole by mělo být stejné e-mailové adresy, kterou jste použili při nastavování služby SendGrid.

Zaškrtněte možnost **Připnout na řídicí panel,** abyste tuto aplikaci v budoucnu snadněji našli, a až budete připraveni pokračovat v dalším kroku, klikněte na **Koupit.**
 
## <a name="build-and-upload-the-code"></a>Sestavení a nahrání kódu

Dále načtěte ukázkový kód v kódu VS a zřídíte potřebné služby Azure.

### <a name="start-vs-code"></a>Spustit vs kód

- Zkontrolujte, zda **není** devKit připojen k počítači.
- Spusťte VS Code.
- Připojte devKit k počítači.

> [!NOTE]
> Při spuštění VS kód, může se zobrazit chybová zpráva oznamující, že nemůže najít Arduino IDE nebo související palubě balíčku. Pokud se zobrazí tato chyba, zavřete Kód VS, spusťte Arduino IDE znovu a VS kód by měl najít Arduino IDE cestu správně.

### <a name="open-arduino-examples-folder"></a>Otevřít složku Příklady Arduina

Rozbalte levou stranu **ARDUINO EXAMPLES** sekce, přejděte na **příklady pro MXCHIP AZ3166 > AzureIoT**, a vyberte **DoorMonitor**. Tato akce otevře nové okno VS Kód se složkou projektu v něm.

![příklady mini řešení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Ukázkovou aplikaci můžete také otevřít z palety příkazů. Pomocí `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a pak najděte a vyberte **Arduino: Příklady**.

### <a name="provision-azure-services"></a>Poskytování služeb Azure

V okně řešení spusťte úlohu zřizování cloudu:
- Typ `Ctrl+P` (macOS: `Cmd+P`).
- Zadejte `task cloud-provision` do dodaných textových pole.

V terminálu VS Code vás interaktivní příkazový řádek provede zřízením požadovaných služeb Azure. Vyberte všechny stejné položky ze seznamu s výzvami, který jste dříve zřídit v [nasazení služby IoT Hub v Azure](#deploy-iot-hub-in-azure).

![Poskytování cloudu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Pokud se stránka zasekne ve stavu načítání při pokusu o přihlášení k Azure, najdete v [části "stránka zablokuje při přihlášení" části IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) k vyřešení tohoto problému. 

### <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

Dále nahrajte kód zařízení.

#### <a name="windows"></a>Windows

1. Slouží `Ctrl+P` ke `task device-upload`spuštění .

2. Terminál vás vyzve k přepání konfiguračního režimu. Chcete-li tak učinit, podržte tlačítko A a stiskněte a uvolněte tlačítko reset. Na obrazovce se zobrazí identifikační číslo DevKit a slovo *Konfigurace*.

#### <a name="macos"></a>macOS

1. Přepněte DevKit do konfiguračního režimu: Podržte tlačítko A a stiskněte a uvolněte tlačítko reset. Na obrazovce se zobrazí "Konfigurace".

2. Klepnutím `Cmd+P` spusťte položku `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Ověření, nahrání a spuštění ukázkové aplikace

Připojovací řetězec, který se načte z kroku [Zřízení služeb Azure](#provision-azure-services) je nyní nastavena. 

VS Kód pak začne ověřovat a nahrávat arduino skicu devKit.

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> V některých případě se může zobrazit chybová zpráva "Chyba: AZ3166: Neznámý balíček" chybová zpráva. K této chybě dochází, pokud index balíčku desky není správně aktualizován. Chcete-li tuto chybu vyřešit, naleznete v [části vývoje v nejčastějších dotazech k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Program se poprvé inicializuje, když je DevKit v přítomnosti stabilního magnetického pole.

Po inicializaci se `Door closed` zobrazí na obrazovce. Dojde-li ke změně magnetického pole, `Door opened`stav se změní na . Pokaždé, když se změní stav dveří, obdržíte e-mailové oznámení. (Přijetí těchto e-mailových zpráv může trvat až pět minut.)

![Magnety v blízkosti senzoru: Dveře zavřené](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnety v blízkosti senzoru: Dveře zavřené")

![Magnet se odklonil od senzoru: Dveře se otevřely](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet se odklonil od senzoru: Dveře se otevřely")

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Naučili jste se připojit zařízení DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT a k odeslání e-mailu použili službu SendGrid. Zde jsou navrhované další kroky:

* [Přehled akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení zařízení MXChip IoT DevKit k aplikaci Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
