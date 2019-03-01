---
title: Shromažďování dat služby Azure AD Identity Protection ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data služby Azure AD Identity Protection v nástroji Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 02057fc0f288c8ae77a700f09a8b6e599f6d1b16
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993227"
---
# <a name="collect-data-from-azure-ad-identity-protection"></a>Shromažďování dat z Azure AD Identity Protection

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete Streamovat protokoly z [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do Azure Sentinelu výstrah datový proud do Azure Sentinelu zobrazení řídicích panelů, vytvářet vlastní výstrahy a zlepší. Azure Active Directory Identity Protection poskytuje ucelený pohled na rizikové uživatele, rizikové události a ohrožení zabezpečení, s možností okamžitě riziko snížit a nastavit zásady, které automaticky opravovaly i budoucí události. Služba je založená na zkušenostech Microsoftu s ochranou identit spotřebitelů a díky z signál z více než 13 miliard odcizí denně nabízí obrovskou přesnost. 


## <a name="prerequisites"></a>Požadavky

- Musíte mít [licence Azure Active Directory Premium P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory/)
- Uživatel se oprávnění správce zabezpečení nebo globální správce


## <a name="connect-to-azure-ad-identity-protection"></a>Připojení k Azure AD Identity Protection

Pokud už máte Azure AD Identity Protection, ujistěte se, že je [povolena ve vaší síti](../active-directory/identity-protection/enable.md).
Pokud je nasazení služby Azure AD Identity Protection a získávání dat, data upozornění snadno Streamovat do ověřovacích Azure.


1. V Azure Sentinelu, vyberte **shromažďování dat** a potom klikněte na tlačítko **Azure AD Identity Protection** dlaždici.

2. Klikněte na tlačítko **připojit** chcete spustit streamování událostí služby Azure AD Identity Protection do ověřovacích Azure.


6. Pokud chcete použít příslušné schéma v Log Analytics pro Azure AD Identity Protection výstrahy, hledejte **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Azure AD Identity Protection. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
