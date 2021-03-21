---
title: Připojení dat mimo beSECURE zabezpečení ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor pro přenos dat beSECURE, který umožňuje načíst beSECURE protokoly do Azure Sentinel. Zobrazení dat beSECURE v sešitech, vytváření výstrah a zlepšení šetření.
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
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541154"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Připojení beSECURE k Azure Sentinel přes zabezpečení

> [!IMPORTANT]
> Konektor pro rozhraní beSECURE pro rámec zabezpečení je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Konektor pro beSECURE Security vám umožňuje snadno propojit všechna vaše protokolová řešení zabezpečení beSECURE s vaší službou Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi beSECURE a službou Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Konfigurace a připojení beSECURE

beSECURE můžete integrovat s a exportovat protokoly přímo do Azure Sentinel.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte **rámec zabezpečení beSECURE (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle následujících kroků, abyste nakonfigurovali řešení beSECURE k odesílání výsledků kontroly, sledování stavu a auditu protokolů záznamu do Azure Sentinel.

    **Přístup k nabídce integrace:**
    1. Klikněte na možnost Další.

    1. Vybrat server

    1. Vybrat integraci

    1. Povolení Azure Sentinelu

    **Zadejte beSECURE s nastaveními Sentinel Azure:**

      Zkopírujte hodnoty ID a *primárního klíče* *pracovního prostoru* na stránce konektoru Sentinel Azure, vložte je do konfigurace BeSECURE a klikněte na **Upravit**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ID pracovního prostoru a primární klíč}":::

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **CustomLogs** v jedné nebo několika následujících tabulkách:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Pokud chcete zadat dotaz na protokoly beSECURE v pravidlech analýz, loveckých dotazech, šetřeních nebo kdekoli jinde v Azure Sentinel, v horní části okna dotazu zadejte jeden z výše uvedených názvů tabulek.

## <a name="validate-connectivity"></a>Ověřit připojení
Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit beSECURE ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
