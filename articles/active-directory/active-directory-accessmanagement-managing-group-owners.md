---
title: Další kroky pro správu přístupu pomocí skupin | Microsoft Docs
description: Jak rozšířené-pro správu skupin zabezpečení a použití těchto skupin můžete spravovat přístup k prostředku.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: 0f379c22f1bad522a2d9bff399bbf7006f7c4920
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713275"
---
# <a name="managing-owners-for-a-group"></a>Správa vlastníků pro skupinu
Jakmile vlastník prostředku přiřazena přístup k prostředku skupině služby Azure AD, vlastník skupiny je spravovaný členství ve skupině. Vlastník prostředku deleguje efektivně oprávnění přiřazovat uživatele k prostředku vlastníkovi skupiny.

## <a name="add-an-owner-to-a-group"></a>Přidat do skupiny vlastníka

1. V [centra pro správu Azure AD](https://aad.portal.azure.com), vyberte **uživatelů a skupin**.
2. Vyberte **všechny skupiny**a pak otevřete skupinu, kterou chcete přidat vlastníkům.
3. Vyberte **přidat vlastníky**.
4. Na **přidat vlastníky** stránky, vyberte uživatele, který chcete přidat jako vlastník této skupiny a zajistěte, aby tento název je přidán do **vybrané** podokně.

## <a name="remove-an-owner-from-a-group"></a>Odebrat ze skupiny vlastníka

1. V [centra pro správu Azure AD](https://aad.portal.azure.com), vyberte **uživatelů a skupin**.
2. Vyberte **všechny skupiny**a pak otevřete skupinu, ze kterého chcete odebrat vlastníky.
3. Vyberte **vlastníky** kartě.
4. Vyberte vlastníka, který chcete odebrat z této skupiny a potom vyberte **odebrat**.

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)
* [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Představení služby Azure Active Directory](active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
