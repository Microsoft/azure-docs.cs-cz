---
title: Připojení lepší ochrany před mobilními hrozbami (MTD) do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor pro lepší ochranu před mobilními hrozbami (MTD) k vyžádání protokolů MTD do služby Azure Sentinel. Zobrazení dat MTD v sešitech, vytváření výstrah a zlepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541543"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Připojení lepší ochrany před mobilními hrozbami (MTD) do Azure Sentinel

> [!IMPORTANT]
> Konektor pro lepší ochranu před mobilními hrozbami (MTD) je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Konektor pro lepší ochranu před mobilními hrozbami (MTD) vám umožní snadno propojit všechny vaše lepší protokoly řešení zabezpečení MTD s vaší službou Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi ochranou lepší ochrany před mobilními hrozbami a používáním služby Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>Konfigurace a připojení lepší ochrany před mobilními hrozbami

LEPŠÍ MTD může integrovat a exportovat protokoly přímo do Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **lepší možnost ochrany před mobilními hrozbami (MTD) (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle kroků na stránce konektoru a na [této stránce z dokumentace k lepšímu MTD](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) k dokončení integrace v lepší konzole MTD.

    Pokud se požaduje zadání hodnot **ID pracovního prostoru** a **primárního klíče** , zkopírujte je ze stránky konektoru Sentinel Azure a vložte je do lepší konfigurace MTD.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID pracovního prostoru a primární klíč}":::

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **CustomLogs** v jedné nebo několika následujících tabulkách:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Pokud chcete zadat dotaz na lepší protokoly MTD v pravidlech analýz, loveckých dotazech nebo kdekoli jinde ve službě Azure Sentinel, v horní části okna dotazu zadejte jeden z výše uvedených názvů tabulek.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit lepší ochranu před mobilními hrozbami (MTD) do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
