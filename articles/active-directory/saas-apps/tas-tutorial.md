---
title: 'Kurz: Azure Active Directory integrace s ú | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ú.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 129b6e69-e3b4-41d7-9ab5-a2ddd0068f76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: efd64830c0d9afa83838adef96cf1c103b4485e8
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943280"
---
# <a name="tutorial-azure-active-directory-integration-with-tas"></a>Kurz: Azure Active Directory integrace s ú

V tomto kurzu se naučíte integrovat ú Azure Active Directory (Azure AD).
Integrace ú se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k ú.
* Uživatelům můžete povolit, aby se automaticky přihlásili k pracovním postupům (jednotné přihlašování) s účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s využitím ú potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním ú

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ÚKOL podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování

## <a name="adding-tas-from-the-gallery"></a>Přidání ú z Galerie

Pokud chcete nakonfigurovat integraci ú až po Azure AD, musíte přidat ú z Galerie do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat úkol z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ú**, vyberte možnost prohledat z panelu výsledek a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ÚKOL v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby ú na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v práci.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí služby ú, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte jednotné přihlašování](#configure-tas-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte testovacího uživatele](#create-tas-test-user)** , abyste měli protějšek Britta Simon v rámci ú, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ú, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací pro **ú** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace jednotného přihlašování k doménám a adresám URL ú](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://taseu.combtas.com/<DOMAIN>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://taseu.combtas.com/<ENVIRONMENTNAME>/AssertionService.aspx`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Informace jednotného přihlašování k doménám a adresám URL ú](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://taseu.combtas.com/<DOMAIN>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujete je pomocí skutečného identifikátoru, adresy URL odpovědi a adresy URL pro přihlášení, které jsou vysvětleny dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení ú** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-tas-single-sign-on"></a>Konfigurace jednotného přihlašování k ú

1. V jiném okně webového prohlížeče se přihlaste k objektu ú jako správce.

2. V levé části nabídky klikněte na **Nastavení** , přejděte na **správce** a pak klikněte na **Spravovat jednotné přihlašování**.

    ![Konfigurace ú](./media/tas-tutorial/configure01.png)

3. Na stránce **Spravovat jednotné přihlašování** proveďte následující kroky:

    ![Konfigurace ú](./media/tas-tutorial/configure02.png)

    a. Do textového pole **název** zadejte název prostředí.
    
    b. Jako **typ ověřování**vyberte **typu Saml2** .

    c. Do textového pole **Zadejte adresu URL** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. V programu Poznámkový blok otevřete certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah a vložte ho do pole **Zadejte certifikaci** .

    e. Do textového pole **Zadejte nové IP** adresy zadejte IP adresu.

    >[!NOTE]
    > Obraťte se na [tým podpory ú](mailto:support@combtas.com) , aby získal IP adresu.

    f. Zkopírujte adresu **URL jednotného přihlašování** a vložte ji do **identifikátoru (ID entity)** a pole pro přihlášení k **adrese url** **základní konfigurace SAML** v Azure Portal. Upozorňujeme, že adresa URL rozlišuje velká a malá písmena a musí končit lomítkem (/).

    g. Zkopírujte adresu URL **služby kontrolního výrazu** na stránce nastavení a vložte ji do textového pole **Adresa URL odpovědi** **základní konfigurace SAML** v Azure Portal.

    h. Klikněte na **Vložit řádek jednotného přihlašování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména** **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ú.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **ú**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **ú**.

    ![Odkaz na ú v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-tas-test-user"></a>Vytvořit testovacího uživatele pro úkol

V této části vytvoříte uživatele s názvem Britta Simon v tématu ú. Pokud chcete přidat uživatele na platformě ú, pracujte s [týmem podpory úkolů](mailto:support@combtas.com) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ú na přístupovém panelu, měli byste se automaticky přihlásit k ú, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

