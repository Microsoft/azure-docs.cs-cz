---
title: 'Rychlý start: Přidání uživatelů typu host na webu Azure Portal'
description: Použijte tento rychlý start, abyste se dozvěděli, jak můžou správci Azure AD přidávat uživatele typu host B2B na webu Azure Portal, a abyste si prošli pracovní postup pozvání B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8916711ab7d5278f34a3d4ad101a0aabb883f84c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182693"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Rychlý start: Přidat uživatele typu Host do adresáře na webu Azure Portal

Ke spolupráci s vaší organizací můžete pozvat kohokoli. Stačí, když je přidáte do adresáře jako uživatele typu host. Pak jim můžete buď poslat uvítací e-mail s odkazem na přijetí pozvánky nebo jim můžete poslat přímý odkaz na aplikaci, kterou chcete sdílet. Uživatelé typu host se můžou přihlásit pomocí pracovní, školní nebo sociální identity.

V tomto rychlém startu přidáte nového uživatele typu host do Azure AD, odešlete pozvánku a podíváte se, jak vypadá proces přijetí pozvánky uživatelem typu host.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení scénáře v tomto kurzu budete potřebovat:

 - Roli, která vám umožňuje vytvářet uživatele v adresáři tenanta, jako je například role globálního správce nebo jiná omezená správcovská role adresáře.
 - Platný e-mailový účet, který můžete přidat do adresáře tenanta a který můžete použít k přijetí testovacího uvítacího e-mailu.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Přidání nového uživatele typu host v Azure AD

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce.
2. V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Uživatele**.

    ![Vyberte Azure Active Directory.](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Vyberte **Nový uživatel typu host**.

    ![Vyberte Azure Active Directory.](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5.  V části **Uživatelské jméno** zadejte e-mailovou adresu externího uživatele. V části **Přiložit k pozvánce osobní zprávu** zadejte uvítací zprávu. 

    ![Vyberte Azure Active Directory.](media/quickstart-add-users-portal/quickstart-users-portal-user-4.png)

6. Výběrem možnosti **Pozvat** automaticky odešlete uživateli typu host pozvánku. V pravém horním rohu se zobrazí oznámení se zprávou **Uživatel je úspěšně pozvaný**. 
7.  Po odeslání e-mailové pozvánky se uživatelský účet automaticky přidá do adresáře jako uživatel typu host.

## <a name="assign-an-app-to-the-guest-user"></a>Přiřazení aplikace uživateli typu host
Přidejte do svého testovacího tenanta aplikaci Salesforce a přiřaďte k ní testovacího uživatele typu host.
1.  Přihlaste se na web Azure Portal jako správce.
2.  V levém podokně vyberte **Podnikové aplikace**.
3.  Vyberte **Nová aplikace**.
4. V části **Přidat z galerie** vyhledejte aplikaci **Salesforce** a vyberte ji.

    ![Vyberte Azure Active Directory.](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Vyberte **Přidat**.
6. V části **Spravovat** vyberte **Jednotné přihlašování** a v části **Režim jednotného přihlašování** vyberte **Přihlašování pomocí hesel** a klikněte na **Uložit**.
7. V části **Spravovat** vyberte **Uživatelé a skupiny** > **Přidat uživatele** > **Uživatelé a skupiny**.
8. Ve vyhledávacím poli vyhledejte testovacího uživatele (pokud je to nutné) a vyberte ho ze seznamu. Pak klikněte na **Vybrat**.
9. Vyberte **Přiřadit**. 

## <a name="accept-the-invitation"></a>Přijetí pozvánky
Teď se přihlásíte jako uživatel typu host, abyste viděli pozvánku.
1.  Přihlaste se k e-mailovému účtu testovacího uživatele typu host.
2.  V doručené poště vyhledejte e-mail „You're invited“ (Zveme vás).

    ![E-mail B2B s pozváním](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  V těle e-mailu vyberte **Get Started** (Začínáme). V prohlížeči se otevře stránka **Zkontrolovat oprávnění**. 

    ![Stránka přijmutí pozvání B2B](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Vyberte **Přijmout**. Otevře se Přístupový panel s aplikacemi, ke kterým má uživatel typu host přístup.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Testovacího uživatele typu host a testovací aplikaci můžete odstranit, pokud už je nepotřebujete.
1.  Přihlaste se na web Azure Portal jako správce.
2.  V levém podokně vyberte **Azure Active Directory**.
3.  V části **Spravovat** vyberte **Podnikové aplikace**.
4.  Otevřete aplikaci **Salesforce** a pak vyberte **Odstranit**.
5.  V levém podokně vyberte **Azure Active Directory**.
6.  V části **Spravovat** vyberte **Uživatele**.
7.  Vyberte testovacího uživatele a potom vyberte **Odstranit uživatele**.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste na webu Azure Portal vytvořili uživatele typu host a odeslali jste pozvánku, která sdílí aplikaci. Potom jste si prohlédli proces přijetí pozvánky z pohledu uživatele typu host a ověřili jste, že se aplikace na Přístupovém panelu uživatele zobrazila. Další informace o přidávání uživatelů typu host ke spolupráci najdete v článku o [přidávání uživatelů pro spolupráci B2B služby Azure Active Directory na webu Azure Portal](add-users-administrator.md).
