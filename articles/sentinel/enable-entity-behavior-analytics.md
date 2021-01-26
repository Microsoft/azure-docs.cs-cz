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
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 0dccd8ac4f852688bf7e59e7ed96c4458c08d18b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784724"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Povolení analýzy chování uživatelů a entit (UEBA) v Azure Sentinel 

> [!IMPORTANT]
>
> - Funkce stránek UEBA a entity jsou teď **obecně dostupné** ve *_všech_* geografických oblastech a oblastech Sentinel Azure.

## <a name="prerequisites"></a>Požadavky

Chcete-li povolit nebo zakázat tuto funkci (tyto požadavky nejsou nutné k použití této funkce):

- Uživatel musí být členem Azure Active Directory vaší organizace, a ne uživatelem typu Host.

- Uživateli musí být přiřazeni role správce účtu (*globální správce*) * nebo **Správce zabezpečení** ve službě Azure AD.

- Uživateli musí být přiřazen alespoň jeden z následujících **rolí Azure** ([Další informace o službě Azure RBAC](roles.md)):
    - **Přispěvatel Sentinel Azure** na úrovni pracovního prostoru nebo skupiny prostředků.
    - **Přispěvatel Log Analytics** na úrovni skupiny prostředků nebo předplatného.

- V pracovním prostoru nesmí být použité žádné zámky prostředků Azure. [Přečtěte si další informace o uzamykání prostředků Azure](../azure-resource-manager/management/lock-resources.md).

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Jak povolit analýzu chování uživatelů a entit

1. V navigační nabídce Azure Sentinel vyberte **chování entity**.

1. V části **zapnout** v záhlaví přepněte přepínač na **zapnuto**.

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
