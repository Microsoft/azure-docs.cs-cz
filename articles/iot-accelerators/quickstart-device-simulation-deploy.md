---
title: Zkouška spuštění řešení pro simulaci zařízení v Azure | Microsoft Docs
description: V tomto rychlém startu nasadíte akcelerátor řešení simulace zařízení Azure IoT. Přihlásíte se k řídicímu panelu řešení a na něm vytvoříte simulaci.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/05/2018
ms.author: dobett
ms.openlocfilehash: 549a1b867ad35c6de42969722ba5a2bd28c8f99a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213152"
---
# <a name="quickstart-deploy-and-run-a-cloud-based-device-simulation-solution"></a>Rychlý start: Nasazení a spuštění cloudového řešení simulace zařízení

V tomto rychlém startu se dozvíte, jak nasadit akcelerátor řešení simulace zařízení Azure IoT pro účely testování řešení IoT. Po nasazení akcelerátoru řešení na stránce **Simulace** vytvoříte a spustíte simulaci.

K dokončení tohoto rychlého startu potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="deploy-the-solution"></a>Nasazení řešení

Při nasazování akcelerátoru řešení do předplatného Azure je potřeba nastavit několik možností konfigurace.

Přihlaste se k webu [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) pomocí přihlašovacích údajů svého účtu Azure.

Klikněte na **Vyzkoušejte** na dlaždici **Simulace zařízení**.

![Výběr simulace zařízení](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Na stránce **Vytvoření řešení Simulace zařízení** zadejte jedinečný **Název řešení**. Poznamenejte si název řešení – je to název skupiny prostředků Azure, která obsahuje všechny prostředky řešení.

Vyberte **Předplatné** a **Oblast**, které chcete při nasazování akcelerátoru řešení použít. Obvykle byste měli zvolit oblast, která je vám nejblíže. V předplatném musíte být [globálním správcem nebo uživatelem](iot-accelerators-permissions.md).

Zaškrtněte políčko pro nasazení centra IoT pro použití s řešením simulace zařízení. Centrum IoT, které simulace využívá, můžete později kdykoli změnit.

Kliknutím na **Vytvořit řešení** zahájíte zřizování řešení. Dokončení tohoto procesu trvá minimálně pět minut:

![Podrobnosti o řešení simulace zařízení](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Přihlášení k řešení

Po dokončení procesu zřizování se můžete přihlásit k řídicímu panelu akcelerátoru řešení simulace zařízení.

Na stránce **Zřízená řešení** klikněte na nový akcelerátor řešení simulace zařízení:

![Výběr nového řešení](./media/quickstart-device-simulation-deploy/choosenew.png)

Na panelu, který se zobrazí, si můžete prohlédnout informace o akcelerátoru řešení simulace zařízení. Zvolte **Řídicí panel řešení** a zobrazte akcelerátor řešení simulace zařízení:

![Panel řešení](./media/quickstart-device-simulation-deploy/solutionpanel.png)

Kliknutím na **Přijmout** přijměte žádost o oprávnění. Ve vašem prohlížeči se zobrazí řídicí panel řešení simulace zařízení:

[![Řídicí panel řešení](./media/quickstart-device-simulation-deploy/solutiondashboard-inline.png)](./media/quickstart-device-simulation-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="configure-the-simulation"></a>Konfigurace simulace

Simulaci nakonfigurujete a spustíte z řídicího panelu. Ke konfiguraci simulace použijte hodnoty v následující tabulce:

| Nastavení             | Hodnota                       |
| ------------------- | --------------------------- |
| Cílová služba IoT Hub      | Použít předem zřízenou službu IoT Hub |
| Model zařízení        | Chladič                     |
| Počet zařízení   | 10                          |
| Frekvence telemetrie | 10 sekund                  |
| Doba trvání simulace | 5 minut                   |

[![Konfigurace simulace](./media/quickstart-device-simulation-deploy/simulationconfig-inline.png)](./media/quickstart-device-simulation-deploy/simulationconfig-expanded.png#lightbox)

## <a name="run-the-simulation"></a>Spuštění simulace

Klikněte na **Spustit simulaci**. Simulace bude spuštěná po vámi zvolenou dobu. Simulaci můžete kdykoli zastavit kliknutím na **Zastavit simulaci**. Simulace ukazuje statistiku aktuálního spuštění. Kliknutím na **Zobrazit metriky služby IoT Hub na webu Azure Portal** zobrazte metriky hlášené centrem IoT:

[![Spuštění simulace](./media/quickstart-device-simulation-deploy/simulationrun-inline.png)](./media/quickstart-device-simulation-deploy/simulationrun-expanded.png#lightbox)

Z každé zřízené instance akcelerátoru řešení můžete spustit pouze jednu simulaci najednou.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v prozkoumávání, ponechte akcelerátor řešení simulace zařízení nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho na stránce [Zřízená řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) tak, že ho vyberete a pak kliknete na **Odstranit řešení**:

![Odstranění řešení](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili akcelerátor řešení simulace zařízení a spustili jste simulaci zařízení IoT.

Informace o tom, jak v simulaci použít stávající IoT Hub, najdete v této příručce:

> [!div class="nextstepaction"]
> [Použití stávajícího centra IoT s akcelerátorem řešení simulace zařízení](iot-accelerators-device-simulation-choose-hub.md)
