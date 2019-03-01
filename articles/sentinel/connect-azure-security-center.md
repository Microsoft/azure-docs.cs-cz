---
title: Shromažďování dat v Azure Security Center ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data o Azure Security Center v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 1770d5a1fd5598972e5966ad041d35b4a9c9138d
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993129"
---
# <a name="collect-data-from-azure-security-center"></a>Shromažďování dat ze služby Azure Security Center

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Azure Sentinel umožňuje shromažďovat výstrahy z [Azure Security Center](../security-center/security-center-intro.md) a Streamovat je do ověřovacích Azure. 

## <a name="prerequisites"></a>Požadavky

- Pokud chcete exportovat oznámení ze služby Azure Security Center, musí být přispěvatele pro předplatné, jehož protokoly budete Streamovat.

- Musíte mít [Azure Security Center úrovně Standard](../security-center/security-center-pricing.md) spuštěna v rámci předplatného. V opačném případě [své předplatné upgradovat na standard](https://azure.microsoft.com/pricing/details/security-center/).

- Musíte přihlásit jako uživatel, který má oprávnění správce zabezpečení nebo globální správce na každé předplatné, které chcete připojit.


## <a name="connect-to-azure-security-center"></a>Připojení ke službě Azure Security Center

1. V Azure Sentinelu, vyberte **shromažďování dat** a potom klikněte na tlačítko **Azure Security Center** dlaždici.
1. Na pravé straně klikněte na **připojit** vedle každého předplatného, jejíž výstrahy chcete Streamovat do ověřovacích Azure. Ujistěte se, že pro každé předplatné upgradovat na Azure Security Center úrovně Standard na datový proud výstrahy k Sentinelu Azure.

3. Klikněte na **Připojit**.

4. Použít příslušné schéma v Log Analytics pro výstrahy Azure Security Center, vyhledejte **SecurityEvent**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Azure Security Center. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
