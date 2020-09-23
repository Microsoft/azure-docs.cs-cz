---
title: Zjištění pokročilých hrozeb pomocí analýzy chování entit | Microsoft Docs
description: Povolení analýzy chování uživatelů a entit ve službě Azure Sentinel a konfigurace zdrojů dat
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995494"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Povolení analýzy chování uživatelů a entit (UEBA) v Azure Sentinel 



## <a name="prerequisites"></a>Požadavky

- Aby bylo možné povolit nebo zakázat UEBA, musíte být vaším uživatelem přiřazeni role **globálního správce** nebo **Správce zabezpečení** ve službě Azure AD, ale ne ho spouštět.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Jak povolit analýzu chování uživatelů a entit

1. V nabídce navigace v Azure Sentinel vyberte **chování entity (Preview)**.

1. V části **zapnout**v záhlaví přepněte přepínač na **zapnuto**.

1. Klikněte na tlačítko **Vybrat zdroje dat** .

1. V podokně **Výběr zdroje dat** označte zaškrtávací políčka vedle zdrojů dat, u kterých chcete povolit UEBA, a pak vyberte **použít**.

    > [!NOTE]
    >
    > V dolní polovině podokna **výběru zdroje dat** se zobrazí seznam zdrojů dat podporovaných UEBA, které jste ještě nepovolili. 
    >
    > Po povolení UEBA budete mít při připojování nových zdrojů dat možnost, abyste je povolili pro UEBA přímo z podokna datový konektor, pokud jsou schopné UEBA.

1. Vyberte **Přejít na vyhledávání entit**. Tím přejdete k podoknu hledání entit, který od této chvíle bude zobrazený při výběru **chování entity** z hlavní nabídky Sentinel Azure.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak ve službě Azure Sentinel povolit a nakonfigurovat analýzu chování uživatelů a entit (UEBA). Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
