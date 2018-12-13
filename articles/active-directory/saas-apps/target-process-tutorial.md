---
title: 'Kurz: Integrace Azure Active Directory s TargetProcess | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/7/2018
ms.author: jeedes
ms.openlocfilehash: 7fb3c1c3e8dbff54802915a69033001cc9378670
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262159"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Kurz: Integrace Azure Active Directory s TargetProcess

V tomto kurzu se dozvíte, jak integrovat TargetProcess s Azure Active Directory (Azure AD).
TargetProcess integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TargetProcess.
* Můžete povolit uživatelům být automaticky přihlášeni k TargetProcess (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TargetProcess, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* TargetProcess jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje TargetProcess **SP** jednotné přihlašování zahájené pomocí
* Podporuje TargetProcess **JIT** zřizování uživatelů

## <a name="adding-targetprocess-from-the-gallery"></a>Přidání TargetProcess z Galerie

Konfigurace integrace TargetProcess do služby Azure AD, budete muset přidat TargetProcess z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TargetProcess z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TargetProcess**vyberte **TargetProcess** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![TargetProcess v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí TargetProcess podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TargetProcess.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TargetProcess, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele TargetProcess](#create-targetprocess-test-user)**  – Pokud chcete mít protějšek Britta Simon TargetProcess, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TargetProcess, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TargetProcess** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![TargetProcess domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.tpondemand.com/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.tpondemand.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem.  Kontakt [tým podpory TargetProcess klienta](mailto:support@targetprocess.com) k získání těchto hodnot.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení TargetProcess** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

7. K automatizaci konfigurace v rámci **TargetProcess**, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![image](./media/target-process-tutorial/install_extension.png)

8. Po přidání rozšíření do prohlížeče, klikněte na **nastavení TargetProcess** nasměruje na TargetProcess aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do TargetProcess. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 13. 9.

    **Pokud chcete nakonfigurovat aplikaci ručně proveďte následující kroky:**

9. Přihlášení do aplikace TargetProcess jako správce.

10. V nabídce v horní části klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/target-process-tutorial/tutorial_target_process_05.png)

11. Klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/target-process-tutorial/tutorial_target_process_06.png)

12. Klikněte na tlačítko **jednotného přihlašování**.

    ![Klikněte na jednotné přihlašování](./media/target-process-tutorial/tutorial_target_process_07.png)

13. Na Single Sign-on dialogovém okně nastavení proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/target-process-tutorial/tutorial_target_process_08.png)

    a. Klikněte na tlačítko **povolit jednotné přihlašování**.

    b. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát** textového pole.

    d. Klikněte na tlačítko **povolení zřizování JIT**.

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k TargetProcess použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TargetProcess**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **TargetProcess**.

    ![Odkaz TargetProcess v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-targetprocess-test-user"></a>Vytvoření TargetProcess testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v TargetProcess. TargetProcess podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k TargetProcess, pokud ještě neexistuje.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory TargetProcess](mailto:support@targetprocess.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TargetProcess na přístupovém panelu, můžete by měl být automaticky přihlášeni k TargetProcess, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)