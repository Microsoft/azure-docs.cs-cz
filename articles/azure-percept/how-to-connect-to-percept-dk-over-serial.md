---
title: Připojení ke službě Azure Percept DK prostřednictvím sériového portu
description: Naučte se, jak nastavit sériové připojení ke službě Azure Percept DK pomocí výstupu a sériového kabelu USB na TTL.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662326"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Připojení ke službě Azure Percept DK prostřednictvím sériového portu

Pomocí následujících kroků nastavte sériové připojení ke službě Azure Percept DK prostřednictvím [výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> Nepokoušejte se připojit DevKit přes sérii s výjimkou případů extrémního selhání (například jste si zařízení **zavedli** ). Oddělte skříňku na nosných desku pro připojení sériového kabelu je velmi obtížné a přeruší vaše Wi-Fi kabely antén.

## <a name="prerequisites"></a>Požadavky

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Hostitelský počítač
- Azure Percept DK
- [Sériový kabel USB na TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Sériový kabel USB na hodnotu TTL.":::

## <a name="initiate-the-serial-connection"></a>Iniciace sériového připojení

1. Pokud je karta dopravce připojená k 80/20 kolejnici, odeberte ji ze kolejnice pomocí šestnáctkového klíče (zahrnutého na úvodní kartě DevKit).

1. Odstraňte šrouby na spodní straně nosiče a rozbalte základní desku.

    > [!WARNING]
    > Odebráním základní desky dojde k přerušení Wi-Fich kabelů antén. Nepokračujte **se** sériovým připojením, pokud se nejedná o poslední možnost obnovení zařízení.

1. Odeberte Heatsink.

1. Odeberte z GPIO kódů PIN.

    > [!TIP]
    > Všimněte si, že před odebráním si poznamenejte orientaci na panelu můstku. Nakreslete například šipku na nebo připojte nálepku k panelu můstku, který odkazuje směrem k obvody. Panel můstku není v provozu a při sestavování kabiny může dojít k náhodnému propojení zpět.

1. Připojte [USB sériový kabel k](https://www.adafruit.com/product/954) GPIO PIN kód na základní desce, jak je znázorněno níže. Upozorňujeme, že červený kabel není připojen.

    - Připojte černý kabel (GND) k PIN kódu 6.
    - Připojte bílý kabel (RX) k PIN kódu 8.
    - Připojte zelený kabel (TX) k pinu 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Připojení sériových kolíků na desce":::

1. Zapněte své DevKit a připojte USB kabel sériového kabelu k počítači.

1. V systému Windows v nabídce **Start**  ->  **web Windows Update nastavení**  ->  **Zobrazte volitelné aktualizace**  ->  **aktualizace ovladačů**. V seznamu vyhledejte aktualizaci sériového portu na USB, zaškrtněte políčko vedle něho a klikněte na **Stáhnout a nainstalovat**.  

1. V dalším kroku otevřete Windows Správce zařízení (**Spustit**  ->  **Správce zařízení**). Přejděte na **porty** a kliknutím na **USB to UART** otevřete **vlastnosti**. Všimněte si, ke kterému portu COM je vaše zařízení připojené.

1. Klikněte na kartu **nastavení portu** . Ujistěte se, že **bity za sekundu** jsou nastavené na 115200.

1. Otevřete PuTTY. Zadejte následující příkaz a kliknutím na **otevřít** se připojte k DevKit prostřednictvím sériového portu:

    1. Sériová čára: COM [port #]
    1. Rychlost: 115200
    1. Typ připojení: sériové

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Okno relace výstupu s vybranými sériovými parametry":::

## <a name="next-steps"></a>Další kroky

Pokud chcete aktualizovat nespouštěcí zařízení přes sériové prostředí pomocí sériového [kabelu s USB na hodnotu TTL](https://www.adafruit.com/product/954), přečtěte si prosím Průvodce aktualizací USB v případě nestandardních situací.

[comment]: # (Pokud je k dispozici, přidejte odkaz na Průvodce aktualizací USB.)