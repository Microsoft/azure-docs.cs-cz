---
title: Opustit organizaci jako uživatele typu Host – Azure Active Directory | Dokumentace Microsoftu
description: Ukazuje, jak uživatele typu Host s Azure AD B2B opustit organizaci pomocí přístupového panelu.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d9eb883cc014c1bea092a12e22b6d144a37994
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112970"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opustit organizaci jako uživatele typu Host

Můžete se rozhodnout opustit organizaci kdykoli, pokud už nepotřebujete k použití aplikací z této organizace nebo udržovat žádné přidružení uživatele typu Host s Azure Active Directory (Azure AD) B2B. Uživatele můžete nechat organizace samostatně, aniž by bylo nutné kontaktovat správce.

> [!NOTE]
> Uživatel typu Host nemůže opustit organizaci, pokud jejich účet je zakázaný v domovském tenantovi nebo prostředku tenanta. Pokud jejich účet není povolen, uživatel typu Host muset požádat správce tenanta, který můžete buď odstranit účet guest nebo povolte účet guest, takže uživatel může opustit organizaci.

## <a name="leave-an-organization"></a>Opuštění organizace

Pokud chcete opustit organizaci, postupujte takto.

1. Přejděte na stránku profil přístupového panelu pomocí jedné z následujících kroků:
   
   - V [webu Azure portal](https://portal.azure.com), klikněte na své jméno v pravém horním rohu a vyberte **zobrazit účet**.
   - Otevřete váš [přístupového panelu](https://myapps.microsoft.com), klikněte na své jméno pravém horním rohu a další **organizace**, vyberte ikonu nastavení (ozubené kolo).
 
   ![Snímek obrazovky zobrazující nastavení uživatele v přístupovém panelu](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Pokud jste to ještě neudělali organizaci chcete ponechat v části **organizace**, klikněte na tlačítko **přihlášení chcete opustit organizaci** odkaz vedle názvu organizace. Jakmile jste přihlášení, klikněte na název znovu v pravém horním rohu a vedle položky **organizace**, vyberte ikonu nastavení (ozubené kolo).

3. V části **organizace**, najít organizace, která chcete ponechat a vyberte **opustit organizaci**.

   ![Snímek obrazovky zobrazující možnost nechte organizace v uživatelském rozhraní](media/leave-the-organization/LeaveOrg.png)

4. Když se zobrazí výzva k potvrzení, vyberte **ponechte**. 

## <a name="account-removal"></a>Odebrání účtu

Když uživatel odejde z organizace, odstranění uživatelského účtu"soft" v adresáři. Ve výchozím nastavení, objekt uživatele přesune **odstraněných uživatelů** oblasti ve službě Azure AD, ale není trvale odstraněny po dobu 30 dnů. Obnovitelné odstranění umožňuje správci obnovit uživatelský účet (včetně skupin a oprávnění), pokud uživatel odešle žádost o obnovení účtu v rámci 30denní období.

V případě potřeby správce tenanta můžete trvale odstranit účet kdykoli během 30 dnů. Použijte následující postup:

1. V [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory**.
2. V části **Spravovat** vyberte **Uživatele**.
3. Vyberte **odstraněných uživatelů**.
4. Zaškrtněte políčko vedle odstraněného uživatele a pak vyberte **trvale odstranit**.

Pokud se trvale odstranit uživatele, tato akce je nezvratná.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další postup

- Přehled Azure AD s B2B, naleznete v tématu [co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)



