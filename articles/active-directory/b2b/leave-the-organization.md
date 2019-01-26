---
title: Opustit organizaci jako uživatele typu Host – Azure Active Directory | Dokumentace Microsoftu
description: Ukazuje, jak uživatele typu Host s Azure AD B2B opustit organizaci pomocí přístupového panelu.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: caca01411b5bd5f41a35dd99b36b21accba47e5c
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077994"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opustit organizaci jako uživatele typu Host

Můžete se rozhodnout opustit organizaci kdykoli, pokud už nepotřebujete k použití aplikací z této organizace nebo udržovat žádné přidružení uživatele typu Host s Azure Active Directory (Azure AD) B2B. Uživatele můžete nechat organizace samostatně, aniž by bylo nutné kontaktovat správce.

## <a name="leave-an-organization"></a>Opuštění organizace

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
2. V části **Spravovat** vyberte **Uživatele**.
3. Vyberte **odstraněných uživatelů**.
4. Zaškrtněte políčko vedle odstraněného uživatele a pak vyberte **trvale odstranit**.

Pokud se trvale odstranit uživatele, tato akce je nezvratná.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Další postup

- Přehled Azure AD s B2B, naleznete v tématu [co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)



