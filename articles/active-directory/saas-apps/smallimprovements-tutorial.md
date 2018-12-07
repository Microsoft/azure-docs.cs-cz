---
title: 'Kurz: Integrace Azure Active Directory se službou menších vylepšení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a menších vylepšení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 588e56c9ae22578c08dbca07c7c576fe8b577b58
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012330"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Kurz: Integrace Azure Active Directory se službou menších vylepšení

V tomto kurzu se dozvíte, jak integrovat menších vylepšení Azure Active Directory (Azure AD).

Malá vylepšení integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do menších vylepšení
- Uživatele, aby automaticky získat přihlášení k menších vylepšení (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s menších vylepšení, potřebujete následující položky:

- Předplatné Azure AD
- Menších vylepšení jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební zde [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání menších vylepšení z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-small-improvements-from-the-gallery"></a>Přidání menších vylepšení z Galerie
Konfigurace integrace menších vylepšení do služby Azure AD, budete muset přidat malá vylepšení v galerii na váš seznam spravovaných aplikací SaaS.

**Přidání menších vylepšení z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **menších vylepšení**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_search.png)

1. Na panelu výsledků vyberte **menších vylepšení**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí menších vylepšení podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v menších vylepšení je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v menších vylepšení.

V menších vylepšení přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s menších vylepšení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele menších vylepšení](#creating-a-small-improvements-test-user)**  – Pokud chcete mít protějšek Britta Simon v menších vylepšení, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Small vylepšení.

**Ke konfiguraci Azure AD jednotné přihlašování s menších vylepšení, proveďte následující kroky:**

1. Na webu Azure Portal na **menších vylepšení** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

1. Na **malá vylepšení domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.small-improvements.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory malá vylepšení klienta](mailto:support@small-improvements.com) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_general_400.png)

1. Na **malá vylepšení konfigurace** klikněte na tlačítko **konfigurace menších vylepšení** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

1. V jiném okně prohlížeče Přihlaste se k webu společnosti menších vylepšení jako správce.

1. Na stránce hlavní řídicí panel, klikněte na tlačítko **správu** tlačítko na levé straně.
   
    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klikněte na tlačítko **SAML SSO** tlačítko **integrace** oddílu.
   
    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. V **koncový bod HTTP** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    b. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **x509 certifikát** textového pole. 

    c. Pokud chcete mít jednotného přihlašování a přihlašovací formulář možnost ověřování k dispozici pro uživatele, zkontrolujte **povolte přístup přes přihlašovací jméno/heslo příliš** možnost.  

    d. Zadejte odpovídající hodnotu název tlačítka pro přihlášení SSO v **SAML výzvy** textového pole.  

    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-small-improvements-test-user"></a>Vytváření menších vylepšení testovacího uživatele

Pokud chcete povolit Azure AD uživatelům přihlášení do menších vylepšení, musí být poskytnuty do menších vylepšení. V případě menších vylepšení zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlašování k webu společnosti menších vylepšení jako správce.

1. Na domovské stránce, přejděte do nabídky na levé, klikněte na tlačítko **správu**.

1. Klikněte na tlačítko **adresář uživatele** tlačítko z části Správa uživatelů. 
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klikněte na tlačítko **přidat uživatele**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Na **Add Users** dialogového okna, proveďte následující kroky: 

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. Zadejte **křestní jméno** uživatele, jako je **Britta**.

    b. Zadejte **příjmení** uživatele, jako je **Simon**.

    c. Zadejte **e-mailu** uživatele, jako je <strong>brittasimon@contoso.com</strong>. 

    d. Můžete také zadat osobní zprávu v **odeslat e-mail s oznámením** pole. Pokud nechcete, aby se odeslat oznámení, poté zrušte zaškrtnutí tohoto políčka.

    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do menších vylepšení.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit menších vylepšení, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **menších vylepšení**.

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.  

Po kliknutí na dlaždici menších vylepšení na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci menších vylepšení.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/smallimprovements-tutorial/tutorial_general_203.png

