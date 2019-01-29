---
title: 'Kurz: Integrace Azure Active Directory s financování portál | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a financování portál.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4663cc8a-976a-4c6c-b3b4-1e5df9b66744
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: c3e094fedae0a395df6862feceec0e60c66ee042
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196221"
---
# <a name="tutorial-azure-active-directory-integration-with-the-funding-portal"></a>Kurz: Integrace Azure Active Directory s financování portál

V tomto kurzu se dozvíte, jak integrovat financování portál Azure Active Directory (Azure AD).

Portál financování integrování Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k financování portál
- Uživatele, aby automaticky získat přihlášení k financování portál (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s financování portál, potřebujete následující položky:

- Předplatné Azure AD
- Financování portál jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání financování portál z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-the-funding-portal-from-the-gallery"></a>Přidání financování portál z Galerie
Konfigurace integrace financování portál do služby Azure AD, budete muset přidat portál financování z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat portál financování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **financování portál**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thefundingportal-tutorial/tutorial_thefundingportal_search.png)

1. Na panelu výsledků vyberte **financování portál**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thefundingportal-tutorial/tutorial_thefundingportal_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí financování portál na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek The financování portálu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské portálu financování potřeba navázat.

Portálu financování přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s financování portál, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváří se testovací uživatel financování portál](#creating-the-funding-portal-test-user)**  – Pokud chcete mít protějšek Britta Simon financování portál, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci financování portál.

**Ke konfiguraci Azure AD jednotné přihlašování s financování portál, proveďte následující kroky:**

1. Na webu Azure Portal na **financování portál** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_samlbase.png)

1. Na **financování domén portál a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.regenteducation.net/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.regenteducation.net`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory financování klient portál](mailto:info@regenteducation.com) k získání těchto hodnot. 

1. Aplikace portálu financování očekává, že kontrolní výrazy SAML obsahuje atribut s názvem "externalId1". Hodnota "externalId1" by měla být rozpoznané studentID. Konfigurace deklarace identity "externalId1" pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** aplikace. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ------------------- | ---------------- |
    | externalId1 | user.extensionattribute1 |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnota atributu** vyberte atribut, který chcete použít pro implementaci. Například pokud hodnota StudentID jste uložili v ExtensionAttribute1, pak vyberte user.extensionattribute1.
    
    d. Klikněte na tlačítko **OK**.
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **financování portál** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory portálu financování](mailto:info@regenteducation.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/thefundingportal-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/thefundingportal-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/thefundingportal-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/thefundingportal-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-the-funding-portal-test-user"></a>Vytváření financování portál testovacího uživatele

V této části vytvořte uživatele Britta Simon The financování portálu. Práce s [tým podpory portálu financování](mailto:info@regenteducation.com) přidat testovacího uživatele a povolení jednotného přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k financování portál.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit financování portál, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **financování portál**.

    ![Konfigurace jednotného přihlašování](./media/thefundingportal-tutorial/tutorial_thefundingportal_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici financování portál na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci financování portál.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/thefundingportal-tutorial/tutorial_general_04.png

[100]: ./media/thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/thefundingportal-tutorial/tutorial_general_201.png
[202]: ./media/thefundingportal-tutorial/tutorial_general_202.png
[203]: ./media/thefundingportal-tutorial/tutorial_general_203.png

