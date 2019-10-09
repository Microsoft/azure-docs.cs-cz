---
title: Delegování zásad správného řízení přístupu k tvůrcům katalogu ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Naučte se delegovat řízení přístupu od správců IT na tvůrce katalogu a vedoucích projektů, aby mohli spravovat přístup sami.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170760"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Delegování zásad správného řízení přístupu k tvůrcům katalogu ve správě nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.
> Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Chcete-li delegovat na uživatele, kteří nejsou správci, aby mohli vytvářet své vlastní katalogy, můžete tyto uživatele přidat do role tvůrce katalogu na základě správy nároků služby Azure AD. Můžete přidat jednotlivé uživatele nebo můžete přidat skupinu, jejíž členové pak mohou vytvářet katalogy.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako správce IT delegovat na tvůrce katalogu

Pomocí těchto kroků přiřaďte uživatele k roli tvůrce katalogu.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V levé nabídce v části **Správa nároků** klikněte na **Nastavení**.

1. Klikněte na **Upravit**.

    ![Nastavení pro přidání tvůrců katalogu](./media/entitlement-management-delegate/settings-delegate.png)

1. V části **Správa nároků delegáta** klikněte na **Přidat tvůrci katalogu** a vyberte uživatele nebo skupiny, kterým chcete delegovat tuto roli správy oprávnění.

1. Klikněte na **Vybrat**.

1. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

- [Vytvoření a správa katalogu prostředků](entitlement-management-catalog-create.md)
- [Zásady správného řízení přístupu pro přístup k správcům balíčků](entitlement-management-delegate-managers.md)

