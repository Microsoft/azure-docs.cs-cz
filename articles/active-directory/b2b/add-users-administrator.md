---
title: Přidat uživatele spolupráce B2B do Azure Portal-Azure Active Directory | Microsoft Docs
description: Ukazuje, jak může správce přidat uživatele typu Host do adresáře z partnerské organizace pomocí spolupráce B2B služby Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21117e551642571d5b974e66d0769ed9906b9d50
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018200"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Přidat uživatele Azure Active Directory spolupráce B2B do Azure Portal

Jako uživatel, kterému je přiřazena kterákoli z omezených rolí adresáře správce, můžete použít Azure Portal k pozvání uživatelů pro spolupráci B2B. Uživatele typu Host můžete pozvat do adresáře, do skupiny nebo do aplikace. Po pozvání uživatele prostřednictvím kterékoli z těchto metod je účet pozvaného uživatele přidán do Azure Active Directory (Azure AD) s uživatelským typem *hosta*. Uživatel typu Host musí následně uplatnit své pozvání k přístupu k prostředkům. Pozvání uživatele nevyprší.

Po přidání uživatele typu Host do adresáře můžete buď Odeslat uživateli typu Host přímý odkaz na sdílenou aplikaci, nebo uživatel typu Host může kliknout na adresu URL pro uplatnění v e-mailu s pozvánkou. Další informace o procesu uplatnění, najdete v tématu věnovaném [uplatnění pozvánky B2B pro spolupráci](redemption-experience.md).

> [!IMPORTANT]
> Měli byste postupovat podle kroků [v tématu Postupy: Přidejte do Azure Active Directory](https://aka.ms/adprivacystatement) informace o ochraně osobních údajů vaší organizace a přidejte adresu URL prohlášení o zásadách ochrany osobních údajů vaší organizace. V rámci prvního procesu uplatnění pozvánky musí pozvaní uživatelé před pokračováním vyjádřit souhlas s vaším podmínkami ochrany osobních údajů. 

## <a name="before-you-begin"></a>Před zahájením

Ujistěte se, že jsou nakonfigurovaná externí nastavení spolupráce vaší organizace, aby bylo možné pozvat hosty. Ve výchozím nastavení mohou všichni uživatelé a správci pozvat hosty. Ale externí zásady spolupráce vaší organizace můžou být nakonfigurované tak, aby se zabránilo určitým typům uživatelů nebo správců v pozvání hostům. Pokud chcete zjistit, jak tyto zásady zobrazit a nastavit, přečtěte si téma [Povolení externí spolupráce B2B a Správa toho, kdo může pozvat hosty](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Přidat uživatele typu Host do adresáře

Pokud chcete do adresáře přidat uživatele spolupráce B2B, postupujte takto:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako uživatel, kterému je přiřazena omezená role adresáře správce nebo role pozvánky hosta.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **Uživatele**.
4. Vyberte **Nový uživatel typu host**.

   ![Zobrazuje, kde je nový uživatel typu Host v uživatelském rozhraní.](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > **Nová možnost uživatele typu Host** je k dispozici také na stránce **vztahy organizace** . V **Azure Active Directory**v části **Spravovat**vyberte **vztahy organizace**.

5. V části **Uživatelské jméno** zadejte e-mailovou adresu externího uživatele. Volitelně můžete zahrnout uvítací zprávu. Příklad:

   ![Zobrazuje, kde je nový uživatel typu Host v uživatelském rozhraní.](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > E-mailové adresy skupin se nepodporují; Zadejte e-mailovou adresu jednotlivce. Někteří poskytovatelé e-mailu navíc umožňují uživatelům přidávat symbol plus (+) a další text na své e-mailové adresy, které vám pomůžou s akcemi jako filtrování doručené pošty. Azure AD ale v současné době nepodporuje symboly plus v e-mailových adresách. Aby nedocházelo k problémům s doručováním, vynechejte symbol plus a všechny znaky, které následují až po symbol @.

6. Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. 
 
Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.


![Zobrazuje uživatele B2B s uživatelem typu Host.](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Přidat uživatele typu Host do skupiny
Pokud potřebujete ručně přidat uživatele spolupráce B2B do skupiny, postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat**vyberte **skupiny**.
4. Vyberte skupinu (nebo kliknutím na **Nová skupina** vytvořte novou). Je vhodné zahrnout do popisu skupiny, že skupina obsahuje uživatele typu Host B2B.
5. Vyberte **členy**. 
6. Udělejte jednu z těchto věcí:
   - Pokud uživatel typu host již v adresáři existuje, vyhledejte uživatele B2B. Vyberte uživatele a kliknutím na tlačítko **Vybrat** přidejte uživatele do skupiny.
   - Pokud uživatel typu Host ještě v adresáři neexistuje, Pozvěte ho do skupiny zadáním jejich e-mailové adresy do vyhledávacího pole, zadáním volitelné osobní zprávy a kliknutím na **Vybrat**. Pozvánka se automaticky přehraje pozvánému uživateli.
     
     ![Přidat tlačítko pozvat pro přidání členů typu Host](./media/add-users-administrator/GroupInvite.png)
   
Dynamické skupiny můžete použít také při spolupráci Azure AD B2B. Další informace najdete v tématu [dynamické skupiny a Azure Active Directory spolupráce B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Přidání uživatelů typu Host do aplikace

Pokud chcete do aplikace přidat uživatele spolupráce B2B, postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat**vyberte **podnikové aplikace** > **všechny aplikace**.
4. Vyberte aplikaci, do které chcete přidat uživatele typu Host.
5. Na řídicím panelu aplikace vyberte **celkem uživatelů** a otevřete podokno **Uživatelé a skupiny** .

    ![Tlačítko celkem uživatelů pro přidání otevřených uživatelů a skupin](./media/add-users-administrator/AppUsersAndGroups.png)

6. Vyberte **Přidat uživatele**.
7. V části **Přidat přiřazení**vyberte **uživatele a skupiny**.
8. Udělejte jednu z těchto věcí:
   - Pokud uživatel typu host již v adresáři existuje, vyhledejte uživatele B2B. Vyberte uživatele, klikněte na **Vybrat**a pak kliknutím na **přiřadit** přidejte uživatele do aplikace.
   - Pokud uživatel typu Host ještě v adresáři neexistuje, zadejte e-mailovou adresu uživatele v části **Vybrat člena nebo Pozvěte externího uživatele**. Do pole zpráva zadejte volitelnou osobní zprávu. V okně se zprávou klikněte na **pozvat**.
           
       ![Přidat tlačítko pozvat pro přidání členů typu Host](./media/add-users-administrator/AppInviteUsers.png)
   
      Klikněte na **Vybrat**a potom kliknutím na **přiřadit** přidejte uživatele do aplikace. Pozvánka se automaticky přehraje pozvánému uživateli.

9. Uživatel typu Host se zobrazí v seznamu **Uživatelé a skupiny** aplikace s přiřazenou rolí **výchozí přístup**. Pokud chcete změnit roli, udělejte toto:
   - Vyberte uživatele typu Host a pak vyberte **Upravit**. 
   - V části **Upravit přiřazení**klikněte na **Vybrat roli**a vyberte roli, kterou chcete vybranému uživateli přiřadit.
   - Klikněte na tlačítko **vyberte**.
   - Klikněte na **Přiřadit**.
 
## <a name="resend-invitations-to-guest-users"></a>Znovu poslat pozvánky uživatelům typu Host

Pokud uživatel typu Host ještě nepoužil své pozvání, můžete znovu odeslat e-mail s pozvánkou.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V navigačním podokně vyberte **Azure Active Directory**.
3. V části **Spravovat** vyberte **Uživatele**.
5. Vyberte uživatelský účet.
6. V části **Spravovat**vyberte **profil**.
7. Pokud uživatel pozvánku ještě nepřijal, je k dispozici možnost **opětovného odeslání pozvánky** . Kliknutím na toto tlačítko se znovu odešlete.

   ![Možnost opětovného odeslání pozvánky v profilu uživatele](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Pokud znovu odešlete pozvánku, která původně nasměrovala uživatele na konkrétní aplikaci, poznáte, že odkaz v nové pozvánce převezme uživatele do přístupového panelu na nejvyšší úrovni.

## <a name="next-steps"></a>Další postup

- Informace o tom, jak mohou správci služby Azure AD přidat uživatele typu Host B2B, najdete v tématu [jak informační pracovníci přidávají uživatele spolupráce B2B?](add-users-information-worker.md)
- Informace o e-mailu s pozvánkou najdete v tématu o [prvcích e-mailu s pozvánkou B2B pro spolupráci](invitation-email-elements.md).

