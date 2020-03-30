---
title: Skrytí nebo odstranění přístupového balíčku ve správě nároků – Azure AD
description: Přečtěte si, jak skrýt nebo odstranit balíček přístupu ve správě nároků služby Azure Active Directory.
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261993"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Skrytí nebo odstranění balíčku přístupu ve správě oprávnění Azure AD

Přístupové balíčky jsou ve výchozím nastavení zjistitelné. To znamená, že pokud zásada umožňuje uživateli požádat o přístupový balíček, automaticky uvidí přístupový balíček uvedený na jejich portálu Můj přístup. Nastavení **Skryté** však můžete změnit tak, aby přístupový balíček nebyl uveden na portálu Můj přístup uživatele.

Tento článek popisuje, jak skrýt nebo odstranit přístupový balíček.

## <a name="change-the-hidden-setting"></a>Změna nastavení Skryté

Chcete-li změnit nastavení **Skryté** pro přístupový balíček, postupujte takto.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Na stránce Přehled klikněte na **Upravit**.

1. Nastavte **nastavení Skryté.**

    Pokud je nastavena možnost **Ne**, bude přístupový balíček uveden na portálu Můj přístup uživatele.

    Pokud je nastavena na **Ano**, přístupový balíček nebude uveden na portálu Můj přístup uživatele. Jediný způsob, jak může uživatel zobrazit přístupový balíček, je, pokud má **přímý odkaz portálu Můj přístup** k přístupovému balíčku. Další informace naleznete v [tématu Odkaz na sdílení a požádejte o přístupový balíček](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Odstranění přístupového balíčku

Přístupový balíček lze odstranit pouze v případě, že nemá žádná aktivní přiřazení uživatelů. Odstraněním přístupového balíčku postupujte takto.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. V levé nabídce klikněte na **Přiřazení** a odeberte přístup všem uživatelům.

1. V levé nabídce klikněte na **Přehled** a potom na **odstranit**.

1. V odstraněné zprávě, která se zobrazí, klepněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky

- [Zobrazení, přidání a odebrání přiřazení pro přístupový balíček](entitlement-management-access-package-assignments.md)
- [Zobrazení sestav a protokolů](entitlement-management-reports.md)
