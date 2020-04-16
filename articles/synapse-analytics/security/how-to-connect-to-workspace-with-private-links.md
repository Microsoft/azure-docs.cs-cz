---
title: Připojení k pracovnímu prostoru Azure Synapse pomocí privátních odkazů
description: Tento článek vás naučí, jak se připojit k pracovnímu prostoru Azure Synapse pomocí privátní odkazy
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432187"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Připojení k pracovnímu prostoru Azure Synapse pomocí privátních odkazů (preview)

Tento článek vás naučí, jak vytvořit soukromý koncový bod do pracovního prostoru Azure Synapse. Další informace najdete v [soukromých odkazech a soukromých koncových bodech.](https://docs.microsoft.com/azure/private-link/)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1: Otevření pracovního prostoru Azure Synapse na webu Azure Portal

V části **Zabezpečení** vyberte **Soukromé připojení koncového bodu** a pak vyberte + Soukromý koncový **bod**.
![Otevření pracovního prostoru Azure Synapse na portálu Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Krok 2: Vyberte podrobnosti o předplatném a oblasti.

Na kartě **Základy** v okně **Vytvořit soukromý koncový bod** zvolte **předplatné** a **skupinu prostředků**. Pojmenujte soukromý koncový bod, který chcete vytvořit. **Name** Vyberte **oblast,** kde chcete vytvořit soukromý koncový bod.

Soukromé koncové body jsou vytvořeny v podsíti. Vybrané předplatné, skupina prostředků a vybraná oblast filtrují podsítě privátního koncového bodu. Vyberte **další: Zdroj >** po dokončení.
![Vyberte podrobnosti o předplatném a oblasti.](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Krok 3: Vyberte podrobnosti pracovního prostoru Azure Synapse

Na kartě **Prostředky** vyberte Připojit k prostředku **Subscription** Azure v **mém adresáři.** **Typ prostředku** pro vytváření privátní koncové body do pracovního prostoru Azure Synapse je *Microsoft.Synapse/pracovní prostory*.

Vyberte pracovní prostor Azure Synapse jako **prostředek**. Každý pracovní prostor Azure Synapse má tři **cílové dílčí prostředky,** které můžete vytvořit privátní koncový bod: Sql, SqlOnDemand a Dev.

Vyberte **Další: Konfigurace>** pro postup do další části nastavení.
![Vyberte podrobnosti o předplatném a oblasti.](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na kartě **Konfigurace** vyberte **virtuální síť** a **podsíť,** ve kterých má být vytvořen soukromý koncový bod. Musíte také vytvořit záznam DNS, který se mapuje na soukromý koncový bod.

Chcete-li integrovat privátní koncový bod se soukromou zónou DNS, vyberte **možnost Ano** pro **integraci se soukromou zónou DNS.** Pokud nemáte privátní zónu DNS přidruženou k vaší virtuální síti, vytvoří se nová privátní zóna DNS. Po dokončení vyberte **Zkontrolovat + vytvořit.**

![Vyberte podrobnosti o předplatném a oblasti.](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Po dokončení nasazení otevřete pracovní prostor Azure Synapse na webu Azure Portal a vyberte **Privátní připojení koncového bodu**. Zobrazí se nový soukromý koncový bod a název připojení privátní koncový bod přidružený k privátnímu koncovému bodu.

![Vyberte podrobnosti o předplatném a oblasti.](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Další kroky

Další informace o [virtuální síti spravovaného pracovního prostoru](./synapse-workspace-managed-vnet.md)

Další informace o [spravovaných privátních koncových bodech](./synapse-workspace-managed-private-endpoints.md)

[Vytvoření spravovaných privátních koncových bodů do zdrojů dat](./how-to-create-managed-private-endpoints.md)
