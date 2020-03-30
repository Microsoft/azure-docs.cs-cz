---
title: Delegovat zásady správného řízení přístupu pro přístup ke správcům balíčků ve správě nároků Azure AD – Azure Active Directory
description: Zjistěte, jak delegovat zásadsprávné řízení přístupu od správců IT pro přístup k správcům balíčků a projektovým manažerům, aby mohli spravovat přístup sami.
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
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174369"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegovat zásady správného řízení přístupu pro přístup ke správcům balíčků ve správě oprávnění Azure AD

Chcete-li delegovat vytváření a správu přístupových balíčků v katalogu, přidejte uživatele do role správce balíčků přístupu. Správci balíčků aplikace Access musí být obeznámeni s potřebou, aby uživatelé požadovali přístup k prostředkům v katalogu. Například pokud katalog se používá pro projekt, pak zájemce projektu může být správce balíčků přístupu pro tento katalog.  Správci balíčků aplikace Access nemohou přidávat prostředky do katalogu, ale mohou spravovat přístupové balíčky a zásady v katalogu.  Při delegování na správce přístupových balíčků může být tato osoba zodpovědná za:

- Jaké role bude mít uživatel k prostředkům v katalogu
- Kdo bude potřebovat přístup
- Kdo potřebuje schválit žádosti o přístup
- Jak dlouho bude projekt trvat

Toto video poskytuje přehled o tom, jak delegovat zásadsprávné řízení přístupu od vlastníka katalogu pro přístup správce balíčků.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Jako vlastník katalogu delegujte na správce přístupových balíčků

Chcete-li přiřadit uživatele k roli správce balíčků přístupu, postupujte takto:

**Předpokladová role:** Globální správce, správce uživatele nebo vlastník katalogu

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a potom otevřete katalog, do kterého chcete přidat správce.

1. V levé nabídce klikněte na **Role a správci**.

    ![Role katalogů a správci](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknutím na **Přidat správce balíčků přístupu** vyberte členy pro tyto role.

1. Chcete-li přidat tyto členy, klepněte na **tlačítko Vybrat.**

## <a name="remove-an-access-package-manager"></a>Odebrání správce přístupových balíčků

Chcete-li odebrat uživatele z role správce balíčků přístupu, postupujte takto:

**Předpokladová role:** Globální správce, správce uživatele nebo vlastník katalogu

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalogy** a potom otevřete katalog, do kterého chcete přidat správce.

1. V levé nabídce klikněte na **Role a správci**.

1. Přidejte zaškrtnutí vedle správce přístupových balíčků, který chcete odebrat.

1. Klikněte na **Odebrat**.

## <a name="next-steps"></a>Další kroky

- [Vytvoření nového přístupového balíčku](entitlement-management-access-package-create.md)
