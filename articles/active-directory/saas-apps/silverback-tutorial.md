---
title: 'Kurz: Integrace Azure Active Directory s Silverback | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Silverback.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172702"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Kurz: Integrace Azure Active Directory s Silverback

V tomto kurzu se dozvíte, jak integrovat Silverback s Azure Active Directory (Azure AD).

Silverback integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Silverback.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Silverback (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Silverback, potřebujete následující položky:

- Předplatné Azure AD
- Aktivní předplatné Silverback

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Silverback z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-silverback-from-the-gallery"></a>Přidání Silverback z Galerie
Konfigurace integrace Silverback do služby Azure AD, budete muset přidat Silverback z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Silverback z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Silverback**vyberte **Silverback** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Silverback v seznamu výsledků](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Silverback podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Silverback je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Silverback potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Silverback, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Silverback](#create-a-silverback-test-user)**  – Pokud chcete mít protějšek Britta Simon Silverback, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Silverback.

**Ke konfiguraci Azure AD jednotné přihlašování s Silverback, proveďte následující kroky:**

1. Na webu Azure Portal na **Silverback** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. Na **Silverback domény a adresy URL** části, proveďte následující kroky:

    ![Silverback domény a adresy URL jednotného přihlašování – informace](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<YOURSILVERBACKURL>.com/ssp`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `<YOURSILVERBACKURL>.com`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory Silverback klienta](mailto:helpdesk@matrix42.com) k získání těchto hodnot. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/silverback-tutorial/tutorial_general_400.png)

6.  Přihlaste se k serveru Silverback jako správce a proveďte následující kroky:

    a.  Přejděte do **správce** > **zprostředkovatele ověřování**.

    b. Na **nastavení poskytovatele ověřování** stránce, proveďte následující kroky:

    ![Správce ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  Klikněte na **importovat z adresy URL**.
    
    d.  Vložte zkopírovanou adresu URL metadat a klikněte na tlačítko **OK**.
    
    e.  Potvrzení pro **OK** pak hodnoty vyplní automaticky.
    
    f.  Povolit **zobrazit na přihlašovací stránce**.
    
    g.  Povolit **dynamické vytvoření uživatele** Pokud chcete přidat uživatele Azure AD oprávnění automaticky (volitelné).
    
    h.  Vytvoření **Title** pro tlačítko na samoobslužný portál.

    i.  Nahrání **ikonu** po kliknutí na **zvolit soubor**.
    
    j.  Vyberte na pozadí **barva** pro tlačítko.
    
    k.  Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/silverback-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/silverback-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/silverback-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/silverback-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-silverback-test-user"></a>Vytvoření zkušebního uživatele Silverback

Přihlaste se k Silverback Azure AD uživatelům umožnit, musí být poskytnuty do Silverback. Zřizování v Silverback, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k serveru Silverback jako správce.

2. Přejděte do **uživatelé** a **přidat nového uživatele zařízení**.

3. Na **základní** stránce, proveďte následující kroky:

    ![Uživatel ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. V **uživatelské jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    c. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **Simon**.

    d. V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    e. V **heslo** textové pole, zadejte své heslo.
    
    f. V **potvrzení hesla** textového pole zadejte znovu své heslo a potvrďte.

    g. Klikněte na **Uložit**.

>[!NOTE]
>Pokud nechcete vytvořit každý uživatel ručně povolit **dynamické vytvoření uživatele** zaškrtávací políčko **správce** > **zprostředkovatele ověřování**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Silverback použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Silverback Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Silverback**.

    ![Odkaz Silverback v seznamu aplikací](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Silverback na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Silverback.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

