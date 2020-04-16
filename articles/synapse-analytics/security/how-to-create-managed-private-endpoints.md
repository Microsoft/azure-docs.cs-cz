---
title: Vytvořte spravovaný soukromý koncový bod pro připojení k výsledkům zdroje dat.
description: Tento článek vás naučí, jak vytvořit spravované soukromé koncového bodu pro vaše zdroje dat z pracovního prostoru Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428898"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Vytvoření spravovaného soukromého koncového bodu do zdroje dat (náhled)

Tento článek vás naučí, jak vytvořit spravovaný soukromý koncový bod pro váš zdroj dat v Azure. Další informace najdete v [tématu Spravované soukromé koncové body.](./synapse-workspace-managed-private-endpoints.md)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1: Otevření pracovního prostoru Azure Synapse na webu Azure Portal

Můžete vytvořit spravované privátní koncový bod pro váš zdroj dat z Azure Synapse Studio. Vyberte kartu **Přehled** na webu Azure Portal a vyberte **Spustit Synapse Studio**.
![Spuštění Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Krok 2: Přechod na kartu Spravované virtuální sítě v Synapse Studiu

V Azure Synapse Studio vyberte kartu **Spravovat** z levé navigace. Vyberte **spravované virtuální sítě** a pak vyberte + **Nový**.
![Vytvoření nového spravovaného soukromého koncového bodu](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Krok 3: Výběr typu zdroje dat

Vyberte typ zdroje dat. V tomto případě cílový zdroj dat je účet Gen2 ADLS. Vyberte **Pokračovat**.
![Výběr cílového typu zdroje dat](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Krok 4: Zadání informací o zdroji dat

V dalším okně zadejte informace o zdroji dat. V tomto příkladu vytváříme spravovaný soukromý koncový bod pro účet Gen2 ADLS. Zadejte **název** spravovaného soukromého koncového bodu. Zadejte **předplatné Azure** a název účtu **úložiště**. Vyberte **Vytvořit**.
![Zadání podrobností o cílovém zdroji dat](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Krok 5: Ověření, že byl úspěšně vytvořen váš spravovaný soukromý koncový bod

Po odeslání žádosti se zobrazí její stav. Chcete-li ověřit úspěšné vytvoření spravovaného soukromého koncového bodu byl vytvořen, zkontrolujte jeho *stav zřizování*. Možná budete muset počkat 1 minutu a vyberte **Aktualizovat** aktualizovat stav zřizování. Uvidíte, že byl úspěšně vytvořen spravovaný soukromý koncový bod pro účet Gen2 ADLS.

Můžete také vidět, že *stav schválení* čeká *na vyřízení*. Vlastník cílového prostředku může schválit nebo odepřít požadavek na připojení privátního koncového bodu. Pokud vlastník schválí požadavek na připojení privátníkoncový koncový bod, je vytvořeno privátní propojení. Pokud je odmítnuto, není vytvořeno soukromé propojení.
![Stav požadavku na vytvoření spravovaného soukromého koncového bodu](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Další kroky

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)