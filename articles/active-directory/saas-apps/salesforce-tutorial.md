---
title: 'Kurz: Integrace Azure Active Directory se Salesforce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 36f1bd9c11c8932968a3501ef22fdb7153411256
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867557"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Kurz: Integrace Azure Active Directory se Salesforce

V tomto kurzu se dozvíte, jak integrovat služby Salesforce se službou Azure Active Directory (Azure AD).

Salesforce integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Salesforce.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Salesforce (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí služby Salesforce, potřebujete následující položky:

- Předplatné Azure AD
- Salesforce jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Salesforce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-salesforce-from-the-gallery"></a>Přidání Salesforce z Galerie

Pokud chcete nakonfigurovat integraci Salesforce do služby Azure AD, budete muset přidat Salesforce z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat z Galerie služby Salesforce, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Salesforce**vyberte **Salesforce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Salesforce v seznamu výsledků](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí služby Salesforce podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Salesforce je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Salesforce.

V Salesforce, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí služby Salesforce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Salesforce](#create-a-salesforce-test-user)**  – Pokud chcete mít protějšek Britta Simon v Salesforce, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Salesforce.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí služby Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal na **Salesforce** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

    ![Nakonfigurovat jednotné přihlašování – odkaz](./media/salesforce-tutorial/tutorial_general_300.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Nakonfigurovat jednotné přihlašování – odkaz](./media/salesforce-tutorial/tutorial_general_301.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.
   
    ![Nakonfigurovat jednotné přihlašování – odkaz](./media/salesforce-tutorial/tutorial_general_302.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Salesforce domény a adresy URL jednotného přihlašování – informace](./media/salesforce-tutorial/tutorial_salesforce_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce:

    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce:

    Účet organizace: `https://<subdomain>.my.salesforce.com`

    Vývojářský účet: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory klient Salesforce](https://help.salesforce.com/support) k získání těchto hodnot.

6. Na **podpisový certifikát SAML** části, klikněte na **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor xml ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

7. Otevře nová karta prohlížeče a přihlaste se k vašemu účtu správce služby Salesforce.

8. Klikněte na **nastavení** pod **ikona nastavení** v pravém horním rohu stránky.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/configure1.png)

9. Přejděte dolů k položce **nastavení** v navigačním podokně klikněte na tlačítko **Identity** tím rozbalíte související. Pak klikněte na tlačítko **nastavení jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso.png)

10. Na **nastavení jednotného přihlašování** stránky, klikněte na tlačítko **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Pokud nemůžete povolit nastavení jednotného přihlašování pro váš účet Salesforce, budete muset kontaktovat [tým podpory klient Salesforce](https://help.salesforce.com/support).

11. Vyberte **povoleno SAML**a potom klikněte na tlačítko **Uložit**.

      ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-enable-saml.png)

12. Konfigurace SAML jednotné přihlašování – nastavení, klikněte na tlačítko **nový ze souboru metadat**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. Klikněte na tlačítko **zvolit soubor** nahrát soubor metadat XML, který jste si stáhli z webu Azure portal a klikněte na tlačítko **vytvořit**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/xmlchoose.png)

14. Na **SAML jednotné přihlašování – nastavení** stránky, automaticky vyplnit pole a klikněte na Uložit.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/salesforcexml.png)

15. Na levém navigačním podokně v Salesforce, klikněte na tlačítko **nastavení společnosti** související rozbalíte, a pak klikněte na **Moje doména**.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-my-domain.png)

16. Přejděte dolů k položce **konfigurace ověřování** a klikněte **upravit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-edit-auth-config.png)

17. V **konfigurace ověřování** části, zkontrolujte **AzureSSO** jako **ověřování Provisioning** Konfigurace jednotného přihlašování SAML, a pak klikněte na tlačítko **uložit** .

    ![Konfigurace jednotného přihlašování](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Pokud je vybrán více než jedna služba ověřování, uživatelé vyzváni k výběru které ověřovací službu, jako jsou se přihlásit pomocí při inicializaci jednotné přihlašování do prostředí Salesforce. Pokud nechcete, aby bylo možné, měli byste **nechte nezaškrtnuté všechny ostatní služby ověřování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/salesforce-tutorial/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/salesforce-tutorial/create_aaduser_02.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-a-salesforce-test-user"></a>Vytvoření zkušebního uživatele Salesforce

V této části se v Salesforce vytvoří uživatelské volá Britta Simon. Salesforce podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Salesforce, vytvoří se nový při pokusu o přístup k Salesforce. Salesforce také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](salesforce-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Salesforce.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit k Salesforce, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Salesforce**.

    ![Propojení služby Salesforce v seznamu aplikací](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat uživatele** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici služby Salesforce na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Salesforce.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png