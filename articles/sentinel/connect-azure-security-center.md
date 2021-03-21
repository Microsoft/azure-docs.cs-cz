---
title: Připojení dat z Azure Defenderu k Azure Sentinel
description: Naučte se, jak propojit výstrahy v programu Azure Defender z Azure Security Center a streamovat je do Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633040"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Připojit data výstrah v programu Azure Defender z Azure Security Center

Pomocí konektoru výstrah v programu Azure Defender ingestujte výstrahy Azure Defenderu od [Azure Security Center](../security-center/security-center-introduction.md) a Streamujte je do Azure Sentinel. 

## <a name="prerequisites"></a>Předpoklady

- Uživatel musí mít roli Čtenář zabezpečení v rámci předplatného zasílaných protokolů.

- V Azure Security Center budete muset povolit Azure Defender. (Úroveň Standard už neexistuje a už není licenčním požadavkem.)

## <a name="connect-to-azure-defender"></a>Připojení k Azure Defenderu

1. V Azure Sentinel vyberte **datové konektory** z navigační nabídky.

1. Z Galerie datových konektorů vyberte **výstrahy v Azure Defenderu od ASC** (může se dál volat Azure Security Center) a klikněte na tlačítko **otevřít stránku konektoru** .

1. V části **Konfigurace** klikněte na **připojit** vedle každého předplatného, jehož výstrahy chcete streamovat do Azure Sentinel. Tlačítko připojit bude k dispozici pouze v případě, že máte požadovaná oprávnění.

1. Můžete vybrat, jestli chcete, aby upozornění z Azure Defenderu automaticky generovala incidenty ve službě Azure Sentinel. V části **vytvořit incidenty** vyberte **povoleno** , pokud chcete zapnout výchozí analytické pravidlo, které automaticky vytvoří incidenty z výstrah. Toto pravidlo pak můžete upravit v části **Analýza** na kartě  **aktivní pravidla** .

1. Pokud chcete použít příslušné schéma v Log Analytics pro výstrahy Azure Defenderu, vyhledejte **SecurityAlert**.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Defender k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
