---
title: 'Kurz: Azure Active Directory integrace s AnswerHub | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AnswerHub.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: ef09ef7d2ca6dd68def732831f9341dcd3aa6d62
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458043"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Kurz: Azure Active Directory integrace s AnswerHub

V tomto kurzu se dozvíte, jak integrovat AnswerHub s Azure Active Directory (Azure AD).
Integrace AnswerHub s Azure AD přináší tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k AnswerHub.
* Uživatelům můžete umožnit, aby se automaticky přihlásili k AnswerHub pomocí svých účtů Azure AD (jednotné přihlašování).
* Účty můžete spravovat z centrálního umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s AnswerHub potřebujete následující:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete zahájit měsíční [zkušební období](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné AnswerHub s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AnswerHub podporuje jednotné přihlašování iniciované v SP.

## <a name="add-answerhub-from-the-gallery"></a>Přidání AnswerHub z Galerie

K nastavení integrace AnswerHub do služby Azure AD je nutné přidat AnswerHub z Galerie do spravovaných aplikací SaaS.

**Přidání AnswerHub z Galerie:**

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte možnost **Azure Active Directory**.

    ![Azure Active Directory – tlačítko](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **AnswerHub**. V seznamu výsledků vyberte **AnswerHub** a pak vyberte **Přidat**.

     ![AnswerHub v seznamu výsledků](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Nastavení a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí AnswerHub pomocí testovacího uživatele s názvem Britta Simon.
V případě jednotného přihlašování je potřeba vytvořit propojení mezi uživatelem služby Azure AD a odpovídajícím uživatelem v AnswerHub.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AnswerHub, musíte dokončit tyto úlohy:

1. [Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on) tak, aby vaši uživatelé mohli používat tuto funkci.
2. [Nakonfigurujte jednotné přihlašování AnswerHub](#configure-answerhub-single-sign-on) , abyste na straně aplikace nastavili nastavení jednotného přihlašování.
3. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) s názvem Britta Simon.
4. [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. Vytvořte testovacího uživatele AnswerHub, který odpovídá a je spojen s testovacím uživatelem služby Azure AD.
6. [Otestujte jednotné přihlašování](#test-single-sign-on) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části nastavíte jednotné přihlašování Azure AD v Azure Portal.

**Konfigurace jednotného přihlašování Azure AD pomocí AnswerHub:**

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **AnswerHub** vyberte **jednotné přihlašování**.

    ![Tlačítko jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování.

    ![Dialogové okno vybrat metodu jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu Upravit a otevřete tak základní dialogové okno **Konfigurace SAML** .

    ![Nastavení jednoho Sign-On pomocí stránky SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Základní konfigurační oddíl SAML](common/sp-identifier.png)

    a. Do pole **přihlašovací adresa URL** zadejte adresu URL, která má tento vzor: `https://<company>.answerhub.com`

    b. Do pole **identifikátor (ID entity)** zadejte adresu URL, která má tento vzor: `https://<company>.answerhub.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru. Pokud chcete získat hodnoty, obraťte se na [tým podpory AnswerHub](mailto:success@answerhub.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz pro **stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení AnswerHub** zkopírujte podle vašich požadavků příslušnou adresu URL nebo adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

   Můžete zkopírovat tyto adresy URL:
    - Přihlašovací adresa URL

    - Identifikátor Azure AD

    - Odhlašovací adresa URL

### <a name="configure-answerhub-single-sign-on"></a>Konfigurace jednotného přihlašování AnswerHub

V této části nastavíte jednotné přihlašování pro AnswerHub.  

**Konfigurace jednotného přihlašování AnswerHub:**

1. V jiném okně webového prohlížeče se přihlaste k webu AnswerHub společnosti jako správce.

    > [!NOTE]
    > Pokud potřebujete pomoc s konfigurací AnswerHub, obraťte se na [tým podpory AnswerHub](mailto:success@answerhub.com.).

2. Přejít na **Správa**.

3. Na kartě **Uživatelé a skupiny** v levém podokně v části **nastavení sociálních sítí** vyberte možnost **Nastavení SAML**.

4. Na kartě **Konfigurace IDP** proveďte tyto kroky:

    ![Snímek obrazovky se zobrazí stránka AnswerHub s vybranou kartou uživatelé & skupiny.](./media/answerhub-tutorial/ic785172.png "Nastavení SAML")  
  
    a. Do pole **Adresa URL přihlášení IDP** vložte **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
  
    b. Do pole **Adresa URL pro odhlášení IDP** vložte **adresu URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    c. V poli **Formát identifikátoru názvu IDP** zadejte hodnotu **identifikátoru** vybranou v části **atributy uživatele** na Azure Portal.
  
    d. Vyberte **klíče a certifikáty**.

5. V části **klíče a certifikáty** proveďte tyto kroky:

    ![Sekce klíčů a certifikátů](./media/answerhub-tutorial/ic785173.png "Klíče a certifikáty")  

    a. Otevřete certifikát kódovaný v kódování Base64, který jste stáhli z Azure Portal v poznámkovém bloku, zkopírujte jeho obsah a pak obsah vložte do pole **IDP Public Key (x509 Format)** .
  
    b. Vyberte **Uložit**.

6. Na kartě **Konfigurace IDP** vyberte **Uložit** znovu.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

**Chcete-li vytvořit testovacího uživatele služby Azure AD:**

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Vyberte Azure Active Directory, uživatelé, všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V části vlastnosti uživatele proveďte tyto kroky.

    ![Uživatelské vlastnosti](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@<yourcompanydomain. Extension>**.  
    Například, BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části nastavíte uživatele Britta Simon, aby používali jednotné přihlašování Azure AD udělením přístupu uživatele k AnswerHub.

**Přiřazení testovacího uživatele Azure AD:**

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **AnswerHub**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **AnswerHub**.

    ![Seznam aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Přidat podokno přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **uživatelů** položku **Britta Simon** a pak vyberte tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. 

7. Vyberte tlačítko **Vybrat** v dolní části obrazovky.

8. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-answerhub-test-user"></a>Vytvořit testovacího uživatele v AnswerHub

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k AnswerHub, musíte je přidat v AnswerHub. V AnswerHub se tato úloha provádí ručně.

**Nastavení uživatelského účtu:**

1. Přihlaste se k webu **AnswerHub** společnosti jako správce.

2. Přejít na **Správa**.

3. Vyberte kartu **uživatelé & skupiny** .

4. V levém podokně v části **Správa uživatelů** vyberte **vytvořit nebo importovat uživatele**a pak vyberte **Uživatelé & skupiny**.

   ![Snímek obrazovky zobrazuje stránku AnswerHub s vybranými kartami uživatelé & skupiny a odkaz vytvořit nebo importovat uživatele se nazývá.](./media/answerhub-tutorial/ic785175.png "Uživatelé & skupiny")

5. Do příslušných polí zadejte **e-mailovou adresu**, **uživatelské jméno**a **heslo** platného účtu Azure AD, který chcete přidat, a pak vyberte **Uložit**.

> [!NOTE]
> K nastavení uživatelských účtů Azure AD můžete použít jakýkoli jiný nástroj pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje AnswerHub.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici AnswerHub, měli byste se automaticky přihlásit k AnswerHub, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)