---
title: Skrytí nebo odstranění balíčku přístupu ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Přečtěte si, jak skrýt nebo odstranit balíček přístupu v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab6f3a86d8e88ee2d7465fd32bde7b7555f1f326
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389169"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management-preview"></a>Skrytí nebo odstranění balíčku pro přístup ve správě nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
