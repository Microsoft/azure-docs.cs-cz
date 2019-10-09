---
title: Zásady správného řízení přístupu delegáta pro přístup ke správcům balíčků ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Naučte se delegovat řízení přístupu od správců IT a získat přístup k správcům balíčků a vedoucím projektu, aby mohli spravovat přístup sami.
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
ms.openlocfilehash: c7a2b6bfdb4904e11ffba3a9fe1097c7f5cfe9d6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170734"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management-preview"></a>Přístup ke Správci balíčků ve správě nároků Azure AD (Preview) prostřednictvím delegování zásad správného řízení přístupu

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.
> Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Chcete-li delegovat vytváření a správu balíčků přístupu v katalogu, přidejte uživatele do role správce balíčků přístupu. Správci balíčků přístupu musí být obeznámeni s potřebou uživatelů vyžadovat přístup k prostředkům v katalogu. Například pokud se katalog používá pro projekt, pak vedoucí projektu může být správce balíčků přístupu pro tento katalog.  Správci balíčků přístupu nemůžou přidávat prostředky do katalogu, ale můžou spravovat balíčky a zásady přístupu v katalogu.  Při delegování do Správce balíčků přístupu může tato osoba následně odpovídat za:

- Jaké role bude uživatel mít k prostředkům v katalogu
- Kdo bude potřebovat přístup
- Kdo potřebuje schvalovat žádosti o přístup
- Doba, po kterou bude projekt naposledy

Toto video poskytuje přehled o tom, jak delegovat řízení přístupu z vlastníka katalogu na přístup ke Správci balíčků.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Jako vlastník katalogu delegovat na správce balíčků přístupu

Pomocí těchto kroků přiřaďte uživatele k roli správce balíčků přístupu:

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, do kterého chcete přidat správce.

1. V nabídce vlevo klikněte na **role a správci**.

    ![Role a správci katalogů](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kliknutím na **přidat správce balíčků pro přístup** můžete vybrat členy pro tyto role.

1. Kliknutím na **Vybrat** přidejte tyto členy.

## <a name="remove-an-access-package-manager"></a>Odebrání správce balíčků přístupu

Pomocí těchto kroků odeberte uživatele z role správce balíčků přístupu:

**Požadovaná role:** Globální správce, Správce uživatelů nebo vlastník katalogu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalogy** a pak otevřete katalog, do kterého chcete přidat správce.

1. V nabídce vlevo klikněte na **role a správci**.

1. Přidejte značku zaškrtnutí vedle správce balíčků přístupu, který chcete odebrat.

1. Klikněte na **Odebrat**.

## <a name="next-steps"></a>Další kroky

- [Vytvoření nového přístupového balíčku](entitlement-management-access-package-create.md)
