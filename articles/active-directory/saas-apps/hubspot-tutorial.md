---
title: 'Kurz: Integrace Azure Active Directory s HubSpot | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.openlocfilehash: 2806288378e5fa080164155b97a47a7046ecbba1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814898"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Kurz: Integrace Azure Active Directory s HubSpot

V tomto kurzu se dozvíte, jak integrovat HubSpot s Azure Active Directory (Azure AD).

HubSpot integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k HubSpot
- Můžete povolit uživatelům, aby automaticky získat přihlášení k HubSpot (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s HubSpot, potřebujete následující položky:

- Předplatné Azure AD
- HubSpot jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání HubSpot z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-hubspot-from-the-gallery"></a>Přidání HubSpot z Galerie

Konfigurace integrace HubSpot do služby Azure AD, budete muset přidat HubSpot z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat HubSpot z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **HubSpot**. Vyberte **HubSpot** z panel výsledků a pak klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s HubSpot podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v HubSpot je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v HubSpot potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s HubSpot, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele HubSpot](#creating-a-hubspot-saml-test-user)**  – Pokud chcete mít protějšek Britta Simon HubSpot, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci HubSpot.

**Ke konfiguraci Azure AD jednotné přihlašování s HubSpot, proveďte následující kroky:**

1. Na webu Azure Portal na **HubSpot** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/tutorial_general_301.png)

3. Pokud potřebujete změnit na **SAML** klikněte na libovolný jiný režim, režim **změnit jednotné přihlašování režim** na obrazovce.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/tutorial_general_300.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/tutorial_general_302.png)

5. Na **základní konfiguraci SAML** části proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![HubSpot domény a adresy URL jednotného přihlašování – informace](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi, který je vysvětlen později v tomto kurzu. Kontakt [tým podpory HubSpot klienta](https://help.hubspot.com/) k získání těchto hodnot.

    c. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![HubSpot domény a adresy URL jednotného přihlašování – informace](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://app.hubspot.com/login`

6. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Na **nastavení HubSpot** části, klikněte na tlačítko Kopírovat zkopírujte **přihlašovací adresa URL** a **Azure AD identifikátor** hodnoty.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Otevře nová karta prohlížeče a přihlaste se k vašemu účtu správce HubSpot.

9. Klikněte na **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/config1.png)

10. Klikněte na **účtu výchozí**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/config2.png)

11. Přejděte dolů k položce **zabezpečení** a klikněte na **nastavit**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/config3.png)

12. Na **nastavit jednotné přihlašování** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/config4.png)

    a. Klikněte na tlačítko **kopírování** tlačítka pro kopírování **cílovou skupinu URl(Service Provider Entity ID)** hodnotu a vložte ho do **identifikátor** textového pole v **základní SAML Konfigurace** části webu Azure Portal.

    b. Klikněte na tlačítko **kopírování** tlačítka pro kopírování **přihlásit na adrese URl, ACS, příjemce nebo přesměrovat** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **základní SAML Konfigurace** části webu Azure Portal.

    c. V **identifikátor zprostředkovatele Identity nebo URL vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. V **jednotné přihlašování – adresa URL zprostředkovatele Identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    e. Otevřete váš stažené **Certificate(Base64)** soubor v poznámkovém bloku. Zkopírujte obsah ho do schránky a vložte ho do **certifikát X.509** pole.

    f. Klikněte na **Ověřit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hubspot-tutorial/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hubspot-tutorial/create_aaduser_02.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-hubspot-test-user"></a>Vytvoření zkušebního uživatele HubSpot

Povolit uživatele Azure AD se přihlaste k HubSpot, musí být poskytnuty do HubSpot.
V případě HubSpot zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **HubSpot** společnosti serveru jako správce.

2. Klikněte na **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/config1.png)

3. Klikněte na **uživatelé a týmy**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/user1.png)

4. Klikněte na tlačítko **vytvořit uživatele**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/user2.png)

5. Zadejte e-mailovou adresu uživatele, jako je **brittasimon@contoso.com** v **přidat e-mailu addess(es)** textového pole a klikněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/user3.png)

6. Na **vytvořit uživatele** tématu, přejděte přes každých jednotlivá karta a vyberte příslušné možnosti a oprávnění pro uživatele a klikněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/user4.png)

7. Klikněte na tlačítko **odeslat** poslat pozvánku uživateli.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Uživatel bude aktivovat po přijetí e-mailové pozvánce.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k HubSpot použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **HubSpot**.

    ![Konfigurace jednotného přihlašování](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici HubSpot na přístupovém panelu, měli byste obdržet automaticky přihlašovací stránku HubSpot aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png