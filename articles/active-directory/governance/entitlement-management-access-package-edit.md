---
title: Skrytí nebo odstranění balíčku pro přístup ve správě nároků – Azure AD
description: Přečtěte si, jak skrýt nebo odstranit balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87798796"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Skrytí nebo odstranění balíčku přístupu v Azure AD – Správa nároků

Ve výchozím nastavení jsou balíčky pro přístup zjistitelné. To znamená, že pokud zásada umožní uživateli požádat o přístup k balíčku, automaticky uvidí balíček pro přístup uvedený na portálu pro přístup. **Skryté** nastavení však můžete změnit tak, aby balíček přístupu nebyl uveden na portálu přístupu uživatele.

Tento článek popisuje, jak skrýt nebo odstranit balíček přístupu.

## <a name="change-the-hidden-setting"></a>Změna skrytého nastavení

Pomocí těchto kroků můžete změnit **skryté** nastavení balíčku pro přístup.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Na stránce Přehled klikněte na **Upravit**.

1. Nastavte **skryté** nastavení.

    Pokud je nastavena na **ne**, bude balíček přístupu uveden na portálu pro přístup uživatele.

    Pokud je nastavená hodnota **Ano**, balíček přístupu nebude uveden na portálu pro přístup uživatele. Jediným způsobem, jak může uživatel zobrazit balíček přístupu, je, že má odkaz na přímý **přístup** do balíčku pro přístup. Další informace najdete v tématu [sdílení odkazu pro vyžádání balíčku přístupu](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Odstranění balíčku pro přístup

Balíček přístupu se dá odstranit jenom v případě, že nemá žádná aktivní uživatelská přiřazení. Pomocí těchto kroků odstraníte balíček pro přístup.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **přiřazení** a odeberte přístup pro všechny uživatele.

1. V nabídce vlevo klikněte na **Přehled** a pak klikněte na **Odstranit**.

1. Ve zprávě odstranit, která se zobrazí, klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky

- [Zobrazení, přidání a odebrání přiřazení balíčku pro přístup](entitlement-management-access-package-assignments.md)
- [Zobrazení sestav a protokolů](entitlement-management-reports.md)
