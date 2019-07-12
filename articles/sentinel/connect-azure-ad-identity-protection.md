---
title: Připojením dat služby Azure AD Identity Protection Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Informace o připojení k Azure Sentinelu dat služby Azure AD Identity Protection.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: c88c157c5f37bb0bd1e82225bdacfbd60806bbf8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620640"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Připojíte si data z Azure AD Identity Protection

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


1. V Azure Sentinelu, vyberte **datové konektory** a potom klikněte na tlačítko **Azure AD Identity Protection** dlaždici.

2. Klikněte na tlačítko **připojit** chcete spustit streamování událostí služby Azure AD Identity Protection do ověřovacích Azure.


6. Pokud chcete použít příslušné schéma v Log Analytics pro Azure AD Identity Protection výstrahy, hledejte **IdentityProtectionLogs_CL**.

## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu Azure AD Identity Protection. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
