---
title: Zkouška spuštění řešení pro simulaci zařízení v Azure | Microsoft Docs
description: V tomto rychlém startu nasadíte Simulaci zařízení Azure IoT a spustíte simulaci.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/28/2018
ms.author: troyhop
ms.openlocfilehash: a109f3536ea8709313de3d1d6d17ce69c5652289
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753926"
---
# <a name="quickstart-deploy-and-run-an-iot-device-simulation-in-azure"></a>Rychlý start: Nasazení a spuštění simulace zařízení IoT v Azure

V tomto rychlém startu se dozvíte, jak nasadit Simulaci zařízení Azure IoT pro účely testování řešení IoT. Po nasazení akcelerátoru řešení začnete spuštěním ukázkové simulace.

K dokončení tohoto rychlého startu potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="deploy-device-simulation"></a>Nasazení Simulace zařízení

Při nasazování Simulace zařízení do předplatného Azure je potřeba nastavit několik možností konfigurace.

Přihlaste se k webu [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) pomocí přihlašovacích údajů svého účtu Azure.

Klikněte na dlaždici **Simulace zařízení**:

![Výběr simulace zařízení](./media/quickstart-device-simulation-deploy/devicesimulation.png)

Na stránce popisu Simulace zařízení klikněte na **Vyzkoušet**:

![Kliknutí na Vyzkoušet](./media/quickstart-device-simulation-deploy/devicesimulationPDP.png)

Na stránce **Vytvoření řešení Simulace zařízení** zadejte jedinečný **Název řešení**.

Vyberte **Předplatné** a **Oblast**, které chcete při nasazování akcelerátoru řešení použít. Obvykle byste měli zvolit oblast, která je vám nejblíže. V předplatném musíte být [globálním správcem nebo uživatelem](iot-accelerators-permissions.md).

Zaškrtněte políčko pro nasazení centra IoT pro použití s řešením simulace zařízení. Centrum IoT, které simulace využívá, můžete později kdykoli změnit.

Kliknutím na **Vytvořit řešení** zahájíte zřizování řešení. Dokončení tohoto procesu trvá minimálně pět minut:

![Podrobnosti o řešení simulace zařízení](./media/quickstart-device-simulation-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Přihlášení k řešení

Po dokončení procesu zřizování se můžete přihlásit k instanci Simulace zařízení kliknutím na tlačítko **Spustit**:

![Otevření Simulace zařízení](./media/quickstart-device-simulation-deploy/choosenew.png)

Kliknutím na **Přijmout** přijměte žádost o oprávnění. Ve vašem prohlížeči se zobrazí řídicí panel řešení Simulace zařízení.

Při prvním otevření se zobrazí řídicí panel Simulace zařízení s příručkou **Začínáme**. Kliknutím na první dlaždici otevřete ukázkovou simulaci. Pokud zavřete příručku **Začínáme**, můžete **jednoduchou ukázkovou simulaci** otevřít kliknutím na příslušnou dlaždici na řídicím panelu:

![Řídicí panel řešení](./media/quickstart-device-simulation-deploy/GettingStarted.png)

## <a name="sample-simulation"></a>Ukázková simulace

Protože se jedná o ukázkovou simulaci, není možné ji upravit. Pro simulaci jsou nakonfigurovaná následující nastavení:

| Nastavení             | Hodnota                       |
| ------------------- | --------------------------- |
| Cílová služba IoT Hub      | Použít předem zřízenou službu IoT Hub |
| Model zařízení        | Nákladní vůz                       |
| Počet zařízení   | 10                          |
| Frekvence telemetrie | 10 sekund                  |
| Doba trvání simulace | Spustit po neomezenou dobu            |

![Konfigurace simulace](./media/quickstart-device-simulation-deploy/SampleSimulation.png)

## <a name="run-the-simulation"></a>Spuštění simulace

Klikněte na **Spustit simulaci**. Simulace se podle konfigurace spustí po neomezenou dobu. Simulaci můžete kdykoli zastavit kliknutím na **Zastavit simulaci**. Simulace ukazuje statistiku aktuálního spuštění.

![Spuštění simulace](./media/quickstart-device-simulation-deploy/runningsimulation.png)

Z každé instance Simulace zařízení můžete spustit pouze jednu simulaci najednou.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v prozkoumávání, ponechte Simulaci zařízení nasazenou.

Pokud už Simulaci zařízení nepotřebujete, odstraňte ji na stránce [Zřízená řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) tak, že kliknete na příslušnou dlaždici a pak na **Odstranit řešení**:

![Odstranění řešení](media/quickstart-device-simulation-deploy/deletesolution.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili Simulaci zařízení a spustili jste ukázkovou simulaci zařízení IoT.

> [!div class="nextstepaction"]
> [Vytvoření simulace s jedním nebo několika typy zařízení](iot-accelerators-device-simulation-create-simulation.md)