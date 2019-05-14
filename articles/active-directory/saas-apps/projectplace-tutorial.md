---
title: 'Kurz: Integrace Azure Active Directory s Projectplace | Dokumentace Microsoftu'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560627"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Kurz: Integrace Azure Active Directory s Projectplace

V tomto kurzu se dozvíte, jak integrovat Projectplace s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Můžete použít Azure AD a určovat, kdo má přístup k Projectplace.
* Můžete povolit uživatelům, aby automaticky přihlášeni k Projectplace (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před zahájením.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Projectplace, budete potřebovat:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete si zaregistrovat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/) předplatného.
* Projectplace předplatné, které obsahuje single sign-on povoleno.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete nakonfigurovat a otestovat Azure AD jednotné přihlašování v testovacím prostředí.

* Projectplace podporuje jednotné přihlašování iniciovaného Zprostředkovatelem přihlašování.

## <a name="add-projectplace-from-the-gallery"></a>Přidání Projectplace z Galerie

Nastavení integrace služby Projectplace do služby Azure AD, budete muset přidat Projectplace v galerii na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** > **všechny aplikace**:

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** v horní části okna:

    ![Vyberte novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Projectplace**. Vyberte **Projectplace** ve výsledcích hledání a pak vyberte **přidat**.

     ![Výsledky vyhledávání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části budete konfigurovat a Azure AD jednotné přihlašování s Projectplace test pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, budete muset vytvořit vztah mezi uživatele služby Azure AD a odpovídajícího uživatele v Projectplace.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Projectplace, které potřebujete k dokončení těchto kroků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  k povolení této funkce pro vaše uživatele.
2. **[Konfigurace Projectplace jednotného přihlašování](#configure-projectplace-single-sign-on)**  na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotného přihlašování.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolení služby Azure AD jednotného přihlašování pro uživatele.
5. **[Vytvoření zkušebního uživatele Projectplace](#create-a-projectplace-test-user)**  připojený k Azure AD zastoupení uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části budete povolení služby Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Projectplace, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Projectplace** integrace stránce aplikace vyberte **jednotného přihlašování**:

    ![Vyberte jednotného přihlašování](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno:

    ![Upravit ikonu](common/edit-urls.png)

4. V **základní konfiguraci SAML** v dialogu **přihlašovací adresa URL** pole, zadejte adresu URL v tomto vzoru:

    `https://<company>.projectplace.com`

   ![Dialogové okno základní konfigurace SAML](common/sp-signonurl.png)
    > [!NOTE]
    > Tato hodnota je zástupný symbol. Budete muset použít skutečné přihlašovací adresa URL. Obraťte se [tým podpory Projectplace](https://success.planview.com/Projectplace/Support) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** dialogové okno na webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** odkaz **kód XML metadat federace** , podle požadavků vaší a uložte certifikát v počítači:

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. V **nastavení Projectplace** tématu, zkopírujte příslušné adresy URL, na základě vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    1. **Adresa URL pro přihlášení**.

    1. **Identifikátor služby Azure AD**.

    1. **Odhlašovací adresa URL**.

### <a name="configure-projectplace-single-sign-on"></a>Konfigurace Projectplace jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Projectplace** straně, je nutné odeslat na stažený **kód XML metadat federace** certifikát a adresy URL, které jste zkopírovali z portálu Azure portal k [ Tým podpory Projectplace](https://success.planview.com/Projectplace/Support). Tento tým zajistí, že je správně nastaveno připojení SAML SSO na obou stranách.

>[!NOTE]
>Konfigurace jednotného přihlašování musí provést [tým podpory Projectplace](https://success.planview.com/Projectplace/Support). Jakmile konfigurace je hotová, zobrazí se oznámení.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** v levém podokně vyberte **uživatelé**a pak vyberte **všichni uživatelé**:

    ![Vyberte možnost Všichni uživatelé](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky:

    ![Vyberte nového uživatele](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatelského](common/user-properties.png)

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **BrittaSimon @\<doména_společnosti >.\< Rozšíření >**. (Například BrittaSimon@contoso.com.)

    1. Vyberte **zobrazit heslo**a zapište si hodnotu, která je v **heslo** pole.

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon používat Azure AD jednotného přihlašování tak, že udělíte přístup k Projectplace.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Projectplace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Projectplace**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **uživatelů a skupin**:

    ![Vyberte uživatele a skupiny](common/users-groups-blade.png)

4. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů a pak klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte, že hodnotu kontrolního výrazu SAML, do role v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte na tlačítko **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogu **přiřadit**.

### <a name="create-a-projectplace-test-user"></a>Vytvoření zkušebního uživatele Projectplace

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Projectplace, budete muset přidat k Projectplace. Je nutné je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte tyto kroky:

1. Přihlaste se k vaší **Projectplace** společnosti serveru jako správce.

2. Přejděte na **lidé**a pak vyberte **členy**:
   
    ![Přejděte na uživatelé a pak vyberte členy](./media/projectplace-tutorial/ic790228.png "osoby")

3. Vyberte **přidat člena**:
   
    ![Výběr možnosti Přidat člen](./media/projectplace-tutorial/ic790232.png "přidat členy")

4. V **přidat člen** části, proveďte následující kroky.
   
    ![Přidat člena oddíl](./media/projectplace-tutorial/ic790233.png "nové členy")
   
    1. V **nové členy** zadejte e-mailová adresa platná Azure AD účet, který chcete přidat.
   
    1. Vyberte **Poslat**.

   K držitel účtu Azure AD se odešle e-mail obsahující odkaz pro potvrzení účtu, pak se změní na aktivní.

>[!NOTE]
>Můžete použít také jakýkoliv jiný nástroj pro vytváření uživatelského účtu nebo rozhraní API poskytovaných Projectplace pro přidání uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Teď je potřeba otestovat vaši konfiguraci Azure AD jednotné přihlašování pomocí přístupového panelu.

Při výběru dlaždice Projectplace na přístupovém panelu můžete by měl být automaticky přihlášeni k Projectplace instanci, u kterého nastavíte jednotné přihlašování. Další informace najdete v tématu [přístup a používání aplikací na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
