---
title: Na webu Azure Portal – Azure Active Directory přidat uživatele spolupráce B2B | Dokumentace Microsoftu
description: Ukazuje, jak správce můžete přidat uživatele typu Host do jejich adresáře z partnerské organizace pomocí spolupráce B2B ve službě Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: bda01b5c9fbd43feed8c407e85b90b31bef0ffa1
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335057"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Přidat uživatele spolupráce Azure Active Directory B2B na webu Azure Portal

Jako globální správce nebo uživatel, který se přiřadí role adresáře správce s omezením můžete na webu Azure portal se pozvat uživatele spolupráce B2B. Můžete pozvat uživatele typu Host do adresáře, skupinu nebo aplikaci. Po pozvat uživatele prostřednictvím některé z těchto metod, pozvaný uživatel účet se přidá do služby Azure Active Directory (Azure AD), mají typ uživatel *hosta*. Uživatel typu Host musíte pak uplatňovat svou pozvánku k přístupu k prostředkům.

Po přidání uživatele typu Host do adresáře, můžete buď poslat uživateli typu Host přímý odkaz do sdílené aplikace, nebo uživatel typu Host můžete kliknout na adresu URL uplatnění v e-mailová pozvánka. Další informace o uplatnění procesu, naleznete v tématu [uplatnění pozvání spolupráce B2B](redemption-experience.md).

> [!IMPORTANT]
> Postupujte podle kroků v [postupy: Přidání informací o ochraně osobních údajů vaší organizace v Azure Active Directory](https://aka.ms/adprivacystatement) přidejte adresu URL prohlášení o ochraně osobních údajů vaší organizace. Jako součást procesu uplatnění pozvání první čas musí pozvaný uživatel vyjádřit souhlas. na vaše zásady ochrany osobních údajů, abyste mohli pokračovat. 

## <a name="add-guest-users-to-the-directory"></a>Přidat uživatele typu Host do adresáře

Chcete-li přidat uživatele spolupráce B2B do adresáře, postupujte podle těchto kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **Uživatele**.
4. Vyberte **Nový uživatel typu host**.

   ![Ukazuje, kde je nový uživatel typu Host v uživatelském rozhraní](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
5. V části **Uživatelské jméno** zadejte e-mailovou adresu externího uživatele. Volitelně můžete zahrnout uvítací zprávu. Příklad:

   ![Ukazuje, kde je nový uživatel typu Host v uživatelském rozhraní](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > Někteří poskytovatelé e-mailu umožňují uživatelům přidat plus symbol (+) a další text pro jejich e-mailové adresy, které vám pomůžou s věci, jako je filtrování doručené pošty. Nicméně, Azure AD nepodporuje aktuálně plus symboly v e-mailové adresy. Abyste předešli problémům s doručování, vynechejte na symbol plus a všechny znaky následující až @ symbol.

6. Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. 
 
Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.


![Zobrazí uživatele B2B uživatele typu Host](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Přidat uživatele typu Host do skupiny
Pokud je potřeba ručně přidat uživatele spolupráce B2B do skupiny jako správce Azure AD, postupujte podle těchto kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**vyberte **skupiny**.
4. Vyberte skupinu (nebo klikněte na tlačítko **novou skupinu** vytvořit nový). Je vhodné zahrnout do popisu skupiny, tato skupina obsahuje hosty B2B.
5. Vyberte **členy**. 
6. Proveďte jednu z následujících akcí:
   - Pokud uživatel typu Host už existuje v adresáři, vyhledejte uživatele B2B. Vyberte uživatele a potom klikněte na tlačítko **vyberte** přidejte uživatele do skupiny.
   - Pokud uživatel typu Host ještě neexistuje v adresáři, pozvánku do skupiny do vyhledávacího pole zadáte e-mailová adresa, zadáním volitelnou osobní zprávu a pak levým na **vyberte**. E-mailové pozvánce automaticky nedostane mimo pozvaného uživatele.
     
     ![Přidejte tlačítko pozvat můžete přidat členy typu Host](./media/add-users-administrator/GroupInvite.png)
   
Můžete také použít dynamické skupiny se spoluprací Azure AD B2B. Další informace najdete v tématu [dynamické skupiny a spolupráce Azure Active Directory s B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Přidat uživatele typu Host do aplikace

Chcete-li přidat uživatele spolupráce B2B do aplikace jako správce Azure AD, postupujte podle těchto kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **spravovat**vyberte **podnikové aplikace** > **všechny aplikace**.
4. Vyberte aplikaci, ke kterému chcete přidat uživatele typu Host.
5. Na řídicím panelu aplikace, vyberte **celkový počet uživatelů** otevřít **uživatelů a skupin** podokně.

    ![Celkový počet uživatelů tlačítko pro přidání otevřete uživatelé a skupiny](./media/add-users-administrator/AppUsersAndGroups.png)

6. Vyberte **přidat uživatele**.
7. V části **přidat přiřazení**vyberte **uživatele a skupiny**.
8. Proveďte jednu z následujících akcí:
   - Pokud uživatel typu Host už existuje v adresáři, vyhledejte uživatele B2B. Vyberte uživatele, klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **přiřadit** přidejte uživatele k aplikaci.
   - Pokud uživatel typu Host ještě neexistuje v adresáři, v části **vyberte člena nebo Pozvěte externího uživatele**, zadejte e-mailovou adresu uživatele. V okně se zprávou Zadejte volitelnou zprávu osobní. V okně se zprávou, klikněte na možnost **pozvat**.
           
       ![Přidejte tlačítko pozvat můžete přidat členy typu Host](./media/add-users-administrator/AppInviteUsers.png)
   
      Klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **přiřadit** přidejte uživatele k aplikaci. Pozvánka se automaticky dostane k pozvaného uživatele.

9. Uživatel typu Host se zobrazí v aplikačním **uživatelů a skupin** seznamu s přiřazenou rolí **výchozího přístupu k**. Pokud chcete změnit roli, postupujte takto:
   - Vyberte uživatele typu Host a pak vyberte **upravit**. 
   - V části **upravit přiřazení**, klikněte na tlačítko **vybrat roli**a vyberte roli, kterou chcete přiřadit vybranému uživateli.
   - Klikněte na **Vybrat**.
   - Klikněte na **Přiřadit**.
 
## <a name="resend-invitations-to-guest-users"></a>Znovu odeslat pozvánky uživatelů typu Host

Pokud uživatel typu Host ještě uplatnit ještě pozvánku nepřijal, můžete znovu poslat e-mailová pozvánka.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **Uživatele**.
5. Vyberte uživatelský účet.
6. V části **spravovat**vyberte **profilu**.
7. Pokud uživatel ještě nepřijal pozvání **znovu odeslat pozvánku** možnost je k dispozici. Klikněte na toto tlačítko znovu odeslat.

   ![Možnost znovu odeslat pozvánku do profilu uživatele](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Pokud jste znovu odeslat pozvánku původně přesměruje uživatele na konkrétní aplikaci, Pochopte, že na odkaz v nové pozvánky uživatel přejde na přístupovém panelu nejvyšší úrovně místo.

## <a name="next-steps"></a>Další postup

- Informace o tom, jak správci mimo Azure AD můžete přidat uživatele typu Host B2B, naleznete v tématu [jak informačních pracovníků vynutit přidat uživatele spolupráce B2B?](add-users-information-worker.md)
- Informace o e-mailovou pozvánku, najdete v části [prvky B2B spolupráce e-mailová pozvánka](invitation-email-elements.md).

