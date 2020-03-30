---
title: Připojení dat ochrany ATP v programu Azure k Azure Sentinel| Dokumenty společnosti Microsoft
description: Zjistěte, jak připojit data ochrany ATP v programu Azure k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588582"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Připojení dat z Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Datový konektor Azure Advanced Threat Protection v Azure Sentinelu je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Protokoly z [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) můžete streamovat do Azure Sentinelu jediným kliknutím.

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Musíte být zákazníkem ochrany ATP v programu Azure a povolit integraci mezi ochrany ATP v programu Azure a zabezpečení aplikací Microsoft Cloud. Další informace naleznete v [tématu Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Připojení k ochrany ATP v programu Azure

Ujistěte se, že je [v síti povolena](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)verze preview ochrany ATP v programu Azure .
Pokud se nasadí tekutá hodnota ochrany ATP v oblasti Azure a bude ingestovat vaše data, podezřelé výstrahy lze snadno streamovat do Azure Sentinelu. Může trvat až 24 hodin, než se výstrahy spustí do Azure Sentinelu.


1. Chcete-li připojit ochrany ATP v programu Azure k Azure Sentinelu, musíte nejprve povolit integraci mezi ochrany ATP v programu Azure a zabezpečení aplikací Microsoft Cloud. Informace o tom, jak to provést, naleznete [v tématu Azure Advanced Threat Protection integrace](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. V Azure Sentinelu vyberte **datové konektory** a klikněte na dlaždici **Azure Advanced Threat Protection (Preview).**

1. Můžete si vybrat, zda chcete, aby výstrahy z ochrany ATP v systému Azure automaticky generovat incidenty v Azure Sentinel. V části **Vytvořit incidenty** vyberte **Povolit,** chcete-li povolit výchozí analytické pravidlo, které automaticky vytváří incidenty z výstrah generovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analytics** a potom **v části Aktivní pravidla**.

1. Klikněte na **Připojit**.

1. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy ochrany ATP v programu Azure, vyhledejte **Výstrahu zabezpečení**.

> [!NOTE]
> Pokud výstrahy jsou větší než 30 KB, Azure Sentinel zastaví zobrazení entity pole ve výstrahách.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Azure Advanced Threat Protection k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).

