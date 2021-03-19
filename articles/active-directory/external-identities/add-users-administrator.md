---
title: Přidání uživatelů spolupráce B2B do Azure Portal – Azure AD
description: Ukazuje, jak může správce přidat uživatele typu Host do adresáře z partnerské organizace pomocí spolupráce B2B služby Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2566b427777aec273df17863f06040de20649d97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581843"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Přidat uživatele Azure Active Directory spolupráce B2B do Azure Portal

Jako uživatel, kterému je přiřazena kterákoli z omezených rolí adresáře správce, můžete použít Azure Portal k pozvání uživatelů pro spolupráci B2B. Uživatele typu Host můžete pozvat do adresáře, do skupiny nebo do aplikace. Po pozvání uživatele prostřednictvím kterékoli z těchto metod je účet pozvaného uživatele přidán do Azure Active Directory (Azure AD) s uživatelským typem *hosta*. Uživatel typu Host musí následně uplatnit své pozvání k přístupu k prostředkům. Pozvání uživatele nevyprší.

Po přidání uživatele typu Host do adresáře můžete buď Odeslat uživateli typu Host přímý odkaz na sdílenou aplikaci, nebo uživatel typu Host může kliknout na adresu URL pro uplatnění v e-mailu s pozvánkou. Další informace o procesu uplatnění, najdete v tématu věnovaném [uplatnění pozvánky B2B pro spolupráci](redemption-experience.md).

> [!IMPORTANT]
> Měli byste postupovat podle kroků v tématu [Postupy: Přidání informací o ochraně osobních údajů vaší organizace v tématu Azure Active Directory](../fundamentals/active-directory-properties-area.md) k přidání adresy URL prohlášení o zásadách ochrany osobních údajů vaší organizace. V rámci prvního procesu uplatnění pozvánky musí pozvaní uživatelé před pokračováním vyjádřit souhlas s vaším podmínkami ochrany osobních údajů. 

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jsou nakonfigurovaná externí nastavení spolupráce vaší organizace, aby bylo možné pozvat hosty. Ve výchozím nastavení mohou všichni uživatelé a správci pozvat hosty. Ale externí zásady spolupráce vaší organizace můžou být nakonfigurované tak, aby se zabránilo určitým typům uživatelů nebo správců v pozvání hostům. Pokud chcete zjistit, jak tyto zásady zobrazit a nastavit, přečtěte si téma [Povolení externí spolupráce B2B a Správa toho, kdo může pozvat hosty](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Přidat uživatele typu Host do adresáře

Pokud chcete do adresáře přidat uživatele spolupráce B2B, postupujte takto:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako uživatel, kterému je přiřazena omezená role adresáře správce nebo role pozvánky hosta.
2. Vyhledejte a vyberte **Azure Active Directory** z libovolné stránky.
3. V části **Spravovat** vyberte **Uživatelé**.
4. Vyberte **Nový uživatel typu host**.

   ![Zobrazuje, kde je nový uživatel typu Host v uživatelském rozhraní.](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. Na stránce **Nový uživatel** vyberte **pozvat uživatele** a pak přidejte informace o uživateli typu Host. 

    > [!NOTE]
    > E-mailové adresy skupin se nepodporují; Zadejte e-mailovou adresu jednotlivce. Někteří poskytovatelé e-mailu navíc umožňují uživatelům přidávat symbol plus (+) a další text na své e-mailové adresy, které vám pomůžou s akcemi jako filtrování doručené pošty. Azure AD ale v současné době nepodporuje symboly plus v e-mailových adresách. Aby nedocházelo k problémům s doručováním, vynechejte symbol plus a všechny znaky, které následují až po symbol @.

   - **Jméno.** Křestní jméno a příjmení uživatele typu Host.
   - **E-mailová adresa (povinné)**. E-mailová adresa uživatele typu Host
   - **Osobní zpráva (volitelné)** Přidejte do uživatele typu Host osobní uvítací zprávu.
   - **Skupiny**: uživatele typu Host můžete přidat do jedné nebo více existujících skupin nebo ho můžete provést později.
   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. 

7. Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. 
 
Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.


![Zobrazuje uživatele B2B s uživatelem typu Host.](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Přidání uživatelů typu host do skupiny
Pokud potřebujete ručně přidat uživatele spolupráce B2B do skupiny, postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte **Azure Active Directory** z libovolné stránky.
3. V části **Spravovat** vyberte **skupiny**.
4. Vyberte skupinu (nebo kliknutím na **Nová skupina** vytvořte novou). Je vhodné zahrnout do popisu skupiny, že skupina obsahuje uživatele typu Host B2B.
5. Vyberte možnost **Členové**. 
6. Proveďte některou z následujících akcí:
   - Pokud uživatel typu host již v adresáři existuje, vyhledejte uživatele B2B. Vyberte uživatele a kliknutím na tlačítko **Vybrat** přidejte uživatele do skupiny.
   - Pokud uživatel typu Host ještě v adresáři neexistuje, Pozvěte ho do skupiny zadáním jejich e-mailové adresy do vyhledávacího pole, zadáním volitelné osobní zprávy a kliknutím na **Vybrat**. Pozvánka se automaticky přehraje pozvánému uživateli.
     
     ![Přidat tlačítko pozvat pro přidání členů typu Host](./media/add-users-administrator/GroupInvite.png)
   
Dynamické skupiny můžete použít také při spolupráci Azure AD B2B. Další informace najdete v tématu [dynamické skupiny a Azure Active Directory spolupráce B2B](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Přidání uživatelů typu host do aplikace

Pokud chcete do aplikace přidat uživatele spolupráce B2B, postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte **Azure Active Directory** z libovolné stránky.
3. V části **Spravovat** vyberte **podnikové aplikace**  >  **všechny aplikace**.
4. Vyberte aplikaci, do které chcete přidat uživatele typu Host.
5. Na řídicím panelu aplikace vyberte **celkem uživatelů** a otevřete podokno **Uživatelé a skupiny** .

    ![Tlačítko celkem uživatelů pro přidání otevřených uživatelů a skupin](./media/add-users-administrator/AppUsersAndGroups.png)

6. Vyberte možnost **Přidat uživatele**.
7. V části **Přidat přiřazení** vyberte **uživatele a skupiny**.
8. Proveďte některou z následujících akcí:
   - Pokud uživatel typu host již v adresáři existuje, vyhledejte uživatele B2B. Vyberte uživatele, klikněte na **Vybrat** a pak kliknutím na **přiřadit** přidejte uživatele do aplikace.
   - Pokud uživatel typu Host ještě v adresáři neexistuje, zadejte e-mailovou adresu uživatele v části **Vybrat člena nebo Pozvěte externího uživatele**. Do pole zpráva zadejte volitelnou osobní zprávu. V okně se zprávou klikněte na **pozvat**.
           
       ![Snímek obrazovky, který zvýrazní, kam se má přidat e-mailová adresa uživatele, individuální zpráva a také zvýrazní tlačítko pozvat.](./media/add-users-administrator/AppInviteUsers.png)
   
      Klikněte na **Vybrat** a potom kliknutím na **přiřadit** přidejte uživatele do aplikace. Pozvánka se automaticky přehraje pozvánému uživateli.

9. Uživatel typu Host se zobrazí v seznamu **Uživatelé a skupiny** aplikace s přiřazenou rolí **výchozí přístup**. Pokud chcete změnit roli, udělejte toto:
   - Vyberte uživatele typu Host a pak vyberte **Upravit**. 
   - V části **Upravit přiřazení** klikněte na **Vybrat roli** a vyberte roli, kterou chcete vybranému uživateli přiřadit.
   - Klikněte na **Vybrat**.
   - Klikněte na **Přiřadit**.
 
## <a name="resend-invitations-to-guest-users"></a>Opětovné poslání pozvánky uživatelům typu host

Pokud uživatel typu Host ještě nepoužil své pozvání, můžete znovu odeslat e-mail s pozvánkou.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte **Azure Active Directory** z libovolné stránky.
3. V části **Spravovat** vyberte **Uživatelé**.
5. Vyberte uživatelský účet.
6. V části **Spravovat** vyberte **profil**.
7. Pokud uživatel tuto pozvánku ještě nepřijal, v části **Identita** se u **pozvánky** nastaví na **ne**. Pokud chcete pozvánku znovu odeslat, vyberte **(spravovat)**. Pak na stránce **Spravovat pozvánky** vedle * * znovu odeslat pozvánku? " Vyberte **Ano** a vyberte **Hotovo**.

> [!NOTE]
> Pokud znovu odešlete pozvánku, která původně nasměrovala uživatele na konkrétní aplikaci, poznáte, že odkaz v nové pozvánce převezme uživatele do přístupového panelu na nejvyšší úrovni.
> Pozvánky budou moct znovu posílat jenom uživatelé s oprávněními k pozvání.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak mohou správci služby Azure AD přidat uživatele typu Host B2B, najdete v tématu [jak informační pracovníci přidávají uživatele spolupráce B2B?](add-users-information-worker.md)
- Informace o e-mailu s pozvánkou najdete v tématu o [prvcích e-mailu s pozvánkou B2B pro spolupráci](invitation-email-elements.md).
