---
title: V Azure Active Directory resetování hesla | Microsoft Docs
description: Správce spustil resetování hesla pro uživatele v Azure Active Directory
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
editor: ''
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: faeb66c9d0638b13c4716062b768a9c6742edcd1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714149"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Resetování hesla pro uživatele v Azure Active Directory

Správci mohou potřebovat resetovat heslo uživatele v případech, kde je zapomněli, byly uzamčena na více systémů nebo jiné scénáře. Následující postup vás provede resetování hesla uživatele.

## <a name="how-to-reset-the-password-for-a-user"></a>Obnovení hesla pro uživatele

1. Přihlaste se k [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má oprávnění adresář k resetování uživatelských hesel.
2. Vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte uživatele, kterého chcete resetovat heslo.
2. Pro vybraného uživatele, vyberte **resetovat heslo**.

    ![Resetování hesla pro uživatele z profilu uživatele ve službě Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. Na **resetovat heslo**, vyberte **resetovat heslo**.
7. Zobrazí se dočasné heslo, potom můžete zadat pro uživatele. Uživatel bude požádán pak změnit své heslo při příštím přihlášení se. 

   > [!NOTE]
   > Toto dočasný heslo není mají čas vypršení platnosti tak, aby ho nebude platné, dokud přihlášení a jsou pak jsou vynutit ho změnit. 

## <a name="next-steps"></a>Další postup
* [Přidání uživatele](active-directory-users-create-azure-portal.md)
* [Přiřazení rolí správce uživateli](active-directory-users-assign-role-azure-portal.md)
* [Správa uživatelských profilů](active-directory-users-profile-azure-portal.md)
* [Odstranění uživatele ve službě Azure AD](active-directory-users-delete-user-azure-portal.md)
