---
title: Vytvoření nové simulace IoT – Azure | Microsoft Docs
description: V tomto kurzu s využitím Simulace zařízení vytvoříte a spustíte novou simulaci.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: e0d7310c4863c8dd431b991a2c249f2d8e257aeb
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852349"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Kurz: Vytvoření a spuštění simulace zařízení IoT

V tomto kurzu s využitím Simulace zařízení vytvoříte a spustíte novou simulaci IoT s jedním nebo několika simulovanými zařízeními.

V tomto kurzu jste:

>[!div class="checklist"]
> * Zobrazení všech aktivních a historických simulací
> * Vytvoření a spuštění nové simulace
> * Zobrazení metrik simulace
> * Zastavení simulace

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci Simulace zařízení.

Pokud jste ještě nasadili simulaci zařízení, přečtěte si téma [nasazení simulace zařízení](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) na GitHubu.

## <a name="view-simulations"></a>Zobrazení simulací

Na řádku nabídek vyberte **Simulace**. Na stránce **Simulace** se zobrazí informace o všech dostupných simulacích. Na této stránce můžete zobrazit simulace, které jsou aktuálně spuštěné, i dříve spuštěné simulace. Pokud chcete znovu spustit dřívější simulaci, kliknutím na dlaždici simulace otevřete podrobnosti o simulaci:

![Simulace](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Vytvoření simulace

Pokud chcete vytvořit simulaci, v pravém horním rohu klikněte na **+ Nová simulace**.

Simulace se skládá z jednoho nebo několika modelů zařízení. Model zařízení definuje chování, telemetrii a formát zpráv zařízení, které se mají simulovat.

Pokud chcete přidat model zařízení, klikněte na **+ Přidat typ zařízení** a vyberte ze seznamu model zařízení. Simulace může obsahovat více než jeden model zařízení. Každý model zařízení může mít různý počet zařízení a frekvenci zasílání zpráv.

Po vyplnění formuláře nové simulace ji kliknutím na **Spustit simulaci** zahajte.

V závislosti na počtu simulovaných zařízení může konfigurace a spuštění simulace trvat několik minut:

![Nová simulace](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Zastavení simulace

Po kliknutí na **Spustit simulaci** se zobrazí stránka podrobností o simulaci. Na této stránce podrobností se zobrazí živé statistiky simulace a metriky ze služby IoT Hub. Na tuto stránku podrobností můžete přejít také kliknutím na dlaždici simulace na stránce **Simulace**.

Pokud chcete simulaci zastavit, na panelu akcí v pravém horním rohu klikněte na **Zastavit simulaci**.

![Zastavení simulace](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit, spustit a zastavit simulaci. Také jste zjistili, jak zobrazit podrobnosti o simulaci. Další informace o spouštění simulací najdete v dalším kurzu:

> [!div class="nextstepaction"]
> [Vytvoření vlastního simulovaného zařízení](iot-accelerators-device-simulation-create-custom-device.md)
