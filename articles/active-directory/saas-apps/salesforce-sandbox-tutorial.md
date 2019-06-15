---
title: 'Kurz: Integrace Azure Active Directory s Salesforce Sandbox | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sandboxu služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 1e303485a03edcd9ba3d3e7380aa4c7ae8b1a4b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092618"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Kurz: Integrace Azure Active Directory s Sandboxu služby Salesforce

V tomto kurzu se dozvíte, jak integrovat Sandboxu služby Salesforce se službou Azure Active Directory (Azure AD).

Sandboxy získáte možnost vytvářet více kopií vaší organizace v samostatných prostředích pro různé účely, jako je vývoj, testování a školení, bez negativního vlivu data a aplikace v produkčním prostředí Salesforce organizace.
Další podrobnosti najdete v tématu [izolovaného prostoru Přehled](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrace s Azure AD Sandboxu služby Salesforce vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Salesforce izolovaného prostoru.
* Uživatelům se automaticky přihlášeni k Salesforce izolovaného prostoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sandboxu služby Salesforce, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Salesforce Sandbox jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Salesforce Sandboxu **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Podporuje Salesforce Sandboxu **JIT** zřizování uživatelů
* Podporuje Salesforce Sandboxu [ **automatizovaná** zřizování uživatelů](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Přidání Sandboxu Salesforce z Galerie

Pokud chcete nakonfigurovat integraci izolovaného prostoru Salesforce do služby Azure AD, budete muset přidat Sandboxu služby Salesforce v galerii na váš seznam spravovaných aplikací SaaS.

**Salesforce izolovaného prostoru přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Salesforce Sandboxu**vyberte **Salesforce Sandboxu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Salesforce izolovaného prostoru v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s Salesforce Sandbox podle testovacího uživatele volá **Britta Simon**.

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Sandboxu služby Salesforce je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sandboxu služby Salesforce.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sandboxu služby Salesforce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace služby Salesforce izolovaného prostoru Single Sign-On](#configure-salesforce-sandbox-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Salesforce Sandboxu](#create-salesforce-sandbox-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce Sandboxu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Sandboxu služby Salesforce, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Salesforce Sandboxu** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb** a chcete nakonfigurovat v **IDP** iniciované režimu proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    > [!NOTE]
    > Poskytovatel služeb soubor metadat panelu zobrazí na portálu pro správu služby Salesforce izolovaného prostoru, který je vysvětlen později v tomto kurzu.

    c. Po úspěšném odeslání souboru metadat **adresy URL odpovědi** hodnota se zobrazí automaticky vyplní v **adresy URL odpovědi** textového pole.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Pokud **adresy URL odpovědi** hodnotu nelze získat automaticky polulated, vyplňte hodnotu ručně podle vašich požadavků.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **soubor XML s metadaty**z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Salesforce Sandboxu** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Konfigurace služby Salesforce Sandboxu jednotného přihlašování

1. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce izolovaného prostoru služby Salesforce.

2. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure1.png)

3. Přejděte dolů k položce **nastavení** v levém navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure3.png)

5. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový ze souboru metadat**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klikněte na tlačítko **zvolit soubor** nahrát soubor metadat XML, který jste si stáhli z webu Azure portal a klikněte na tlačítko **vytvořit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na **SAML jednotné přihlašování – nastavení** stránky, automaticky vyplnit pole a klikněte na Uložit.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **stáhnout Metadata** tlačítko a stáhněte si soubor metadat zprostředkovatele služby. Použít tento soubor **základní konfiguraci SAML** části na webu Azure Portal ke konfiguraci potřebné adresy URL, jak je vysvětleno výše.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure4.png)

10. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu po jsou požadavky, které:

    a. Měli byste mít ověřenou doménu.

    b. Budete muset nakonfigurovat a povolit vaši doménu v Sandboxu služby Salesforce, kroky pro tento jsou vysvětleny dále v tomto kurzu.

    c. Na webu Azure Portal na **základní konfiguraci SAML** klikněte na tlačítko **nastavit další adresy URL** a postupujte následovně:
  
    ![Salesforce izolovaného prostoru domény a adresy URL jednotného přihlašování – informace](common/both-signonurl.png)

    V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Tato hodnota by měla zkopírovali z portálu Sandboxu služby Salesforce, jakmile povolíte domény.

11. Na **podpisový certifikát SAML** klikněte na tlačítko **kód XML metadat federace** a uložte soubor xml ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

12. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce izolovaného prostoru služby Salesforce.

13. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure1.png)

14. Přejděte dolů k položce **nastavení** v levém navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure3.png)

16. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový ze souboru metadat**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klikněte na tlačítko **zvolit soubor** nahrát soubor metadat XML a klepněte na tlačítko **vytvořit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na **SAML jednotné přihlašování – nastavení** stránky, automaticky vyplnit pole, zadejte název konfigurace (například: *SPSSOWAAD_Test*) v **název** textového pole a klikněte na Uložit.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Pokud chcete povolit vaši doménu v Sandboxu služby Salesforce, postupujte následovně:

    > [!NOTE]
    > Před povolením doménu je potřeba vytvořit stejný v Sandboxu služby Salesforce. Další informace najdete v tématu [definování váš název domény](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Po vytvoření domény, ujistěte se prosím, že je správně nakonfigurován.

21. Na levém navigačním podokně v Sandboxu služby Salesforce, klikněte na tlačítko **nastavení společnosti** související rozbalíte, a pak klikněte na **Moje doména**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. V **konfigurace ověřování** klikněte na tlačítko **upravit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. V **konfigurace ověřování** části jako **ověřovací službu**, vyberte název SAML jednotné přihlašování – nastavení, které jste nastavili během konfigurace jednotného přihlašování v Sandboxu služby Salesforce a Klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například, BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do izolovaného prostoru služby Salesforce.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Salesforce Sandboxu**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Salesforce Sandboxu**.

    ![Odkaz Sandboxu služby Salesforce v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-salesforce-sandbox-test-user"></a>Vytvoření izolovaného prostoru Salesforce testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Sandboxu služby Salesforce. Salesforce Sandbox podporuje zřizování just-in-time je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Sandboxu služby Salesforce, je vytvořen nový při pokusu o přístup k Salesforce izolovaného prostoru. Salesforce Sandbox také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](salesforce-sandbox-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby Salesforce izolovaného prostoru na přístupovém panelu, vám by měl být automaticky přihlášeni do izolovaného prostoru služby Salesforce, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurace zřizování uživatelů](salesforce-sandbox-provisioning-tutorial.md)
