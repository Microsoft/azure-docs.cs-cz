---
title: Připojení dat Azure Security Center k Azure Sentinelu
description: Přečtěte si, jak propojit data Azure Security Center s Azure Sentinelem.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588531"
---
# <a name="connect-data-from-azure-security-center"></a>Připojení dat z Azure Security Center





Azure Sentinel umožňuje připojit výstrahy z [Azure Security Center](../security-center/security-center-intro.md) a streamovat je do Azure Sentinelu. 

## <a name="prerequisites"></a>Požadavky

- Chcete-li exportovat výstrahy z Azure Security Center, musíte mít roli Čtečka zabezpečení v předplatném protokolů, které streamujete.

- V rámci předplatného musíte mít spuštěnou [úroveň Azure Security Center Standard.](../security-center/security-center-pricing.md) Pokud ne, [upgradujte předplatné na standardní](https://azure.microsoft.com/pricing/details/security-center/).



## <a name="connect-to-azure-security-center"></a>Připojení k Azure Security Center

1. V Azure Sentinelu vyberte **Datové konektory** a klikněte na dlaždici **Azure Security Center.**

1. Vpravo klikněte na **Připojit** vedle každého předplatného, jehož výstrahy, které chcete streamovat do Azure Sentinelu. Nezapomeňte upgradovat každé předplatné na úroveň Azure Security Center Standard, abyste streamovali výstrahy do Azure Sentinelu.

1. Můžete si vybrat, jestli chcete, aby výstrahy z Azure Security Center automaticky generovat incidenty v Azure Sentinelu automaticky. V části **Vytvořit incidenty** vyberte **Povolit,** chcete-li povolit výchozí analytické pravidlo, které automaticky vytváří incidenty z výstrah generovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analytics** a potom **v části Aktivní pravidla**.

3. Klikněte na **Připojit**.

4. Chcete-li použít příslušné schéma v Log Analytics pro výstrahy Azure Security Center, vyhledejte **SecurityAlert**.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit Azure Security Center k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
