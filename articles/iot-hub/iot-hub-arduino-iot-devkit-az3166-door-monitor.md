---
title: Obdrží e-mail, po otevření dveří pomocí služby sendgrid vám umožňuje a Azure Functions | Microsoft Docs
description: Monitorování magnetické senzoru ke zjišťování po otevření dveří a používat Azure Functions k odesílání e-mailové oznámení.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: ed0718f2d9d5093442001005e43551e7e9749eef
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="door-monitor"></a>Dvířka monitorování          

MXChip IoT DevKit obsahuje vestavěné magnetické možnosti. V tomto projektu můžete zjišťovat existenci nebo neexistenci těchto blízkým silné magnetické pole – v takovém případě pocházejících z malá. Trvalý magnet.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu dozvíte:
- Jak používat magnetické senzor MXChip IoT DevKit k detekci pohybu blízkým magnet.
- Jak používat služby sendgrid vám umožňuje odeslat oznámení e-mailovou adresu.

> [!NOTE]
> Pro praktická použití tohoto projektu:
> - Připojte magnet hrany dveří.
> - Připojte DevKit na jamb dveře blízko magnetu. Otevírání nebo při zavření dvířka aktivují senzoru, což vede e-mailové oznámení události přijetí.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [Příručka Začínáme]({{"/docs/get-started/" | absolute_url }}) na:

* Mít vaše DevKit připojení Wi-Fi
* Příprava vývojového prostředí

Aktivní předplatné Azure. Pokud jeden nemáte, můžete zaregistrovat pomocí jedné z těchto metod:

* Aktivovat [Bezplatný zkušební účet Microsoft Azure 30denní](https://azure.microsoft.com/free/).
* Deklarace identity vaší [kreditu Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) Pokud jste webu MSDN nebo v sadě Visual Studio odběratele.

## <a name="deploy-sendgrid-service-in-azure"></a>Nasazení služby sendgrid vám umožňuje v Azure

[Sendgrid vám umožňuje](https://sendgrid.com/) je platforma pro doručení cloudové e-mailu. Tato služba se používá k odesílání e-mailová oznámení.

> [!NOTE]
> Pokud už mají nasazenou službu Sendgridu, můžete přímo na [nasazení služby IoT Hub v Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Sendgrid vám umožňuje nasazení

Chcete-li zřídit služby Azure, použijte **nasadit do Azure** tlačítko. Toto tlačítko umožňuje rychlé a snadné nasazení open-source projekty, aby Microsoft Azure.

Klikněte **nasadit do Azure** tlačítko níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Zobrazí se na následující stránce.

> [!NOTE]
> Pokud se zobrazí následující stránka nezobrazí, musíte nejdřív přihlásit k účtu Azure.

Vyplnění registračního formuláře:

  * **Skupina prostředků**: vytvoření skupiny prostředků k hostování služby sendgrid vám umožňuje, nebo použijte existující. V tématu [použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Název**: název služby sendgrid vám umožňuje. Vyberte jedinečný název, které se liší od jiných služeb, které by mohly být.

  * **Heslo**: služba vyžaduje, aby hesla, které nebudou pro všechno, co je v tomto projektu.

  * **E-mailu**: služby sendgrid vám umožňuje odešle ověření této e-mailovou adresu.

  > [!NOTE]
  > Zkontrolujte **připnout na řídicí panel** možnost snadněji najít v budoucnu tuto aplikaci.
 
![Sendgrid vám umožňuje nasazení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Klíč rozhraní API sendgrid vám umožňuje vytvoření

Po úspěšné nasazení, klikněte na něj a potom klikněte na **spravovat** tlačítko. Jsou přesměrováni na stránku sendgrid vám umožňuje a potřebujete ověřit e-mailovou adresu.

![Sendgrid vám umožňuje spravovat](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na stránce Sendgridu, klikněte na **nastavení** > **klíče rozhraní API** > **vytvořit klíč rozhraní API**. Vstup **název klíče rozhraní API** a klikněte na tlačítko **vytvořit & zobrazení**.

![Sendgrid vám umožňuje vytvořit první rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Sendgrid vám umožňuje vytvořit druhý rozhraní API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Klíč rozhraní API se zobrazí pouze jednou. Nezapomeňte zkopírovat a bezpečně, uložit jako se používá v dalším kroku.

## <a name="deploy-iot-hub-in-azure"></a>Nasazení služby IoT Hub v Azure

Následující postup se zřídit další Azure IoT související služby a nasazení Azure Functions pro tento projekt.

Klikněte **nasadit do Azure** tlačítko níže. 

[![Nasazení do Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Zobrazí se na následující stránce.

> [!NOTE]
> Pokud se nezobrazí na následující stránce, musíte nejdřív přihlásit k účtu Azure.

Vyplnění registračního formuláře:

  * **Skupina prostředků**: vytvoření skupiny prostředků k hostování služby sendgrid vám umožňuje, nebo použijte existující. V tématu [použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Název centra IOT**: název služby IoT hub. Vyberte jedinečný název, které se liší od jiných služeb, které by mohly být.

  * **IOT Hub Sku**: F1 (omezený jeden do každého předplatného) je bezplatná. Zobrazí se další informace o cenách na [cenovou a škálovací úroveň](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Z e-mailu**: to by měl být stejný e-mailovou adresu, které jste použili při nastavování služby sendgrid vám umožňuje.

  > [!NOTE]
  > Zkontrolujte **připnout na řídicí panel** možnost snadněji najít v budoucnu tuto aplikaci.
 
![Nasazení IoTHub](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Vytvořit a odeslat kód

### <a name="start-vs-code"></a>Kód pro spuštění VS

- Zkontrolujte, zda je vaše DevKit **není** připojené k vašemu počítači.
- Spustí kód VS.
- Připojení DevKit k vašemu počítači.

> [!NOTE]
> Při spuštění VS Code, může se zobrazit chybová zpráva s oznámením, že nelze najít Arduino IDE nebo balíček Příbuzná panelu. Pokud se zobrazí tato chyba, Zavřít VS Code, spusťte znovu Arduino IDE a VS Code by měl vyhledejte Arduino IDE cestu správně.

### <a name="open-arduino-examples-folder"></a>Otevřít složku Arduino příklady

Rozbalte položku na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > AzureIoT**a vyberte **DoorMonitor**. Tato akce otevře nové okno VS Code s složce projektu v ní.

![Mini solution příklady](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> Příklad můžete také otevřít z palety příkaz. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

### <a name="provision-azure-services"></a>Zřídit služby Azure

V okně řešení spusťte cloudu zřizování úloh:
- Typ `Ctrl+P` (systému macOS: `Cmd+P`).
- Zadejte `task cloud-provision` v zadané textové pole.

V terminálu VS Code interaktivního příkazového řádku vás provede zřizování požadované služby Azure. Vyberte všechny stejné položky ze seznamu výzvami, který jste dříve zřízené v [nasazení služby IoT Hub v Azure](#deploy-iot-hub-in-azure).

![Zřizování cloudové](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Pokud při pokusu o přihlášení k Azure, přestane reagovat stav načítání stránky, podívejte se na [– nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) k vyřešení tohoto problému. 

### <a name="build-and-upload-the-device-code"></a>Vytvořit a odeslat kód zařízení

#### <a name="windows"></a>Windows

1. Použití `Ctrl+P` ke spuštění `task device-upload`.
2. Terminálu zobrazí výzvu k zadání režim konfigurace. Uděláte to tak, podržte tlačítko A pak push a verzí na tlačítko Obnovit. Na obrazovce zobrazí DevKit identifikační číslo a je slovo *konfigurace*.

Tento postup nastaví připojovací řetězec, který je načten z [služeb Azure zřídit](#provision-azure-services) krok.

VS Code pak spustí ověření a odeslání Arduino načrtnout DevKit:

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit restartuje a spuštění kódu.

> [!NOTE]
> V některých případech se může zobrazit "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, když není správně aktualizovat index balíčků panelu. Tuto chybu vyřešit, najdete v tématu to [– nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Přepnout do režimu konfigurace, DevKit: podržte tlačítko A pak nabízené a verze na tlačítko Obnovit. Na obrazovce zobrazí "Konfigurace".
2. Použití `Cmd+P` ke spuštění `task device-upload`.

Tento postup nastaví připojovací řetězec, který je načten z [služeb Azure zřídit](#provision-azure-services) krok.

VS Code pak spustí ověření a odeslání Arduino načrtnout DevKit:

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit restartuje a spuštění kódu.

> [!NOTE]
> V některých případech se může zobrazit "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, když není správně aktualizovat index balíčků panelu. Tuto chybu vyřešit, najdete v tématu to [– nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

Program nejprve inicializuje po DevKit případě stabilní magnetické pole.

Po inicializaci `Door closed` se zobrazí na obrazovce. Pokud dojde ke změně v poli magnetická, stav se změní na `Door opened`. Každé změně stavu dveří, obdržíte e-mail s oznámením. (Tyto e-mailové zprávy může trvat až pět minut, aby bylo možné přijímat.)

![Magnets blízko senzoru: dvířka zavřená](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magnets blízko senzoru: dvířka zavřená")

![Magnet přesunout mimo senzoru: otevřít dvířka](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet přesunout mimo senzoru: otevřít dvířka")

## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na problémy, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následující kanály:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Další postup

Jste se naučili postup DevKit zařízení připojit k vaší Azure IoT Suite a služby sendgrid vám umožňuje používat k odesílání e-mailu. Tady jsou navrhované další kroky:

* [Přehled služby Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Připojte zařízení MXChip IoT DevKit do aplikace Microsoft IoT centrální](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
