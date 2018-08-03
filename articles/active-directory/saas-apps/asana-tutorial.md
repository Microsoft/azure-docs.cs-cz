---
title: 'Kurz: Integrace Azure Active Directory se službou Asana | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.openlocfilehash: f5ea7a330891d4befeb6388bbe7f37b2a4aa848f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438204"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Kurz: Integrace Azure Active Directory se službou Asaně

V tomto kurzu se dozvíte, jak integrovat Asana s Azure Active Directory (Azure AD).

Asana integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Asaně
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Asana (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Asana, potřebujete následující položky:

- S předplatným služby Azure AD
- Asana jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Asana z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-asana-from-the-gallery"></a>Přidání Asana z Galerie
Konfigurace integrace Asana do služby Azure AD, budete muset přidat z Galerie Asana pro váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Asana z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Asana**vyberte **Asana** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Asana podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Asaně je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Asaně.

V Asaně, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Asana, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele Asana](#create-an-asana-test-user)**  – Pokud chcete mít protějšek Britta Simon v Asaně, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v Asaně aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s Asana, proveďte následující kroky:**

1. Na webu Azure Portal na **Asana** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/asana-tutorial/tutorial_asana_samlbase.png)

1. Na **Asana domény a adresy URL** části, proveďte následující kroky:

    ![Asana domény a adresy URL jednotného přihlašování – informace](./media/asana-tutorial/tutorial_asana_url.png)

    a. V **přihlašovací adresa URL** textového pole zadat adresu URL: `https://app.asana.com/`

    b. V **identifikátor** textové pole, typ hodnoty: `https://app.asana.com/`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/asana-tutorial/tutorial_asana_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/asana-tutorial/tutorial_general_400.png)

1. Na **Asana konfigurace** klikněte na tlačítko **nakonfigurovat Asana** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Asaně](./media/asana-tutorial/tutorial_asana_configure.png)

1. V jiném okně prohlížeče přihlašování k aplikaci Asana. Pokud chcete nakonfigurovat jednotné přihlašování v Asaně, přístup k nastavení pracovního prostoru klikněte na název pracovního prostoru v pravém horním rohu obrazovky. Potom klikněte na  **\<názvu pracovního prostoru\> nastavení**.

    ![Nastavení jednotného přihlašování v Asaně](./media/asana-tutorial/tutorial_asana_09.png)

1. Na **nastavení organizace** okna, klikněte na tlačítko **správu**. Potom klikněte na **členy se musí přihlásit prostřednictvím SAML** umožňující konfiguraci jednotného přihlašování. Proveďte následující kroky:

    ![Konfigurace nastavení organizace jednotné přihlašování](./media/asana-tutorial/tutorial_asana_10.png)  

     a. V **přihlašovací adresa URL stránky** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby**.

     b. Klikněte pravým tlačítkem na certifikát stažený z webu Azure portal a pak otevřete soubor certifikátu pomocí poznámkového bloku nebo upřednostňovaném textovém editoru. Obsah mezi zahájit a název certifikátu koncovým zkopírujte a vložte ji **certifikát X.509** textového pole.

1. Klikněte na **Uložit**. Přejděte na [Asana Průvodce pro nastavení jednotné přihlašování](https://asana.com/guide/help/premium/authentication#gl-saml) Pokud potřebujete další pomoc.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/asana-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/asana-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asana-tutorial/create_aaduser_03.png)

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Tlačítko Přidat](./media/asana-tutorial/create_aaduser_04.png)

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-asana-test-user"></a>Vytvořit testovacího uživatele Asaně

Cílem této části je vytvořte uživatele Britta Simon v Asaně. Asana podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](asana-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

V této části vytvořte uživatele Britta Simon v Asaně.

1. Na **Asana**, přejděte **týmy** části na levém panelu. Klikněte na tlačítko plus.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

1. Zadejte e-mailu britta.simon@contoso.com v textovém poli a pak vyberte **pozvat**.

1. Klikněte na tlačítko **odeslat pozvánku**. Nový uživatel obdrží e-mailu do svého e-mailový účet. Uživatel muset vytvořit a ověřit účet.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Asana.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Asana, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **Asana**.

    ![Odkaz Asana v seznamu aplikací](./media/asana-tutorial/tutorial_asana_app.png)

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je test vaší služby Azure AD jednotného přihlašování.

Přejděte na stránku pro přihlášení Asana. V textovém poli e-mailovou adresu, vložte e-mailovou adresu britta.simon@contoso.com. Ponechte textové pole hesla prázdný a potom klikněte na tlačítko **přihlásit**. Budete přesměrováni na přihlašovací stránku Azure AD. Vyplňte svoje přihlašovací údaje Azure AD. Nyní jste přihlášeni Asaně.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](asana-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/asana-tutorial/tutorial_general_01.png
[2]: ./media/asana-tutorial/tutorial_general_02.png
[3]: ./media/asana-tutorial/tutorial_general_03.png
[4]: ./media/asana-tutorial/tutorial_general_04.png

[100]: ./media/asana-tutorial/tutorial_general_100.png

[200]: ./media/asana-tutorial/tutorial_general_200.png
[201]: ./media/asana-tutorial/tutorial_general_201.png
[202]: ./media/asana-tutorial/tutorial_general_202.png
[203]: ./media/asana-tutorial/tutorial_general_203.png
[10]: ./media/asana-tutorial/tutorial_general_060.png
[11]: ./media/asana-tutorial/tutorial_general_070.png