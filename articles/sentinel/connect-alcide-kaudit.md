---
title: Připojení dat Alcide kAudit k Azure Sentinel | Microsoft Docs
description: Naučte se připojit data Alcide kAudit k Azure Sentinel.
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
ms.openlocfilehash: cf8da1d88529a823ff4399fb955c8a5e0abbd20e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87038235"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Připojení Alcide kAudit k Azure Sentinel

> [!IMPORTANT]
> Datový konektor Alcide kAudit v Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) pomáhá identifikovat chování neobvyklé Kubernetes a soustředit se na Kubernetes porušení a incidenty při zkrácení doby detekce. Tento článek vysvětluje, jak připojit řešení Alcide kAudit k Sentinel Azure. Datový konektor Alcide kAudit umožňuje snadno přenést data protokolu kAudit do služby Azure Sentinel, takže ji můžete zobrazit v sešitech, použít ji k vytvoření vlastních výstrah a začlenit je k vylepšení šetření. Integrace mezi Alcide kAudit a službou Azure Sentinel využívá REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pro pracovní prostor musíte mít oprávnění ke čtení sdílených klíčů.

## <a name="configure-and-connect-alcide-kaudit"></a>Konfigurace a připojení Alcide kAudit

Alcide kAudit může exportovat protokoly přímo do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte v navigační nabídce na **datové konektory** .

1. Z Galerie vyberte **Alcide kAudit** a pak klikněte na tlačítko **otevřít stránku konektoru** .

1. Postupujte podle podrobných pokynů uvedených v [příručce pro instalaci Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Po zobrazení výzvy k zadání ID pracovního prostoru a primárního klíče je můžete zkopírovat ze stránky Alcide kAudit data Connector.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="ID a primární klíč pracovního prostoru":::

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v následujících datových typech v **CustomLogs**:

- detekce kAudit **alcide_kaudit_detections_1_CL** Alcide 
- protokoly aktivit **alcide_kaudit_activity_1_CL** Alcide kaudit
- počty aktivit **alcide_kaudit_selections_count_1_CL** Alcide kaudit
- Podrobnosti o aktivitě **alcide_kaudit_selections_details_1_CL** Alcide kaudit

Chcete-li použít příslušné schéma v protokolech pro Alcide kAudit, vyhledejte datové typy uvedené výše.

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Alcide kAudit ke službě Azure Sentinel. Pokud chcete plně využít možnosti integrované s tímto datovým konektorem, klikněte na kartě **Další kroky** na stránce datový konektor. Najdete tu předem připravené Ukázkové dotazy, abyste mohli začít vyhledávat užitečné informace.

Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
