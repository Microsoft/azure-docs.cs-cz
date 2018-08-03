---
title: 'Kurz: Integrace Azure Active Directory s plátna Lms | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LMS plátna.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: af2c997f0842da751eb93f0788a7402fc7d144ae
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433285"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Kurz: Integrace Azure Active Directory s LMS plátna

V tomto kurzu se dozvíte, jak integrovat plátna pomocí služby Azure Active Directory (Azure AD).

Integrace plátno s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k plátnu
- Můžete povolit uživatelům, aby automaticky získat přihlášeného ho na plátno (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s plátno, potřebujete následující položky:

- S předplatným služby Azure AD
- Plátně jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání plátna z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-canvas-from-the-gallery"></a>Přidání plátna z Galerie
Konfigurace integrace plátna do služby Azure AD, budete muset přidat plátna z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat plátna z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **plátna**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/canvas-lms-tutorial/tutorial_canvaslms_search.png)

1. Na panelu výsledků vyberte **plátna**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s plátno, na základě testovací uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek plátně je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské plátně.

Plátno, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s plátno, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele plátna](#creating-a-canvas-test-user)**  – Pokud chcete mít protějšek Britta Simon plátno, na kterém je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci plátna.

**Ke konfiguraci Azure AD jednotné přihlašování s plátno, proveďte následující kroky:**

1. Na webu Azure Portal na **plátna** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

1. Na **plátna domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.instructure.com`

    b. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory plátna klienta](https://community.canvaslms.com/community/help) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurace jednotného přihlašování](./media/canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/canvas-lms-tutorial/tutorial_general_400.png)

1. Na **plátna konfigurace** klikněte na tlačítko **konfigurace plátna** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **heslo změnit adresu URL, adresa URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti plátna jako správce.

1. Přejděte na **kurzy \> spravované účty \> Microsoft**.
   
    ![Plátno](./media/canvas-lms-tutorial/IC775990.png "plátna")

1. V navigačním podokně na levé straně vyberte **ověřování**a potom klikněte na tlačítko **přidat novou konfiguraci SAML**.
   
    ![Ověřování](./media/canvas-lms-tutorial/IC775991.png "ověřování")

1. Na stránce aktuální integrace postupujte následovně:
   
    ![Aktuální integrace](./media/canvas-lms-tutorial/IC775992.png "aktuální integrace")

    a. V **IdP Entity ID** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.

    b. V **protokolu na adresu URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. V **adresy URL odhlašovací** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

    d. V **odkaz změnit heslo** textového pole vložte hodnotu **heslo změnit adresu URL** zkopírovanou z webu Azure portal. 

    e. V **otisku certifikátu** vložit do textového pole **kryptografický otisk** hodnotu certifikát, který jste zkopírovali z portálu Azure portal.      
        
    f. Z **přihlášení atribut** seznamu vyberte **NameID**.

    g. Z **identifikátor formátu** seznamu vyberte **emailAddress**.

    h. Klikněte na tlačítko **uložit nastavení ověřování**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/canvas-lms-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/canvas-lms-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/canvas-lms-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/canvas-lms-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-canvas-test-user"></a>Vytvoření zkušebního uživatele plátna

Pokud chcete povolit Azure AD uživatelům přihlášení na plátno, musí být zřízená plátno.

V případě plátno zřizování uživatelů je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **plátna** tenanta.

1. Přejděte na **kurzy \> spravované účty \> Microsoft**.
   
   ![Plátno](./media/canvas-lms-tutorial/IC775990.png "plátna")

1. Klikněte na tlačítko **uživatelé**.
   
   ![Uživatelé](./media/canvas-lms-tutorial/IC775995.png "uživatelů")

1. Klikněte na tlačítko **přidání nového uživatele**.
   
   ![Uživatelé](./media/canvas-lms-tutorial/IC775996.png "uživatelů")

1. Na stránce dialogového okna nového uživatele přidat proveďte následující kroky:
   
   ![Přidání uživatele](./media/canvas-lms-tutorial/IC775997.png "přidat uživatele")
   
   a. V **jméno a příjmení** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.

   b. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

   c. V **přihlášení** textového pole zadejte uživatele Azure AD e-mailovou adresu jako **brittasimon@contoso.com**.

   d. Vyberte **e-mailu uživatele o vytvoření tohoto účtu**.

   e. Klikněte na tlačítko **přidat uživatele**.

>[!NOTE]
>Můžete použít jakékoli jiné plátna uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných plátna uživatelským účtům, zřídit AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na plátno.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon na plátno, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **plátna**.

    ![Konfigurace jednotného přihlašování](./media/canvas-lms-tutorial/tutorial_canvaslms_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici plátno, na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci plátna.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/canvas-lms-tutorial/tutorial_general_203.png

