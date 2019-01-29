---
title: 'Kurz: Integrace Azure Active Directory s Ziflow | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: ca687f15187d4dd485d2c59e3c46fa3158802ff4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179998"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Kurz: Integrace Azure Active Directory s Ziflow

V tomto kurzu se dozvíte, jak integrovat Ziflow s Azure Active Directory (Azure AD).

Ziflow integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Ziflow.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Ziflow (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Ziflow, potřebujete následující položky:

- Předplatné Azure AD
- Ziflow jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Ziflow z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ziflow-from-the-gallery"></a>Přidání Ziflow z Galerie
Konfigurace integrace Ziflow do služby Azure AD, budete muset přidat Ziflow z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Ziflow z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Ziflow**vyberte **Ziflow** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Ziflow v seznamu výsledků](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Ziflow podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Ziflow je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Ziflow potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Ziflow, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Ziflow](#create-a-ziflow-test-user)**  – Pokud chcete mít protějšek Britta Simon Ziflow, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Ziflow.

**Ke konfiguraci Azure AD jednotné přihlašování s Ziflow, proveďte následující kroky:**

1. Na webu Azure Portal na **Ziflow** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Na **Ziflow domény a adresy URL** části, proveďte následující kroky:

    ![Ziflow domény a adresy URL jednotného přihlašování – informace](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečný. Skutečné hodnoty, který je vysvětlen později v tomto kurzu jste se aktualizuje jedinečnou hodnotu ID v identifikátoru a přihlášení na adrese URL.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/ziflow-tutorial/tutorial_general_400.png)

6. Na **Ziflow konfigurace** klikněte na tlačítko **nakonfigurovat Ziflow** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. V jiné okno webového prohlížeče, přihlaste se k Ziflow jako správce zabezpečení.

8. Klikněte na miniaturu v pravém horním rohu a pak klikněte na tlačítko **spravovat účet**.

    ![Správa konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. V levé horní části, klikněte na tlačítko **Single Sign-On**.

    ![Konfigurace přihlašování Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Na **Single Sign-On** stránce, proveďte následující kroky:

    ![Konfigurace jednoho Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Vyberte **typ** jako **SAML2.0**.

    b. V **přihlašování v adrese URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. Nahrát certifikát kódováním base-64, který jste si stáhli z portálu Azure portal do **X509 podpisový certifikát**.

    d. V **adresy URL odhlašovací** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    e. Z **nastavení konfigurace pro poskytovatele identifikátor** části, zkopírujte zvýrazněné jedinečnou hodnotu ID a připojit ho s identifikátorem a adresa URL v přihlašování **Ziflow domény a adresy URL části** na Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ziflow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/ziflow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ziflow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ziflow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-ziflow-test-user"></a>Vytvoření zkušebního uživatele Ziflow

Přihlaste se k Ziflow Azure AD uživatelům umožnit, musí být poskytnuty do Ziflow. Zřizování v Ziflow, je ruční úlohy.

K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k Ziflow jako správce zabezpečení.

2. Přejděte do **lidé** nahoře.

    ![Konfigurace Ziflow osoby](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klikněte na tlačítko **přidat** a potom klikněte na tlačítko **přidat uživatele**.

    ![Konfigurace Ziflow přidávání uživatele](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Na **přidání uživatele** automaticky otevírané okno, proveďte následující kroky:

    ![Konfigurace Ziflow přidávání uživatele](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je brittasimon@contoso.com.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je Britta.

    c. V **příjmení** textové pole, zadejte příjmení uživatele, jako je Simon.

    d. Vyberte svou roli Ziflow.

    e. Klikněte na tlačítko **přidat 1 uživatel**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Ziflow použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Ziflow Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Ziflow**.

    ![Odkaz Ziflow v seznamu aplikací](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Ziflow na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Ziflow.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

