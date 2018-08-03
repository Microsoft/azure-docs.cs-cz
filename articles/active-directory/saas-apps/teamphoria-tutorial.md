---
title: 'Kurz: Integrace Azure Active Directory se službou Teamphoria | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 554930b18a271a677aeb5e82c3e62a94965a8e7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439605"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Kurz: Integrace Azure Active Directory se službou Teamphoria

V tomto kurzu se dozvíte, jak integrovat Teamphoria s Azure Active Directory (Azure AD).

Teamphoria integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Teamphoria
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Teamphoria (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Teamphoria, potřebujete následující položky:

- S předplatným služby Azure AD
- Teamphoria jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Teamphoria z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-teamphoria-from-the-gallery"></a>Přidání Teamphoria z Galerie
Konfigurace integrace Teamphoria do služby Azure AD, budete muset přidat Teamphoria z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Teamphoria z galerie, postupujte následovně:**

1. V  **[webu Azure Portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Teamphoria**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

1. Na panelu výsledků vyberte **Teamphoria**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Teamphoria podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Teamphoria je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Teamphoria potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Teamphoria, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Teamphoria](#creating-a-teamphoria-test-user)**  – Pokud chcete mít protějšek Britta Simon Teamphoria, který je propojený s Azure AD reprezentace jí.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Teamphoria.

**Ke konfiguraci Azure AD jednotné přihlašování s Teamphoria, proveďte následující kroky:**

1. Na webu Azure Portal na **Teamphoria** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

1. Na **Teamphoria domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Budete muset aktualizovat tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Teamphoria klienta](https://www.teamphoria.com/) získat adresu URL přihlašování.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte certifikát ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/tutorial_general_400.png)

1. Na **Teamphoria konfigurace** klikněte na tlačítko **nakonfigurovat Teamphoria** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

1. Ke konfiguraci jednotného přihlašování na **Teamphoria** straně, přihlaste se k aplikaci Teamphoria jako správce.

1. Přejděte na **nastavení správy** možnost v levém panelu nástrojů a na kartě Konfigurace klikněte na **jednotné přihlašování** otevřete okno Konfigurace jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Klikněte na **přidat nového zprostředkovatele IDENTITY** možnost v pravém horním rohu otevřete formulář pro přidání nastavení pro jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Do polí zadejte podrobnosti, jak je popsáno níže –

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **ZOBRAZOVANÝ název**: Zadejte zobrazovaný název modulu plug-in na stránky pro správu.

    b. **Název TLAČÍTKA**: název karty, které se zobrazí na přihlašovací stránku pro přihlášení pomocí jednotného přihlašování.

    c. **CERTIFIKÁT**: otevřít certifikátu dříve stáhli z portálu Azure v programu Poznámkový blok, zkopírujte obsah stejné a vložte ho do pole.

    d. **VSTUPNÍ bod**: Vložit **SAML jednotné přihlašování – adresa URL služby** zkopírovali z portálu Azure portal.

    e. Přepněte možnost **ON** a klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamphoria-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/teamphoria-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/teamphoria-tutorial/create_aaduser_03.png)

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-teamphoria-test-user"></a>Vytvoření zkušebního uživatele Teamphoria

Chcete-li povolit uživatele Azure AD k přihlášení do Teamphoria, musí být poskytnuty do Teamphoria. V případě Teamphoria zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Teamphoria jako správce.

1. Klikněte na **správce** nastavení na levém panelu nástrojů a v části **SPRAVOVAT** klikněte na kartě **uživatelé** otevřete stránku Správce pro uživatele.

    ![Přidat zaměstnance](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klikněte na **POZVAT RUČNÍ** možnost.

    ![Pozvat](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Na této stránce proveďte následující akce.
    
    ![Pozvat](./media/teamphoria-tutorial/manual_user_invite.png)

    a. V **e-MAILOVOU adresu** textového pole **e-mailová adresa** z BrittaSimon.

    b. V **KŘESTNÍ jméno** textové pole, typ **Britta**.

    c. V **příjmení** textové pole, typ **Simon**.

    d. Klikněte na tlačítko **pozvání 1 uživatel**. Uživatel musí přijmout pozvánku pro vytvořené v systému.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Teamphoria použití Azure jednotného přihlašování.

![Přiřadit uživatele][200]

**Přiřadit Teamphoria Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **Teamphoria**.

    ![Konfigurace jednotného přihlašování](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
