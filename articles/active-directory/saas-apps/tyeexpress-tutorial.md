---
title: 'Kurz: Integrace Azure Active Directory s T & E Express | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 67cc50f3b6812abc566620396369b195d106f2be
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815170"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Kurz: Integrace Azure Active Directory s T & E Express

V tomto kurzu se dozvíte, jak integrovat T & E Express s Azure Active Directory (Azure AD).

T & E Express integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k & E Express
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k & E Express (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s T & E, Express, potřebujete následující položky:

- Předplatné Azure AD
- T & E Express jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Č & E Express z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-te-express-from-the-gallery"></a>Přidání Č & E Express z Galerie
Pokud chcete nakonfigurovat integrace T & E Express do služby Azure AD, budete muset přidat k & E Express z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat k & E Express z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **T & E Express**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

1. Na panelu výsledků vyberte **T & E Express**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s T & E Express podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v T & E Express je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v T & E Express je nutné stanovit.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v T & E Express.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s T & E, Express, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele T & E Express](#creating-a-te-express-test-user)**  – Pokud chcete mít protějšek Britta Simon v T & E, který je propojený s Azure AD reprezentace jí Express.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci T & E Express.

**Ke konfiguraci Azure AD jednotné přihlašování s T & E, Express, proveďte následující kroky:**

1. V portálu pro správu Azure na **T & E Express** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

1. Na **T & E Express domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. V **identifikátor** textového pole zadejte hodnotu jako: `https://<domain>.tyeexpress.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Tady doporučujeme používat jedinečnou hodnotu řetězce v identifikátoru. Kontakt [T & E Express tým podpory](http://www.tyeexpress.com/contacto.aspx) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **T & E Express** straně, přihlaste se k T & E express aplikaci bez služby SAML jednotného přihlašování pomocí přihlašovacích údajů správce.

1. V části **správce** klikněte na tlačítko na **SAML domény** otevřete stránku nastavení SAML.

    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tye-SAML.png)

1. Vyberte **Activar(Activate)** možnost **ne** k **SI(Yes)**. V **metadat zprostředkovatele Identity** textového pole vložte kód XML, který máte metadat stáhl z webu Azure portal.

    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Klikněte na **Guardar(Save)** uložte nastavení tlačítkem.  


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tyeexpress-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/tyeexpress-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/tyeexpress-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-te-express-test-user"></a>Vytvoření zkušebního uživatele T & E Express

Chcete-li povolit Azure AD uživatelům přihlásit se do T & E, Express, musí být poskytnuty do T & E Express.  
V případě T & E, Express zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Připojte se k serveru vaší společnosti T & E Express jako správce.

1. Pod značku správce klikněte na uživatelé a otevřete stránku předlohy uživatelů.

    ![Přidat zaměstnance](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na domovské stránce klikněte na **+** přidejte uživatele.

    ![Přidat zaměstnance](./media/tyeexpress-tutorial/tye-usershome.png)

1. Zadejte všechny povinné informace, jak dotaz ve formě a kliknutím na tlačítko Uložit uložte podrobnosti.

    ![Přidat zaměstnance](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Přidat zaměstnance](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k & E Express.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit T & E, Express, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **T & E Express**.

    ![Konfigurace jednotného přihlašování](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici T & E Express na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci T & E Express.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

