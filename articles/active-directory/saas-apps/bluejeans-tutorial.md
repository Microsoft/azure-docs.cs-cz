---
title: 'Kurz: Integrace Azure Active Directory se službou BlueJeans | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095224"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Kurz: Integrace Azure Active Directory se službou BlueJeans

V tomto kurzu se dozvíte, jak integrovat BlueJeans s Azure Active Directory (Azure AD).

BlueJeans integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k BlueJeans.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BlueJeans (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BlueJeans, potřebujete následující položky:

- Předplatné Azure AD
- BlueJeans jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BlueJeans z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bluejeans-from-the-gallery"></a>Přidání BlueJeans z Galerie

Konfigurace integrace BlueJeans do služby Azure AD, budete muset přidat BlueJeans z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BlueJeans z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **BlueJeans**vyberte **BlueJeans** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![BlueJeans v seznamu výsledků](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí BlueJeans podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v BlueJeans je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v BlueJeans potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BlueJeans, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele BlueJeans](#creating-a-bluejeans-test-user)**  – Pokud chcete mít protějšek Britta Simon v BlueJeans, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci BlueJeans.

**Ke konfiguraci Azure AD jednotné přihlašování s BlueJeans, proveďte následující kroky:**

1. Na webu Azure Portal na **BlueJeans** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_general_301.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![BlueJeans domény a adresy URL jednotného přihlašování – informace](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Hodnota Sign-on není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory BlueJeans klienta](https://support.bluejeans.com/contact) má být získána hodnota.

6. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Na **nastavení BlueJeans** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace blueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. V okně jiné webové prohlížeče, přihlaste se k vaší **BlueJeans** společnosti serveru jako správce.

9. Přejděte na **správce \> nastavení skupiny \> zabezpečení**.

    ![Správce](./media/bluejeans-tutorial/IC785868.png "správce")

10. V **zabezpečení** části, proveďte následující kroky:

    ![Jednotné přihlašování SAML na](./media/bluejeans-tutorial/IC785869.png "SAML jednotného přihlašování")

    a. Vyberte **jednotného přihlašování SAML**.

    b. Vyberte **povolit automatické zřizování**.

11. Přesunout pomocí následujících kroků:

    ![Cesta k certifikátu](./media/bluejeans-tutorial/IC785870.png "cesta certifikátu")

    a. Klikněte na tlačítko **zvolit soubor**a nahrajte base-64 kódovaných certifikát, který jste si stáhli z portálu Azure portal.

    b. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. V **heslo změnit adresu URL** textového pole vložte hodnotu **heslo změnit adresu URL** zkopírovanou z webu Azure portal.

    d. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

12. Přesunout pomocí následujících kroků:

    ![Uložit změny](./media/bluejeans-tutorial/IC785874.png "uložit změny")

    a. V **id uživatele** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klikněte na tlačítko **ULOŽTE změny**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-bluejeans-test-user"></a>Vytvoření zkušebního uživatele BlueJeans

Cílem této části je vytvořte uživatele Britta Simon v BlueJeans. BlueJeans podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](bluejeans-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **BlueJeans** společnosti serveru jako správce.

2. Přejděte na **správce \> MANAGE USERS \> přidat uživatele**.

    ![Správce](./media/bluejeans-tutorial/IC785877.png "správce")

    >[!IMPORTANT]
    >**Přidat uživatele** karta je dostupná pouze if, v **POLOŽENÍM kartu**, **povolit automatické zřizování** není zaškrtnuta. 

3. V **přidat uživatele** části, proveďte následující kroky:

    ![Přidání uživatele](./media/bluejeans-tutorial/IC785886.png "přidat uživatele")

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **vyberte uživatelské jméno BlueJeans** textové pole, zadejte uživatelské jméno uživatele, jako je **Brittasimon**

    d. V **vytvořit heslo** textové pole, zadejte své heslo.

    e. V **společnosti** textové pole, zadejte vaší společnosti.

    f. V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    g. V **vytvořit i d BlueJeans schůzky** textové pole, zadejte své ID schůzky.

    h. V **vybrat heslo moderátor** textové pole, zadejte své heslo.

    i. Klikněte na tlačítko **pokračovat**.

    ![Uživatel Addd](./media/bluejeans-tutorial/IC785887.png "Addd uživatele")

    J. Klikněte na tlačítko **přidat uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné BlueJeans uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných BlueJeans zřízení uživatelských účtů služby Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k BlueJeans použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **BlueJeans**.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici BlueJeans na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci BlueJeans.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
