---
title: Jak aktivovat nebo deaktivovat roli | Microsoft Docs
description: Informace o aktivaci role pro privilegované identity s aplikací Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 057960d09a0a47544fa1295dafa4dc387c018ea6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698284"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Postup aktivace nebo deaktivace role v Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management zjednodušuje, jak spravovat podniky privilegovaný přístup k prostředkům v Azure AD a dalším službám Microsoft online jako je Office 365 nebo Microsoft Intune.  

Pokud jste zadali byl způsobilý pro roli správce, která znamená, že tato role může aktivovat, pokud budete potřebovat k provedení privilegovaných akcí. Například pokud někdy spravujete funkcí Office 365, vaší organizace privilegované role správců nemusí mít je trvalé globální správce, vzhledem k tomu, že tato role má to dopad na jiné služby příliš. Místo toho budou požadovat, abyste vhodné pro Azure AD rolí, například správce serveru Exchange Online. Může požádat o k aktivaci této role, když potřebujete svá oprávnění a potom budete mít kontroly správce po předem určenou dobu.

Tento článek je pro správce, který je nutné aktivovat jejich role v Azure AD Privileged Identity Management (PIM). Provede vás provede kroky pro aktivaci role, když potřebujete oprávnění a deaktivovat roli, když jste hotovi. Kromě toho privilegované role správců může vyžadovat schválení pro aktivaci role (Preview). Další informace o [PIM schválení pracovních](./privileged-identity-management/azure-ad-pim-approval-workflow.md) sem.

## <a name="add-the-privileged-identity-management-application"></a>Přidání aplikace Privileged Identity Management
Pomocí aplikace Azure AD Privileged Identity Management [portál Azure](https://portal.azure.com/) požádat o aktivaci role, i v případě, že se chystáte provozovat v jiném portálu nebo prostředí PowerShell. Pokud nemáte aplikaci Azure AD Privileged Identity Management na portálu Azure, postupujte podle těchto kroků, abyste mohli začít.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte své uživatelské jméno v pravém horním rohu portálu Azure a vyberte adresář, kde vám budou pracovat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

## <a name="activate-a-role"></a>Aktivovat roli
Pokud je nutné vzít v roli, můžete požádat o aktivaci výběrem **Moje role** levé sloupec navigační prvku možnost navigace v aplikaci Azure AD Privileged Identity Management.

1. Přihlaste se k [portál Azure](https://portal.azure.com/) a vyberte dlaždici Azure AD Privileged Identity Management.
2. Vyberte **Moje role**. V seskupení v horní části stránky se zobrazí seznam přiřazené vhodné role.
3. Vyberte roli, kterou chcete aktivovat.
4. Vyberte **aktivovat**. **Žádosti o aktivaci role** otevře se okno.
5. Některé role vyžadují služby Multi-Factor Authentication (MFA) před aktivací roli. Můžete mít pouze k ověření na začátku každé relace.
   
    ![Ověřit pomocí vícefaktorového ověřování předtím, než aktivace role – snímek obrazovky][2]
6. V textovém poli zadejte důvod pro žádost o aktivaci.  Některé role vyžadují, abyste zadat číslo lístku řešení problémů.
7. Vyberte **OK**.  Pokud roli nevyžaduje schválení, bude aktivován a role se zobrazí v seznamu aktivní role (přímo pod seznamem přiřazení role vhodné). Pokud [role vyžaduje schválení](./privileged-identity-management/azure-ad-pim-approval-workflow.md) Pokud chcete aktivovat, se krátce zobrazí oznámení s informační zprávou v pravém horním rohu prohlížeče vás informuje o požadavek čeká na schválení.

    ![Žádost čeká na oznámení – snímek obrazovky][3]

## <a name="deactivate-a-role"></a>Deaktivovat roli
Po aktivaci role automaticky deaktivuje po dosažení jeho časový limit (vhodné doba trvání).

Pokud již v rané fázi dokončení úlohy správy, můžete také deaktivovat roli ručně v aplikaci Azure AD Privileged Identity Management.  Vyberte **Moje role**, zvolte roli s tím budete hotovi pomocí z **přiřazení Active role** seskupování a vyberte možnost **deaktivovat**.  

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o
V případě, že nechcete, aby aktivace role, který vyžaduje schválení, můžete kdykoli zrušit čekající žádosti. Jednoduše vyberte **Moje role** levé sloupec navigační prvku možnost navigace v aplikaci Azure AD Privileged Identity Management.

1. Přihlaste se k [portál Azure](https://portal.azure.com/) a vyberte dlaždici Azure AD Privileged Identity Management.
2. Vyberte **Moje role**. V seskupení v horní části stránky se zobrazí seznam přiřazené vhodné role.
3. Vyberte roli.
4. Vyberte **Aktivace čeká na schválení** hlavičku v okně Podrobnosti o aktivaci role.
5. Vyberte **zrušit** v horní části **čekající na schválení** okno.

   ![Zrušit čekající na vyřízení žádosti – snímek obrazovky][4]

## <a name="next-steps"></a>Další postup
Pokud byste chtěli dozvědět více o Azure AD Privileged Identity Management, tyto odkazy obsahovat další informace.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
