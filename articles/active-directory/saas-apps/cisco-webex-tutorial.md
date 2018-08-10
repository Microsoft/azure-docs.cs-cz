---
title: 'Kurz: Integrace Azure Active Directory se službou Cisco Webex | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005511"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Kurz: Integrace Azure Active Directory se službou Cisco Webex

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Cisco Webex.

Integrace Cisco Webex s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Cisco Webex.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Cisco Webex pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cisco Webex, potřebujete následující položky:

- Předplatné Azure AD
- Předplatné Cisco Webex jednotné přihlašování na povoleno

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cisco Webex z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie
Konfigurace integrace Cisco Webex do služby Azure AD, budete muset přidat Cisco Webex z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cisco Webex z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Webex**. 

5. Vyberte **Cisco Webex** na panelu výsledků. Vyberte **přidat** tlačítko pro přidání aplikace.

    ![Cisco Webex v seznamu výsledků](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo je uživatel protějšky v Cisco Webex pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatelské v Cisco Webex.

V Cisco Webex, zadejte hodnotu **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dva uživatele. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele Cisco Webex](#create-a-cisco-webex-test-user) mít protějšek Britta Simon Cisco Webex, který je propojený s Azure AD reprezentace uživatele.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Cisco Webex.

**Ke konfiguraci Azure AD jednotné přihlašování s Cisco Webex, proveďte následující kroky:**

1. Na webu Azure Portal na **Cisco Webex** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogu **režimu** rozevíracího seznamu vyberte **přihlašování na základě SAML**.
 
    ![Jednotné přihlašování – dialogové okno](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Cisco Webex jako správce.

4. Klikněte na tlačítko **nastavení** z levé nabídky.

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. Na stránce nastavení přejděte výše **ověřování** klikněte na tlačítko **změnit**.

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Vyberte **integrovat zprostředkovatele identity 3. stran. (Rozšířené) ** a přejít na další obrazovce.

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Na **exportovat Metadata adresáře** klikněte na **stáhnout soubor metadat** se stažení souboru metadat.

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Na webu Azure Portal v části **Cisco Webex domény a adresy URL** části tím, že nahrajete na stažený **soubor metadat poskytovatele služeb** a nakonfigurovat aplikaci tak, že provedete následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Cisco Webex domény a adresy URL jednotného přihlašování – informace](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Cisco Webex domény a adresy URL jednotného přihlašování – informace](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. Po úspěšném dokončení nahrávání **soubor metadat poskytovatele služeb** **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **Cisco Webex Domény a adresy URL** části textového pole, jak je znázorněno níže:

    ![Cisco Webex domény a adresy URL jednotného přihlašování – informace](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. V **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL. Kontakt [tým podpory klienta Cisco Webex](https://www.webex.co.in/support/support-overview.html) k získání těchto hodnot.

9. Aplikaci Webex Cisco očekává, že kontrolní výrazy SAML obsahuje konkrétní atributy. Nakonfigurujte následující atributy pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    |  Název atributu  | Hodnota atributu |
    | --------------- | -------------------- |    
    |   jméno    | user.givenname |
    |   Příjmení    | user.surname |
    |   UID    | User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

11. Na **podpisový certifikát SAML** vyberte **soubor XML s metadaty**a poté uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Vyberte **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. Na stránce Cisco Webex společnosti Web správce pomocí možnosti prohlížeč souborů najděte a nahrajte soubor metadat služby Azure AD. Vyberte **vyžadovat certifikát podepsaný certifikační autoritou v metadatech (s vyšším zabezpečením)** a přejít na další obrazovce. 

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Vyberte **Test jednotného přihlašování k připojení**a když se otevře na nové kartě prohlížeče, proveďte ověření pomocí služby Azure AD přihlášením.

15. Zpět **správu spolupráci cloudu Cisco** záložce prohlížeče. Pokud byl test úspěšný, vyberte **tento test proběhl úspěšně. Možnost jednotného přihlašování** a klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-cisco-webex-test-user"></a>Vytvoření zkušebního uživatele Cisco Webex

Cílem této části je vytvořte uživatele Britta Simon v Cisco Webex. Cisco Webex podporuje just-in-time zřizování a automatické zřizování uživatelů, která je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure udělením přístupu k Cisco Webex.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Cisco Webex, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace. Další, přejděte na zobrazení adresáře a potom na **podnikové aplikace**.  

2. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

3. V seznamu aplikací vyberte **Cisco Webex**.

    ![Odkaz Cisco Webex v seznamu aplikací](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Podokno Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu.

6. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko.

7. Vyberte **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici Cisco Webex na přístupovém panelu, můžete automaticky získat přihlášení k aplikaci Cisco Webex.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

