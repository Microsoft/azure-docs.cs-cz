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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102834"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Připojení beSECURE k Azure Sentinel přes zabezpečení

> [!IMPORTANT]
> Data konektoru služby beSECURE nad rámec zabezpečení dat v Azure Sentinel je momentálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Rámec Security beSECURE Connector vám umožní snadno propojit všechna vaše protokolová řešení zabezpečení beSECURE s vaší službou Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi beSECURE a službou Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-besecure"></a>Konfigurace a připojení beSECURE

beSECURE můžete integrovat s a exportovat protokoly přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte **mimo BeSECURE zabezpečení (Preview)** a pak **otevřete stránku konektor** .

1. Postupujte podle následujících kroků, abyste nakonfigurovali řešení beSECURE k odesílání výsledků kontroly, sledování stavu a auditu protokolů záznamu do Azure Sentinel.

    **Přístup k nabídce integrace:**
    1. Klikněte na možnost Další.

    1. Vybrat server

    1. Vybrat integraci

    1. Povolení Azure Sentinelu

    **Zadejte beSECURE s nastavením Sentinel Azure.**
      - Zkopírujte hodnoty ID a *primárního klíče* *pracovního prostoru* na stránce konektoru Sentinel Azure, vložte je do konfigurace BeSECURE a klikněte na **Upravit** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **CustomLogs** v jedné nebo několika následujících tabulkách:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Pokud chcete zadat dotaz na protokoly beSECURE v Log Analytics, zadejte v horní části okna dotazu jeden z výše uvedených názvů tabulek.

## <a name="validate-connectivity"></a>Ověřit připojení
Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit beSECURE ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
