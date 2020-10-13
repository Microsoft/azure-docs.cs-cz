---
title: Delegování zásad správného řízení přístupu k tvůrcům katalogu ve správě nároků Azure AD – Azure Active Directory
description: Naučte se delegovat řízení přístupu od správců IT na tvůrce katalogu a vedoucích projektů, aby mohli spravovat přístup sami.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41f372cd0105896765d5a267456b6bda767dd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798422"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegování zásad správného řízení přístupu tvůrcům katalogu v Azure AD – Správa nároků

Katalog je kontejner prostředků a přístup k balíčkům. Katalog můžete vytvořit v případě, že chcete seskupit související prostředky a přistupovat k balíčkům. Ve výchozím nastavení může globální správce nebo Správce uživatelů [vytvořit katalog](entitlement-management-catalog-create.md)a může přidat další uživatele jako vlastníky katalogu.

Chcete-li delegovat na uživatele, kteří nejsou správci, aby mohli vytvářet své vlastní katalogy, můžete tyto uživatele přidat do role tvůrce katalogu na základě správy nároků služby Azure AD. Můžete přidat jednotlivé uživatele nebo můžete přidat skupinu, jejíž členové pak mohou vytvářet katalogy.  Po vytvoření katalogu pak můžou přidat prostředky, které vlastní, do svého katalogu.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako správce IT delegovat na tvůrce katalogu

Pomocí těchto kroků přiřaďte uživatele k roli tvůrce katalogu.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V levé nabídce v části **Správa nároků** klikněte na **Nastavení**.

1. Klikněte na **Upravit**.

    ![Nastavení pro přidání tvůrců katalogu](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. V části **Správa nároků delegáta** klikněte na **Přidat tvůrci katalogu** a vyberte uživatele nebo skupiny, kterým chcete delegovat tuto roli správy oprávnění.

1. Klikněte na **Vybrat**.

1. Klikněte na **Uložit**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Umožnění přístupu k Azure Portal delegovaným rolím

Abyste povolili delegované role, jako jsou tvůrci katalogu a správci balíčků přístupu, pro přístup k Azure Portal ke správě balíčků přístupu, byste měli zaškrtnout nastavení portálu pro správu.

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Uživatelé**.

1. V nabídce vlevo klikněte na **nastavení uživatele**.

1. Ujistěte se, že **omezení přístupu k portálu pro správu Azure AD** je nastavené na **ne**.

    ![Nastavení uživatele Azure AD – portál pro správu](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Další kroky

- [Vytvoření a správa katalogu prostředků](entitlement-management-catalog-create.md)
- [Zásady správného řízení přístupu pro přístup k správcům balíčků](entitlement-management-delegate-managers.md)

