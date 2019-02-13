---
title: 'Kurz: Integrace Azure Active Directory s způsob děláme | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a způsob, jak vidíme.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc6d8e2cf7ac4786f30484325406a1fe696dff3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165123"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Kurz: Integrace Azure Active Directory s způsob děláme

V tomto kurzu se dozvíte, jak integrovat způsob, jak jsme se s Azure Active Directory (Azure AD).

Integrace způsob, jak jsme se s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k způsob děláme.
- Uživatele, aby automaticky získat přihlášení k způsob děláme (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s způsob děláme, potřebujete následující položky:

- Předplatné Azure AD
- Předplatné povolené A způsob, jak že jsme jednotné přihlašování

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání způsob, jak můžeme udělat z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-way-we-do-from-the-gallery"></a>Přidání způsob, jak můžeme udělat z Galerie
Pokud chcete nakonfigurovat integraci děláme způsob, jak do služby Azure AD, musíte doplnit způsob, jak můžeme udělat z Galerie váš seznam spravovaných aplikací SaaS.

**Děláme způsob, jak přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **způsob děláme**vyberte **způsob děláme** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Způsob, jak jsme se v seznamu výsledků](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s způsob, jak jsme se na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v způsob, jak jsme to je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v způsob, jak jsme to je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s způsob děláme, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele způsob děláme](#create-a-way-we-do-test-user)**  – Pokud chcete mít protějšek Britta Simon způsob, jak to je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci způsob děláme.

**Ke konfiguraci Azure AD jednotné přihlašování s způsob děláme, proveďte následující kroky:**

1. Na webu Azure Portal na **způsob děláme** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. Na **způsob, jak můžeme udělat domény a adresy URL** části, proveďte následující kroky:

    ![Způsob, jak můžeme udělat domény a adresy URL jednotného přihlašování – informace](./media/waywedo-tutorial/tutorial_waywedo_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory způsob, jak můžeme udělat klienta](mailto:support@waywedo.com) k získání těchto hodnot. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/waywedo-tutorial/tutorial_general_400.png)

6. Na **způsob, jak můžeme provést konfiguraci** klikněte na tlačítko **nakonfigurovat způsob, jak děláme** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Způsob, jakým jsme provést konfiguraci](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. V jiné okno webového prohlížeče, přihlaste se k způsob, jak můžeme udělat jako správce zabezpečení.

8. Klikněte na tlačítko **osoba ikonu** v pravém horním rohu jakékoli stránky v způsob, jak jsme to klikněte **účtu** v rozevírací nabídce.

    ![Způsob, jak děláme účtu](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. Klikněte na tlačítko **ikonu nabídky** otevřete navigační nabídky nabízených oznámení a klikněte na tlačítko **Single Sign On**.

    ![Způsob, jak můžeme udělat jeden](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. Na **nastavení přihlašování –** stránce, proveďte následující kroky:

    ![Způsob, jak děláme uložit](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klikněte na tlačítko **zapnout jednotné přihlašování** přepnutím **Ano** povolit jednotné přihlašování.

    b. V **jednotné přihlašování – název** textového pole zadejte své jméno.

    c. V **Entity ID** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    d. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    e. Nahrajte certifikát po kliknutí **vyberte** vedle **certifikát**.

    f. **Volitelná nastavení** -
    
    * Hesla – povolit po tato možnost je zakázaná, pravidelné heslo funkce pro způsob, jak můžeme udělat tak, aby uživatelé mohou použít pouze jednotné přihlašování.

    * Povolení automatického zřizování – když je tato možnost povolena, bude e-mailovou adresu používá k přihlašování do seznamu uživatelů v způsob, jak jsme to automaticky porovnání. Pokud e-mailovou adresu se neshoduje s aktivní uživatel v způsob děláme, automaticky se přidá nový uživatelský účet pro osobu, která se přihlašujete, požadování všechny chybějící informace.

      > [!NOTE]
      > Přidané prostřednictvím jednotného přihlašování uživatele jsou přidány jako obecný uživatel a nejsou přiřazena role v systému. Správce se zúčastnit a změnit jejich role zabezpečení jako editor nebo správce a můžete také přiřadit organizační diagram jednoho nebo několika rolí. 

    g. Klikněte na tlačítko **Uložit** k uchování vašeho nastavení.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/waywedo-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/waywedo-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/waywedo-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/waywedo-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-way-we-do-test-user"></a>Vytvoření zkušebního uživatele způsob děláme

Cílem této části je vytvořte uživatele Britta Simon v způsob, jak jsme to. Způsob děláme podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k způsob, jak můžeme udělat, pokud ještě neexistuje.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory způsob, jak můžeme udělat klienta](mailto:support@waywedo.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu způsob, jak můžeme udělat.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon způsob, jak můžeme udělat, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **způsob děláme**.

    ![Způsob, jak děláme odkaz v seznamu aplikací](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici způsob, jak můžeme udělat na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci způsob děláme.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png

