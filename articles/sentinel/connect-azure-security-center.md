---
title: Připojení dat Azure Security Center k Azure Sentinel
description: Naučte se, jak propojit výstrahy z úrovně Standard Azure Security Center (ASC) a streamovat je do Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559158"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Připojení dat z Azure Security Center (ASC)

Azure Sentinel umožňuje propojit výstrahy z [Azure Security Center](../security-center/security-center-intro.md) a streamovat je do Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Pokud chcete exportovat výstrahy z Azure Security Center, musíte mít v předplatném zasílaných protokolů roli čtenáře zabezpečení.

- Musíte mít na předplatném spuštěnou [úroveň Standard Azure Security Center](../security-center/security-center-pricing.md) . V takovém případě [upgradujte předplatné na úroveň Standard](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="connect-to-azure-security-center"></a>Připojení k Azure Security Center

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Z Galerie datových konektorů vyberte **Azure Security Center**a klikněte na tlačítko **otevřít stránku konektoru** .

1. V části **Konfigurace**klikněte na **připojit** vedle každého předplatného, jehož výstrahy chcete streamovat do Azure Sentinel. Tlačítko připojit bude k dispozici jenom v případě, že máte požadovaná oprávnění a předplatné úrovně Standard ASC.

1. Můžete vybrat, jestli chcete, aby výstrahy z Azure Security Center automaticky generovaly incidenty ve službě Azure Sentinel. V části **vytvořit incidenty**vyberte **povoleno** , pokud chcete zapnout výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah. Toto pravidlo pak můžete upravit v části **Analýza**na kartě **aktivní pravidla** .

1. Pokud chcete pro Azure Security Center výstrahy použít příslušné schéma v Log Analytics, vyhledejte **SecurityAlert**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Security Center ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
