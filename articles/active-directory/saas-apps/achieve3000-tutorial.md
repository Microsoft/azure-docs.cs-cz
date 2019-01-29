---
title: 'Kurz: Integrace Azure Active Directory s Achieve3000 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Achieve3000.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 83a83d07-ff9c-46c4-b5ba-25fe2b2cd003
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jeedes
ms.openlocfilehash: c2a80c2accee391457a94efa2522f960bfbcf91e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174920"
---
# <a name="tutorial-azure-active-directory-integration-with-achieve3000"></a>Kurz: Integrace Azure Active Directory s Achieve3000

V tomto kurzu se dozvíte, jak integrovat Achieve3000 s Azure Active Directory (Azure AD).

Achieve3000 integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Achieve3000.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Achieve3000 (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Achieve3000, potřebujete následující položky:

- Předplatné Azure AD
- Achieve3000 jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Achieve3000 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-achieve3000-from-the-gallery"></a>Přidání Achieve3000 z Galerie
Konfigurace integrace Achieve3000 do služby Azure AD, budete muset přidat Achieve3000 z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Achieve3000 z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Achieve3000**vyberte **Achieve3000** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Achieve3000 v seznamu výsledků](./media/achieve3000-tutorial/tutorial_achieve3000_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Achieve3000 podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Achieve3000 je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Achieve3000 potřeba navázat.

V Achieve3000, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Achieve3000, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Achieve3000](#create-an-achieve3000-test-user)**  – Pokud chcete mít protějšek Britta Simon Achieve3000, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Achieve3000.

**Ke konfiguraci Azure AD jednotné přihlašování s Achieve3000, proveďte následující kroky:**

1. Na webu Azure Portal na **Achieve3000** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/achieve3000-tutorial/tutorial_achieve3000_samlbase.png)

3. Na **Achieve3000 domény a adresy URL** části, proveďte následující kroky:

    ![Achieve3000 domény a adresy URL jednotného přihlašování – informace](./media/achieve3000-tutorial/tutorial_achieve3000_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následující: vzoru: `https://saml.achieve3000.com/district/<District Identifier>`

    b. V **identifikátor** textového pole zadejte hodnotu: `achieve3000-saml`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Achieve3000 klienta](https://www.achieve3000.com/contact-us/) má být získána hodnota. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/achieve3000-tutorial/tutorial_achieve3000_certificate.png) 

5. Aplikace Achieve3000 očekává jedinečné **studentID** hodnota v názvu identifikátor deklarace identity. Odběratele můžete namapovat správnou hodnotu pro deklaraci identity identifikátor názvu. V tomto případě jsme změnili **user.mail** pro účely ukázky. Ale podle jedinečný identifikátor, byste měli namapovat správnou hodnotu pro něj.   

    ![Konfigurovat Single Sign-On attb](./media/achieve3000-tutorial/tutorial_achieve3000_attribute.png)

6. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | studentID               | user.mail |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat](./media/achieve3000-tutorial/tutorial_officespace_04.png)

    ![Nakonfigurovat jednotné přihlašování Addattb](./media/achieve3000-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/achieve3000-tutorial/tutorial_general_400.png)
    
8. Ke konfiguraci jednotného přihlašování na **Achieve3000** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Achieve3000](https://www.achieve3000.com/contact-us/). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/achieve3000-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/achieve3000-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/achieve3000-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/achieve3000-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-achieve3000-test-user"></a>Vytvořit testovacího uživatele Achieve3000

V této části vytvoříte uživatele v Achieve3000 jako Britta Simon. Práce s [tým podpory Achieve3000](https://www.achieve3000.com/contact-us/) přidat uživatele na platformě Achieve3000. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Achieve3000 použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Achieve3000 Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Achieve3000**.

    ![Odkaz Achieve3000 v seznamu aplikací](./media/achieve3000-tutorial/tutorial_achieve3000_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Achieve3000 na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Achieve3000.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/achieve3000-tutorial/tutorial_general_01.png
[2]: ./media/achieve3000-tutorial/tutorial_general_02.png
[3]: ./media/achieve3000-tutorial/tutorial_general_03.png
[4]: ./media/achieve3000-tutorial/tutorial_general_04.png

[100]: ./media/achieve3000-tutorial/tutorial_general_100.png

[200]: ./media/achieve3000-tutorial/tutorial_general_200.png
[201]: ./media/achieve3000-tutorial/tutorial_general_201.png
[202]: ./media/achieve3000-tutorial/tutorial_general_202.png
[203]: ./media/achieve3000-tutorial/tutorial_general_203.png

