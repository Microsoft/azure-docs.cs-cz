---
title: 'Kurz: Azure Active Directory integrace s pole | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pole.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c15894f8cd559a08a1d75e2ac29cc0da0fc9e963
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226574"
---
# <a name="integrate-azure-active-directory-with-box"></a>Pole integrovat Azure Active Directory

V tomto kurzu jste zjistěte, jak integrovat Azure Active Directory (Azure AD) s pole.

Díky integraci služby Azure AD se pole, získáte následující výhody:

- Můžete ovládat ve službě Azure AD, kdo má přístup k poli.
- Můžete povolit uživatelům získat přihlášení automaticky pole (jednotné přihlašování a jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě, portálu Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s poli, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené jednotné přihlašování pole

> [!NOTE]
> Při testování kroky v tomto kurzu, doporučujeme, abyste provedli *není* použít provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání pole z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-box-from-the-gallery"></a>Přidat pole z Galerie
Při konfiguraci integrace služby Azure AD s pole, přidejte pole z Galerie si na seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [portál Azure](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![Okno "Podnikové aplikace"][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítka v horní části okna.

    !["Nová aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **pole**, vyberte **pole** v seznamu výsledků a potom vyberte **přidat**.

    ![Pole v seznamu výsledků](./media/box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s pole, podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí identifikovat pole uživatel a jeho protějšku ve službě Azure AD. Jinými slovy je nutné vytvořit vztah propojení mezi uživatele Azure AD a stejného uživatele v poli.

K navázání vztahu odkaz, přiřadit jako pole *uživatelské jméno* hodnotu *uživatelské jméno* ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s poli, dokončete stavebních bloků v následujících pět částech.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

Povolení služby Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci pole následujícím způsobem:

1. Na portálu Azure v **pole** okno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Na odkaz "Jednotného přihlašování"][4]

2. V **jednotného přihlašování** okno v **režimu přihlašování** vyberte **na základě SAML přihlašování**.
 
    ![Okno "Jednotného přihlašování"](./media/box-tutorial/tutorial_box_samlbase.png)

3. V části **pole domény a adresy URL**, postupujte takto:

    !["Zadejte domény a adresy URL" jednotné přihlašování informace](./media/box-tutorial/url3.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: *https://\<subdoména >. box.com*.

    b. V **identifikátor** textovému poli, typ **box.net**.
     
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Aktualizaci s skutečné přihlašovací adresa URL a identifikátor. Chcete-li získat hodnoty, obraťte se [tým podpory klienta pole](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. V části **SAML podpisový certifikát**, vyberte **soubor XML s metadaty**a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/box-tutorial/tutorial_box_certificate.png) 

5. Vyberte **Uložit**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/box-tutorial/tutorial_general_400.png)
    
6. Chcete-li nakonfigurovat jednotné přihlašování pro aplikace, postupujte podle pokynů v [nastavit jednotné přihlašování na vlastní](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Pokud pro váš účet pole nelze povolit nastavení jednotného přihlašování, možná budete muset kontaktovat [tým podpory klienta pole](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) a zadejte stažený soubor XML.

> [!TIP]
> Jak při instalaci aplikace, můžete si přečíst stručným verzi podle předchozích pokynů v [portál Azure](https://portal.azure.com). Po přidání aplikace v **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a potom přejdete vložené v dokumentaci **konfigurace** v dolní části. Další informace o funkci embedded dokumentaci najdete v tématu [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

V této části vytvoříte testovacího uživatele Britta Simon na portálu Azure, následujícím způsobem:

![Vytvořit testovací uživatele Azure AD][100]

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Odkaz Azure Active Directory](./media/box-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam aktuálního uživatele, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/box-tutorial/create_aaduser_02.png)

3. V horní části **všichni uživatelé** vyberte **přidat**.

    ![Tlačítko Přidat](./media/box-tutorial/create_aaduser_03.png)

    **Uživatele** otevře se okno.

4. V **uživatele** okno, postupujte takto:

    ![Okno uživatele](./media/box-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-box-test-user"></a>Vytvoření zkušebního uživatele pole

V této části vytvoříte testovacího uživatele Britta Simon pole. Pole podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Pokud uživatel ještě neexistuje, vytvoří se nový při pokusu o přístup k poli. Není třeba z vám umožní vytvořit uživateli žádné akce.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolit uživatele Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k poli. Chcete-li to provést, postupujte takto:

![Přiřadit role uživatele][200]

1. Na portálu Azure otevřete **aplikace** zobrazení, přejděte na **Directory** zobrazit a potom vyberte **podnikové aplikace, které** > **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

2. V **aplikace** seznamu, vyberte **pole**.

    ![Propojení pole](./media/box-tutorial/tutorial_box_app.png)  

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** okno v **uživatelé** seznamu, vyberte **Britta Simon**.

6. Vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete **pole** dlaždici na přístupovém panelu, můžete otevřít stránku přihlášení pro přihlášení do aplikace pole.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS do Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/box-tutorial/tutorial_general_01.png
[2]: ./media/box-tutorial/tutorial_general_02.png
[3]: ./media/box-tutorial/tutorial_general_03.png
[4]: ./media/box-tutorial/tutorial_general_04.png

[100]: ./media/box-tutorial/tutorial_general_100.png

[200]: ./media/box-tutorial/tutorial_general_200.png
[201]: ./media/box-tutorial/tutorial_general_201.png
[202]: ./media/box-tutorial/tutorial_general_202.png
[203]: ./media/box-tutorial/tutorial_general_203.png

