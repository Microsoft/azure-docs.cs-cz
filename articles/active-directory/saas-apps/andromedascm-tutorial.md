---
title: 'Kurz: Integrace Azure Active Directory s Andromeda | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 6d280a7e0e10b00e4d8d8f631d0d5987f2fbecb4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157869"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Kurz: Integrace Azure Active Directory s Andromeda

V tomto kurzu se dozvíte, jak integrovat Andromeda s Azure Active Directory (Azure AD).

Andromeda integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Andromeda.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Andromeda (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Andromeda, potřebujete následující položky:

- Předplatné Azure AD
- Andromeda jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Andromeda z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-andromeda-from-the-gallery"></a>Přidání Andromeda z Galerie
Konfigurace integrace Andromeda do služby Azure AD, budete muset přidat Andromeda z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Andromeda z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Andromeda**vyberte **Andromeda** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Andromeda v seznamu výsledků](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Andromeda podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Andromeda je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Andromeda potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Andromeda, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Andromeda](#create-an-andromeda-test-user)**  – Pokud chcete mít protějšek Britta Simon Andromeda, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Andromeda.

**Ke konfiguraci Azure AD jednotné přihlašování s Andromeda, proveďte následující kroky:**

1. Na webu Azure Portal na **Andromeda** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Na **Andromeda domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Andromeda domény a adresy URL jednotného přihlašování – informace](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantURL>.ngcxpress.com/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Andromeda domény a adresy URL jednotného přihlašování – informace](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Předchozí hodnota není skutečné hodnoty. Hodnota aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, která je vysvětlen později v tomto kurzu.

5. Andromeda aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurovat Single Sign-On attb](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Vymazání při nastavování tyto definice oboru názvů.
    
6. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | -------------- | -------------------- |    
    | role        | Konkrétní role aplikace |
    | type        | Typ aplikace |
    | Společnosti       | CompanyName    |

    > [!NOTE]
    > Nejsou k dispozici skutečné hodnoty. Tyto hodnoty jsou určeny pouze pro účely ukázky, použijte prosím vaše organizace role.

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Nakonfigurovat jednotné přihlašování Addattb](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

7. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. Na **Andromeda konfigurace** klikněte na tlačítko **nakonfigurovat Andromeda** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Andromeda Configuration](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Přihlašování k webu společnosti Andromeda jako správce.

11. Nahoře v řádku nabídek klikněte na **správce** a přejděte do **správu**.

    ![Andromeda správce](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. Na levé straně panelu nástrojů v rámci **rozhraní** klikněte na tlačítko **konfigurace SAML**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Na **konfigurace SAML** části stránky, proveďte následující kroky:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Zkontrolujte **povolit jednotné přihlašování pomocí SAML**.

    b. V části **Andromeda informace** tématu, zkopírujte **SP Identity** hodnotu a vložte ho do **identifikátor** textové pole z **Andromeda domény a adresy URL** oddílu.

    c. Kopírování **příjemce URL** hodnotu a vložte ho do **adresy URL odpovědi** textové pole z **Andromeda domény a adresy URL** oddílu.

    d. Kopírovat **přihlašovací adresa URL** hodnotu a vložte ho do **přihlašovací adresa URL** textové pole z **Andromeda domény a adresy URL** oddílu.

    e. V části **zprostředkovatele Identity SAML** části, zadejte název vašeho zprostředkovatele identity.

    f. V **jednotné přihlašování na koncový bod** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** , který jste zkopírovali z portálu Azure portal.

    g. Otevřete na stažený **certifikát kódovaný v Base64** z webu Azure portal v programu Poznámkový blok, vložte jej do **certifikátu X 509** textového pole.
    
    h. Mapování následujících atributů s odpovídající hodnotou usnadnit přihlašování jednotné přihlašování z Azure AD. **ID uživatele** atribut je vyžadován pro přihlášení. Pro zřizování, **e-mailu**, **společnosti**, **UserType**, a **Role** jsou povinné. V této části definujeme atributů mapování (název a hodnoty) korelovat těm, které jsou definované v rámci webu Azure portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/andromedascm-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-andromeda-test-user"></a>Vytvořit testovacího uživatele Andromeda

Cílem této části je vytvořte uživatele Britta Simon v Andromeda. Andromeda podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Andromeda, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Andromeda klienta](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Andromeda použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Andromeda Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Andromeda**.

    ![Odkaz Andromeda v seznamu aplikací](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Andromeda na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Andromeda.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
