---
title: 'Rychlý Start: přiřazení uživatelů k aplikaci, která používá Azure Active Directory jako zprostředkovatele identity'
description: Tento rychlý Start vás provede procesem, který uživatelům umožňuje používat aplikaci, kterou jste nastavili pro použití Azure AD jako zprostředkovatele identity.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/01/2020
ms.author: kenwith
ms.openlocfilehash: 53dd2d15565149c3a9888ba063a6194ae033d8e0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258367"
---
# <a name="quickstart-assign-users-to-an-app-that-is-using-azure-ad-as-an-identity-provider"></a>Rychlý Start: přiřazení uživatelů k aplikaci, která používá službu Azure AD jako zprostředkovatele identity

V předchozím rychlém startu jste nakonfigurovali vlastnosti pro aplikaci. Při nastavování vlastností jste nakonfigurovali prostředí pro přiřazené i nepřiřazené uživatele. Tento rychlý Start vás provede procesem přiřazování uživatelů k aplikaci.

## <a name="prerequisites"></a>Požadavky

Pokud chcete přiřadit uživatele k aplikaci, kterou jste přidali do svého tenanta Azure AD, budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z následujících rolí: globální správce, správce cloudové aplikace, správce aplikace nebo vlastník instančního objektu.
- Volitelné: dokončování [zobrazení vašich aplikací](view-applications-portal.md)
- Volitelné: dokončení [Přidání aplikace](add-application-portal.md)
- Volitelné: dokončení [Konfigurace aplikace](add-application-portal-configure.md)

>[!IMPORTANT]
>K otestování kroků v tomto rychlém startu použijte neprodukční prostředí.

## <a name="assign-users-to-an-app"></a>Přiřazení uživatelů k aplikaci
1. Na portálu Azure AD vyberte **podnikové aplikace**. Pak vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat.
2. V navigační nabídce vlevo vyberte **Uživatelé a skupiny**.
   > [!NOTE]
   > Některé aplikace Microsoft 365 vyžadují použití PowerShellu. 
3. Klikněte na tlačítko **Přidat uživatele** .
4. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
5. Vyberte uživatele nebo skupinu, které chcete aplikaci přiřadit. Do vyhledávacího pole můžete také začít psát jméno uživatele nebo skupiny. Můžete zvolit více uživatelů a skupin a vaše výběry se zobrazí v části **vybrané položky**.
    > [!IMPORTANT]
    > Když přiřadíte skupinu k aplikaci, budou mít přístup jenom uživatelé ve skupině. Přiřazení se neprovádí kaskádovitě pro vnořené skupiny.

    > [!NOTE]
    > Přiřazení na základě skupin vyžaduje Azure Active Directory Premium edici P1 nebo P2. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách a skupiny Microsoft 365 nejsou aktuálně podporovány. Další licenční požadavky na funkce popsané v tomto článku najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 
6. Po dokončení klikněte na **Vybrat**.
   ![Přiřazení uživatele nebo skupiny k aplikaci](./media/assign-user-or-group-access-portal/assign-users.png)
7. V podokně **Uživatelé a skupiny** vyberte jednoho nebo více uživatelů nebo skupin ze seznamu a pak klikněte na tlačítko **Vybrat** v dolní části podokna.
8. Pokud je aplikace podporuje, můžete uživateli nebo skupině přiřadit roli. V podokně **Přidat přiřazení** zvolte **Vybrat roli**. Pak v podokně **Vybrat roli** zvolte roli, kterou chcete použít pro vybrané uživatele nebo skupiny, a potom v dolní části podokna vyberte **OK** . 
    > [!NOTE]
    > Pokud aplikace nepodporuje výběr rolí, přiřadí se výchozí role přístupu. V takovém případě aplikace spravuje úroveň přístupu uživatelů.
9. V podokně **Přidat přiřazení** vyberte v dolní části podokna tlačítko **přiřadit** .

Pomocí stejného postupu můžete zrušit přiřazení uživatelů nebo skupin. Vyberte uživatele nebo skupinu, kterým chcete zrušit přiřazení, a pak vyberte **Odebrat**. Některé aplikace Microsoft 365 a Office 365 vyžadují použití PowerShellu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s rychlým startem hotovi, zvažte odstranění aplikace. Tímto způsobem můžete zachovat čistou zkušebního tenanta. Odstranění aplikace se zabývá posledním rychlým startem v této sérii, najdete v tématu [odstranění aplikace](delete-application-portal.md).

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nastavit jednotné přihlašování pro aplikaci.
> [!div class="nextstepaction"]
> [Nastavení jednotného přihlašování pomocí SAML](add-application-portal-setup-sso.md)

NEBO

> [!div class="nextstepaction"]
> [Nastavení jednotného přihlašování pomocí OIDC](add-application-portal-setup-oidc-sso.md)
