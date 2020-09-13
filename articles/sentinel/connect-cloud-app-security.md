---
title: Připojit Cloud App Security dat ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat konektor Microsoft Cloud App Security (MCAS) ke streamování výstrah a Cloud Discovery protokolů z MCAS do Azure Sentinel. 
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 6be2805cab1bcaadb7878be69399e14ffe59d7b0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659734"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojení dat z Microsoft Cloud App Security 

Konektor [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) umožňuje streamovat výstrahy a [protokoly Cloud Discovery](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) z MCAS do služby Azure Sentinel. To vám umožní získat přehled o vašich cloudových aplikacích, získávat sofistikované analýzy, které vám pomůžou identifikovat a bojovat proti týká kybernetických hrozeb a řídit, jak se data cestují.

## <a name="prerequisites"></a>Požadavky

- Váš uživatel musí mít v pracovním prostoru oprávnění ke čtení a zápisu.
- Váš uživatel musí mít oprávnění globálního správce nebo správce zabezpečení v tenantovi pracovního prostoru.
- Pokud chcete streamovat Cloud Discovery protokoly do Azure Sentinel, [Povolte jako Siem v Microsoft Cloud App Security možnost Sentinel Azure](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Přijímání protokolů Cloud Discovery je v současnosti ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Připojení k Cloud App Security

Pokud už máte Cloud App Security, ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Pokud je Cloud App Security nasazená a ingestují vaše data, můžou se data výstrah do Azure Sentinel snadno streamovat.


1. V navigační nabídce Azure Sentinel vyberte **datové konektory**. V seznamu konektorů klikněte na dlaždici **Microsoft Cloud App Security** a pak na pravé straně tlačítko **otevřít konektor** .

1. Vyberte protokoly, které chcete streamovat do Azure Sentinel; můžete zvolit **výstrahy** a **protokoly Cloud Discovery** (Preview). 

1. Klikněte na **použít změny**.

1. Můžete vybrat, jestli chcete, aby výstrahy z Azure Defenderu (dříve Azure Security Center) automaticky generovaly incidenty v Azure Sentinel. V části **vytvořit incidenty**vyberte **povoleno** , pokud chcete zapnout výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah. Toto pravidlo pak můžete upravit v části **Analýza**na kartě  **aktivní pravidla** .

1. Pokud chcete použít příslušné schéma v Log Analytics pro Cloud App Security výstrahy, zadejte `SecurityAlert` do okna dotazu. Do pole Cloud Discovery protokoly schématu zadejte `McasShadowItReporting` .

> [!NOTE]
> Cloud Discovery pomáhá detekovat a identifikovat trendy tím, že agreguje data základních uživatelů ke cloudovým aplikacím.
>
> Vzhledem k tomu, že Cloud Discovery Data jsou agregována každý den, mějte na paměti, že v rámci služby Azure Sentinel se neprojeví až 24 hodin nejaktuálnějších dat. V případě, že vyšetřování nízké úrovně vyžaduje dobezprostředníná data, je třeba je provést přímo ve zdrojovém zařízení nebo službě, kde se nacházejí nezpracovaná data.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Microsoft Cloud App Security ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.
