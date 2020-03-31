---
title: Opuštění organizace jako uživatele typu hosta – Azure Active Directory
description: Ukazuje, jak může uživatel typu Host Azure AD B2B opustit organizaci pomocí přístupového panelu.
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
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272495"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opuštění organizace jako uživatele typu hosta

Uživatel typu Host služby Azure Active Directory (Azure AD) B2B se může kdykoli rozhodnout opustit organizaci, pokud už nebude potřebovat používat aplikace od této organizace nebo udržovat žádné přidružení. Uživatel může opustit organizaci samostatně, aniž by musel kontaktovat správce.

> [!NOTE]
> Uživatel typu Host nemůže opustit organizaci, pokud je jeho účet zakázán v domovském klientovi nebo v tenantovi prostředku. Pokud je jejich účet zakázán, uživatel typu Host bude muset kontaktovat správce klienta, který může buď odstranit účet hosta, nebo povolit účet guest, aby uživatel mohl opustit organizaci.

## <a name="leave-an-organization"></a>Opuštění organizace

Chcete-li opustit organizaci, postupujte takto.

1. Přejděte na stránku profilu přístupového panelu jedním z následujících kroků:
   
   - Na [webu Azure Portal](https://portal.azure.com)klikněte v pravém horním najetí na své jméno a vyberte **Zobrazit účet**.
   - Otevřete [přístupový panel](https://myapps.microsoft.com), klikněte v pravém horním horním patře na své jméno a vedle **položky Organizace**vyberte ikonu nastavení (ozubené kolo).
 
   ![Snímek obrazovky s uživatelskými nastaveními na přístupovém panelu](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Pokud ještě nejste přihlášeni k organizaci, kterou chcete opustit, klikněte v **části Organizace**na odkaz **Přihlásit se a ponechte** odkaz organizace vedle názvu organizace. Po přihlášení klikněte znovu na své jméno v pravém horním a vedle **položky Organizace**a vyberte ikonu nastavení (ozubené kolo).

3. V části **Organizace**najděte organizaci, kterou chcete opustit, a vyberte **Opustit organizaci**.

   ![Snímek obrazovky s možností Opustit organizaci v uživatelském rozhraní](media/leave-the-organization/LeaveOrg.png)

4. Až budete vyzváni k potvrzení, vyberte **Opustit**. 

## <a name="account-removal"></a>Odebrání účtu

Když uživatel opustí organizaci, uživatelský účet je v adresáři "obnovitelné odstranění". Ve výchozím nastavení se objekt uživatele přesune do oblasti **Odstranění uživatelé** ve službě Azure AD, ale není trvale odstraněn po dobu 30 dnů. Toto měkké odstranění umožňuje správci obnovit uživatelský účet (včetně skupin a oprávnění), pokud uživatel požádá o obnovení účtu během 30denního období.

V případě potřeby může správce klienta účet trvale odstranit kdykoli během 30denního období. Použijte následující postup:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Azure Active Directory**.
2. V části **Spravovat** vyberte **Uživatele**.
3. Vyberte **Položku Odstranění uživatelé**.
4. Zaškrtněte políčko vedle odstraněného uživatele a pak **vyberte Trvale odstranit**.

Pokud trvale odstraníte uživatele, je tato akce neodvolatelná.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další kroky

- Přehled Azure AD B2B najdete v tématu [Co je spolupráce Azure AD B2B?](what-is-b2b.md)



