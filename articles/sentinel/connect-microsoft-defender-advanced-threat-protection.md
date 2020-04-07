---
title: Připojení dat ochrany ATP programu Microsoft Defender k programu Azure Sentinel| Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data microsoft defenderu Advanced Threat Protection s Azure Sentinelem.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756352"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Připojení výstrah od programu Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> Ingestování výstrah Microsoft Defender Advanced Threat Protection je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Konektor [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umožňuje streamovat výstrahy z microsoft defenderu Advanced Threat Protection do Azure Sentinelu. To vám umožní komplexněji analyzovat události zabezpečení v celé organizaci a vytvářet playbooky pro efektivní a okamžitou reakci.

## <a name="prerequisites"></a>Požadavky

- Musíte mít platnou licenci pro pokročilou ochranu před internetovými hrozbami v programu Microsoft Defender, jak je popsáno v [nawebu Nastavení nasazení ochrany ATP v programu Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musíte být správce nebo správce zabezpečení v tenantovi Azure Sentinelu.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Připojení k rozšířené ochraně před internetovými hrozbami v programu Microsoft Defender

Pokud je nasazena pokročilá ochrana před internetovými hrozbami v programu Microsoft Defender a ingestování vašich dat, výstrahy lze snadno streamovat do Azure Sentinelu.


1. V Azure Sentinelu vyberte **Datové konektory**, klikněte na dlaždici **Microsoft Defender Advanced Threat Protection** a vyberte Otevřít **stránku konektoru**.
1. Klikněte na **Připojit**. 
1. Chcete-li použít příslušné schéma v Log Analytics pro ochrana ATP výstrahy defenderu, vyhledejte **SecurityAlert** a **název zprostředkovatele** je **MDATP**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit microsoft defender atp k Azure Sentinel. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
