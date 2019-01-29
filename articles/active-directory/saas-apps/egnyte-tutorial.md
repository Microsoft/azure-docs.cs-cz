---
title: 'Kurz: Integrace Azure Active Directory s Egnyte | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: e33fc71e0e43864d7d70495fc5056a8acaf4ad56
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159006"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Kurz: Integrace Azure Active Directory s Egnyte

V tomto kurzu se dozvíte, jak integrovat Egnyte s Azure Active Directory (Azure AD).

Egnyte integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Egnyte.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Egnyte (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Egnyte, potřebujete následující položky:

- Předplatné Azure AD
- Egnyte jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Egnyte z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-egnyte-from-the-gallery"></a>Přidání Egnyte z Galerie

Konfigurace integrace Egnyte do služby Azure AD, budete muset přidat Egnyte z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Egnyte z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Egnyte**vyberte **Egnyte** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Egnyte v seznamu výsledků](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Egnyte podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Egnyte je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Egnyte potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Egnyte, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno Egnyte](#creating-an-egnyte-test-user)**  – Pokud chcete mít protějšek Britta Simon Egnyte, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Egnyte.

**Ke konfiguraci Azure AD jednotné přihlašování s Egnyte, proveďte následující kroky:**

1. Na webu Azure Portal na **Egnyte** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Egnyte domény a adresy URL jednotného přihlašování – informace](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Egnyte klienta](https://www.egnyte.com/corp/contact_egnyte.html) tuto výhodu získáte. 

5. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. Na **nastavení Egnyte** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace Egnyte](common/configuresection.png)

7. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Egnyte jako správce.

8. Klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/egnyte-tutorial/ic787819.png "nastavení")

9. V nabídce klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/egnyte-tutorial/ic787820.png "nastavení")

10. Klikněte na tlačítko **konfigurace** kartu a potom klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/egnyte-tutorial/ic787821.png "zabezpečení")

11. V **ověření jednotného přihlašování** části, proveďte následující kroky:

    ![Jednotné přihlašování na ověřování](./media/egnyte-tutorial/ic787822.png "jednotné přihlašování na ověřování")   
    
    a. Jako **ověření jednotného přihlašování**vyberte **SAML 2.0**.
   
    b. Jako **zprostředkovatele Identity**vyberte **AzureAD**.
   
    c. Vložit **přihlašovací adresa URL** zkopírovanými z webu Azure portal do **přihlašovací adresa URL zprostředkovatele Identity** textového pole.
   
    d. Vložit **Azure AD identifikátor** zkopírovanou z webu Azure portal do **ID entity zprostředkovatele Identity** textového pole.
      
    e. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.
   
    f. Jako **výchozí mapování uživatele**vyberte **e-mailová adresa**.
   
    g. Jako **použijte hodnotu issuer specifického pro doménu**vyberte **zakázané**.
   
    h. Klikněte na **Uložit**.

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

### <a name="creating-an-egnyte-test-user"></a>Vytvoření Egnyte testovacího uživatele

Přihlaste se k Egnyte Azure AD uživatelům umožnit, musí být poskytnuty do Egnyte. V případě Egnyte zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Egnyte** společnosti serveru jako správce.

2. Přejděte na **nastavení \> uživatelů a skupin**.

3. Klikněte na tlačítko **Add New User**a pak vyberte typ uživatele, které chcete přidat.
   
    ![Uživatelé](./media/egnyte-tutorial/ic787824.png "uživatelů")

4. V **nové skupiny Power Users** části, proveďte následující kroky:
    
    ![Nového standardního uživatele](./media/egnyte-tutorial/ic787825.png "nového standardního uživatele")   

    a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    b. V **uživatelské jméno** textové pole, zadejte uživatelské jméno uživatele, jako je **Brittasimon**.

    c. Vyberte **jednotného přihlašování** jako **typ ověřování**.
   
    d. Klikněte na **Uložit**.
    
    >[!NOTE]
    >Držitel účtu Azure Active Directory obdrží e-mailové oznámení.
    >

>[!NOTE]
>Můžete použít jakékoli jiné Egnyte uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Egnyte uživatelským účtům, zřídit AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Egnyte použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Egnyte**.

    ![Konfigurace jednotného přihlašování](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Egnyte na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Egnyte.
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
