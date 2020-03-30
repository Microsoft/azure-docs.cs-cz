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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588361"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Připojení dat ze zabezpečení aplikací Microsoft Cloud App Security 



Můžete datové proudy protokolů z [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) do Azure Sentinelu s jedním kliknutím. Toto připojení umožňuje streamovat výstrahy z Cloud App Security do Azure Sentinelu. 

## <a name="prerequisites"></a>Požadavky

- Uživatel s oprávněními globálního správce nebo správce zabezpečení
- Pokud chcete streamovat protokoly Cloud Discovery do Azure Sentinelu, [povolte Azure Sentinel jako SIEM v Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Ingestování protokolů Zjišťování cloudu je aktuálně ve verzi Public Preview.
> Tato funkce je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Připojení k zabezpečení cloudových aplikací

Pokud už cloudové zabezpečení aplikací máte, zkontrolujte, zda je [v síti povolená](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Pokud cloudové zabezpečení aplikací se nasadí a ingestuje vaše data, data výstrah lze snadno streamovat do Azure Sentinelu.


1. V Azure Sentinelu vyberte **Datové konektory**, klikněte na dlaždici **Cloud App Security** a vyberte Otevřít **stránku konektoru**.

1. Vyberte, které protokoly chcete streamovat do Azure Sentinelu, můžete zvolit **výstrahy** a **protokoly cloudového zjišťování** (náhled). 

1. Klikněte na **Připojit**.

1. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy zabezpečení cloudových aplikací, vyhledejte **SecurityAlert**.




## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Microsoft Cloud App Security k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats.md).
