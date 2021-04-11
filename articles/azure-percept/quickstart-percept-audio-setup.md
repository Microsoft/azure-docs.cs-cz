---
title: Nastavení zvuku Azure Percept
description: Naučte se připojit své zvukové zařízení Azure Percept ke službě Azure Percept DK.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605786"
---
# <a name="azure-percept-audio-setup"></a>Nastavení služby Azure Percept audio

Azure Percept audio funguje se službou Azure Percept DK mimo box. Nevyžaduje se žádné jedinečné nastavení.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- Zvuk Azure Percept
- [Předplatné Azure](https://azure.microsoft.com/free/)
- [Prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): připojili jste DevKit k síti Wi-Fi, vytvořili IoT Hub a připojili jste devkit k IoT Hub
- Mluvčí nebo sluchátka, které se můžou připojit ke zvukové zdířkě 3,5 mm (volitelné)

## <a name="connecting-your-devices"></a>Připojení zařízení

1. Připojte zvukové zařízení Azure Percept k desce služby Azure Percept DK s integrovaným typem Micro USB to USB – kabel. Připojte Micro USB konec kabelu k desce zvukového přijímače (pro vývojáře) a zadejte konec na desku dopravce Percept DK.

1. (Volitelné) Připojte svůj mluvčí nebo sluchátka k vašemu zvukovému zařízení Azure Percept přes zvukový konektor, který je označený jako "řádek ven". To vám umožní slyšet zvukové odezvy.

1. Zapněte DevKit. Indikátor LED L02 na panelu zvukového zařízení se změní na blikání, aby označoval, že zařízení bylo zapnuté a že se ověřuje zvuk SoM.

1. Počkejte na dokončení procesu ověřování – může to trvat až 3 minuty.

1. Když vidíte jednu z následujících možností, můžete začít s vytvářením prototypů:

    - Indikátor LED L02 se změní na Solid bílá: to znamená, že ověřování je hotové a DevKit ještě není nakonfigurovaný s klíčovým slovem.
    - Všechny tři diody LED jsou modré: to znamená, že ověřování je hotové a DevKit je nakonfigurovaný pomocí klíčového slova.

## <a name="next-steps"></a>Další kroky

Vytvořte v [Azure Percept studiu](https://go.microsoft.com/fwlink/?linkid=2135819) [řešení pro rozpoznávání řeči bez kódu](./tutorial-no-code-speech.md) .
