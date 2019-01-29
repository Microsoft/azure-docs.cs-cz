---
title: 'Kurz: Integrace Azure Active Directory s obchodními analýzami agilní Birst | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Birst agilní obchodní analýzy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 61c95d17493dff493787be00fb56ec2f799b5d19
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175064"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Kurz: Integrace Azure Active Directory s Birst agilní obchodní analýzy

V tomto kurzu se dozvíte, jak integrovat Birst agilní obchodní analýzy s Azure Active Directory (Azure AD).

Integrace Birst agilní obchodní analýzy s využitím Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Birst agilní obchodní analýzy
- Uživatele, aby automaticky získat přihlášení k Birst agilní obchodní analýzy (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Birst agilní obchodní analýzy, potřebujete následující položky:

- Předplatné Azure AD
- Birst agilní obchodní analýzy jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Birst agilní obchodní analýzy z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Přidání Birst agilní obchodní analýzy z Galerie
Ke konfiguraci integrace Birst agilní obchodní analýzy do služby Azure AD, budete muset přidat Birst agilní obchodní analýzy z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Birst agilní obchodní analýzy z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Birst agilní obchodní analýzy**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/birst-tutorial/tutorial_birst_search.png)

1. Na panelu výsledků vyberte **Birst agilní obchodní analýzy**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Birst agilní obchodní analýzy založené na testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Birst agilní obchodní analýzy je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Birst agilní obchodní analýzy.

V Birst agilní obchodní analýzy, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Birst agilní obchodní analýzy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Birst agilní obchodní analýzy](#creating-a-birst-agile-business-analytics-test-user)**  – Pokud chcete mít protějšek Britta Simon Birst agilní obchodní analýzy, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Birst agilní obchodní analýzy.

**Ke konfiguraci Azure AD jednotné přihlašování s Birst agilní obchodní analýzy, proveďte následující kroky:**

1. Na webu Azure Portal na **Birst agilní obchodní analýzy** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/birst-tutorial/tutorial_birst_samlbase.png)

1. Na **Birst agilní obchodní analýzy domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/birst-tutorial/tutorial_birst_url.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     Adresa URL bude záviset na datového centra, se nachází váš účet Birst: 

     * Pro americké datového centra pomocí následujícího vzoru: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` 

     * Evropa datacenter používají následující vzor: `https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Birst agilní obchodní analýzy klienta](mailto:info@birst.com) má být získána hodnota. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/birst-tutorial/tutorial_birst_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/birst-tutorial/tutorial_general_400.png)

1. Na **Birst agilní obchodní analýzy konfigurace** klikněte na tlačítko **konfigurace Birst agilní obchodní analýzy** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/birst-tutorial/tutorial_birst_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Birst agilní obchodní analýzy** straně, je nutné odeslat na stažený **certifikát (Base64)**, **URL odhlašování SAML Entity ID a SAML jednotného přihlašování Adresa URL služby** k [tým podpory Birst agilní obchodní analýzy](mailto:info@birst.com). 

    > [!NOTE]
    > Zmínění Birst týmu, že tato integrační potřebuje algoritmus SHA256 (SHA1 nebude podporována) tak, aby na příslušném serveru mohou nastavit jednotné přihlašování, jako jsou **app2101** atd.
  

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/birst-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/birst-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/birst-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/birst-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Vytvoření zkušebního uživatele Birst agilní obchodní analýzy

Cílem této části je vytvořte uživatele Britta Simon v Birst agilní obchodní analýzy. Práce s [tým podpory Birst agilní obchodní analýzy](mailto:info@birst.com) k přidání uživatelů v účtu Birst. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Birst agilní obchodní analýzy.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Birst agilní obchodní analýzy, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Birst agilní obchodní analýzy**.

    ![Konfigurace jednotného přihlašování](./media/birst-tutorial/tutorial_birst_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Birst agilní obchodní analýzy, na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Birst agilní obchodní analýzy. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/birst-tutorial/tutorial_general_01.png
[2]: ./media/birst-tutorial/tutorial_general_02.png
[3]: ./media/birst-tutorial/tutorial_general_03.png
[4]: ./media/birst-tutorial/tutorial_general_04.png

[100]: ./media/birst-tutorial/tutorial_general_100.png

[200]: ./media/birst-tutorial/tutorial_general_200.png
[201]: ./media/birst-tutorial/tutorial_general_201.png
[202]: ./media/birst-tutorial/tutorial_general_202.png
[203]: ./media/birst-tutorial/tutorial_general_203.png

