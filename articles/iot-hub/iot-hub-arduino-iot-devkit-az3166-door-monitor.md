---
title: Odeslat e-mail, když se otevřou dvířka pomocí Azure Functions
description: Monitorujte magnetické senzory a zjistěte, kdy se dvířka otevřou, a použijte Azure Functions k odeslání e-mailového oznámení.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: d4f77cec793e13ff943c173d88948eb5b5de0957
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91356243"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitorování dvířek – používání Azure Functions a SendGrid, odesílání e-mailů, když se otevřou dvířka           

MXChip IoT DevKit obsahuje vestavěný magnetický senzor. V tomto projektu zjistíte přítomnost nebo absence nejbližšího magnetického magnetického pole – v tomto případě přichází od malého a trvalého magnetu.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se naučíte:
- Jak pomocí magnetického senzoru MXChip IoT DevKit detekovat pohyb okolního magnetu.
- Jak používat službu SendGrid k odeslání oznámení na vaši e-mailovou adresu.

> [!NOTE]
> Pro praktické použití tohoto projektu proveďte následující úlohy:
> - Připojte magnet k okraji dvířek.
> - Připojte DevKit na dvířka jamb blízko k magnetu. Otevřením nebo zavřením dvířek se spustí senzor a výsledkem bude příjem e-mailových oznámení o události.

## <a name="what-you-need"></a>Co budete potřebovat

Dokončete [průvodce Začínáme](iot-hub-arduino-iot-devkit-az3166-get-started.md) :

* Připojení DevKit k Wi-Fi
* Příprava vývojového prostředí

Musíte mít aktivní předplatné Azure. Pokud ho nemáte, můžete se zaregistrovat pomocí jedné z těchto metod:

* Aktivujte si [bezplatný 30denní zkušební účet Microsoft Azure](https://azure.microsoft.com/free/).
* Pokud jste předplatitelem MSDN nebo Visual studia, můžete svůj [kredit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) uplatnit.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Nasazení služby SendGrid v Azure

[SendGrid](https://sendgrid.com/) je cloudová platforma pro doručování e-mailů. Tato služba se použije k posílání e-mailových oznámení.

> [!NOTE]
> Pokud jste už nasadili službu SendGrid, můžete přejít přímo na [nasazení IoT Hub v Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Nasazení SendGrid

Ke zřízení služeb Azure použijte tlačítko **nasadit do Azure** . Toto tlačítko umožňuje rychlé a snadné nasazení open source projektů pro Microsoft Azure.

Klikněte na tlačítko **nasadit do Azure** níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Pokud ještě nejste přihlášení ke svému účtu Azure, přihlaste se hned. 

Nyní se zobrazí formulář pro registraci SendGrid.

![Nasazení SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Vyplňte formulář pro registraci:

   * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid nebo použijte existující. Další informace najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Name**(název): název vaší služby SendGrid. Vyberte jedinečný název, který se liší od jiných služeb, které máte.

   * **Heslo**: služba vyžaduje heslo, které se nebude používat pro cokoli v tomto projektu.

   * **E-mail**: služba SendGrid bude odesílat ověření na tuto e-mailovou adresu.

Zaškrtněte možnost **Připnout na řídicí panel** , aby se tato aplikace v budoucnu zjednodušila, a pak kliknutím na **koupit** odešlete formulář pro přihlášení.
 
### <a name="sendgrid-api-key-creation"></a>Vytvoření klíče rozhraní API SendGrid

Po dokončení nasazení klikněte na něj a potom klikněte na tlačítko **Spravovat** . Zobrazí se stránka účet SendGrid, ve které budete potřebovat ověřit svou e-mailovou adresu.

![Správa SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stránce SendGrid klikněte na **Nastavení**  >  **klíče rozhraní API**  >  **vytvořit klíč rozhraní API**.

![Nejdřív SendGrid vytvořit rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na stránce **vytvořit klíč rozhraní** API zadejte **název klíče rozhraní API** a klikněte na **vytvořit & zobrazení**.

![SendGrid vytvoření rozhraní API sekundu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klíč rozhraní API se zobrazuje jenom jednou. Nezapomeňte je zkopírovat a bezpečně uložit, jak se používá v dalším kroku.

## <a name="deploy-iot-hub-in-azure"></a>Nasazení IoT Hub v Azure

Následující kroky zřídí další služby související se službou Azure IoT a nasadí Azure Functions pro tento projekt.

Klikněte na tlačítko **nasadit do Azure** níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Zobrazí se formulář pro registraci.

![Nasazení IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Vyplňte pole ve formuláři pro registraci.

   * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid nebo použijte existující. Další informace najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Název centra IoT**: název vašeho centra IoT. Vyberte jedinečný název, který se liší od jiných služeb, které máte.

   * **SKU centra IoT**: F1 (omezeno na jeden pro každé předplatné) je zdarma. Další informace o cenách najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Z e-mailu**: Toto pole musí být stejná e-mailová adresa, kterou jste použili při nastavování služby SendGrid.

Zaškrtněte možnost **Připnout na řídicí panel** , aby se tato aplikace v budoucnu zjednodušila a pak klikněte na **koupit** , až budete připraveni pokračovat k dalšímu kroku.
 
## <a name="build-and-upload-the-code"></a>Sestavení a nahrání kódu

Dále načtěte ukázkový kód v VS Code a zřiďte nezbytné služby Azure.

### <a name="start-vs-code"></a>Spustit VS Code

- Ujistěte se, že vaše DevKit **není připojené k** vašemu počítači.
- Spusťte VS Code.
- Připojte DevKit k počítači.

> [!NOTE]
> Při spuštění VS Code se může zobrazit chybová zpráva s oznámením, že nemůže najít Arduino IDE nebo balíček související desky. Pokud se zobrazí tato chyba, zavřete VS Code, znovu spusťte IDE Arduino a VS Code by měla správně najít cestu IDE Arduino.

### <a name="open-arduino-examples-folder"></a>Otevřít složku příkladů Arduino

Rozbalte část s **Příklady Arduino** na levé straně, přejděte k **příkladům pro MXCHIP AZ3166 > AzureIoT**a vyberte **DoorMonitor**. Tato akce otevře nové okno VS Code se složkou projektu v něm.

![zkrácené řešení – příklady](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Ukázkovou aplikaci můžete otevřít také z palety příkazů. Použijte `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) k otevření palety příkazů, zadejte **Arduino**a pak vyhledejte a vyberte **Arduino: příklady**.

### <a name="provision-azure-services"></a>Zřizování služeb Azure

V okně řešení spusťte úlohu zřízení cloudu:
- Typ `Ctrl+P` (MacOS: `Cmd+P` ).
- `task cloud-provision`Do zadaného textového pole zadejte.

V terminálu VS Code vás interaktivní příkazový řádek provede zřízením požadovaných služeb Azure. Vyberte všechny stejné položky ze seznamu výzev, které jste dříve zřídili v [nasazení IoT Hub v Azure](#deploy-iot-hub-in-azure).

![Zřízení cloudu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Pokud se stránka při pokusu o přihlášení k Azure přestane načítat, přečtěte si část Nejčastější dotazy k řešení tohoto problému v [části "stránka se zablokuje, když se přihlašujete" v článku věnovaném přihlášení v DevKit IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) . 

### <a name="build-and-upload-the-device-code"></a>Sestavení a nahrání kódu zařízení

V dalším kroku nahrajte kód pro zařízení.

#### <a name="windows"></a>Windows

1. Použijte `Ctrl+P` ke spuštění `task device-upload` .

2. Terminál vás vyzve k zadání režimu konfigurace. Provedete to tak, že stisknete tlačítko a, nahrajete a uvolníte tlačítko obnovit. Na obrazovce se zobrazí identifikační číslo DevKit a *Konfigurace*aplikace Word.

#### <a name="macos"></a>macOS

1. Vložte DevKit do režimu konfigurace: podržte stisknuté tlačítko a a pak nasaďte a uvolněte tlačítko Resetovat. Obrazovka zobrazí ' Configuration '.

2. Kliknutím `Cmd+P` spustíte `task device-upload` .

#### <a name="verify-upload-and-run-the-sample-app"></a>Ověřte, nahrajte a spusťte ukázkovou aplikaci.

Připojovací řetězec, který se načte z kroku [zřízení služeb Azure](#provision-azure-services) , je teď nastavený. 

VS Code potom spustí ověřování a nahrání Arduino náčrtu do DevKit.

![Snímek obrazovky ukazuje Visual Studio Code ověřování a nahrávání náčrtu Arduino.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit se restartuje a začne používat kód.

> [!NOTE]
> V některých případech se může zobrazit chybová zpráva "Chyba: AZ3166: neznámý balíček". K této chybě dochází, pokud se index balíčku panelu není správně aktualizovat. Pokud chcete tuto chybu vyřešit, přečtěte si [část věnované vývoji v tématu Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Program se nejprve inicializuje, když je DevKit v přítomnosti stabilního magnetického pole.

Po inicializaci `Door closed` se na obrazovce zobrazí. Když dojde ke změně v magnetickém poli, stav se změní na `Door opened` . Pokaždé, když se změní stav dvířek, obdržíte e-mailové oznámení. (Tyto e-mailové zprávy můžou trvat až pět minut.)

![Magnety blízko senzoru: dvířka zavřena](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnety blízko senzoru: dvířka zavřena")

![Magnet se přesunul mimo senzor: otevřená dvířka](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet se přesunul mimo senzor: otevřená dvířka")

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, přečtěte si [Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak připojit zařízení DevKit k akcelerátoru řešení vzdáleného monitorování Azure IoT a použít službu SendGrid k odeslání e-mailu. Tady je doporučený další krok:[Přehled akcelerátoru řešení Azure IoT Remote Monitoring](https://docs.microsoft.com/azure/iot-suite/) .
