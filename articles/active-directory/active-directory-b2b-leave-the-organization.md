---
title: Nechte organizaci jako uživatel guest - Azure Active Directory | Microsoft Docs
description: Ukazuje, jak může uživatel hosta s Azure AD s B2B nechat organizace pomocí přístupového panelu.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="leave-an-organization-as-a-guest-user"></a>Nechte organizaci jako uživatel guest

Uživatel hosta s B2B Azure Active Directory (Azure AD) můžete rozhodnout opustit organizaci kdykoli, pokud již nepotřebujete k použití aplikací z dané organizace nebo udržování žádné přidružení. Uživatele můžete ponechat organizaci samostatně, aniž by museli obraťte se na správce.

## <a name="leave-an-organization"></a>Nechte organizace

Chcete nechat organizaci, jako je uživatel přihlášený ke [přístupový Panel](https://myapps.microsoft.com), postupujte takto:

1. Pokud jste již přihlášení pro organizace, která chcete opustit, vyberte své jméno v pravém horním rohu a klikněte na organizaci, které chcete opustit.
2. V pravém horním rohu vyberte své jméno.
3. Vedle **organizace**, vyberte ikonu nastavení (ozubené kolečko).
 
   ![Snímek obrazovky zobrazující uživatelská nastavení v přístupového panelu](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. V části **organizace**, najít organizace, která chcete ponechat a vyberte **nechte organizace**.

   ![Snímek obrazovky zobrazující možnost ponechejte organizace v uživatelském rozhraní](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. Pokud budete vyzváni k potvrzení, vyberte **nechte**. 

## <a name="account-removal"></a>Odebrání účtu

Když uživatel opustí organizaci, uživatelský účet je "logicky Odstraněná" v adresáři. Ve výchozím nastavení, objekt uživatel přesune do **odstranit uživatele** oblasti ve službě Azure AD, ale není trvale odstraněny po dobu 30 dnů. Obnovitelného odstranění umožňuje správci obnovit uživatelský účet (včetně skupiny a oprávnění), pokud uživatel zadá požadavek na obnovení účtu v období 30 dnů.

V případě potřeby správce klienta trvale odstranit účet kdykoli během období 30 dnů. Použijte následující postup:

1. V [portál Azure](https://portal.azure.com), vyberte **Azure Active Directory**.
2. V části **spravovat**, vyberte **uživatelé**.
3. Vyberte **odstranit uživatele**.
4. Zaškrtněte políčko vedle odstraněného uživatele a potom vyberte **trvale odstranit**.

Pokud jste se trvale odstranit uživatele, tato akce je nezměnitelná.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další postup

- Přehled Azure AD B2B najdete v tématu [co je spolupráce Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)



