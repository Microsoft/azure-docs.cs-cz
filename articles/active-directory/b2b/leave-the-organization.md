---
title: Opustit organizaci jako uživatele typu Host – Azure Active Directory | Dokumentace Microsoftu
description: Ukazuje, jak uživatele typu Host s Azure AD B2B opustit organizaci pomocí přístupového panelu.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 17b34b173a10a355817fee0f5928b7fb478125e3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590419"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opustit organizaci jako uživatele typu Host

Můžete se rozhodnout opustit organizaci kdykoli, pokud už nepotřebujete k použití aplikací z této organizace nebo udržovat žádné přidružení uživatele typu Host s Azure Active Directory (Azure AD) B2B. Uživatele můžete nechat organizace samostatně, aniž by bylo nutné kontaktovat správce.

## <a name="leave-an-organization"></a>Opustit organizaci

Pokud chcete opustit organizaci, jako uživatel přihlásil k [přístupového panelu](https://myapps.microsoft.com), postupujte takto:

1. Pokud jste ještě nejste přihlášení organizace, která chcete ponechat, v pravém horním rohu vyberte své jméno a klikněte na tlačítko, které chcete opustit organizaci.
2. V pravém horním rohu vyberte své jméno.
3. Vedle položky **organizace**, vyberte ikonu nastavení (ozubené kolo).
 
   ![Snímek obrazovky zobrazující nastavení uživatele v přístupovém panelu](media/leave-the-organization/UserSettings.png) 

3. V části **organizace**, najít organizace, která chcete ponechat a vyberte **opustit organizaci**.

   ![Snímek obrazovky zobrazující možnost nechte organizace v uživatelském rozhraní](media/leave-the-organization/LeaveOrg.png)

4. Když se zobrazí výzva k potvrzení, vyberte **ponechte**. 

## <a name="account-removal"></a>Odebrání účtu

Když uživatel odejde z organizace, odstranění uživatelského účtu"soft" v adresáři. Ve výchozím nastavení, objekt uživatele přesune **odstraněných uživatelů** oblasti ve službě Azure AD ale není trvale odstraněny po dobu 30 dnů. Obnovitelné odstranění umožňuje správci obnovit uživatelský účet (včetně skupin a oprávnění), pokud uživatel odešle žádost o obnovení účtu v rámci 30denní období.

V případě potřeby správce tenanta můžete trvale odstranit účet kdykoli během 30 dnů. Použijte následující postup:

1. V [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory**.
2. V části **spravovat**vyberte **uživatelé**.
3. Vyberte **odstraněných uživatelů**.
4. Zaškrtněte políčko vedle odstraněného uživatele a pak vyberte **trvale odstranit**.

Pokud se trvale odstranit uživatele, tato akce je nezvratná.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další postup

- Přehled Azure AD s B2B, naleznete v tématu [co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)



