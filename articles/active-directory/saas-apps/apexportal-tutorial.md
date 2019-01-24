---
title: 'Kurz: Integrace Azure Active Directory pomocí portálu vrcholu | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a portálu vrcholu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: db204a46-6460-4ace-bdbb-4353846723ad
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: jeedes
ms.openlocfilehash: 25fb68d3f29e0b3589e582551b88cc1cc7b5392f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819845"
---
# <a name="tutorial-azure-active-directory-integration-with-apex-portal"></a>Kurz: Integrace Azure Active Directory pomocí portálu vrcholu

V tomto kurzu se dozvíte, jak integrovat vrcholu portálu Azure Active Directory (Azure AD).

Integrace s Azure AD Portal vrcholu poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k portálu vrcholu.
- Uživatele, aby automaticky získat přihlášení k portálu vrcholu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí portálu vrcholu, potřebujete následující položky:

- Předplatné Azure AD
- Portálu vrcholu jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání portálu vrcholu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-apex-portal-from-the-gallery"></a>Přidání portálu vrcholu z Galerie
Konfigurovat integraci portálu vrcholu do služby Azure AD, budete muset přidat portál vrcholu v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat portál vrcholu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **vrcholu portál**vyberte **vrcholu portál** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Portál vrcholu v seznamu výsledků](./media/apexportal-tutorial/tutorial_apexonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí portálu vrcholu podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek portálu vrcholu je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatel portálu vrcholu.

Na portálu vrcholu, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí portálu vrcholu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele portál vrcholu](#create-an-apex-online-test-user)**  – Pokud chcete mít protějšek Britta Simon vrcholu portálu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci portál vrcholu.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí portálu vrcholu, proveďte následující kroky:**

1. Na webu Azure Portal na **vrcholu portál** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/apexportal-tutorial/tutorial_apexonline_samlbase.png)

3. Na **vrcholu portál domény a adresy URL** části, proveďte následující kroky:

    ![Vrcholu portál domény a adresy URL jednotného přihlašování – informace](./media/apexportal-tutorial/tutorial_apexonline_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory vrcholu portál](mailto:support@apexanalytix.com) k získání těchto hodnot.
 
4. Aplikace portálu vrcholu očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. 

    ![Konfigurace jednotného přihlašování](./media/apexportal-tutorial/attribute.png)

5. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | JMÉNO | user.givenname |
    | PŘÍJMENÍ | user.surname |
    | MAIL | user.mail |    

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/apexportal-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/apexportal-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

6. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/apexportal-tutorial/tutorial_apexonline_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/apexportal-tutorial/tutorial_general_400.png)

8. Ke konfiguraci jednotného přihlašování na **vrcholu portál** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory vrcholu portál](mailto:support@apexanalytix.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/apexportal-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/apexportal-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/apexportal-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/apexportal-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-an-apex-portal-test-user"></a>Vytvořit testovacího uživatele portál vrcholu

Cílem této části je vytvořte uživatele Britta Simon portálu vrcholu. Vrcholu portál podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k portálu vrcholu, pokud ještě neexistuje.
 
> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory vrcholu portál](mailto:support@apexanalytix.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na portál vrcholu.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon portál vrcholu, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **vrcholu portál**.

    ![Na portálu vrcholu odkaz v seznamu aplikací](./media/apexportal-tutorial/tutorial_apexonline_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici vrcholu portál na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci portál vrcholu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/apexportal-tutorial/tutorial_general_01.png
[2]: ./media/apexportal-tutorial/tutorial_general_02.png
[3]: ./media/apexportal-tutorial/tutorial_general_03.png
[4]: ./media/apexportal-tutorial/tutorial_general_04.png

[100]: ./media/apexportal-tutorial/tutorial_general_100.png

[200]: ./media/apexportal-tutorial/tutorial_general_200.png
[201]: ./media/apexportal-tutorial/tutorial_general_201.png
[202]: ./media/apexportal-tutorial/tutorial_general_202.png
[203]: ./media/apexportal-tutorial/tutorial_general_203.png

