---
title: 'Kurz: Integrace Azure Active Directory s AppBlade | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppBlade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 119647c4aeabc8f4a6dd3a1933fcfafe6b6cf5c2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813317"
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Kurz: Integrace Azure Active Directory s AppBlade

V tomto kurzu se dozvíte, jak integrovat AppBlade s Azure Active Directory (Azure AD).

AppBlade integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AppBlade
- Můžete povolit uživatelům, aby automaticky získat přihlášení k AppBlade (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AppBlade, potřebujete následující položky:

- Předplatné Azure AD
- AppBlade jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání AppBlade z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-appblade-from-the-gallery"></a>Přidání AppBlade z Galerie
Konfigurace integrace AppBlade do služby Azure AD, budete muset přidat AppBlade z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat AppBlade z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **AppBlade**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appblade-tutorial/tutorial_appblade_search.png)

5. Na panelu výsledků vyberte **AppBlade**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appblade-tutorial/tutorial_appblade_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s AppBlade podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v AppBlade je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v AppBlade potřeba navázat.

V AppBlade, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AppBlade, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno AppBlade](#creating-an-appblade-test-user)**  – Pokud chcete mít protějšek Britta Simon AppBlade, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci AppBlade.

**Ke konfiguraci Azure AD jednotné přihlašování s AppBlade, proveďte následující kroky:**

1. Na webu Azure Portal na **AppBlade** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/appblade-tutorial/tutorial_appblade_samlbase.png)

3. Na **AppBlade domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/appblade-tutorial/tutorial_appblade_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory AppBlade klienta](mailto:support@appblade.com) má být získána hodnota. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/appblade-tutorial/tutorial_appblade_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/appblade-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **AppBlade** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory AppBlade](mailto:support@appblade.com). Kromě toho prosím požádejte ho, aby konfigurace **URL vystavitele jednotného přihlašování** jako `https://appblade.com/saml`. Toto nastavení je povinné pro jednotné přihlašování pro práci.


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appblade-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/appblade-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/appblade-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/appblade-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-appblade-test-user"></a>Vytvoření AppBlade testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v AppBlade. AppBlade podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. **Ujistěte se, že váš název domény má nakonfigurovanou AppBlade pro zřizování uživatelů. Poté, co to jenom uživatel just-in-time zřizování funguje.**

Pokud má uživatel e-mailovou adresu, končí domény ve AppBlade nakonfigurované pro váš účet a uživatel se automaticky připojí k účtu jako člena s úrovní oprávnění, kterou zadáte, což je jedna z "Basic" (základní uživatel, který může jen instalovat aplikace) , "Člen týmu" (uživatel, který můžete nahrát nové verze aplikace a spravovat projekty), nebo "Administrator" (oprávnění správce k účtu). Obvykle jednu by vyberte Basic a pak ji převeďte uživatele ručně přes (AppBlade musí konfigurovat buď k přihlášení správce na základě e-mailu předem nebo podporu po přihlášení uživatele jménem zákazníka) přihlašovací jméno správce.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k AppBlade, pokud ještě neexistuje. 

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory AppBlade](mailto:support@appblade.com).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k AppBlade použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit AppBlade Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **AppBlade**.

    ![Konfigurace jednotného přihlašování](./media/appblade-tutorial/tutorial_appblade_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.  
Po kliknutí na dlaždici AppBlade na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci AppBlade. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/appblade-tutorial/tutorial_general_01.png
[2]: ./media/appblade-tutorial/tutorial_general_02.png
[3]: ./media/appblade-tutorial/tutorial_general_03.png
[4]: ./media/appblade-tutorial/tutorial_general_04.png

[100]: ./media/appblade-tutorial/tutorial_general_100.png

[200]: ./media/appblade-tutorial/tutorial_general_200.png
[201]: ./media/appblade-tutorial/tutorial_general_201.png
[202]: ./media/appblade-tutorial/tutorial_general_202.png
[203]: ./media/appblade-tutorial/tutorial_general_203.png

