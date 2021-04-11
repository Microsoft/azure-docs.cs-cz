---
title: Unbox a sestavte své komponenty Azure Percept DK
description: Přečtěte si, jak unbox, připojit a zapnout Azure Percept DK.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608176"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Rychlý Start: Unbox a sestavení komponent Azure Percept DK

Po přijetí služby Azure Percept DK si v této příručce najdete informace o tom, jak připojit komponenty a zapnout zařízení.

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK (DevKit)
- P7 Screwdriver (nepovinný, použitý k zabezpečení konektoru napájecího kabelu do nosné desky)

## <a name="unbox-and-assemble-your-device"></a>Unbox a sestavení zařízení

1. Unbox součásti Azure Percept DK.

    DevKit obsahuje desku dopravce, Azure Percept Vision SoM, příslušenství obsahující antény a kabely a uvítací kartu s šestnáctkovým klíčem.

1. Připojte komponenty DevKit.

    > [!NOTE]
    > Port adaptéru napájení je umístěný na pravé straně nosné desky. Zbývající porty (2x USB-A, 1x USB-C a 1x Ethernet) a tlačítko napájení se nachází na levé straně nosné desky.

    1. Došroubujte Wi-Fi antény do nosné desky.

    1. Připojte Vision SoM k portu USB-C nosné desky pomocí kabelu USB-C.

    1. Připojte napájecí kabel k napájecímu adaptéru.

    1. Odeberte ze zařízení všechny zbývající plastové obaly.

    1. Připojte napájecí adaptér/kabel k nosné desce a zásuvce na zdi. K naplnění konektoru napájecího kabelu na desku použijte P7 Screwdriver (není součástí DevKit) pro zvýšení šrouby konektorů.

    1. Po připojení napájecího kabelu do zásuvky na zdi se zařízení automaticky zapne. Tlačítko napájení na levé straně nosné desky bude osvětlené. Počkejte prosím nějakou dobu, než se zařízení spustí.

        > [!NOTE]
        > Tlačítko napájení slouží k vypnutí nebo restartování zařízení při připojení k napájení z elektrické zásuvky. V případě výpadku napájení se zařízení automaticky restartuje.

Pro vizuální ukázku sestavení DevKit se podívejte na 0:00 až 0:50 z následujícího videa:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Další kroky

Teď, když je váš DevKit připojený a zapnutý, přečtěte si průvodce [prostředím pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) a dokončete nastavení zařízení. Prostředí pro instalaci umožňuje připojit DevKit k síti Wi-Fi, nastavit přihlášení SSH, vytvořit IoT Hub a zřídit DevKit pro svůj účet Azure. Po dokončení instalace zařízení budete připraveni začít vytváření prototypů.