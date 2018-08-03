---
title: 'Kurz: Integrace Azure Active Directory se službou BlueJeans | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2ec94217a8df2efaa23eb3cc2c9d5a80e8037615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425983"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Kurz: Integrace Azure Active Directory se službou BlueJeans

V tomto kurzu se dozvíte, jak integrovat BlueJeans s Azure Active Directory (Azure AD).

BlueJeans integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k BlueJeans
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BlueJeans (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BlueJeans, potřebujete následující položky:

- S předplatným služby Azure AD
- BlueJeans jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BlueJeans z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bluejeans-from-the-gallery"></a>Přidání BlueJeans z Galerie
Konfigurace integrace BlueJeans do služby Azure AD, budete muset přidat BlueJeans z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BlueJeans z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **BlueJeans**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/tutorial_bluejeans_search.png)

1. Na panelu výsledků vyberte **BlueJeans**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s BlueJeans podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v BlueJeans je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v BlueJeans potřeba navázat.

V BlueJeans, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BlueJeans, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele BlueJeans](#creating-a-bluejeans-test-user)**  – Pokud chcete mít protějšek Britta Simon v BlueJeans, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci BlueJeans.

**Ke konfiguraci Azure AD jednotné přihlašování s BlueJeans, proveďte následující kroky:**

1. Na webu Azure Portal na **BlueJeans** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

1. Na **BlueJeans domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.BlueJeans.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory BlueJeans klienta](https://support.bluejeans.com/contact) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_general_400.png)

1. Na **BlueJeans konfigurace** klikněte na tlačítko **nakonfigurovat BlueJeans** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL, adresy URL pro změnu hesla a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

1. V okně jiné webové prohlížeče, přihlaste se k vaší **BlueJeans** společnosti serveru jako správce.

1. Přejděte na **správce \> nastavení skupiny \> zabezpečení**.

   ![Správce](./media/bluejeans-tutorial/IC785868.png "správce")

1. V **zabezpečení** části, proveďte následující kroky:

   ![Jednotné přihlašování SAML na](./media/bluejeans-tutorial/IC785869.png "SAML jednotného přihlašování")

   a. Vyberte **jednotného přihlašování SAML**.

   b. Vyberte **povolit automatické zřizování**.

1. Přesunout pomocí následujících kroků:

    ![Cesta k certifikátu](./media/bluejeans-tutorial/IC785870.png "cesta certifikátu")

    a. Klikněte na tlačítko **zvolit soubor**a pak nahrajte staženého certifikátu.

    b. Vložit **SAML jednotné přihlašování – adresa URL služby** do **přihlašovací adresa URL** textového pole.

    c. Vložit **heslo změnit adresu URL** do **heslo změnit adresu URL** textového pole.

    d. Vložit **odhlašování URL** do **odhlašovací adresa URL** textového pole.

1. Přesunout pomocí následujících kroků:

    ![Uložit změny](./media/bluejeans-tutorial/IC785874.png "uložit změny")

    a. V **id uživatele** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. V **e-mailu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klikněte na tlačítko **uložit změny**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/create_aaduser_03.png)

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/bluejeans-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-bluejeans-test-user"></a>Vytvoření zkušebního uživatele BlueJeans

Cílem této části je vytvořte uživatele Britta Simon v BlueJeans. BlueJeans podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](bluejeans-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k vaší **BlueJeans** společnosti serveru jako správce.

1. Přejděte na **správce \> spravovat uživatele \> přidat uživatele**.

   ![Správce](./media/bluejeans-tutorial/IC785877.png "správce")

   >[!IMPORTANT]
   >**Přidat uživatele** karta je k dispozici pouze pokud v **karta zabezpečení**, **povolit automatické zřizování** není zaškrtnuta. 

1. V **přidat uživatele** části, proveďte následující kroky:

    ![Přidání uživatele](./media/bluejeans-tutorial/IC785886.png "přidat uživatele")

    a. Zadejte **BlueJeans uživatelské jméno**, **e-mailová adresa**, **BlueJeans schůzky ID**, **moderátor hesla**, **jméno a příjmení** , **společnosti** platného účtu AAD ke zřízení do související textových polí.

    b. Klikněte na tlačítko **přidat uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné BlueJeans uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných BlueJeans uživatelským účtům, zřídit AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k BlueJeans použití Azure jednotného přihlašování.

![Přiřadit uživatele][200]

**Přiřadit BlueJeans Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **BlueJeans**.

    ![Konfigurace jednotného přihlašování](./media/bluejeans-tutorial/tutorial_bluejeans_app.png)

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici BlueJeans na přístupovém panelu, měli byste obdržet přihlašovací stránku BlueJeans aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](bluejeans-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
