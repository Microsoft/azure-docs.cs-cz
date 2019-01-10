---
title: Dostávat e-mailu při otevření dveří pomocí služby SendGrid a Azure Functions | Dokumentace Microsoftu
description: Monitorování magnetických senzorů a rozpoznat, kdy se otevře dveře k odesílání e-mailové oznámení pomocí služby Azure Functions.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 293c2325f57f04c3a77b0b2b8ec2ef30e8c912b8
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156234"
---
# <a name="door-monitor"></a>Dvířka knihovny monitorování          

MXChip IoT DevKit obsahuje integrované magnetických senzorů. V tomto projektu je zjistit přítomnost nebo nepřítomnost blízké silné magnetické pole – v takovém případě pocházející z malá, permanentním magnetem.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se dozvíte:
- Jak používat MXChip IoT DevKit magnetických senzorů k detekci pohybu blízké magnet.
- Jak používat službu SendGrid k odesílání oznámení e-mailovou adresu.

> [!NOTE]
> Praktické použití tohoto projektu proveďte následující úkoly:
> - Připojte magnet hrany dveří.
> - Připojení DevKit v jamb dveře blízko magnetu. Otevírání nebo zavírání dveří aktivují senzor, což vede k e-mailové oznámení události přijetí.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](iot-hub-arduino-iot-devkit-az3166-get-started.md) na:

* Mají vaše DevKit připojení k Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud nemáte, můžete zaregistrovat pomocí jedné z těchto metod:

* Aktivovat [bezplatné 30denní zkušební verze Microsoft Azure účet](https://azure.microsoft.com/free/).
* Deklarace identity vaší [kredit Azure ve výši](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Pokud jste předplatitelem MSDN nebo Visual Studio.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Nasazení služby SendGrid v Azure

[SendGrid](https://sendgrid.com/) je platforma pro doručení e-mailu založené na cloudu. Tato služba se používá k odesílání e-mailová oznámení.

> [!NOTE]
> Pokud jste už nasadili služby SendGrid, můžete pokračovat přímo na [nasazení centra IoT Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Nasazení služby SendGrid

Chcete-li zřídit služby Azure, použijte **nasadit do Azure** tlačítko. Toto tlačítko umožňuje rychlé a snadné nasazení open source projektů Microsoft Azure.

Klikněte na tlačítko **nasadit do Azure** tlačítko níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Pokud již nejste přihlášeni ke svému účtu Azure, přihlaste se hned. 

Nyní uvidíte registrační formulář SendGrid.

![Nasazení služby SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Vyplnění registračního formuláře:

   * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid, nebo použijte již existující. Zobrazit [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-portal.md).

   * **Název**: Název vaší služby SendGrid. Vyberte jedinečný název, který je odlišný od jiných služeb, které máte uzavřeny.

   * **Heslo**: Služba vyžaduje, aby heslo, které se nepoužije pro všechno, co je v tomto projektu.

   * **e-mailu**: Služby SendGrid pošle ověření na této e-mailovou adresu.

Zkontrolujte **připnout na řídicí panel** umožňuje jednodušeji najít v budoucnu tuto aplikaci a pak klikněte na **nákupní** odeslat přihlašovací formulář.
 
### <a name="sendgrid-api-key-creation"></a>Vytvoření klíče rozhraní API SendGrid

Jakmile se nasazení dokončí, klikněte na něj a potom klikněte na tlačítko **spravovat** tlačítko. Zobrazí se stránce účtu SendGrid, kdy potřebujete ověřit e-mailovou adresu.

![Správa služby SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Klikněte na stránce SendGrid **nastavení** > **klíče rozhraní API** > **vytvořit klíč rozhraní API**.

![SendGrid nejprve vytvořit rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Na **vytvořit klíč rozhraní API** stránky, zadejte **název klíče rozhraní API** a klikněte na tlačítko **vytvořit & zobrazení**.

![SendGrid druhé vytvořit rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klíč rozhraní API se zobrazí pouze jednou. Nezapomeňte si zkopírujte a uložte ho bez obav, protože se používá v dalším kroku.

## <a name="deploy-iot-hub-in-azure"></a>Nasazení služby IoT Hub v Azure

Takto bude poskytovat další Azure IoT týkající se služby a nasazení služby Azure Functions pro tento projekt.

Klikněte na tlačítko **nasadit do Azure** tlačítko níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Registrační formulář se zobrazí.

![Nasazení IOT hub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Přejít k vyplnění polí na formuláři pro registraci.

   * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid, nebo použijte již existující. Zobrazit [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-portal.md).

   * **Název centra IOT**: Název služby IoT hub. Vyberte jedinečný název, který je odlišný od jiných služeb, které máte uzavřeny.

   * **Sku služby IOT Hub**: F1 (omezeno na jeden do každého předplatného) je zdarma. Další informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Z e-mailu**: Toto pole musí mít stejné e-mailovou adresu, kterou jste použili při nastavování služby SendGrid.

Zkontrolujte **připnout na řídicí panel** umožňuje jednodušeji najít v budoucnu tuto aplikaci a pak klikněte na **nákupní** až budete připravení pokračovat k dalšímu kroku.
 
## <a name="build-and-upload-the-code"></a>Vytvoření a nahrání kódu

V dalším kroku načíst ukázkový kód v nástroji VS Code a zřídit nezbytné služby Azure.

### <a name="start-vs-code"></a>Spusťte VS Code

- Ujistěte se, že je vaše DevKit **není** připojené k vašemu počítači.
- Spusťte VS Code.
- Připojení DevKit k vašemu počítači.

> [!NOTE]
> Když spustíte VS Code, může se zobrazit chybová zpráva s oznámením, že nemůže nalézt rozhraním Arduino IDE nebo související Rady balíčku. Pokud se zobrazí tato chyba, Zavřít VS Code, spuštění znovu rozhraním Arduino IDE a VS Code by měl najít cestu rozhraním Arduino IDE správně.

### <a name="open-arduino-examples-folder"></a>Otevřít složku příklady Arduino

Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **DoorMonitor**. Tato akce otevře nové okno VS Code se v něm složku projektu.

![Příklady mini solution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Ukázkové aplikace můžete také otevřít z palety příkazů. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

### <a name="provision-azure-services"></a>Zřízení služby Azure

V okně řešení spouštění úloh zřizování cloudu:
- Typ `Ctrl+P` (macOS: `Cmd+P`).
- Zadejte `task cloud-provision` do zadaného textového pole.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadovaných služeb Azure. Všechny položky na stejné vyberte ze seznamu výzvami, kterou jste zřídili dříve v [nasazení centra IoT Azure](#deploy-iot-hub-in-azure).

![Zřizování cloudu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Pokud na stránce přestane reagovat ve stavu načítání při pokusu o přihlášení k Azure, přečtěte si ["stránce kající změny při přihlašování" část Nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) k vyřešení tohoto problému. 

### <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

Dále odešlete kód pro zařízení.

#### <a name="windows"></a>Windows

1. Použití `Ctrl+P` spuštění `task device-upload`.

2. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí DevKit identifikačního čísla a slovo *konfigurace*.

#### <a name="macos"></a>macOS

1. Přepněte DevKit do režimu konfigurace: Podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí "Configuration".

2. Klikněte na tlačítko `Cmd+P` spuštění `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Ověření, nahrát a spuštění ukázkové aplikace

Připojovací řetězec, který je načten z [zřízení Azure services](#provision-azure-services) kroku je nyní nastaveno. 

VS Code pak spustí ověření a nahrajete Arduino náčrt DevKit.

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> V některých případech se může zobrazit "Chyba: AZ3166 SE: Neznámý balíček"chybová zpráva. Tato chyba nastane, pokud index panelu balíček není správně aktualizovat. Chcete-li tuto chybu vyřešit, přečtěte si [části vývoj nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Program nejprve inicializuje po DevKit za přítomnosti stabilní magnetické pole.

Po inicializaci `Door closed` se zobrazí na obrazovce. Když dojde ke změně v magnetické pole, se stav změní na `Door opened`. Pokaždé, když změny stavu dveří, dostanete e-mailové oznámení. (Tyto e-mailové zprávy může trvat až pět minut, než k přijetí.)

![Magnets blízko senzor: Dvířka uzavřeno](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnets blízko senzor: Dvířka knihovny uzavřeno")

![Přesunout mimo senzor magnet: Dvířka otevřen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet přesunout mimo senzor: Otevřít dvířka knihovny")

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, přečtěte si [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Jste se naučili, jak se připojit k akcelerátor řešení vzdáleného monitorování Azure IoT DevKit zařízení a používaný k odesílání e-mailu služby SendGrid. Tady jsou další navrhované kroky:

* [Azure IoT vzdálené monitorování přehled akcelerátorů řešení](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
