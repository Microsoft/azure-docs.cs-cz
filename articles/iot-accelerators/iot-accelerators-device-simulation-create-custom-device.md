---
title: Vytvoření vlastního simulovaného zařízení – Azure | Microsoft Docs
description: V tomto kurzu s využitím Simulace zařízení vytvoříte vlastní simulované zařízení pro použití v simulacích.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756750"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Kurz: Vytvoření vlastního simulovaného zařízení

V tomto kurzu s využitím Simulace zařízení vytvoříte vlastní simulované zařízení pro použití v simulacích. Při začátcích se Simulací zařízení můžete použít některé z dodávaných ukázkových simulovaných zařízení. Můžete také vytvářet vlastní simulovaná zařízení, jak je popsáno v tomto článku. Další možnosti přizpůsobení najdete v tématu [Vytvoření pokročilého modelu zařízení](iot-accelerators-device-simulation-advanced-device.md).

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Zobrazení seznamu modelů simulovaných zařízení
> * Vytvoření vlastního simulovaného zařízení
> * Klonování modelu zařízení
> * Odstranění modelu zařízení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci Simulace zařízení.

Pokud jste Simulaci zařízení ještě nenasadili, měli byste dokončit rychlý start [Nasazení a spuštění simulace zařízení IoT v Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Otevření Simulace zařízení

Pokud chcete spustit Simulaci zařízení v prohlížeči, nejprve přejděte na web [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com).

Může se zobrazit výzva k přihlášení pomocí vašich přihlašovacích údajů k předplatnému Azure.

Pak klikněte na **Spustit** na dlaždici instance Simulace zařízení, kterou jste nasadili v rychlém startu [Nasazení a spuštění simulace zařízení IoT v Azure](quickstart-device-simulation-deploy.md).

## <a name="view-your-device-models"></a>Zobrazení modelů zařízení

Na řádku nabídek vyberte **Modely zařízení**. Na stránce **Modely zařízení** se zobrazí seznam všech dostupných modelů zařízení v této instanci Simulace zařízení:

![Modely zařízení](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Vytvoření modelu zařízení

V pravém horním rohu stránky klikněte na **+ Přidat modely zařízení**:

![Přidání modelu zařízení](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

V tomto kurzu vytvoříte simulovanou lednici, která odesílá data o teplotě a vlhkosti.

Do formuláře zadejte následující informace:

| Nastavení             | Hodnota                                                |
| ------------------- | ---------------------------------------------------- |
| Název modelu zařízení   | Lednice                                         |
| Popis modelu   | Lednice se snímači teploty a vlhkosti |
| Verze             | 1.0                                                  |

> [!NOTE]
> Název modelu zařízení musí být jedinečný.

Klikněte na **+ Přidat datový bod** a přidejte datové body pro teplotu a vlhkost s následujícími hodnotami:

| Datový bod          | Chování        | Min. hodnota | Max. hodnota | Jednotka |
| ------------------- | --------------- | --------- | --------- | ---- |
| Teplota         | Náhodné          | −50       | 100       | F    |
| Vlhkost            | Náhodné          | 0         | 100       | %    |

Kliknutím na **Uložit** uložte model zařízení.

![Vytvoření modelu zařízení](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Vaše lednice je teď uvedená v seznamu modelů zařízení. Možná bude nutné kliknutím na **Další** přejít na další stránku, kde se lednice zobrazí.

## <a name="clone-a-device-model"></a>Klonování modelu zařízení

Klonování modelu zařízení umožňuje vytvořit kopii existujícího modelu zařízení. Kopii pak můžete upravit tak, aby splňovala vaše konkrétní požadavky. Klonování šetří čas, když potřebujete vytvořit podobné modely zařízení.

Pokud chcete naklonovat model zařízení, zaškrtněte políčko vedle požadovaného modelu a pak na panelu akcí klikněte na **Klonovat**:

![Odstranění modelu zařízení](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Odstranění modelu zařízení

Jakýkoli vlastní model zařízení můžete odstranit. Pokud chcete odstranit model zařízení, zaškrtněte políčko vedle požadovaného modelu a pak na panelu akcí klikněte na **Odstranit**:

![Odstranění modelu zařízení](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvářet, klonovat a odstraňovat vlastní modely zařízení. Další informace o modelech zařízení najdete v následujícím článku s postupy:

> [!div class="nextstepaction"]
> [Vytvoření pokročilého modelu zařízení](iot-accelerators-device-simulation-advanced-device.md)