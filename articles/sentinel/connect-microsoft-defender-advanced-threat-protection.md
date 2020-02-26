---
title: Připojit data ATP Microsoft Defenderu ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak propojit data rozšířené ochrany před internetovými útoky z Microsoft Defenderu do Azure Sentinel.
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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588208"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Připojení upozornění z rozšířené ochrany před internetovými útoky v programu Microsoft Defender 


> [!IMPORTANT]
> Ingestování protokolů rozšířené ochrany před internetovými útoky v programu Microsoft Defender je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Pomocí jediného kliknutí můžete streamovat výstrahy z [rozšířené ochrany před internetovými útoky v programu Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) do Azure Sentinel. Toto připojení umožňuje streamovat výstrahy z rozšířené ochrany před internetovými útoky v programu Microsoft Defender do Azure Sentinel. 

## <a name="prerequisites"></a>Předpoklady

- Platná licence pro Microsoft Defender Advanced Threat Protection, která je povolená, jak je popsáno v tématu [Ověření zřízení licencí a kompletní nastavení pro Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musíte být správce nebo správce zabezpečení v tenantovi Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Připojení k Rozšířené ochraně před internetovými útoky v programu Microsoft Defender

Pokud je nainstalovaná Rozšířená ochrana před internetovými útoky v programu Microsoft Defender a ingestuje vaše data, můžou se výstrahy do Azure Sentinel snadno streamovat.


1. V Azure Sentinel vyberte **datové konektory**, klikněte na dlaždici **Rozšířená ochrana před internetovými útoky v programu Microsoft Defender** a vyberte **otevřít konektor stránky**.
1. Klikněte na **Připojit**. 
1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy ATP ATP, vyhledejte **SecurityAlert** a **název poskytovatele** je **MDATP**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak propojit službu Microsoft Defender ATP s Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
