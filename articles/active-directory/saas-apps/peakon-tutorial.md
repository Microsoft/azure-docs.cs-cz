---
title: 'Kurz: Integrace Azure Active Directory s Peakon | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 637988179228fbf0a6000de74a1185af98277e3c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178949"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Kurz: Integrace Azure Active Directory s Peakon

V tomto kurzu se dozvíte, jak integrovat Peakon s Azure Active Directory (Azure AD).

Peakon integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Peakon.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Peakon (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Peakon, potřebujete následující položky:

- Předplatné Azure AD
- Peakon jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkční prostředí, nepoužívejte, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Peakon z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-peakon-from-the-gallery"></a>Přidání Peakon z Galerie

Konfigurace integrace Peakon do služby Azure AD, budete muset přidat Peakon z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Peakon z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Peakon**vyberte **Peakon** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Peakon v seznamu výsledků](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Peakon podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Peakon je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Peakon potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Peakon, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Peakon](#creating-a-peakon-test-user)**  – Pokud chcete mít protějšek Britta Simon Peakon, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Peakon.

**Ke konfiguraci Azure AD jednotné přihlašování s Peakon, proveďte následující kroky:**

1. Na webu Azure Portal na **Peakon** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavení jednotného přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Peakon domény a adresy URL jednotného přihlašování – informace](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://app.peakon.com/saml/<companyid>/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://app.peakon.com/saml/<companyid>/assert`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Peakon domény a adresy URL jednotného přihlašování – informace](./media/peakon-tutorial/tutorial_peakon_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi, což je vysvětleno latert v tomto kurzu.

6. V **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Na **nastavení Peakon** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace Peakon](common/configuresection.png)

8. V okně jiné webové prohlížeče Přihlaste se k Peakon jako správce.

9. V panelu nabídky na levé straně stránky klikněte na tlačítko **konfigurace**, přejděte na **integrace**.

    ![Konfigurace](./media/peakon-tutorial/tutorial_peakon_config.png)

10. Na **integrace** stránky, klikněte na **Single Sign-On**.

    ![Jedné](./media/peakon-tutorial/tutorial_peakon_single.png)

11. V části **Single Sign-On** části, klikněte na **povolit**.

    ![Zapnout](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Na **jednotného přihlašování pro zaměstnance pomocí SAML** části, proveďte následující kroky:

    ![Saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. V **přihlašovací adresu URL pro jednotné přihlašování** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. V **jednotného přihlašování k odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. Klikněte na tlačítko **zvolte soubor** nahrát certifikát, který jste si stáhli z portálu Azure portal do pole certifikátu.

    d. Klikněte na tlačítko **ikonu** zkopírovat **Entity ID** a vložte **identifikátor** textového pole v **základní konfiguraci SAML** oddílu na portálu Azure portal.

    e. Klikněte na tlačítko **ikonu** zkopírovat **adresy URL odpovědi (ACS)** a vložte **adresy URL odpovědi** textového pole v **základní konfiguraci SAML**   části na webu Azure portal.

    f. Klikněte na **Uložit**.

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

### <a name="creating-a-peakon-test-user"></a>Vytvoření zkušebního uživatele Peakon

Pro povolení služby Azure AD uživatelům umožní přihlásit k Peakon, musí být poskytnuty do Peakon.  
V případě Peakon zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k webu společnosti Peakon jako správce.

2. V panelu nabídky na levé straně stránky klikněte na tlačítko **konfigurace**, přejděte na **zaměstnanci**.

    ![Zaměstnance](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. V horní pravé straně stránky klikněte na **přidat zaměstnance**.

      ![Přidat zaměstnance](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na **nového zaměstnance** dialogového okna stránky, proveďte následující kroky:

     ![Nové zaměstnance](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. V **název** textové pole, typ křestní jméno jako **Britta** a příjmení jako **simon**.

    b. V **e-mailu** textové pole, typ, jako jsou e-mailovou adresu **Brittasimon@contoso.com**.

    c. Klikněte na tlačítko **vytvořit zaměstnance**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Peakon použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Peakon**.

    ![Konfigurace jednotného přihlašování](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogového okna, vyberte **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Peakon na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Peakon.
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
