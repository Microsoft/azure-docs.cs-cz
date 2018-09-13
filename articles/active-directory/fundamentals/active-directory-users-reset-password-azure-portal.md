---
title: Resetování hesla ve službě Azure Active Directory | Dokumentace Microsoftu
description: Správce iniciované resetování hesla pro uživatele v Azure Active Directory
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
ms.openlocfilehash: 689ab264e56bc8559db6237eee19566e9e3c429f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643105"
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Resetování hesla pro uživatele v Azure Active Directory

Správce může být potřeba resetovat heslo uživatele v případech, kde se zapomněli, byly zamknuté na více instancí nebo jiné scénáře. Následující kroky vás provedou resetování hesla uživatele.

## <a name="how-to-reset-the-password-for-a-user"></a>Jak resetovat heslo pro uživatele

1. Přihlaste se k [centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má oprávnění adresáře k resetování uživatelských hesel.
2. Vyberte **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte uživatele, který chcete resetovat heslo.
2. Pro vybraného uživatele vyberte **resetovat heslo**.

    ![Resetování hesla pro uživatele z profilu uživatele ve službě Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. Na **resetovat heslo**vyberte **resetovat heslo**.
7. Dočasné heslo se zobrazí, že jste jim pak můžou uživateli. Potom změnit své heslo při příštím přihlášení, vyzve uživatele. 

   > [!NOTE]
   > Toto dočasné heslo není nastavený čas vypršení platnosti tak bude platit, dokud přihlášení a jsou pak se vynutilo ho změnit. 

## <a name="next-steps"></a>Další postup
* [Přidání uživatele](../add-users-azure-active-directory.md)
* [Přiřazení role správce uživateli](active-directory-users-assign-role-azure-portal.md)
* [Správa uživatelských profilů](active-directory-users-profile-azure-portal.md)
* [Odstranění uživatele ve službě Azure AD](../add-users-azure-active-directory.md)
