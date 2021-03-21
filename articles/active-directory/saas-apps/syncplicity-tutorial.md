---
title: 'Kurz: Azure Active Directory integrace s syncplicity | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201527"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Kurz: integrace syncplicity s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat syncplicity s Azure Active Directory (Azure AD). Když integrujete syncplicity s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k syncplicity.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k syncplicity svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete si [ho](https://azure.microsoft.com/free/)stáhnout z 12 měsíců zdarma.
* Syncplicity odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Syncplicity podporuje jednotné přihlašování iniciované v **SP** .

## <a name="adding-syncplicity-from-the-gallery"></a>Přidání syncplicity z Galerie

Pokud chcete nakonfigurovat integraci syncplicity do služby Azure AD, musíte přidat syncplicity z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. V části **vytvořit** klikněte na **podniková aplikace**.
1. V části **Procházet galerii Azure AD** zadejte do vyhledávacího pole **syncplicity** .
1. Vyberte **syncplicity** z panelu výsledků a pak kliknutím na **vytvořit** přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí syncplicity pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v syncplicity.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí syncplicity, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte SYNCPLICITY SSO](#configure-syncplicity-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte syncplicity Test User](#create-syncplicity-test-user)** -to, abyste měli protějšek B. Simon v syncplicity, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.
7. **[Aktualizovat jednotné přihlašování](#update-sso)** – Chcete-li změnit nastavení jednotného přihlašování v Azure AD, proveďte v syncplicity potřebné změny.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Syncplicity** Najděte oddíl **Začínáme** a vyberte **nastavit jednotné přihlašování**.
2. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
3. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.syncplicity.com/sp`

    b. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.syncplicity.com`
    
    c. Do textového pole **Adresa URL odpovědi (adresa URL služby vyhodnocení zákazníka)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta syncplicity](https://www.syncplicity.com/contact-us) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Upravit**. Pak v dialogovém okně klikněte na tlačítko se třemi tečkami vedle aktivního certifikátu a vyberte **stažení certifikátu PEM**.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

    > [!NOTE]
    > Potřebujete certifikát PEM, protože syncplicity nepřijímá certifikáty ve formátu CER.

6. V části **Nastavení syncplicity** zkopírujte na základě vašeho požadavku příslušné adresy URL.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurace jednotného přihlašování syncplicity

1. Přihlaste se ke svému tenantovi **syncplicity** .

1. V nabídce v horní části klikněte na **správce**, vyberte **Nastavení** a pak klikněte na **vlastní doména a jednotné přihlašování**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na stránce **jediného Sign-On (SSO)** proveďte následující kroky:

    ![Jednotné \( jednotné přihlašování Sign-On\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Do textového pole **vlastní doména** zadejte název vaší domény.
  
    b. Vyberte možnost **povoleno** jako **stav jedné Sign-On**.

    c. Do textového pole **ID entity** vložte hodnotu **identifikátor (ID entity)** , kterou jste použili v **základní konfiguraci SAML** v Azure Portal.

    d. Do textového pole **Adresa URL přihlašovací stránky** vložte **přihlašovací adresu URL** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **Adresa URL odhlašovací stránky** vložte **adresu URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    f. V části **certifikát poskytovatele identity** klikněte na **zvolit soubor** a pak Nahrajte certifikát, který jste stáhli z Azure Portal.

    například Klikněte na **Uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
2. V horní části obrazovky vyberte **Nový uživatel** .
3. Ve vlastnostech **uživatele** proveďte následující kroky:

   a. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.

   b. Do pole **Název** zadejte `B.Simon`.  
   
   c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   
   d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k syncplicity.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **syncplicity**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele/skupinu**.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)
1. Na stránce **Přidat přiřazení** vyberte **Uživatelé**. 
1. V dialogovém okně **Uživatelé** v seznamu Uživatelé vyberte **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Na stránce **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-syncplicity-test-user"></a>Vytvořit testovacího uživatele syncplicity

Aby se uživatelé Azure AD mohli přihlásit, musí se zřídit pro syncplicity aplikaci. Tato část popisuje, jak vytvářet uživatelské účty Azure AD v syncplicity.

**Pokud chcete zřídit uživatelský účet syncplicity, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **syncplicity** (například: `https://company.Syncplicity.com` ).

1. Klikněte na **správce** a vyberte **uživatelské účty** a pak klikněte na **Přidat uživatele**.

    ![Správa uživatelů](./media/syncplicity-tutorial/ic769764.png "Správa uživatelů")

1. Zadejte **e-mailové adresy** účtu Azure AD, které chcete zřídit, vyberte **uživatele** jako **roli** a pak klikněte na **Další**.

    ![Informace o účtu](./media/syncplicity-tutorial/ic769765.png "Informace o účtu")

    > [!NOTE]
    > Držitel účtu Azure AD získá e-mail, včetně odkazu na potvrzení a aktivaci účtu.

1. Vyberte skupinu ve vaší společnosti, které by měl nový uživatel být členem, a pak klikněte na **Další**.

    ![Členství ve skupinách](./media/syncplicity-tutorial/ic769772.png "Členství ve skupinách")

    > [!NOTE]
    > Pokud nejsou uvedeny žádné skupiny, klikněte na tlačítko **Další**.

1. Vyberte složky, které chcete umístit do ovládacího prvku syncplicity v počítači uživatele, a poté klikněte na tlačítko **Další**.

    ![Syncplicity složky](./media/syncplicity-tutorial/ic769773.png "Syncplicity složky")

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů syncplicity nebo rozhraní API poskytovaná syncplicity.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici syncplicity, měli byste se automaticky přihlásit k syncplicity, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

### <a name="update-sso"></a>Aktualizovat jednotné přihlašování

Kdykoli budete potřebovat provést změny jednotného přihlašování, musíte zkontrolovat použitý **podpisový certifikát SAML** . Pokud se certifikát změnil, nezapomeňte nahrát nový syncplicity, jak je popsáno v tématu **[Konfigurace jednotného přihlašování syncplicity](#configure-syncplicity-sso)**.

Pokud používáte mobilní aplikaci syncplicity, obraťte se na zákaznickou podporu syncplicity (), support@syncplicity.com kde najdete pomoc.

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
