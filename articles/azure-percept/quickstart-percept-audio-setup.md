---
title: Začínáme se službou Azure Percept audio
description: Naučte se připojit své zvukové zařízení Azure Percept ke službě Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179268"
---
# <a name="azure-percept-audio-setup"></a>Nastavení služby Azure Percept audio

Azure Percept audio funguje se službou Azure Percept DK mimo box. Nevyžaduje se žádné jedinečné nastavení.

## <a name="prerequisites"></a>Předpoklady

- Azure Percept DK (DevKit)
- Zvuk Azure Percept
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub
- Mluvčí nebo sluchátka, které se můžou připojit ke zvukové zdířkě 3,5 mm (volitelné)

## <a name="connecting-your-devices"></a>Připojení zařízení

1. Připojte zvukové zařízení Azure Percept k desce služby Azure Percept DK s integrovaným typem Micro USB to USB – kabel. Připojte mikroUSB konec kabelu k panelu pro propojování (vývojářům) a zadejte konec na desku dopravců Percept DK.
1. (Volitelné) Připojte svůj mluvčí nebo sluchátka k Percept zvuku Azure pomocí zvukové zásuvky, která je označena jako "řádek ven". To vám umožní slyšet zvukové odezvy hlasového asistenta. Pokud nepřipojíte mluvčí nebo sluchátka, budete moci v ukázkovém okně zobrazit odpovědi jako text. 

1. Zapněte DevKit. Indikátor LED, který se L02 na panelu, se změní na blikání, aby označoval, že zařízení bylo zapnuté a že se ověřuje zvuk SoM.

1. Počkejte na dokončení procesu ověřování – může to trvat až 3 minuty.

1. Když vidíte jednu z následujících možností, můžete začít s vytvářením prototypů:

    - Indikátor LED L02 se změní na plnou bílou. To znamená, že ověřování bylo dokončeno a DevKit ještě nebyl nakonfigurován s klíčovým slovem.
    - Všechny tři diody LED se vypnou modře. To znamená, že ověřování je dokončeno a DevKit je nakonfigurován s klíčovým slovem.

## <a name="next-steps"></a>Další kroky

Vytvořte v [Azure Percept studiu](https://go.microsoft.com/fwlink/?linkid=2135819) [řešení pro rozpoznávání řeči bez kódu](./tutorial-no-code-speech.md) .
