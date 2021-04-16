---
title: 'Rychlý Start: Přidání uživatelů typu Host v Azure Portal – Azure AD'
description: Použijte tento rychlý start, abyste se dozvěděli, jak můžou správci Azure AD přidávat uživatele typu host B2B na webu Azure Portal, a abyste si prošli pracovní postup pozvání B2B.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/05/2020
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7326a35d07715eae75f70f2f33763f82946c589c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529851"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Rychlý start: Přidání uživatelů typu host do adresáře pomocí webu Azure Portal

Ke spolupráci s vaší organizací můžete pozvat kohokoli. Stačí, když je přidáte do adresáře jako uživatele typu host. Pak mu můžete buď poslat uvítací e-mail s odkazem na přijetí pozvánky nebo mu můžete poslat přímý odkaz na aplikaci, kterou chcete sdílet. Uživatelé typu host se můžou přihlásit pomocí pracovní, školní nebo sociální identity. Společně s tímto rychlým startem si můžete přečíst další informace o přidávání uživatelů typu host [v Azure Portal](add-users-administrator.md), prostřednictvím [PowerShellu](b2b-quickstart-invite-powershell.md)nebo [hromadně](tutorial-bulk-invite.md).

V tomto rychlém startu přidáte nového uživatele typu Host do adresáře služby Azure AD prostřednictvím Azure Portal, odešlete pozvánku a zjistíte, jak bude proces uplatnění pozvánky uživatele typu Host vypadat.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto kurzu budete potřebovat:

 - Roli, která vám umožňuje vytvářet uživatele v adresáři tenanta, jako je například role globálního správce nebo jiná omezená správcovská role adresáře.
 - Platný e-mailový účet, který můžete přidat do adresáře tenanta a který můžete použít k přijetí testovacího uvítacího e-mailu.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Přidání nového uživatele typu host v Azure AD

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Uživatelé**.

    ![Snímek obrazovky s informacemi o tom, kde vybrat možnost uživatelů](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Vyberte **Nový uživatel typu host**.

    ![Snímek obrazovky znázorňující, kde vybrat novou možnost uživatele typu Host](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Na stránce **Nový uživatel** vyberte **pozvat uživatele** a pak přidejte informace o uživateli typu Host. 

   - **Jméno.** Křestní jméno a příjmení uživatele typu Host.
   - **E-mailová adresa (povinné)**. E-mailová adresa uživatele typu Host
   - **Osobní zpráva (volitelné)** Přidejte do uživatele typu Host osobní uvítací zprávu.
   - **Skupiny**: uživatele typu Host můžete přidat do jedné nebo více existujících skupin nebo ho můžete provést později.
   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. 

6. Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. V pravém horním rohu se zobrazí oznámení se zprávou **Uživatel je úspěšně pozvaný**. 
7.  Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.

## <a name="assign-an-app-to-the-guest-user"></a>Přiřazení aplikace uživateli typu host
Přidejte do svého testovacího tenanta aplikaci Salesforce a přiřaďte k ní testovacího uživatele typu host.
1.  Přihlaste se na web Azure Portal jako správce.
2.  V levém podokně vyberte **Podnikové aplikace**.
3.  Vyberte **Nová aplikace**.
4. V části **Přidat z galerie** vyhledejte aplikaci **Salesforce** a vyberte ji.

    ![Snímek obrazovky s polem přidat z Galerie hledání](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Vyberte **Přidat**.
6. V části **Spravovat** vyberte **Jednotné přihlašování** a v části **Režim jednotného přihlašování** vyberte **Přihlašování pomocí hesel** a klikněte na **Uložit**.
7. V části **Spravovat** vyberte **Uživatelé a skupiny** > **Přidat uživatele** > **Uživatelé a skupiny**.
8. Ve vyhledávacím poli vyhledejte testovacího uživatele (pokud je to nutné) a vyberte ho ze seznamu. Pak klikněte na **Vybrat**.
9. Vyberte **Přiřadit**. 

## <a name="accept-the-invitation"></a>Přijetí pozvánky
Teď se přihlásíte jako uživatel typu host, abyste viděli pozvánku.
1.  Přihlaste se k e-mailovému účtu testovacího uživatele typu host.
2.  V doručené poště vyhledejte e-mail „You're invited“ (Zveme vás).

    ![Snímek obrazovky s e-mailem pozvánky B2B](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  V těle e-mailu vyberte **Get Started** (Začínáme). V prohlížeči se otevře stránka **Zkontrolovat oprávnění**. 

    ![Snímek obrazovky se stránkou pro kontrolu oprávnění](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Vyberte **Přijmout**. Otevře se Přístupový panel s aplikacemi, ke kterým má uživatel typu host přístup.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Testovacího uživatele typu host a testovací aplikaci můžete odstranit, pokud už je nepotřebujete.
1.  Přihlaste se na web Azure Portal jako správce.
2.  V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Podnikové aplikace**.
4.  Otevřete aplikaci **Salesforce** a pak vyberte **Odstranit**.
5.  V levém podokně vyberte **Azure Active Directory**.
6.  V části **Spravovat** vyberte **Uživatelé**.
7.  Vyberte testovacího uživatele a potom vyberte **Odstranit uživatele**.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste na webu Azure Portal vytvořili uživatele typu host a odeslali jste pozvánku, která sdílí aplikaci. Potom jste si prohlédli proces přijetí pozvánky z pohledu uživatele typu host a ověřili jste, že se aplikace na Přístupovém panelu uživatele zobrazila. Další informace o přidávání uživatelů typu host ke spolupráci najdete v článku o [přidávání uživatelů pro spolupráci B2B služby Azure Active Directory na webu Azure Portal](add-users-administrator.md).
