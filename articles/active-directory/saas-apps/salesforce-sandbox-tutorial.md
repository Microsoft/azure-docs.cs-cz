---
title: 'Kurz: Integrace Azure Active Directory s Salesforce Sandbox | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sandboxu služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: 33d39e617bd55b88d8f04785d937faf176463ee5
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808812"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Kurz: Integrace Azure Active Directory s Sandboxu služby Salesforce

V tomto kurzu se dozvíte, jak integrovat Sandboxu služby Salesforce se službou Azure Active Directory (Azure AD).

Sandboxy získáte možnost vytvářet více kopií vaší organizace v samostatných prostředích pro různé účely, jako je vývoj, testování a školení, bez negativního vlivu data a aplikace v produkčním prostředí Salesforce organizace.
Další podrobnosti najdete v tématu [izolovaného prostoru Přehled](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrace s Azure AD Sandboxu služby Salesforce vám poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Salesforce izolovaného prostoru.
- Uživatele, aby automaticky získat přihlášení k Salesforce izolovaného prostoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Sandboxu služby Salesforce, potřebujete následující položky:

- Předplatné Azure AD
- Izolovaného prostoru Salesforce jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Sandboxu Salesforce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Přidání Sandboxu Salesforce z Galerie

Pokud chcete nakonfigurovat integraci izolovaného prostoru Salesforce do služby Azure AD, budete muset přidat Sandboxu služby Salesforce v galerii na váš seznam spravovaných aplikací SaaS.

**Salesforce izolovaného prostoru přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Salesforce Sandboxu**vyberte **Salesforce Sandboxu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Salesforce izolovaného prostoru v seznamu výsledků](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s Salesforce Sandbox podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Sandboxu služby Salesforce je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Sandboxu služby Salesforce.

V Sandboxu služby Salesforce, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Sandboxu služby Salesforce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Salesforce Sandboxu](#create-a-salesforce-sandbox-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce Sandboxu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Salesforce izolovaného prostoru.

**Ke konfiguraci Azure AD jednotné přihlašování s Sandboxu služby Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal na **Salesforce Sandboxu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

      ![Nakonfigurovat jednotné přihlašování – odkaz](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Nakonfigurovat jednotné přihlašování – odkaz](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.
   
    ![Nakonfigurovat jednotné přihlašování – odkaz](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Poskytovatel služeb soubor metadat panelu zobrazí na portálu pro správu služby Salesforce izolovaného prostoru, který je vysvětlen později v tomto kurzu.

    c. Po úspěšném odeslání souboru metadat **adresy URL odpovědi** hodnota se zobrazí automaticky vyplní v **adresy URL odpovědi** textového pole.

    ![Salesforce izolovaného prostoru domény a adresy URL jednotného přihlašování – informace](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Na **podpisový certifikát SAML** části, klikněte na **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor xml ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce izolovaného prostoru služby Salesforce.

8. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure1.png)

9. Přejděte dolů k položce **nastavení** v levém navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure3.png)

11. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový ze souboru metadat**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Klikněte na tlačítko **zvolit soubor** nahrát soubor metadat XML, který jste si stáhli z webu Azure portal a klikněte na tlačítko **vytvořit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Na **SAML jednotné přihlašování – nastavení** stránky, automaticky vyplnit pole a klikněte na Uložit.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **stáhnout Metadata** tlačítko a stáhněte si soubor metadat zprostředkovatele služby. Použít tento soubor **základní konfiguraci SAML** části na webu Azure Portal ke konfiguraci potřebné adresy URL, jak je vysvětleno výše.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure4.png)

16. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu po jsou požadavky, které:

    a. Měli byste mít ověřenou doménu.

    b. Budete muset nakonfigurovat a povolit vaši doménu v Sandboxu služby Salesforce, kroky pro tento jsou vysvětleny dále v tomto kurzu.

    c. Na webu Azure Portal na **základní konfiguraci SAML** klikněte na tlačítko **nastavit další adresy URL** a postupujte následovně:
  
    ![Salesforce izolovaného prostoru domény a adresy URL jednotného přihlašování – informace](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Tato hodnota by měla zkopírovali z portálu Sandboxu služby Salesforce, jakmile povolíte domény.

17. Na **podpisový certifikát SAML** klikněte na tlačítko **kód XML metadat federace** a uložte soubor xml ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce izolovaného prostoru služby Salesforce.

19. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure1.png)

20. Přejděte dolů k položce **nastavení** v levém navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure3.png)

22. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový ze souboru metadat**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Klikněte na tlačítko **zvolit soubor** nahrát soubor metadat XML a klepněte na tlačítko **vytvořit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Na **SAML jednotné přihlašování – nastavení** stránky, automaticky vyplnit pole, zadejte název konfigurace (například: *SPSSOWAAD_Test*) v **název** textového pole a klikněte na Uložit.

    ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Pokud chcete povolit vaši doménu v Sandboxu služby Salesforce, postupujte následovně:

    > [!NOTE]
    > Před povolením doménu je potřeba vytvořit stejný v Sandboxu služby Salesforce. Další informace najdete v tématu [definování váš název domény](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Po vytvoření domény, ujistěte se prosím, že je správně nakonfigurován.

    * Na levém navigačním podokně v Sandboxu služby Salesforce, klikněte na tlačítko **nastavení společnosti** související rozbalíte, a pak klikněte na **Moje doména**.

         ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * V **konfigurace ověřování** klikněte na tlačítko **upravit**.

        ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * V **konfigurace ověřování** části jako **ověřovací službu**, vyberte název SAML jednotné přihlašování – nastavení, které jste nastavili během konfigurace jednotného přihlašování v Sandboxu služby Salesforce a Klikněte na tlačítko **Uložit**.

        ![Konfigurace jednotného přihlašování](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Vytvoření zkušebního uživatele Sandboxu služby Salesforce

V této části se vytvoří uživateli Britta Simon v Sandboxu služby Salesforce. Salesforce Sandbox podporuje zřizování just-in-time je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Sandboxu služby Salesforce, je vytvořen nový při pokusu o přístup k Salesforce izolovaného prostoru. Salesforce Sandbox také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](salesforce-sandbox-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do izolovaného prostoru služby Salesforce.

![Přiřazení role uživatele][200]

**Přiřadit Britta Simon do izolovaného prostoru služby Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Salesforce Sandboxu**.

    ![Odkaz Sandboxu služby Salesforce v seznamu aplikací](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat uživatele** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby Salesforce izolovaného prostoru na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Salesforce izolovaného prostoru.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png