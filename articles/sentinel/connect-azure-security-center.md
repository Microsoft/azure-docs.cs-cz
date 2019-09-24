---
title: Připojení dat Azure Security Center ke službě Azure Sentinel | Microsoft Docs
description: Přečtěte si, jak připojit Azure Security Center dat ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240224"
---
# <a name="connect-data-from-azure-security-center"></a>Připojení dat z Azure Security Center





Azure Sentinel umožňuje propojit výstrahy z [Azure Security Center](../security-center/security-center-intro.md) a streamovat je do Azure Sentinel. 

## <a name="prerequisites"></a>Požadavky

- Pokud chcete exportovat výstrahy z Azure Security Center, musíte být přispěvatelem předplatného, jehož protokoly jste streamují.

- Musíte mít na předplatném spuštěnou [úroveň Standard Azure Security Center](../security-center/security-center-pricing.md) . V takovém případě [upgradujte předplatné na úroveň Standard](https://azure.microsoft.com/pricing/details/security-center/).

- Musíte se přihlásit pomocí uživatele, který má oprávnění globálního správce nebo správce zabezpečení u každého předplatného, které chcete připojit.


## <a name="connect-to-azure-security-center"></a>Připojení k Azure Security Center

1. V Azure Sentinel vyberte **datové konektory** a pak klikněte na dlaždici **Azure Security Center** .

1. V pravém rámečku klikněte na **připojit** vedle každého předplatného, jehož výstrahy chcete streamovat do Azure Sentinel. Nezapomeňte upgradovat každé předplatné na Azure Security Center úrovně Standard na streamování výstrah do Azure Sentinel.

1. Můžete vybrat, jestli chcete, aby výstrahy z Azure Security Center automaticky generovaly incidenty v rámci služby Azure Sentinel automaticky. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete povolit výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah vygenerovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analýza** a pak na **aktivní pravidla**.

3. Klikněte na **Připojit**.

4. Pokud chcete pro Azure Security Center výstrahy použít příslušné schéma v Log Analytics, vyhledejte **SecurityAlert**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit Azure Security Center ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
