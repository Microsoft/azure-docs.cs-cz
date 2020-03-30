---
title: Přidání uživatelů spolupráce B2B na webu Azure Portal – Azure AD
description: Ukazuje, jak správce můžete přidat uživatele typu Host do svého adresáře z partnerské organizace pomocí Azure Active Directory (Azure AD) B2B spolupráce.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263501"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Přidání uživatelů spolupráce služby Azure Active Directory B2B na webu Azure Portal

Jako uživatel, kterému je přiřazena některá z rolí adresáře s omezeným přístupem, můžete pomocí portálu Azure pozvat uživatele spolupráce B2B. Uživatele typu Host můžete pozvat do adresáře, do skupiny nebo do aplikace. Po pozvání uživatele prostřednictvím některé z těchto metod se účet pozvaného uživatele přidá do služby Azure Active Directory (Azure AD) s typem *uživatele Guest*. Uživatel typu Host pak musí uplatnit pozvánku pro přístup k prostředkům. Platnost pozvání uživatele nevyprší.

Po přidání uživatele typu Host do adresáře můžete buď odeslat uživateli typu Host přímý odkaz na sdílenou aplikaci, nebo může uživatel typu Host kliknout na adresu URL pro využití v e-mailu s pozvánkou. Další informace o procesu uplatnění naleznete v tématu [Uplatnění pozvání na spolupráci B2B](redemption-experience.md).

> [!IMPORTANT]
> Měli byste postupovat podle pokynů v [části Postupy: Přidáním informací o ochraně osobních údajů vaší organizace ve službě Azure Active Directory](https://aka.ms/adprivacystatement) přidejte adresu URL prohlášení o zásadách ochrany osobních údajů vaší organizace. V rámci prvního procesu uplatnění pozvánky musí pozvaný uživatel souhlasit s podmínkami ochrany osobních údajů, aby mohl pokračovat. 

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že nastavení externí spolupráce vaší organizace je nakonfigurované tak, abyste mohli pozvat hosty. Ve výchozím nastavení mohou všichni uživatelé a správci pozvat hosty. Zásady externí spolupráce vaší organizace však mohou být nakonfigurovány tak, aby určitým typům uživatelů nebo správcům bránily v pozvání hostů. Informace o tom, jak zobrazit a nastavit tyto zásady, najdete [v tématu Povolení externí spolupráce B2B a správa toho, kdo může pozvat hosty](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Přidání uživatelů typu Host do adresáře

Chcete-li do adresáře přidat uživatele spolupráce B2B, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako uživatel, kterému je přiřazena role adresáře s omezeným přístupem nebo role Pozvaní hosta.
2. Vyhledejte a vyberte **službu Azure Active Directory** na libovolné stránce.
3. V části **Spravovat** vyberte **Uživatele**.
4. Vyberte **Nový uživatel typu host**.

   ![Zobrazuje, kde se v uživatelském rozhraní nachází nový uživatel typu Host.](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > Možnost **Nový uživatel hosta** je k dispozici také na stránce **Vztahy organizace.** Ve **službě Azure Active Directory**vyberte v části **Správa** **položku Vztahy organizace**.

5. Na stránce **Nový uživatel** vyberte **Pozvat uživatele** a přidejte informace o hosta. 

    > [!NOTE]
    > Skupinové e-mailové adresy nejsou podporovány. zadejte e-mailovou adresu jednotlivce. Někteří poskytovatelé e-mailu také umožňují uživatelům přidat symbol plus (+) a další text na své e-mailové adresy, aby pomohli s filtrováním doručené pošty. Azure AD však aktuálně nepodporuje plus symboly v e-mailových adresách. Chcete-li se vyhnout problémům s doručením, vynechejte symbol plus a všechny znaky, které následují po symbolu @.

   - **Jméno.** Jméno a příjmení uživatele typu Host.
   - **E-mailová adresa (povinné)**. E-mailová adresa uživatele typu Host.
   - **Osobní zpráva (nepovinné)** Uveďte osobní uvítací zprávu pro uživatele typu Host.
   - **Skupiny**: Uživatele typu Host můžete přidat do jedné nebo více existujících skupin nebo můžete provést později.
   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. 

7. Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. 
 
Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.


![Zobrazí uživatele B2B s typem uživatele typu Host.](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Přidání uživatelů typu host do skupiny
Pokud potřebujete ručně přidat uživatele spolupráce B2B do skupiny, postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte **službu Azure Active Directory** na libovolné stránce.
3. V části **Manage**vyberte **Skupiny**.
4. Vyberte skupinu (nebo kliknutím na **Nová skupina** vytvořte novou). Je vhodné zahrnout do popisu skupiny, že skupina obsahuje uživatele typu Host B2B.
5. Vyberte možnost **Členové**. 
6. Proveďte jednu z těchto akcí:
   - Pokud uživatel typu Host již v adresáři existuje, vyhledejte uživatele B2B. Vyberte uživatele a kliknutím na **Vybrat** přidejte uživatele do skupiny.
   - Pokud uživatel typu Host v adresáři ještě neexistuje, pozvěte ho do skupiny zadáním jeho e-mailové adresy do vyhledávacího pole, zadáním volitelné osobní zprávy a klepnutím na tlačítko **Vybrat**. Pozvánka se automaticky dostane k pozvaným uživatelům.
     
     ![Tlačítko Přidat pozvánku pro přidání členů hosta](./media/add-users-administrator/GroupInvite.png)
   
Dynamické skupiny můžete také použít se spoluprací Azure AD B2B. Další informace naleznete [v tématu Dynamické skupiny a spolupráce služby Azure Active Directory B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Přidání uživatelů typu host do aplikace

Chcete-li přidat uživatele spolupráce B2B do aplikace, postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte **službu Azure Active Directory** na libovolné stránce.
3. V části **Správa**vyberte **možnost Podnikové aplikace** > **Všechny aplikace**.
4. Vyberte aplikaci, do které chcete přidat uživatele typu Host.
5. Na řídicím panelu aplikace vyberte **Celkový počet uživatelů,** chcete-li otevřít podokno **Uživatelé a skupiny.**

    ![Tlačítko Celkový počet uživatelů pro přidání otevřených uživatelů a skupin](./media/add-users-administrator/AppUsersAndGroups.png)

6. Vyberte **Přidat uživatele**.
7. V části **Přidat přiřazení**vyberte Možnost Uživatel **a skupiny**.
8. Proveďte jednu z těchto akcí:
   - Pokud uživatel typu Host již v adresáři existuje, vyhledejte uživatele B2B. Vyberte uživatele, klikněte na **Vybrat**a potom kliknutím na **Přiřadit** přidejte uživatele do aplikace.
   - Pokud uživatel typu Host v adresáři ještě neexistuje, zadejte v části **Vybrat člena nebo pozvání externího uživatele**e-mailovou adresu uživatele. Do okna se zprávou zadejte volitelnou osobní zprávu. Pod polem se zprávou klikněte na **Pozvat**.
           
       ![Tlačítko Přidat pozvánku pro přidání členů hosta](./media/add-users-administrator/AppInviteUsers.png)
   
      Klikněte na **Vybrat**a potom kliknutím na **Přiřadit** přidejte uživatele do aplikace. Pozvánka se automaticky dostane k pozvaným uživatelům.

9. Uživatel typu Host se zobrazí v seznamu **Uživatelé a skupiny** aplikace s přiřazenou rolí **Výchozí přístup**. Pokud chcete roli změnit, postupujte takto:
   - Vyberte uživatele typu Host a pak vyberte **Upravit**. 
   - V části **Upravit přiřazení**klikněte na **Vybrat roli**a vyberte roli, kterou chcete přiřadit vybranému uživateli.
   - Klepněte na **tlačítko Vybrat**.
   - Klikněte na **Přiřadit**.
 
## <a name="resend-invitations-to-guest-users"></a>Opětovné poslání pozvánky uživatelům typu host

Pokud uživatel typu Host ještě pozvánku neuplatnil, můžete e-mail s pozvánkou odeslat znovu.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte **službu Azure Active Directory** na libovolné stránce.
3. V části **Spravovat** vyberte **Uživatele**.
5. Vyberte uživatelský účet.
6. V části **Manage**vyberte **Profil**.
7. Pokud uživatel pozvánku ještě nepřijal, je k dispozici možnost **Opětovné odeslání pozvánky.** Toto tlačítko vyberte, chcete-li jej znovu odeslat.

   ![Možnost opětovného odeslání pozvánky v profilu uživatele](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Pokud znovu odešlete pozvánku, která původně uživatele přesměrovala do určité aplikace, uvědomte si, že odkaz v nové pozvánce přenese uživatele na přístupový panel nejvyšší úrovně.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak mohou správci služby Azure AD přidávat uživatele typu Host b2B, najdete v tématu [Jak pracovníci s informacemi přidávají uživatele spolupráce B2B?](add-users-information-worker.md)
- Informace o e-mailu s pozvánkou najdete v [tématu Prvky e-mailu s pozvánkou na spolupráci B2B](invitation-email-elements.md).

