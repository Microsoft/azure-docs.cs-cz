---
title: Připojení dat zabezpečení cloudových aplikací k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak propojit data cloudového zabezpečení aplikací s Azure Sentinelem.
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
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422147"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojení dat ze zabezpečení aplikací Microsoft Cloud App Security 



Konektor [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) umožňuje streamovat výstrahy a [protokoly Cloud Discovery](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) z MCAS do Azure Sentinelu. To vám umožní získat přehled o vašich cloudových aplikacích, získat sofistikované analýzy pro identifikaci a boj proti kybernetickým hrozbám a řídit způsob, jakým vaše data cestují.

## <a name="prerequisites"></a>Požadavky

- Uživatel musí mít oprávnění ke čtení a zápisu do pracovního prostoru.
- Uživatel musí mít oprávnění globálního správce nebo správce zabezpečení v tenantovi pracovního prostoru.
- Pokud chcete streamovat protokoly Cloud Discovery do Azure Sentinelu, [povolte Azure Sentinel jako SIEM v Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Ingestování protokolů Zjišťování cloudu je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Připojení k zabezpečení cloudových aplikací

Pokud už cloudové zabezpečení aplikací máte, zkontrolujte, zda je [v síti povolená](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Pokud cloudové zabezpečení aplikací se nasadí a ingestuje vaše data, data výstrah lze snadno streamovat do Azure Sentinelu.


1. V navigační nabídce Azure Sentinel vyberte **Datové konektory**. V seznamu konektorů klikněte na dlaždici **Microsoft Cloud App Security** a potom na tlačítko Otevřít **stránku konektoru** v pravém dolním.

1. Vyberte protokoly, které chcete streamovat do Azure Sentinelu; můžete zvolit **výstrahy** a **protokoly zjišťování cloudu** (náhled). 

1. Klepněte na **tlačítko Použít změny**.

1. Chcete-li použít příslušné schéma v Log Analytics pro `SecurityAlert` výstrahy Cloud App Security, zadejte okno dotazu. Pro schéma protokolů cloudového zjišťování `McasShadowItReporting`zadejte .

> [!NOTE]
> Cloud Discovery pomáhá zjišťovat a identifikovat trendy agregací dat, na kterých jsou uživatelé připojení, do cloudových aplikací.
>
> Vzhledem k tomu, že data cloudového zjišťování se agregují na základě denní hodu, uvědomte si, že v Azure Sentinelu se neprojeví až 24 hodin nejnovějších dat. V případě, že šetření na nízké úrovni vyžaduje bezprostřednější údaje, mělo by být provedeno přímo ve zdrojovém zařízení nebo službě, kde se nezpracovaná data nacházejí.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Microsoft Cloud App Security k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Můžete začít zjišťovat hrozby pomocí Azure Sentinelu pomocí [integrovaných](tutorial-detect-threats.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.
