---
title: 'Kurz: Integrace Azure Active Directory s 8 x 8 virtuálních Office | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 8 x 8 virtuální Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.openlocfilehash: 53db637bf7ad47896747b491fcbe31123fdb104e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741806"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Kurz: Integrace Azure Active Directory s 8 x 8 virtuálních Office

V tomto kurzu se dozvíte, jak integrovat 8 x 8 virtuálních Office se službou Azure Active Directory (Azure AD).

Integrace 8 x 8 virtuální Office pomocí služby Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Office virtuální 8 x 8.
- Uživatele, aby automaticky získat přihlášení k Office virtuální 8 x 8 (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 8 x 8 virtuálních Office, potřebujete následující položky:

- Předplatné Azure AD
- 8 x 8 virtuální Office jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání virtuální Office 8 x 8 z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Přidání virtuální Office 8 x 8 z Galerie

Pokud chcete nakonfigurovat integraci Office virtuální 8 x 8 do služby Azure AD, budete muset přidat Office virtuální 8 x 8 z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Office virtuální 8 x 8 z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **8 x 8 virtuálních Office**vyberte **8 x 8 virtuálních Office** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![8 x 8 virtuálních Office v seznamu výsledků](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s 8 x 8 virtuální Office založená na uživateli test "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v 8 x 8 virtuální Office je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v 8 x 8 virtuální Office je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 8 x 8 virtuálních Office, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření testovacího uživatele virtuální Office 8 x 8](#creating-a-8x8-virtual-office-test-user)**  – Pokud chcete mít protějšek Britta Simon v Office virtuální 8 x 8, která souvisí s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Office virtuální 8 x 8.

**Ke konfiguraci Azure AD jednotné přihlašování s 8 x 8 virtuálních Office, proveďte následující kroky:**

1. Na webu Azure Portal na **8 x 8 virtuálních Office** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![8 x 8 virtuální domény Office a adresy URL jednotné přihlašování – informace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://sso.8x8.com/saml2`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL: `https://sso.8x8.com/saml2`

5. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_certificate.png) 

6. Na **nastavení 8 x 8 virtuálních Office** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace virtuální Office 8 x 8](common/configuresection.png)

7. Přihlášení k tenantovi virtuální Office 8 x 8 jako správce.

8. Vyberte **virtuální Office účet správce** na panelu aplikace.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

9. Vyberte **obchodní** účtu pro správu a klikněte na tlačítko **Sign In** tlačítko.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

10. Klikněte na tlačítko **účty** karty v seznamu v nabídce.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

11. Klikněte na tlačítko **Single Sign On** v seznamu účtů.
  
    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

12. Vyberte **Single Sign On** podle metody ověřování a klikněte na tlačítko **SAML**.

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

13. V **jednotného přihlašování SAML** části, proveďte následující kroky:

    ![Konfigurace na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. V **přihlašování v adrese URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **adresy URL odhlašovací** vložit do textového pole **odhlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **URL vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **Procházet** tlačítko Nahrát certifikát, který jste si stáhli z webu Azure portal.

    e. Klikněte na tlačítko **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
  
### <a name="creating-a-8x8-virtual-office-test-user"></a>Vytváření 8 x 8 virtuální Office testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Office virtuální 8 x 8. 8 x 8 virtuální podporuje Office just-in-time zřizování, který je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Office virtuální 8 x 8, pokud ještě neexistuje.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory virtuální Office 8 x 8](https://www.8x8.com/about-us/contact-us).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Office virtuální 8 x 8.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **8 x 8 virtuálních Office**.

    ![Konfigurace jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Office virtuální 8 x 8 na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Office virtuální 8 x 8.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
