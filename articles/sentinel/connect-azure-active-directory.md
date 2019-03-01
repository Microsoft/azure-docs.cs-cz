---
title: Shromažďování dat služby Azure AD ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data o Azure Active Directory v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2019
ms.author: rkarlin
ms.openlocfilehash: e145807b3170b7b822eabba09ce2e97da5467761
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993255"
---
# <a name="collect-data-from-azure-active-directory"></a>Shromažďování dat ze služby Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel umožňuje shromažďovat data z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a Streamovat do ověřovacích Azure. Můžete použít na datový proud [protokolů přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [protokoly auditu](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Požadavky

- Pokud chcete exportovat data přihlášení ze služby Active Directory, musí mít licenci Azure AD P1 nebo P2.

- Uživatel se globální správce nebo bezpečnostní oprávnění správce v tenantovi chcete Streamovat protokoly z.


## <a name="connect-to-azure-ad"></a>Připojení k Azure AD

1. V Azure Sentinelu, vyberte **shromažďování dat** a potom klikněte na tlačítko **Azure Active Directory** dlaždici.

2. Vedle chcete Streamovat do Azure ověřovacích protokolů, klikněte na tlačítko **připojit**.






## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit služby Azure AD do Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
