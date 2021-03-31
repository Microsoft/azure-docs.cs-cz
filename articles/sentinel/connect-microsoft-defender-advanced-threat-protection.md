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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98623362"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Připojit výstrahy z programu Microsoft Defender pro koncový bod (dříve ATP. Microsoft Defender)

> [!IMPORTANT]
>
> - **Microsoft Defender pro koncové body** se dřív jmenoval jako **Rozšířená ochrana před internetovými útoky v programu Microsoft Defender** nebo **MDATP**.
>
>     Starý název se může v produktu (včetně datového konektoru v Azure Sentinel) v časovém intervalu zobrazovat i nadále.

Konektor [Microsoft Defender pro koncové body](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umožňuje streamovat výstrahy z Microsoft Defenderu na koncový bod do Azure Sentinel. To vám umožní lépe analyzovat události zabezpečení napříč vaší organizací a sestavovat playbooky pro efektivní a okamžitou reakci.

> [!NOTE]
>
> Chcete-li ingestovat nové protokoly nezpracovaných dat od programu Microsoft Defender pro [Pokročilé lov](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)koncových bodů, použijte nový konektor pro Microsoft 365 Defender (dříve Microsoft Threat Protection, [Viz dokumentace](./connect-microsoft-365-defender.md)).

## <a name="prerequisites"></a>Požadavky

- Musíte mít platnou licenci pro Microsoft Defender pro koncový bod, jak je popsáno v tématu [Nastavení programu Microsoft Defender pro nasazení koncového bodu](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Musíte být globálním správcem nebo správcem zabezpečení v tenantovi Azure Sentinel.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Připojit se k programu Microsoft Defender pro koncový bod

Pokud je Microsoft Defender pro koncový bod nasazený a ingestuje vaše data, můžete výstrahy snadno streamovat do Azure Sentinel.

1. V Azure Sentinel vyberte **datové konektory**, v galerii vyberte **Microsoft Defender pro koncový bod** (na stránce Galerie se může dál volat *Rozšířená ochrana před internetovými útoky programu Microsoft Defender*) a vyberte **stránku otevřít konektor**.

1. Klikněte na **Připojit**. 

1. Pro dotazování programu Microsoft Defender pro upozornění koncového bodu v **protokolech** zadejte **SecurityAlert** do okna dotazu a přidejte filtr, kde je **název poskytovatele** **MDATP**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Microsoft Defender ke koncovému bodu do Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](./tutorial-detect-threats-built-in.md).