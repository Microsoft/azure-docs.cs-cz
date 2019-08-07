---
title: Opravit doporučení v Azure Security Center | Microsoft Docs
description: Tento dokument vysvětluje, jak opravit doporučení v Azure Security Center, která vám pomůžou chránit prostředky Azure a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779001"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Oprava doporučení v Azure Security Center

Doporučení vám poskytnou návrhy na to, jak lépe zabezpečit prostředky.  Doporučení můžete implementovat podle kroků pro nápravu, které jsou uvedené v doporučení. 

## Postup odstranění problému<a name="remediation-steps"></a>

Po kontrole všech doporučení se rozhodněte, která z nich se má opravit jako první. Doporučujeme, abyste používali [dopad bezpečného skóre](security-center-recommendations.md#monitor-recommendations) , abyste lépe určili prioritu toho, co udělat jako první.

1. V seznamu klikněte na doporučení.
1. Postupujte podle pokynů v části **Postup odstranění problému** . Každé doporučení má svou vlastní sadu instrukcí. Níže vidíte postup, jak nakonfigurovat aplikace tak, aby povolovaly jenom přenosy přes protokol HTTPS.

    ![Podrobnosti doporučení](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po dokončení se zobrazí oznámení o tom, jestli se oprava úspěšně provedla.

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste si ukázali, jak opravit doporučení v Security Center. Další informace o Security Center najdete v následujících tématech:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Přečtěte si, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
