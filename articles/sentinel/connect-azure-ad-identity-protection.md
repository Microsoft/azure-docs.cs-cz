---
title: Připojení dat azure ad identity protection k Azure Sentinelu
description: Zjistěte, jak propojit data Azure AD Identity Protection s Azure Sentinelem.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588565"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Připojení dat ze služby Azure AD Identity Protection



Protokoly z [Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) do Azure Sentinelu můžete streamovat a streamovat výstrahy do Azure Sentinelu za účelem zobrazení řídicích panelů, vytvoření vlastních výstrah a zlepšení vyšetřování. Azure Active Directory Identity Protection poskytuje konsolidované zobrazení u rizikových uživatelů, detekce rizik a zranitelnosti, se schopností okamžitě napravit riziko a nastavit zásady pro automatické nazamování budoucích událostí. Služba je postavena na zkušenostech společnosti Microsoft s ochranou identit spotřebitelů a získává obrovskou přesnost ze signálu z více než 13 miliard přihlášení denně. 


## <a name="prerequisites"></a>Požadavky

- Musíte mít [licenci Azure Active Directory Premium P1 nebo P2.](https://azure.microsoft.com/pricing/details/active-directory/)
- Uživatel s oprávněními globálního správce nebo správce zabezpečení


## <a name="connect-to-azure-ad-identity-protection"></a>Připojení k ochraně identity Azure AD

Pokud už máte Azure AD Identity Protection, ujistěte se, že je [povolená ve vaší síti](../active-directory/identity-protection/overview-identity-protection.md).
Pokud je nasaditá Azure AD Identity Protection a získávání dat, data výstrah lze snadno streamovat do Azure Sentinelu.


1. V Azure Sentinelu vyberte **datové konektory** a klikněte na dlaždici **Azure AD Identity Protection.**

2. Kliknutím na **Připojit** spusťte streamování událostí Azure AD Identity Protection do Azure Sentinelu.


6. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy ochrany identity Azure AD, vyhledejte **Výstrahu zabezpečení**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Azure AD Identity Protection k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
