---
title: Připojit data o hraniční 81 k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data hraničních 81 k Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093093"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>Připojení protokolů aktivit hraničního 81 k Azure Sentinel

> [!IMPORTANT]
> Datový konektor hraničního 81 ve službě Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak připojit zařízení [protokolu hraničního 81 s protokolem](https://www.perimeter81.com/) do Azure Sentinel. Konektor pro protokoly aktivit hraničního prostředí 81 umožňuje snadno převést data hraničních 81 do Azure Sentinel, abyste je mohli zobrazit v sešitech, použít je k vytváření vlastních výstrah a začlenit je ke zlepšení šetření.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>Konfigurace a připojení protokolů aktivit hraničního 81

Protokoly aktivit hraničního 81 mohou integrovat a exportovat protokoly přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte v navigační nabídce na **datové konektory** .

1. Z Galerie vyberte **protokoly aktivit hraničního 81** a potom klikněte na tlačítko **otevřít stránku konektoru** .

1. Na stránce konektor protokolů aktivit hraničního 81 zkopírujte ID a **primární klíč** **pracovního prostoru** a vložte je do hraničního 81 [podle pokynů uvedených tady](https://support.perimeter81.com/hc/en-us/articles/360012680780).

1. Po dokončení pokynů se na stránce konektoru služby Azure Sentinel zobrazí připojené datové typy.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **CustomLogs**  -  **Perimeter81_CL**.

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak propojit protokoly aktivit hraničního 81 do Azure Sentinel. Pokud chcete plně využít možnosti integrované s tímto datovým konektorem, klikněte na kartě **Další kroky** na stránce datový konektor. Najdete tu připravený sešit a některé ukázkové dotazy, abyste mohli začít s hledáním užitečných informací.

Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
