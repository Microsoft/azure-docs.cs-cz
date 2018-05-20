---
title: Přidání uživatelů spolupráce B2B Azure portal – Azure Active Directory | Microsoft Docs
description: Ukazuje, jak správce můžete přidat uživatele typu Host do svého adresáře z partnerské organizace pomocí spolupráce B2B Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7dddc41d35ae466f7c1392450fbda3c86a72a538
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Přidat uživatele Azure Active Directory s B2B spolupráce na portálu Azure

Jako globální správce nebo uživatel, který je přiřazen directory role správce s omezeními můžete portál Azure pozvaným uživatelům spolupráce B2B. Můžete pozvat uživatele typu Host k adresáři, do skupiny nebo k aplikaci. Po pozvat uživatele pomocí některé z těchto metod, pozvané uživatele účet je přidán do služby Azure Active Directory (Azure AD), s typem uživatele *hosta*. Uživatele guest musí pak uplatnit své pozvánky pro přístup k prostředkům.

Po přidání uživatele guest do adresáře, můžete buď odeslat uživatele guest přímý odkaz sdílené aplikaci nebo uživatele guest můžete kliknout na adresu URL se v e-mailová pozvánka. Další informace o procesu uplatnění najdete v tématu [uplatnění pozvánku spolupráce B2B](redemption-experience.md).

> [!IMPORTANT]
> Postupujte podle kroků v [postupy: přidání informace o ochraně osobních údajů vaší organizace v Azure Active Directory](https://aka.ms/adprivacystatement) přidat adresu URL prohlášení o ochraně osobních údajů vaší organizace. Jako součást první čas pozvánku k uplatnění proces musí vaše zásady ochrany osobních údajů, chcete-li pokračovat souhlas pozvané uživatele. 

## <a name="add-guest-users-to-the-directory"></a>Přidat uživatele typu Host do adresáře

Přidat uživatele spolupráce B2B do adresáře, postupujte takto:

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako správce Azure AD.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**, vyberte **uživatelů a skupin** > **všichni uživatelé**.
4. Vyberte **nového uživatele guest**.

   ![Ukazuje, kde je nový uživatel guest v uživatelském rozhraní](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
7. V části **pozvání hosta**, zadejte e-mailovou adresu externího uživatele. Volitelně můžete zahrnout uvítací zprávy. Příklad:

   ![Ukazuje, kde je nový uživatel guest v uživatelském rozhraní](./media/add-users-administrator/InviteGuest.png) 

8. Vyberte **pozvat** automaticky odeslat pozvánky k účtu guest. V **oznámení** oblasti, podívejte se **úspěšně pozvané uživatele** zprávy. 
 
Po odeslání pozvánky se uživatelský účet automaticky přidá do adresáře jako Host.


![Zobrazí uživatele B2B uživatele typu Host](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Přidat do skupiny uživatelů typu Host
Pokud potřebujete ručně přidat uživatele spolupráce B2B pro skupinu jako správce Azure AD, postupujte takto:

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako správce Azure AD.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**, vyberte **uživatelů a skupin** > **všechny skupiny**.
4. Vyberte skupinu (nebo klikněte na tlačítko **nové skupiny** vytvoříte nový). Je vhodné pro zahrnutí do popis skupiny, aby tato skupina obsahuje uživatele typu Host B2B.
5. Vyberte **členy** > **přidat členy**. 
6. Proveďte jednu z následujících akcí:
   - Pokud uživatel guest již existuje v adresáři, vyhledejte uživatele B2B. Vyberte uživatele > klikněte na tlačítko **vyberte** přidejte uživatele do skupiny.
   - Pokud uživatel guest v adresáři neexistuje, vyberte **pozvat**.
   ![Přidání tlačítka pozvání přidání členů do hosta](./media/add-users-administrator/GroupInvite.png)
   
      V části **pozvání hosta**, zadejte e-mailovou adresu a volitelné osobní zprávu > vyberte **pozvat**. Klikněte na tlačítko **vyberte** přidejte uživatele do skupiny.

      Pozvánku automaticky nedostane mimo pozvané uživatele. V **oznámení** oblasti, podívejte se úspěšně **Invited uživatele** zprávy. 

Můžete taky dynamických skupin se spolupráce Azure AD B2B. Další informace najdete v tématu [dynamických skupin a spolupráce Azure Active Directory s B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Přidat uživatele typu Host do aplikace

K přidání uživatele spolupráce B2B do aplikace jako správce Azure AD, postupujte takto:

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako správce Azure AD.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**, vyberte **podnikové aplikace, které** > **všechny aplikace**.
4. Vyberte aplikaci, do které chcete přidat uživatele typu Host.
5. V části **spravovat**, vyberte **uživatelů a skupin**.
6. Vyberte **přidat uživatele**.
7. V části **přidat přiřazení**, vyberte **uživatele a skupiny**.
8. Proveďte jednu z následujících akcí:
   - Pokud uživatel guest již existuje v adresáři, vyhledejte uživatele B2B. Vyberte uživatele a pak klikněte na **vyberte** přidejte uživatele do aplikace.
   - Pokud uživatel guest v adresáři neexistuje, vyberte **pozvat**.
   ![Přidání tlačítka pozvání přidání členů do hosta](./media/add-users-administrator/AppInviteUsers.png)
   
      V části **pozvání hosta**, zadejte e-mailovou adresu a volitelné osobní zprávu > vyberte **pozvat**. Klikněte na tlačítko **vyberte** přidejte uživatele do aplikace.

      Pozvánku automaticky nedostane mimo pozvané uživatele. V **oznámení** oblasti, podívejte se úspěšně **Invited uživatele** zprávy.

9. V části **přidat přiřazení**, klikněte na tlačítko **vybrat roli** > vyberte roli, kterou chcete použít pro vybraného uživatele (pokud existuje) > vyberte **OK**.
10. Klikněte na **Přiřadit**.
 
## <a name="resend-invitations-to-guest-users"></a>Opětovné odeslání pozvánky pro uživatele typu Host

Pokud uživatel guest nebyl ještě uplatněn jejich pozvánku, můžete znovu odeslat e-mailová pozvánka.

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako správce Azure AD.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**, vyberte **uživatelů a skupin**.
4. Vyberte **všichni uživatelé**.
5. Vyberte uživatelský účet.
6. V části **spravovat**, vyberte **profil**.
7. Pokud uživatel ještě nepřijme pozvání, **znovu odeslat pozvánky** možnost je k dispozici. Kliknutím na toto tlačítko znovu odeslat.

   ![Možnost znovu odeslat pozvánky v profilu uživatele](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Pokud znovu odešlete požadavek, který původně přesměruje uživatele pro konkrétní aplikaci, uvědomte si, že na odkaz v nová pozvánka uživatel přejde na panelu nejvyšší úrovně přístupu místo.

## <a name="next-steps"></a>Další postup

- Další informace, jak přidat uživatele typu Host B2B admins mimo Azure AD, najdete v části [jak informační pracovníci přidat uživatele spolupráce B2B?](add-users-information-worker.md)
- Informace o e-mailová pozvánka najdete v tématu [elementy e-mailová B2B spolupráce Pozvánka](invitation-email-elements.md).

