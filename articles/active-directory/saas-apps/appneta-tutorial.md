---
title: 'Kurz: Integrace Azure Active Directory pomocí nástroje Sledování výkonu AppNeta | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppNeta sledování výkonu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 19d79f65746b5ee03209bfd7d8405ddaa24bb825
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194878"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Kurz: Integrace Azure Active Directory pomocí nástroje Sledování výkonu AppNeta

V tomto kurzu se dozvíte, jak integrovat AppNeta sledování výkonu služby Azure Active Directory (Azure AD).

Sledování výkonu AppNeta integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AppNeta Performance monitoru.
- Uživatele, aby automaticky získat přihlášení k AppNeta Performance Monitor (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AppNeta Performance Monitor, potřebujete následující položky:

- Předplatné Azure AD
- Monitorování výkonu AppNeta jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání nástroje Sledování výkonu AppNeta z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Přidání nástroje Sledování výkonu AppNeta z Galerie
Konfigurace integrace nástroje Sledování výkonu AppNeta do služby Azure AD, budete muset přidat sledování výkonu AppNeta z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání nástroje Sledování výkonu AppNeta z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **sledování výkonu AppNeta**vyberte **sledování výkonu AppNeta** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Sledování výkonu AppNeta v seznamu výsledků](./media/appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí sledování výkonu AppNeta podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v nástroji Sledování výkonu AppNeta je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v nástroji Sledování výkonu AppNeta musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AppNeta sledování výkonu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele monitorování výkonu AppNeta](#create-an-appneta-performance-monitor-test-user)**  – Pokud chcete mít protějšek Britta Simon v AppNeta výkon, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci sledování výkonu AppNeta.

**Ke konfiguraci Azure AD jednotné přihlašování s monitorováním výkonu AppNeta, proveďte následující kroky:**

1. Na webu Azure Portal na **sledování výkonu AppNeta** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/appneta-tutorial/tutorial_appneta_samlbase.png)

3. Na **AppNeta výkonu monitorování domény a adresy URL** části, proveďte následující kroky:

    ![AppNeta výkonu monitorování domény a adresy URL jednotného přihlašování – informace](./media/appneta-tutorial/tutorial_appneta_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.pm.appneta.com`

    b. V **identifikátor** textového pole zadejte hodnotu: `PingConnect`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory AppNeta výkonu monitorování klientské](mailto:support@appneta.com) tuto výhodu získáte. 

5. Monitorování výkonu AppNeta aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci.

    ![Konfigurace jednotného přihlašování](./media/appneta-tutorial/attribute.png)

6. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
           
    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | Jméno| user.givenname|
    | Příjmení| user.surname|
    | e-mail| user.userprincipalname|
    | jméno| user.userprincipalname|
    | skupiny   | user.assignedroles |
    | telefon| user.telephonenumber |
    | název| user.jobtitle|

    > [!NOTE]
    > 'skupiny' odkazuje na skupiny zabezpečení v Appneta, která se mapuje na "Role ve službě Azure AD. Najdete [to](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) dokumentu, který vysvětluje, jak vytvořit vlastní role ve službě Azure AD.
        
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/appneta-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/appneta-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Obor názvů, ponechte prázdné.
    
    e. Klikněte na tlačítko **OK**.  

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/appneta-tutorial/tutorial_appneta_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/appneta-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **sledování výkonu AppNeta** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory sledování výkonu AppNeta](mailto:support@appneta.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/appneta-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/appneta-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/appneta-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/appneta-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Vytvořit testovacího uživatele AppNeta sledování výkonu

Cílem této části je vytvořte uživatele Britta Simon v nástroji Sledování výkonu AppNeta. Sledování výkonu AppNeta podporuje just-in-time zřizování, což je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k monitorování výkonu AppNeta, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory sledování výkonu AppNeta](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k AppNeta Performance monitoru.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon se sledováním výkonu AppNeta, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **sledování výkonu AppNeta**.

    ![Odkaz sledování výkonu AppNeta v seznamu aplikací](./media/appneta-tutorial/tutorial_appneta_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici monitorování výkonu AppNeta na přístupovém panelu, vám by měl získat automaticky přihlášení k AppNeta monitorování výkonu aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appneta-tutorial/tutorial_general_01.png
[2]: ./media/appneta-tutorial/tutorial_general_02.png
[3]: ./media/appneta-tutorial/tutorial_general_03.png
[4]: ./media/appneta-tutorial/tutorial_general_04.png

[100]: ./media/appneta-tutorial/tutorial_general_100.png

[200]: ./media/appneta-tutorial/tutorial_general_200.png
[201]: ./media/appneta-tutorial/tutorial_general_201.png
[202]: ./media/appneta-tutorial/tutorial_general_202.png
[203]: ./media/appneta-tutorial/tutorial_general_203.png

