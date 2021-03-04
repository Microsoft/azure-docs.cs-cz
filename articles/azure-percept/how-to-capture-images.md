---
title: Zachycení imagí pro řešení vidění bez kódu v Azure Percept studiu
description: Přečtěte si, jak zachytit image pomocí Azure Percept DK v Azure Percept studiu pro řešení vize bez kódu
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 44bf498af52f4d8a0d880dc1f1d5874d5b444cae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035530"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Zachycení imagí pro projekt Vision v Azure Percept studiu

Použijte tento průvodce k zachycení imagí pomocí Vision SoM Azure Percept DK pro existující projekt Vision v Azure Percept studiu. Pokud jste ještě nevytvořili projekt vize, přečtěte si [kurz vize necode](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub
- [Projekt vize bez kódu](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Zachytit image

1. Zapněte si DevKit.

1. Přejděte do [Azure Percept studia](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Na levé straně stránky přehled klikněte na **zařízení**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Obrazovka s přehledem Azure Percept Studio" lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Vyberte DevKit ze seznamu.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Seznam zařízení Percept.":::

1. Na stránce zařízení klikněte na **zachytit image pro projekt**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Stránka zařízení Percept s uvedenými dostupnými akcemi":::

1. V okně **zachycení bitové kopie** udělejte toto:

    1. V rozevírací nabídce **projekt** vyberte projekt vize, pro který chcete shromáždit obrázky.

    1. Klikněte na **Zobrazit datový proud zařízení** , aby se zajistilo, že fotoaparát SOM ve službě Vision SOM je správně umístěný.

    1. Pro zachycení image klikněte na **pořídit fotografii** .

    1. Případně můžete zaškrtnout políčko u **automatického zachycení obrázků** a nastavit časovač pro zachycení imagí:

        1. V části **rychlost zachytávání** vyberte upřednostňovanou rychlost zpracování obrazu.
        1. V části **cíl** vyberte celkový počet imagí, které chcete shromáždit.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Obrazovka zachycení obrázku":::

Všechny obrázky budou přístupné v [Custom Vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Další kroky

[Otestujte a přeškolujte si model vize bez kódu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).