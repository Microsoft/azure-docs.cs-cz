---
title: 'Kurz: Integrace Azure Active Directory s ITRP | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ITRP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 626163bb512b7b3b651d016f21fc465c398a01e6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236747"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Kurz: Integrace Azure Active Directory s ITRP

V tomto kurzu se dozvíte, jak integrovat ITRP s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Můžete řídit, kdo má přístup k ITRP Azure AD.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k ITRP (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ITRP, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ITRP obsahující single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* ITRP podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-itrp-from-the-gallery"></a>Přidání ITRP z Galerie

Nastavení integrace ITRP do služby Azure AD, budete muset přidat ITRP z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![Okno aplikace organizace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ITRP**. Vyberte **ITRP** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky vyhledávání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s ITRP test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v ITRP.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ITRP, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace ITRP jednotného přihlašování](#configure-itrp-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvořit testovacího uživatele ITRP](#create-an-itrp-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ITRP, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/), vyberte na stránce ITRP integrace aplikací, **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno základní konfigurace SAML](common/sp-identifier.png)

    1. V **přihlašovací adresa URL** pole, zadejte adresu URL v tomto vzoru:
    
       `https://<tenant-name>.itrp.com`

    1. V **identifikátor (Entity ID)** pole, zadejte adresu URL v tomto vzoru:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Budete muset použít skutečné přihlašovací adresu URL a identifikátor. Obraťte se [tým podpory ITRP](https://www.itrp.com/support) k získání hodnoty. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

5. V **podpisový certifikát SAML** vyberte **upravit** ikony otevřete **podpisový certifikát SAML** dialogové okno:

    ![Upravit ikonu](common/edit-certificate.png)

6. V **podpisový certifikát SAML** dialogové okno, kopie **kryptografický otisk** hodnotu a uložte ho:

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V **nastavení ITRP** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků:

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurace ITRP jednotného přihlašování

1. V novém okně webového prohlížeče Přihlaste se k webu společnosti ITRP jako správce.

1. V horní části okna, vyberte **nastavení** ikony:

    ![Ikona nastavení](./media/itrp-tutorial/ic775570.png "ikona nastavení")

1. V levém podokně vyberte **Single Sign-On**:

    ![Vyberte Single Sign-On](./media/itrp-tutorial/ic775571.png "vyberte jednotného přihlašování")

1. V **Single Sign-On** konfigurační oddíl, proveďte následující kroky.

    ![Jednotné přihlašování – část](./media/itrp-tutorial/ic775572.png "části jednotného přihlašování")

    ![Jednotné přihlašování – část](./media/itrp-tutorial/ic775573.png "části jednotného přihlašování")

    1. Vyberte **povolené**.

    1. V **vzdálené odhlašovací adresa URL** pole, vložte **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V **adresu URL jednotného přihlašování SAML** pole, vložte **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    1. V **otisku certifikátu** pole, vložte **kryptografický otisk** hodnota certifikátu, který jste zkopírovali z portálu Azure portal.

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >** . (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat jednotné přihlašování Azure tím, že udělíte přístup k ITRP Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ITRP**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ITRP**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části okna.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-an-itrp-test-user"></a>Vytvořit testovacího uživatele ITRP

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k ITRP, budete muset přidat je do ITRP. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte tyto kroky:

1. Přihlaste se k vašemu tenantovi ITRP.

1. V horní části okna, vyberte **záznamy** ikony:

    ![Ikona záznamy](./media/itrp-tutorial/ic775575.png "ikonu záznamů")

1. V nabídce vyberte **lidé**:

    ![Vyberte osoby](./media/itrp-tutorial/ic775587.png "vybrat osoby")

1. Vyberte znaménko plus ( **+** ) chcete přidat nové uživatele:

    ![Vyberte znaménko plus](./media/itrp-tutorial/ic775576.png "vyberte znaménko plus")

1. V **přidat nové osobě** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno Přidat nové osobě](./media/itrp-tutorial/ic775577.png "dialogové okno Přidat nové osoby")

    1. Zadejte jméno a e-mailovou adresu platnou Azure AD účet, který chcete přidat.

    1. Vyberte **Uložit**.

> [!NOTE]
> Můžete použít libovolný nástroj pro vytváření účtu uživatele nebo rozhraní API poskytovaných ITRP zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice ITRP na přístupovém panelu, vám by měl být automaticky přihlášeni ITRP instanci, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
