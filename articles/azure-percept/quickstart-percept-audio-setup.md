---
title: Začínáme se službou Azure Percept audio
description: Naučte se připojit své zvukové zařízení Azure Percept ke službě Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665382"
---
# <a name="azure-percept-audio-setup"></a>Nastavení služby Azure Percept audio

Azure Percept audio funguje se službou Azure Percept DK mimo box. Nevyžaduje se žádné jedinečné nastavení.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- Zvuk Azure Percept
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub

## <a name="connecting-your-devices"></a>Připojení zařízení

1. Připojte zvukové zařízení Azure Percept ke Board Percept v systému Azure pomocí integrovaného typu USB Micro-B do USB typu – kabel. Připojte typ Micro-B konec kabelu ke zvukovému modelu SoM a typu na konci na desku Percept DK.

1. Zapněte DevKit.

    - Indikátor LED l01 ve zvukovém modelu SoM se změní na plnou zelenou, aby označoval, že zařízení bylo zapnuté.
    - Indikátor LED L02 se změní na blikající zelenou, aby označoval, že se ověřuje zvuk SoM.

1. Počkejte na dokončení procesu ověřování – může to trvat až 3 minuty.

1. Když vidíte jednu z následujících možností, můžete začít s vytvářením prototypů:

    - Indikátor LED l01 vypne a L02 se změní na bílou. To znamená, že ověřování bylo dokončeno a DevKit ještě nebyl nakonfigurován s klíčovým slovem.
    - Všechny tři diody LED se vypnou modře. To znamená, že ověřování je dokončeno a DevKit je nakonfigurován s klíčovým slovem.

    > [!NOTE]
    > Pokud se vaše DevKit neověřuje, obraťte se na podporu.

## <a name="next-steps"></a>Další kroky

Vytvořte [řešení pro rozpoznávání řeči bez kódu](./tutorial-no-code-speech.md).