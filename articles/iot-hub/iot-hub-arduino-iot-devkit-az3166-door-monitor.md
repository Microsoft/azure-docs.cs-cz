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
ms.openlocfilehash: 25cb3ba53c663a642f0871becbfbcab39d521c67
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437711"
---
# <a name="door-monitor"></a>Dvířka knihovny monitorování          

MXChip IoT DevKit obsahuje integrované magnetických senzorů. V tomto projektu zjistíte přítomnost nebo nepřítomnost blízké silné magnetické pole – v takovém případě pocházející z malá. Trvalý magnet.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se dozvíte:
- Jak používat MXChip IoT DevKit magnetických senzorů k detekci pohybu blízké magnet.
- Jak používat službu SendGrid k odesílání oznámení e-mailovou adresu.

> [!NOTE]
> Pro praktická použití tohoto projektu:
> - Připojte magnet hrany dveří.
> - Připojení DevKit v jamb dveře blízko magnetu. Otevírání nebo zavírání dveří aktivují senzor, což vede k e-mailové oznámení události přijetí.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](iot-hub-arduino-iot-devkit-az3166-get-started.md) na:

* Mají vaše DevKit připojení k Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud nemáte, můžete zaregistrovat pomocí jedné z těchto metod:

* Aktivovat [bezplatné 30denní zkušební verze Microsoft Azure účet](https://azure.microsoft.com/free/).
* Deklarace identity vaší [kredit Azure ve výši](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Pokud jste předplatitelem MSDN nebo Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Nasazení služby SendGrid v Azure

[SendGrid](https://sendgrid.com/) je platforma pro doručení e-mailu založené na cloudu. Tato služba se používá k odesílání e-mailová oznámení.

> [!NOTE]
> Pokud jste už nasadili služby SendGrid, můžete pokračovat přímo na [nasazení centra IoT Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Nasazení služby SendGrid

Chcete-li zřídit služby Azure, použijte **nasadit do Azure** tlačítko. Toto tlačítko umožňuje rychlé a snadné nasazení open source projektů Microsoft Azure.

Klikněte na tlačítko **nasadit do Azure** tlačítko níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Zobrazí se následující stránka.

> [!NOTE]
> Pokud se na následující stránce nezobrazí, budete muset nejdřív přihlásit k účtu Azure.

Vyplnění registračního formuláře:

  * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid, nebo použijte již existující. Zobrazit [použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Název**: název vaší služby SendGrid. Vyberte jedinečný název, který je odlišný od jiných služeb, které máte uzavřeny.

  * **Heslo**: služba vyžaduje, aby heslo, které nebudou pro všechno, co je v tomto projektu.

  * **E-mailu**: služby SendGrid pošle ověření na této e-mailovou adresu.

  > [!NOTE]
  > Zkontrolujte, **připnout na řídicí panel** umožňuje jednodušeji najít v budoucnu tuto aplikaci.
 
![Nasazení služby SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Vytvoření klíče rozhraní API SendGrid

Po úspěšném nasazení, klikněte na něj a potom klikněte na tlačítko **spravovat** tlačítko. Přesměrováni na stránku SendGrid a potřebujeme si ověřit vaši e-mailovou adresu.

![Správa služby SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Klikněte na stránce SendGrid **nastavení** > **klíče rozhraní API** > **vytvořit klíč rozhraní API**. Vstup **název klíče rozhraní API** a klikněte na tlačítko **vytvořit & zobrazení**.

![SendGrid nejprve vytvořit rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![SendGrid druhé vytvořit rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klíč rozhraní API se zobrazí pouze jednou. Nezapomeňte si zkopírujte a uložte ho bez obav, protože se používá v dalším kroku.

## <a name="deploy-iot-hub-in-azure"></a>Nasazení služby IoT Hub v Azure

Takto bude poskytovat další Azure IoT týkající se služby a nasazení služby Azure Functions pro tento projekt.

Klikněte na tlačítko **nasadit do Azure** tlačítko níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Zobrazí se následující stránka.

> [!NOTE]
> Pokud se nezobrazí na následující stránce, budete muset nejdřív přihlásit k účtu Azure.

Vyplnění registračního formuláře:

  * **Skupina prostředků**: Vytvořte skupinu prostředků pro hostování služby SendGrid, nebo použijte již existující. Zobrazit [použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Název centra IOT**: název služby IoT hub. Vyberte jedinečný název, který je odlišný od jiných služeb, které máte uzavřeny.

  * **IOT Hub Sku**: F1 (omezená na jedno předplatné) je zdarma. Zobrazí se další informace o cenách za [cenovou a škálovací úroveň](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Z e-mailu**: měl by to být stejné e-mailovou adresu, které jste použili při nastavování služby SendGrid.

  > [!NOTE]
  > Zkontrolujte, **připnout na řídicí panel** umožňuje jednodušeji najít v budoucnu tuto aplikaci.
 
![Nasazení IOT hub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Vytvoření a nahrání kódu

### <a name="start-vs-code"></a>Spusťte VS Code

- Ujistěte se, že je vaše DevKit **není** připojené k vašemu počítači.
- Spusťte VS Code.
- Připojení DevKit k vašemu počítači.

> [!NOTE]
> Když spustíte VS Code, může se zobrazit chybová zpráva s oznámením, že nemůže nalézt rozhraním Arduino IDE nebo související Rady balíčku. Pokud se zobrazí tato chyba, Zavřít VS Code, spuštění znovu rozhraním Arduino IDE a VS Code by měl najít cestu rozhraním Arduino IDE správně.

### <a name="open-arduino-examples-folder"></a>Otevřít složku příklady Arduino

Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **DoorMonitor**. Tato akce otevře nové okno VS Code se v něm složku projektu.

![Příklady mini solution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> Příklad lze také otevřít z palety příkazů. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

### <a name="provision-azure-services"></a>Zřízení služby Azure

V okně řešení spouštění úloh zřizování cloudu:
- Typ `Ctrl+P` (macOS: `Cmd+P`).
- Zadejte `task cloud-provision` do zadaného textového pole.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadovaných služeb Azure. Všechny položky na stejné vyberte ze seznamu výzvami, kterou jste zřídili dříve v [nasazení centra IoT Azure](#deploy-iot-hub-in-azure).

![Zřizování cloudu](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Pokud na stránce přestane reagovat ve stavu načítání při pokusu o přihlášení k Azure, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) k vyřešení tohoto problému. 

### <a name="build-and-upload-the-device-code"></a>Vytvoření a nahrání kódu zařízení

#### <a name="windows"></a>Windows

1. Použití `Ctrl+P` spuštění `task device-upload`.
2. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí DevKit identifikačního čísla a slovo *konfigurace*.

Tento postup nastaví připojovací řetězec, který je načten z [zřízení Azure services](#provision-azure-services) kroku.

VS Code pak spustí ověření a nahrajete Arduino náčrt DevKit:

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> V některých případech se může zobrazit "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, pokud index panelu balíček není správně aktualizovat. Pro vyřešení této chyby, přečtěte si tento [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Přepnout do režimu konfigurace, DevKit: podržte tlačítko A pak nabízených oznámení a uvolněte tlačítko Obnovení nastavení. Na obrazovce se zobrazí "Configuration".
2. Použití `Cmd+P` spuštění `task device-upload`.

Tento postup nastaví připojovací řetězec, který je načten z [zřízení Azure services](#provision-azure-services) kroku.

VS Code pak spustí ověření a nahrajete Arduino náčrt DevKit:

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit restartuje a spustí kód.

> [!NOTE]
> V některých případech se může zobrazit "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, pokud index panelu balíček není správně aktualizovat. Pro vyřešení této chyby, přečtěte si tento [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Program nejprve inicializuje po DevKit za přítomnosti stabilní magnetické pole.

Po inicializaci `Door closed` se zobrazí na obrazovce. Když dojde ke změně v magnetické pole, se stav změní na `Door opened`. Pokaždé, když změny stavu dveří, dostanete e-mailové oznámení. (Tyto e-mailové zprávy může trvat až pět minut, než k přijetí.)

![Magnets blízko senzor: uzavřeno dveře](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnets blízko senzor: dveře uzavřeno")

![Magnet přesunout mimo senzor: otevřít dvířka](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet přesunout mimo senzor: otevřít dvířka knihovny")

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [nejčastějších dotazech týkajících se](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Jste se naučili, jak připojit k akcelerátor řešení vzdáleného monitorování Azure IoT DevKit zařízení a používat k odesílání e-mailu služby SendGrid. Tady jsou další navrhované kroky:

* [Azure IoT vzdálené monitorování přehled akcelerátorů řešení](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
