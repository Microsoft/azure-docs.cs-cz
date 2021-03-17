---
title: Opuštění organizace jako uživatel typu Host-Azure Active Directory
description: Ukazuje, jak může uživatel typu Host služby Azure AD B2B opustit organizaci pomocí přístupového panelu.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908589"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opuštění organizace jako uživatel typu host

Uživatel typu Host pro Azure Active Directory (Azure AD) B2B se může kdykoli rozhodnout opustit organizaci, pokud už nepotřebují používat aplikace z této organizace nebo spravovat jakékoli přidružení. Uživatel může organizaci opustit svou vlastní, aniž by se museli kontaktovat správce.

> [!NOTE]
> Uživatel typu Host nemůže opustit organizaci, pokud je jejich účet zakázaný buď v domácím klientovi, nebo v tenantovi prostředků. Pokud je jejich účet zakázaný, uživatel typu host bude muset kontaktovat správce tenanta, který může buď odstranit účet hosta, nebo povolit účet Guest, aby uživatel mohl opustit organizaci.

## <a name="leave-an-organization"></a>Opuštění organizace

Pokud chcete opustit organizaci, postupujte podle těchto kroků.

1. Přejděte na stránku profilu přístupového panelu pomocí jednoho z následujících kroků:
   
   - V [Azure Portal](https://portal.azure.com)klikněte na jméno v pravém horním rohu a vyberte **Zobrazit účet**.
   - Otevřete [přístupový panel](https://myapps.microsoft.com), klikněte na jméno v pravém horním rohu a vedle možnosti **organizace**vyberte ikonu nastavení (ozubené kolo).
 
   ![Snímek obrazovky s uživatelským nastavením na přístupovém panelu](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Pokud ještě nejste přihlášeni k organizaci, kterou chcete opustit, klikněte v části **organizace**na odkaz **Přihlásit se a ponechte** název organizace vedle názvu organizace. Po přihlášení klikněte v pravém horním **rohu na své**jméno a pak vyberte ikonu nastavení (ozubené kolo).

3. V části **organizace**Najděte organizaci, kterou chcete opustit, a vyberte **opustit organizaci**.

   ![Snímek obrazovky, který ukazuje možnost opustit organizaci v uživatelském rozhraní](media/leave-the-organization/LeaveOrg.png)

4. Po zobrazení výzvy k potvrzení vyberte **opustit**. 

## <a name="account-removal"></a>Odebrání účtu

Když uživatel opustí organizaci, uživatelský účet bude v adresáři "soft odstraněn". Ve výchozím nastavení se objekt uživatele přesouvá do oblasti **odstraněné uživatele** ve službě Azure AD, ale po dobu 30 dnů se trvale neodstraní. Toto obnovitelné odstranění umožňuje správci obnovit uživatelský účet (včetně skupin a oprávnění), pokud uživatel vytvoří požadavek na obnovení účtu v průběhu 30 dnů.

V případě potřeby může správce klienta účet trvale odstranit kdykoli během 30 dnů. Použijte následující postup:

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**.
2. V části **Spravovat** vyberte **Uživatelé**.
3. Vyberte **odstraněné uživatele**.
4. Zaškrtněte políčko vedle odstraněného uživatele a pak vyberte možnost **odstranit trvale**.

Pokud uživatele trvale odstraníte, tato akce je nevratná.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

- Přehled služby Azure AD B2B najdete v tématu [co je spolupráce B2B Azure AD?](what-is-b2b.md)



