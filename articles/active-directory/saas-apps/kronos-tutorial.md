---
title: 'Kurz: Integrace Azure Active Directory se službou Kronos | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kronos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e28d6191-c375-43c6-b2df-22daa88d9939
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 31e8c990e39b3dc99ccd4dcda0a8d00ecb83b440
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435879"
---
# <a name="tutorial-azure-active-directory-integration-with-kronos"></a>Kurz: Integrace Azure Active Directory se službou Kronos

V tomto kurzu se dozvíte, jak integrovat Kronos s Azure Active Directory (Azure AD).

Kronos integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Kronos
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Kronos (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Kronos, potřebujete následující položky:

- S předplatným služby Azure AD
- A **Kronos pracovní síly centrální** jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kronos z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kronos-from-the-gallery"></a>Přidání Kronos z Galerie
Konfigurace integrace Kronos do služby Azure AD, budete muset přidat Kronos z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Kronos z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Kronos**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kronos-tutorial/tutorial_kronos_search.png)

1. Na panelu výsledků vyberte **Kronos**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kronos-tutorial/tutorial_kronos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Kronos podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Kronos je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Kronos potřeba navázat.

V Kronos, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Kronos, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Kronos](#creating-a-kronos-test-user)**  – Pokud chcete mít protějšek Britta Simon Kronos, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Kronos.

**Ke konfiguraci Azure AD jednotné přihlašování s Kronos, proveďte následující kroky:**

1. Na webu Azure Portal na **Kronos** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/kronos-tutorial/tutorial_kronos_samlbase.png)

1. Na **Kronos domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kronos-tutorial/tutorial_kronos_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.kronos.net/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory Kronos](https://www.kronos.in/contact/en-in/form) k získání těchto hodnot.
 
1. Vaše aplikace Kronos očekává, že kontrolní výrazy SAML v určitém formátu. Práce s [tým podpory Kronos](https://www.kronos.in/contact/en-in/form) nejprve k identifikaci identifikátor správné uživatele, která je namapována na aplikaci. Také využijte pokyny pro atribut, který chce použít toto mapování.
 
     Společnost Microsoft doporučuje používat **"NameIdentifier"** atribut jako identifikátor uživatele. Můžete spravovat hodnotami těchto atributů z **"Atributy uživatele"** části na stránce aplikací pro integraci.
     
     Následující snímek obrazovky ukazuje příklad pro tuto. Tady jsme změnili **identifikátor uživatele (nameid)** s **ExtractMailPrefix()** funkce **user.userprincipalname**. Díky tomu získá hodnotu předpony z e-mailu uživatele, který je jedinečné ID uživatele. Ta se odešle do aplikace Kronos v každé úspěšné odpovědi. 
     
    ![Konfigurace jednotného přihlašování](./media/kronos-tutorial/tutorial_kronos_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogové okno:

    a. V rozevíracím seznamu identifikátor uživatele, vyberte **ExtractMailPrefix**.

    b. V **e-mailu** rozevíracího seznamu vyberte **user.userprincipalname**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/kronos-tutorial/tutorial_kronos_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/kronos-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Kronos** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory Kronos](https://www.kronos.in/contact/en-in/form). 

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kronos-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/kronos-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kronos-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kronos-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kronos-test-user"></a>Vytvoření zkušebního uživatele Kronos

V této části vytvoříte uživatele v Kronos jako Britta Simon. Aplikace Kronos musí všichni uživatelé, které se mají zřídit v aplikaci před provedením jednotného přihlašování. 

Práce s [tým podpory Kronos](https://www.kronos.in/contact/en-in/form) zřídit všechny tyto uživatele do aplikace. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Kronos použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Kronos Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Kronos**.

    ![Konfigurace jednotného přihlašování](./media/kronos-tutorial/tutorial_kronos_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kronos na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Kronos.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kronos-tutorial/tutorial_general_01.png
[2]: ./media/kronos-tutorial/tutorial_general_02.png
[3]: ./media/kronos-tutorial/tutorial_general_03.png
[4]: ./media/kronos-tutorial/tutorial_general_04.png

[100]: ./media/kronos-tutorial/tutorial_general_100.png

[200]: ./media/kronos-tutorial/tutorial_general_200.png
[201]: ./media/kronos-tutorial/tutorial_general_201.png
[202]: ./media/kronos-tutorial/tutorial_general_202.png
[203]: ./media/kronos-tutorial/tutorial_general_203.png

