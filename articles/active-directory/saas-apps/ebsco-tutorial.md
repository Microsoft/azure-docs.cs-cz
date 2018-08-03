---
title: 'Kurz: Integrace Azure Active Directory se službou EBSCO | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 5ecb0e87d45cc01b65c91ee4c5c9d29806999269
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431677"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Kurz: Integrace Azure Active Directory se službou EBSCO

V tomto kurzu se dozvíte, jak integrovat EBSCO s Azure Active Directory (Azure AD).

EBSCO integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k EBSCO.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k EBSCO (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s EBSCO, potřebujete následující položky:

- S předplatným služby Azure AD
- EBSCO jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání EBSCO z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ebsco-from-the-gallery"></a>Přidání EBSCO z Galerie
Konfigurace integrace EBSCO do služby Azure AD, budete muset přidat EBSCO z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat EBSCO z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **EBSCO**vyberte **EBSCO** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![EBSCO v seznamu výsledků](./media/ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí EBSCO podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v EBSCO je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v EBSCO potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s EBSCO, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele EBSCO](#create-an-ebsco-test-user)**  – můžete automatizovat zřizování uživatelů EBSCOhost/přizpůsobení. EBSCO podporuje Just-In-Time zřizování uživatelů.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci EBSCO.

**Ke konfiguraci Azure AD jednotné přihlašování s EBSCO, proveďte následující kroky:**

1. Na webu Azure Portal na **EBSCO** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/ebsco-tutorial/tutorial_ebsco_samlbase.png)

1. Na **EBSCO domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![EBSCO domény a adresy URL jednotného přihlašování – informace](./media/ebsco-tutorial/tutorial_ebsco_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `pingsso.ebscohost.com`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![EBSCO domény a adresy URL jednotného přihlašování – informace](./media/ebsco-tutorial/tutorial_ebsco_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory EBSCO klienta](mailto:sso@ebsco.com) má být získána hodnota. 

    o **jedinečných prvků:**  

    o **Custid** = zadejte jedinečné ID zákazníka EBSCO 

    o **profilu** = klientů můžete přizpůsobit na odkaz k přesměrování uživatelů k určitému profilu (v závislosti na tom, co může zakoupit od EBSCO). Zadat ID konkrétní profil Hlavní ID jsou eds (EBSCO zjišťování služby) a ehost (EBSOCOhost databáze). Jsou uvedeny pokyny pro stejný [tady](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. EBSCO aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > **Název** atribut je povinný a je namapovaný s **identifikátor uživatele** EBSCO aplikace. Ve výchozím nastavení to je přidáván, takže není nutné přidat ručně.
    
1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/ebsco-tutorial/tutorial_officespace_04.png)

    ![Konfigurace jednotného přihlašování](./media/ebsco-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/ebsco-tutorial/tutorial_ebsco_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/ebsco-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **EBSCO** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory EBSCO](mailto:sso@ebsco.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ebsco-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/ebsco-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ebsco-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ebsco-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-ebsco-test-user"></a>Vytvořit testovacího uživatele EBSCO

V případě EBSCO je automatické zřizování uživatelů.

**K poskytnutí uživatelského účtu, postupujte následovně:**

Azure AD předá aplikaci EBSCO požadovaná data. Zřizování uživatelů pro EBSCO lze automatické nebo vyžadují formuláři jednorázového. To závisí na Určuje, zda má klient spoustu existující účty EBSCOhost s osobní nastavení se uložila. Stejné můžete popsané s [tým podpory EBSCO](mailto:sso@ebsco.com) během provádění. V obou případech klient nemá k vytváření účtů EBSCOhost před testování.

   >[!Note]
   >Můžete automatizovat zřizování uživatelů EBSCOhost/přizpůsobení. Kontakt [tým podpory EBSCO](mailto:sso@ebsco.com) o Just-In-Time zřizování uživatelů. 
 
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k EBSCO použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit EBSCO Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **EBSCO**.

    ![Odkaz EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Po kliknutí na dlaždici EBSCO na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci EBSCO.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

1. Jakmile se přihlásíte do aplikace, klikněte na **přihlášení** tlačítko v pravém horním rohu.

    ![Signin EBSCO v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
1. Zobrazí se jednorázové řádku spárovat institucionální/SAML přihlášení pomocí **propojit existující účet MyEBSCOhost se svým účtem instituce nyní** nebo **vytvořit nový účet MyEBSCOhost a připojit ho k vaší účet instituce**. Účet se používá pro přizpůsobení v aplikaci EBSCOhost. Vyberte možnost **vytvořit nový účet** a uvidíte, že formuláře pro přizpůsobení je předem vyplněný s hodnotami z odpověď saml, jak je znázorněno v následujícím snímku obrazovky. Klikněte na tlačítko **'Pokračovat'** uložte tento výběr.
    
     ![EBSCO uživatele v seznamu aplikací](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po dokončení výše uvedených nastavení vymazání mezipaměti nebo soubory cookie a přihlaste se znovu. Nebudete už muset ručně signin znovu a uloží, individuální nastavení

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ebsco-tutorial/tutorial_general_01.png
[2]: ./media/ebsco-tutorial/tutorial_general_02.png
[3]: ./media/ebsco-tutorial/tutorial_general_03.png
[4]: ./media/ebsco-tutorial/tutorial_general_04.png

[100]: ./media/ebsco-tutorial/tutorial_general_100.png

[200]: ./media/ebsco-tutorial/tutorial_general_200.png
[201]: ./media/ebsco-tutorial/tutorial_general_201.png
[202]: ./media/ebsco-tutorial/tutorial_general_202.png
[203]: ./media/ebsco-tutorial/tutorial_general_203.png

