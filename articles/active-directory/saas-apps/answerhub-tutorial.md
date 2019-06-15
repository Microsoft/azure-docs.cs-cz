---
title: 'Kurz: Integrace Azure Active Directory s AnswerHub | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e51c37f983162b82747760b05e2ec5e4e6553c0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106946"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Kurz: Integrace Azure Active Directory s AnswerHub

V tomto kurzu se dozvíte, jak integrovat AnswerHub s Azure Active Directory (Azure AD).
AnswerHub integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit, kdo má přístup k AnswerHub Azure AD.
* Můžete nechat uživatele automaticky se přihlašovat k AnswerHub pomocí jejich účtů služby Azure AD (jednotné přihlašování).
* Vaše účty můžete spravovat z centrálního umístění: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AnswerHub, budete potřebovat následující:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete začít [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné AnswerHub obsahující single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* AnswerHub podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-answerhub-from-the-gallery"></a>Přidání AnswerHub z Galerie

Nastavení integrace AnswerHub do služby Azure AD, budete muset přidat AnswerHub z Galerie do spravovaných aplikací SaaS.

**Chcete-li přidat AnswerHub z galerie:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **AnswerHub**. Vyberte **AnswerHub** v seznamu výsledků a pak vyberte **přidat**.

     ![AnswerHub v seznamu výsledků](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Nastavení a testování Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s AnswerHub pomocí testovacího uživatele s názvem Britta Simon.
Pro jednotné přihlašování budete muset vytvořit propojení mezi uživatele služby Azure AD a odpovídajícího uživatele v AnswerHub.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AnswerHub, které potřebujete k dokončení těchto úloh:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům použít funkci.
2. [Konfigurace AnswerHub jednotného přihlašování](#configure-answerhub-single-sign-on) nastavit nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) s názvem Britta Simon.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. Vytvořte testovacího uživatele AnswerHub, která odpovídá a je propojena s testovacího uživatele Azure AD.
6. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části nastavíte Azure AD jednotného přihlašování na portálu Azure portal.

**Konfigurace služby Azure AD jednotné přihlašování s AnswerHub:**

1. V [webu Azure portal](https://portal.azure.com/)na **AnswerHub** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Jednotné přihlašování – tlačítko](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Dialogové okno jednotné přihlašování – vyberte – metoda](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte ikonu úprav otevřete **základní konfiguraci SAML** dialogové okno.

    ![Nastavte si jednotné přihlašování pomocí SAML, stránce](common/edit-urls.png)

4. V **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Základní část konfigurace SAML](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** zadejte adresu URL, která má tento model: `https://<company>.answerhub.com`

    b. V **identifikátor (Entity ID)** zadejte adresu URL, která má tento model: `https://<company>.answerhub.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Obraťte se [tým podpory AnswerHub](mailto:success@answerhub.com) k získání hodnoty. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **certifikát (Base64)** , podle požadavků vaší a uložte certifikát ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V **nastavení AnswerHub** tématu, zkopírujte příslušné adresy URL nebo adresy URL, na základě vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

   Můžete zkopírovat tyto adresy URL:
    - Přihlašovací adresa URL

    - Identifikátor Azure AD

    - Adresa URL – odhlášení

### <a name="configure-answerhub-single-sign-on"></a>Konfigurace AnswerHub jednotného přihlašování

V této části nastavíte jednotné přihlašování pro AnswerHub.  

**Postup konfigurace AnswerHub jednotné přihlašování:**

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti AnswerHub jako správce.

    > [!NOTE]
    > Pokud potřebujete pomoc s konfigurací AnswerHub, obraťte se [tým podpory AnswerHub](mailto:success@answerhub.com.).

2. Přejděte na **správu**.

3. Na **uživatele a skupiny** kartu v levém podokně v **sociální nastavení** vyberte **nastavení SAML**.

4. Na **konfigurace zprostředkovatele identity** kartu, proveďte následující kroky:

    ![Kartu uživatelé a skupiny](./media/answerhub-tutorial/ic785172.png "nastavení SAML")  
  
    a. V **přihlašovací adresa URL zprostředkovatele identity** pole, vložte **přihlašovací adresa URL** , který jste zkopírovali z portálu Azure portal.
  
    b. V **odhlašovací adresa URL zprostředkovatele identity** pole, vložte **odhlašovací adresa URL** , který jste zkopírovali z portálu Azure portal.

    c. V **formát identifikátor zprostředkovatele identity názvu** zadejte **identifikátor** hodnoty vybrané v **atributy uživatele** části na webu Azure portal.
  
    d. Vyberte **klíčích a certifikátech**.

5. V **klíčích a certifikátech** části, proveďte následující kroky:

    ![Část klíče a certifikáty](./media/answerhub-tutorial/ic785173.png "klíčů a certifikátů")  

    a. Otevřete certifikát, který jste stáhli z portálu Azure v programu Poznámkový blok s kódováním Base64, zkopírujte jeho obsah a vložte obsah do **zprostředkovatele identity veřejného klíče (x 509 formátu)** pole.
  
    b. Vyberte **Uložit**.

6. Na **konfigurace zprostředkovatele identity** kartu, vyberte možnost **Uložit** znovu.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

**Chcete-li vytvořit testovacího uživatele Azure AD:**

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vyberte Azure Active Directory, uživatelů, všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnostech uživatele dokončete tyto kroky.

    ![Uživatelské vlastnosti](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon\@< yourcompanydomain.extension >** .  
    Například, BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části nastavíte Britta Simon používat Azure AD jednotného přihlašování tak, že udělíte přístup k AnswerHub.

**Přiřadit uživatele Azure AD:**

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **AnswerHub**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **AnswerHub**.

    ![Seznam aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Přidat přiřazení podokno](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu a pak vyberte **vyberte** tlačítko v dolní části na obrazovce.

6. Pokud očekáváte hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. 

7. Vyberte **vyberte** tlačítko v dolní části obrazovky.

8. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-an-answerhub-test-user"></a>Vytvořit testovacího uživatele AnswerHub

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k AnswerHub, budete muset přidat v AnswerHub. Tato úloha AnswerHub, se provádí ručně.

**Nastavení uživatelského účtu:**

1. Přihlaste se k vaší **AnswerHub** společnosti serveru jako správce.

2. Přejděte na **správu**.

3. Vyberte **uživatelů a skupin** kartu.

4. V levém podokně v **spravovat uživatele** vyberte **vytvoření nebo import uživatelů**a pak vyberte **uživatelů a skupin**.

   ![Kartu uživatelé a skupiny](./media/answerhub-tutorial/ic785175.png "uživatelů a skupin")

5. Do příslušných polí zadejte **e-mailová adresa**, **uživatelské jméno**, a **heslo** platný Azure AD, že chcete přidat a pak vyberte účtu **uložit** .

> [!NOTE]
> Můžete použít jakýkoli jiný nástroj vytváření uživatelského účtu nebo rozhraní API poskytovaných AnswerHub nastavení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Při výběru dlaždice AnswerHub na přístupovém panelu, můžete by měl být automaticky přihlášeni k AnswerHub, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

