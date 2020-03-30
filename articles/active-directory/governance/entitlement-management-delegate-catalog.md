---
title: Delegování zásad správného řízení přístupu na tvůrce katalogů ve správě nároků Azure AD – Azure Active Directory
description: Zjistěte, jak delegovat zásadsprávné řízení přístupu od správců IT na tvůrce katalogů a vedoucí projektů, aby mohli spravovat přístup sami.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120192"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Delegovat zásadsprávné řízení přístupu na tvůrce katalogů ve správě oprávnění Azure AD

Katalog je kontejner prostředků a přístup k balíčkům. Katalog vytvoříte, pokud chcete seskupit související prostředky a získat přístup k balíčkům. Ve výchozím nastavení může globální správce nebo správce uživatele [vytvořit katalog](entitlement-management-catalog-create.md)a přidat další uživatele jako vlastníky katalogu.

Chcete-li delegovat na uživatele, kteří nejsou správci, aby mohli vytvářet své vlastní katalogy, můžete přidat tyto uživatele do role tvůrce katalogu definovaného správou nároků Azure AD. Můžete přidat jednotlivé uživatele nebo můžete přidat skupinu, jejíž členové pak mohou vytvářet katalogy.  Po vytvoření katalogu mohou následně přidat prostředky, které vlastní, do svého katalogu.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Jako správce IT delegujte na tvůrce katalogu

Chcete-li přiřadit uživatele k roli tvůrce katalogu, postupujte takto.

**Předpokladová role:** Globální správce nebo správce uživatelů

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte v části **Správa nároků** na **Nastavení**.

1. Klikněte na **Upravit**.

    ![Nastavení pro přidání tvůrců katalogu](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. V části **Správa oprávnění delegáta** klikněte na **Přidat tvůrce katalogu** a vyberte uživatele nebo skupiny, kterým chcete delegovat tuto roli správy oprávnění.

1. Klepněte na **tlačítko Vybrat**.

1. Klikněte na **Uložit**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Povolení přístupu delegovaných rolí k portálu Azure

Chcete-li povolit delegované role, jako jsou tvůrci katalogu a správci balíčků přístupu, přístup k portálu Azure pro správu přístupových balíčků, měli byste zkontrolovat nastavení portálu pro správu.

**Předpokladová role:** Globální správce nebo správce uživatelů

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Users**.

1. V levé nabídce klepněte na **položku Uživatelská nastavení**.

1. Ujistěte **se, že omezit přístup k portálu správy Azure AD** je nastavena na **ne**.

    ![Nastavení uživatelů Azure AD – portál pro správu](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Další kroky

- [Vytvoření a správa katalogu zdrojů](entitlement-management-catalog-create.md)
- [Delegovat zásady správného řízení přístupu pro přístup ke správcům balíčků](entitlement-management-delegate-managers.md)

