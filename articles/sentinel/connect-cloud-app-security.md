---
title: Připojit Cloud App Security dat ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Cloud App Security dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: 32e7e79465b68caca9a636019f65b9f08284fc72
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928220"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojení dat z Microsoft Cloud App Security 



Pomocí jediného kliknutí můžete streamovat protokoly z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do Azure Sentinel. Toto připojení umožňuje streamovat výstrahy z Cloud App Security do Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Pokud chcete streamovat Cloud Discovery protokoly do Azure Sentinel, [Povolte jako Siem v Microsoft Cloud App Security možnost Sentinel Azure](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Přijímání protokolů Cloud Discovery je v současnosti ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Připojení k Cloud App Security

Pokud už máte Cloud App Security, ujistěte se, že je [ve vaší síti povolená](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Pokud je Cloud App Security nasazená a ingestují vaše data, můžou se data výstrah do Azure Sentinel snadno streamovat.


1. V Azure Sentinel vyberte **datové konektory**, klikněte na dlaždici **Cloud App Security** a vyberte **otevřít konektor**.

1. Vyberte protokoly, které chcete streamovat do Azure Sentinel, a můžete zvolit **výstrahy** a **protokoly Cloud Discovery** (Preview). 

1. Klikněte na **Připojit**.

1. Pokud chcete pro Cloud App Security výstrahy použít příslušné schéma v Log Analytics, vyhledejte **SecurityAlert**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Microsoft Cloud App Security ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
