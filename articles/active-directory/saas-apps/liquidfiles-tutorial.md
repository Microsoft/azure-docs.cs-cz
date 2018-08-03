---
title: 'Kurz: Integrace Azure Active Directory se službou LiquidFiles | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: f1fc9be81cb5ada628c253351746dbaf11fe3b84
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446091"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Kurz: Integrace Azure Active Directory se službou LiquidFiles

V tomto kurzu se dozvíte, jak integrovat LiquidFiles s Azure Active Directory (Azure AD).

LiquidFiles integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LiquidFiles
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LiquidFiles (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LiquidFiles, potřebujete následující položky:

- S předplatným služby Azure AD
- LiquidFiles jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LiquidFiles z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-liquidfiles-from-the-gallery"></a>Přidání LiquidFiles z Galerie
Konfigurace integrace LiquidFiles do služby Azure AD, budete muset přidat LiquidFiles z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LiquidFiles z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LiquidFiles**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/liquidfiles-tutorial/tutorial_liquidfiles_search.png)

1. Na panelu výsledků vyberte **LiquidFiles**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/liquidfiles-tutorial/tutorial_liquidfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí LiquidFiles podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v LiquidFiles je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LiquidFiles potřeba navázat.

V LiquidFiles, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LiquidFiles, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LiquidFiles](#creating-a-liquidfiles-test-user)**  – Pokud chcete mít protějšek Britta Simon LiquidFiles, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LiquidFiles.

**Ke konfiguraci Azure AD jednotné přihlašování s LiquidFiles, proveďte následující kroky:**

1. Na webu Azure Portal na **LiquidFiles** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_liquidfiles_samlbase.png)

1. Na **LiquidFiles domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_liquidfiles_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<YOUR_SERVER_URL>/saml/init`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<YOUR_SERVER_URL>`

    c. b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory LiquidFiles klienta](https://www.liquidfiles.com/support.html) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_liquidfiles_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_general_400.png)

1. Na **LiquidFiles konfigurace** klikněte na tlačítko **nakonfigurovat LiquidFiles** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_liquidfiles_configure.png)
 
1. Přihlašování k webu společnosti LiquidFiles jako správce.

1. Klikněte na tlačítko **Single Sign-On** v **správce > Konfigurace** z nabídky.

1. Na **Konfigurace jednotného přihlašování** stránce, proveďte následující kroky

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. Jako **jednotné přihlašování na metodu**vyberte **SAML 2**.

    b. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **odhlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. V **otisk certifikátu zprostředkovatele identity** vložit do textového pole **kryptografický OTISK** hodnotu, která jste zkopírovali z portálu Azure portal...

    e. V textovém poli Formát názvu identifikátor zadejte hodnotu **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: emailAddress**.

    f. V textovém poli Authn kontextu, zadejte hodnotu **urn: oasis: názvy: tc: SAML:2.0:ac:classes:PasswordProtectedTransport**.

    g. Klikněte na **Uložit**.  

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/liquidfiles-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/liquidfiles-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/liquidfiles-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/liquidfiles-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-liquidfiles-test-user"></a>Vytvoření zkušebního uživatele LiquidFiles

Cílem této části je vytvořte uživatele Britta Simon v LiquidFiles. Práce s vaším správcem serveru LiquidFiles nechejte se přidat jako uživatele před přihlášením k aplikaci LiquidFiles získat.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k LiquidFiles použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit LiquidFiles Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **LiquidFiles**.

    ![Konfigurace jednotného přihlašování](./media/liquidfiles-tutorial/tutorial_liquidfiles_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici LiquidFiles na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci LiquidFiles.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/liquidfiles-tutorial/tutorial_general_01.png
[2]: ./media/liquidfiles-tutorial/tutorial_general_02.png
[3]: ./media/liquidfiles-tutorial/tutorial_general_03.png
[4]: ./media/liquidfiles-tutorial/tutorial_general_04.png

[100]: ./media/liquidfiles-tutorial/tutorial_general_100.png

[200]: ./media/liquidfiles-tutorial/tutorial_general_200.png
[201]: ./media/liquidfiles-tutorial/tutorial_general_201.png
[202]: ./media/liquidfiles-tutorial/tutorial_general_202.png
[203]: ./media/liquidfiles-tutorial/tutorial_general_203.png

