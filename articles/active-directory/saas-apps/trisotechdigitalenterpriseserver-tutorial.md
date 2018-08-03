---
title: 'Kurz: Integrace Azure Active Directory se službou Trisotech digitální Enterprise Server | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trisotech digitální Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: e36a4be3a95b67c040855171d4b167e495a22496
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439622"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Kurz: Integrace Azure Active Directory se službou Trisotech digitální Enterprise Server

V tomto kurzu se dozvíte, jak integrovat Trisotech digitální Enterprise serveru s Azure Active Directory (Azure AD).

Integrace Trisotech digitální Enterprise serveru s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Trisotech digitální Enterprise Server.
- Uživatele, aby automaticky získat přihlášeného k Trisotech digitální Enterprise serveru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Trisotech digitální Enterprise serveru, potřebujete následující položky:

- S předplatným služby Azure AD
- Trisotech digitální Enterprise serveru jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Trisotech digitální Enterprise serveru z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Přidání Trisotech digitální Enterprise serveru z Galerie
Ke konfiguraci integrace Trisotech digitální Enterprise serveru do služby Azure AD, budete muset přidat Trisotech digitální Enterprise serveru z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Trisotech digitální Enterprise serveru z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Trisotech digitální Enterprise Server**vyberte **Trisotech digitální Enterprise Server** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Trisotech digitální Enterprise Server v seznamu výsledků](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Trisotech digitální Enterprise Server vytvořený podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Trisotech digitální Enterprise serveru je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské Trisotech digitální Enterprise serveru je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Trisotech digitální Enterprise serveru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Trisotech digitální Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)**  – Pokud chcete mít protějšek Britta Simon Trisotech digitální Enterprise serveru, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Trisotech digitální Enterprise Server.

**Ke konfiguraci Azure AD jednotné přihlašování s Trisotech digitální Enterprise serveru, proveďte následující kroky:**

1. Na webu Azure Portal na **Trisotech digitální Enterprise Server** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

1. Na **Trisotech digitální Enterprise serveru domény a adresy URL** části, proveďte následující kroky:

    ![Trisotech digitální Enterprise serveru domény a adresy URL jednotného přihlašování – informace](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.trisotech.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Trisotech digitální Enterprise serveru Client](mailto:support@trisotech.com) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku. 

    ![Odkaz ke stažení certifikátu](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Trisotech digitální podniková konfigurace serveru jako správce.

1. Klikněte na **ikonu nabídky** a pak vyberte **správu**.

    ![Konfigurace jednotného přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

1. Vyberte **zprostředkovatel uživatele**.

    ![Konfigurace jednotného přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

1. V **uživatele poskytovatele konfigurace** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Vyberte **zabezpečené kontrolní výraz Markup Language 2 (SAML 2)** z rozevíracího seznamu v **metodu ověřování**.

    b. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, kterou jste zkopírovali formuláře na webu Azure portal.

    c. V **ID aplikace** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.trisotech.com`.

    d. Klikněte na **Uložit**.

    e. Zadejte název domény v **povolené domén (prázdná hodnota znamená všem uživatelům)** textového pole automaticky přiřadí licence pro porovnávání domén povolené uživatele

    f. Klikněte na **Uložit**.

 ### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Vytvoření zkušebního uživatele Trisotech digitální Enterprise Server

Cílem této části je vytvořte uživatele Britta Simon Trisotech digitální Enterprise serveru. Trisotech digitální Enterprise Server podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Trisotech digitální Enterprise serveru, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Trisotech digitální Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Trisotech digitální Enterprise serveru.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit k Trisotech digitální Enterprise serveru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Trisotech digitální Enterprise Server**.

    ![Odkaz Trisotech digitální Enterprise Server v seznamu aplikací](./media/trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Trisotech digitální Enterprise Server na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Trisotech digitální Enterprise Server.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

