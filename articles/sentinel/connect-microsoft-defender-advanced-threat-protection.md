---
title: Připojit Microsoft Defender pro data koncového bodu do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data programu Microsoft Defender pro koncový bod (dříve ATP. Microsoft Defender) do Azure Sentinel.
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
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657536"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Připojit výstrahy z programu Microsoft Defender pro koncový bod (dříve ATP. Microsoft Defender) 


> [!IMPORTANT]
> Ingestování programu Microsoft Defender pro upozornění koncového bodu je momentálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Konektor [Microsoft Defender pro koncové body](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umožňuje streamovat výstrahy z Microsoft Defenderu na koncový bod do Azure Sentinel. To vám umožní lépe analyzovat události zabezpečení napříč vaší organizací a sestavovat playbooky pro efektivní a okamžitou reakci.

## <a name="prerequisites"></a>Požadavky

- Musíte mít platnou licenci pro Microsoft Defender pro koncový bod, jak je popsáno v tématu [Nastavení programu Microsoft Defender pro nasazení koncového bodu](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musíte být správce nebo správce zabezpečení v tenantovi Azure Sentinel.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Připojit se k programu Microsoft Defender pro koncový bod

Pokud je Microsoft Defender pro koncový bod nasazený a ingestuje vaše data, můžete výstrahy snadno streamovat do Azure Sentinel.


1. V Azure Sentinel vyberte **datové konektory**, v galerii vyberte **Microsoft Defender pro koncový bod** (na stránce Galerie se může dál volat Rozšířená ochrana před internetovými útoky programu Microsoft Defender) a vyberte **stránku otevřít konektor**.
1. Klikněte na **Připojit**. 
1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy ATP ATP, vyhledejte **SecurityAlert** a **název poskytovatele** je **MDATP**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Microsoft Defender ke koncovému bodu do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
