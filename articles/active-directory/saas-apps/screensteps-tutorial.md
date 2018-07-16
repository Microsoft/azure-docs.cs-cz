---
title: 'Kurz: Integrace Azure Active Directory se službou ScreenSteps | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 40aa948547587b224a81ac97a3554345d8ca783d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051362"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Kurz: Integrace Azure Active Directory se službou ScreenSteps

V tomto kurzu se dozvíte, jak integrovat ScreenSteps s Azure Active Directory (Azure AD).

ScreenSteps integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ScreenSteps.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ScreenSteps (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ScreenSteps, potřebujete následující položky:

- S předplatným služby Azure AD
- ScreenSteps jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ScreenSteps z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-screensteps-from-the-gallery"></a>Přidání ScreenSteps z Galerie
Konfigurace integrace ScreenSteps do služby Azure AD, budete muset přidat ScreenSteps z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ScreenSteps z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ScreenSteps**vyberte **ScreenSteps** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ScreenSteps v seznamu výsledků](./media/screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí ScreenSteps podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ScreenSteps je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ScreenSteps potřeba navázat.

V ScreenSteps, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ScreenSteps, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ScreenSteps](#create-a-screensteps-test-user)**  – Pokud chcete mít protějšek Britta Simon ScreenSteps, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ScreenSteps.

**Ke konfiguraci Azure AD jednotné přihlašování s ScreenSteps, proveďte následující kroky:**

1. Na webu Azure Portal na **ScreenSteps** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. Na **ScreenSteps domény a adresy URL** části, proveďte následující kroky:

    ![ScreenSteps domény a adresy URL jednotného přihlašování – informace](./media/screensteps-tutorial/tutorial_screensteps_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL, který je vysvětlen později v tomto kurzu. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/screensteps-tutorial/tutorial_general_400.png)

6. Na **ScreenSteps konfigurace** klikněte na tlačítko **nakonfigurovat ScreenSteps** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace ScreenSteps](./media/screensteps-tutorial/tutorial_screensteps_configure.png) 

7. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti ScreenSteps.

8. Klikněte na tlačítko **nastavení účtu**.

    ![Správa účtů](./media/screensteps-tutorial/ic778523.png "Správa účtů")

9. Klikněte na tlačítko **jednotného přihlašování**.

    ![Ověřování vzdálené](./media/screensteps-tutorial/ic778524.png "vzdáleného ověřování")

10. Klikněte na tlačítko **vytvořit jeden koncový bod přihlašování –**.

    ![Ověřování vzdálené](./media/screensteps-tutorial/ic778525.png "vzdáleného ověřování")

11. V **vytvořit jeden koncový bod přihlašování –** části, proveďte následující kroky:

    ![Vytvořit koncový bod ověřování](./media/screensteps-tutorial/ic778526.png "vytvořit koncový bod ověřování")
    
    a. V **název** textového pole zadejte název.
    
    b. Z **režimu** seznamu vyberte **SAML**.
    
    c. Klikněte na možnost **Vytvořit**.

12. **Upravit** nový koncový bod.

    ![Upravit koncový bod](./media/screensteps-tutorial/ic778528.png "upravit koncový bod")

13. V **upravit jeden koncový bod přihlašování –** části, proveďte následující kroky:

    ![Koncový bod vzdáleného ověřování](./media/screensteps-tutorial/ic778527.png "koncový bod vzdáleného ověřování")

    a. Klikněte na tlačítko **nahrát nový certifikát SAML soubor**a pak nahrajte certifikát, který jste si stáhli z webu Azure portal.
    
    b. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal do **Vzdálená adresa URL pro přihlášení** textového pole.
    
    c. Vložit **odhlašování URL** hodnotu, kterou jste zkopírovali z portálu Azure portal do **URL odhlašovací stránky** textového pole.
    
    d. Vyberte **skupiny** přiřazovat uživatele k kdy byly povolené.
    
    e. Při navazování připojení k serveru SQL Server došlo k chybě související se sítí nebo s instancí.

    f. Kopírovat **adresu URL příjemce SAML** do schránky a vložte do **přihlašovací adresa URL** textového pole v **ScreenSteps domény a adresy URL** oddílu.
    
    g. Vraťte se **upravit jeden koncový bod přihlašování –**.
    
    h. Klikněte na tlačítko **nastavit jako výchozí pro účet** tlačítko použít tento koncový bod pro všechny uživatele, kteří se přihlásí do ScreenSteps. Případně můžete kliknout **přidat do lokality** tlačítko použít tento koncový bod pro určité weby v **ScreenSteps**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/screensteps-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/screensteps-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/screensteps-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/screensteps-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-screensteps-test-user"></a>Vytvoření zkušebního uživatele ScreenSteps

V této části vytvoříte uživatele v ScreenSteps jako Britta Simon. Práce s [tým podpory ScreenSteps klienta](https://www.screensteps.com/contact) přidat uživatele na platformě ScreenSteps. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k ScreenSteps použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit ScreenSteps Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ScreenSteps**.

    ![Odkaz ScreenSteps v seznamu aplikací](./media/screensteps-tutorial/tutorial_screensteps_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ScreenSteps na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci ScreenSteps.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/screensteps-tutorial/tutorial_general_01.png
[2]: ./media/screensteps-tutorial/tutorial_general_02.png
[3]: ./media/screensteps-tutorial/tutorial_general_03.png
[4]: ./media/screensteps-tutorial/tutorial_general_04.png

[100]: ./media/screensteps-tutorial/tutorial_general_100.png

[200]: ./media/screensteps-tutorial/tutorial_general_200.png
[201]: ./media/screensteps-tutorial/tutorial_general_201.png
[202]: ./media/screensteps-tutorial/tutorial_general_202.png
[203]: ./media/screensteps-tutorial/tutorial_general_203.png

