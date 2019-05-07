---
title: Připojení k Azure Preview Sentinelu datům Azure AD | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu data služby Azure Active Directory.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 357435b8a4ac396c1548c89206f269730e871f6b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204495"
---
# <a name="connect-data-from-azure-active-directory"></a>Připojení dat ze služby Azure Active Directory

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel umožňuje shromažďovat data z [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) a Streamovat do ověřovacích Azure. Můžete použít na datový proud [protokolů přihlášení](../active-directory/reports-monitoring/concept-sign-ins.md) a [protokoly auditu](../active-directory/reports-monitoring/concept-audit-logs.md) .

## <a name="prerequisites"></a>Požadavky

- Pokud chcete exportovat data přihlášení ze služby Active Directory, musí mít licenci Azure AD P1 nebo P2.

- Uživatel se globální správce nebo bezpečnostní oprávnění správce v tenantovi chcete Streamovat protokoly z.


## <a name="connect-to-azure-ad"></a>Připojení k Azure AD

1. V Azure Sentinelu, vyberte **datové konektory** a potom klikněte na tlačítko **Azure Active Directory** dlaždici.

2. Vedle chcete Streamovat do Azure ověřovacích protokolů, klikněte na tlačítko **připojit**.

6. Chcete-li použít příslušné schéma v Log Analytics pro oznámení služby Azure AD, vyhledejte **SigninLogs** a **mají**.




## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit služby Azure AD do Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
