---
title: 'Kurz: Integrace Azure Active Directory s RingCentral | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e01b06b2031da20ee9349588bd4a7ee757fbc22c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173296"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Kurz: Integrace Azure Active Directory s RingCentral

V tomto kurzu se dozvíte, jak integrovat RingCentral s Azure Active Directory (Azure AD).

RingCentral integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k RingCentral.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k RingCentral (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RingCentral, potřebujete následující položky:

- Předplatné Azure AD
- RingCentral jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání RingCentral z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ringcentral-from-the-gallery"></a>Přidání RingCentral z Galerie
Konfigurace integrace RingCentral do služby Azure AD, budete muset přidat RingCentral z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat RingCentral z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **RingCentral**vyberte **RingCentral** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí RingCentral podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v RingCentral je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v RingCentral potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RingCentral, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele RingCentral](#create-a-ringcentral-test-user)**  – Pokud chcete mít protějšek Britta Simon RingCentral, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci RingCentral.

**Ke konfiguraci Azure AD jednotné přihlašování s RingCentral, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **RingCentral** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Klikněte na tlačítko **změnit jednotné přihlašování režim** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části textového pole, jak je znázorněno níže :

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. V **přihlašovací adresa URL** textového pole zadejte adresu URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Můžete získat **soubor metadat poskytovatele služeb** na stránce Konfigurace jednotného přihlašování RingCentral, který je vysvětlen později v tomto kurzu.

6. Pokud nemáte **soubor metadat poskytovatele služeb**, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. V **identifikátor** textového pole zadejte adresu URL:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení certifikátu z se zadanými možnostmi, jak je uvedeno vaše požadavek a uložte ho do počítače.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. V jiné okno webového prohlížeče, přihlaste se k RingCentral jako správce zabezpečení.

9. V horní části klikněte na **nástroje**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Přejděte do **jednotného přihlašování**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Na **Single Sign-on** stránce v části **Konfigurace jednotného přihlašování** části z **kroku 1** klikněte na tlačítko **upravit** a proveďte následující kroky:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Na **nastavit jednotné přihlašování** stránce, proveďte následující kroky:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klikněte na tlačítko **Procházet** nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    b. Po nahrání metadat získat hodnoty automaticky dosadí **jednotného přihlašování obecné informace o** oddílu.

    c. V části **mapování atributů** vyberte **mapu e-mailu atributu** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klikněte na **Uložit**.

    e. Z **kroku 2** klikněte na tlačítko **Stáhnout** ke stažení **soubor metadat poskytovatele služeb** a nahrajte ho v **základní konfiguraci SAML** oddílu Automatické polulate **identifikátor** a **adresy URL odpovědi** hodnoty na webu Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Přejděte na stejné stránce **povolit jednotné přihlašování** části a proveďte následující kroky:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Vyberte **povolení jednotného přihlašování služby**.
    
    b. Vyberte **umožňují uživatelům přihlásit se prostřednictvím přihlašovacích údajů pro jednotné přihlašování nebo RingCentral**.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-ringcentral-test-user"></a>Vytvoření zkušebního uživatele RingCentral

V této části vytvoříte uživatele v RingCentral jako Britta Simon. Práce s [tým podpory RingCentral klienta](https://success.ringcentral.com/RCContactSupp) přidat uživatele na platformě RingCentral. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k RingCentral použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici RingCentral na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci RingCentral.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

