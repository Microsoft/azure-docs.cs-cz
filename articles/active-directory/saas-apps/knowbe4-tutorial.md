---
title: 'Kurz: Integrace Azure Active Directory s školení povědomí o zabezpečení KnowBe4 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a KnowBe4 školení o zabezpečení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: c27af4e7bc88f24b76310336859740d8795f7cbe
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449269"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Kurz: Integrace Azure Active Directory s KnowBe4 školení o zabezpečení

V tomto kurzu se dozvíte, jak integrovat KnowBe4 školení o zabezpečení Azure Active Directory (Azure AD).

Školení o zabezpečení KnowBe4 integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k KnowBe4 školení o zabezpečení.
- Uživatele, aby automaticky získat přihlášeného k tréninku povědomí o zabezpečení KnowBe4 (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s KnowBe4 školení o zabezpečení, potřebujete následující položky:

- S předplatným služby Azure AD
- Školení o zabezpečení KnowBe4 jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání KnowBe4 školení o zabezpečení z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Přidání KnowBe4 školení o zabezpečení z Galerie
Konfigurace integrace KnowBe4 školení o zabezpečení do služby Azure AD, budete muset přidat KnowBe4 školení o zabezpečení z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat KnowBe4 školení o zabezpečení z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **školení povědomí o zabezpečení KnowBe4**vyberte **školení povědomí o zabezpečení KnowBe4** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Školení o zabezpečení KnowBe4 v seznamu výsledků](./media/knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí školení povědomí o zabezpečení KnowBe4 podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v KnowBe4 školení o zabezpečení je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v KnowBe4 školení o zabezpečení.

V KnowBe4 školení povědomí o zabezpečení, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s KnowBe4 školení o zabezpečení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele školení povědomí o zabezpečení KnowBe4](#create-a-knowbe4-security-awareness-training-test-user)**  – Pokud chcete mít protějšek Britta Simon KnowBe4 povědomí školení týkající se zabezpečení, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci KnowBe4 školení o zabezpečení.

**Chcete-li nakonfigurovat KnowBe4 školení o zabezpečení Azure AD jednotného přihlašování, postupujte následovně:**

1. Na webu Azure Portal na **školení povědomí o zabezpečení KnowBe4** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

1. Na **KnowBe4 zabezpečení povědomí školení domény a adresy URL** části, proveďte následující kroky:

    ![KnowBe4 zabezpečení povědomí školení domény a adresy URL jednotného přihlašování – informace](./media/knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Přihlašování byste použili hodnotu URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta školení o zabezpečení KnowBe4](mailto:support@KnowBe4.com) tuto výhodu získáte. 

    b. V **identifikátor** textového pole zadejte hodnotu řetězce: `KnowBe4`

    > [!NOTE]
    >Toto je velká a malá písmena

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/knowbe4-tutorial/tutorial_general_400.png)

1. Na **konfigurace školení o zabezpečení KnowBe4** klikněte na tlačítko **konfigurace školení o zabezpečení KnowBe4** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace školení o zabezpečení KnowBe4](./media/knowbe4-tutorial/tutorial_knowbe4_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **školení povědomí o zabezpečení KnowBe4** straně, je nutné odeslat na stažený **certifikát (Raw)**, **URL odhlašování SAML Entity ID a SAML jednotného přihlašování Adresa URL služby** k [tým podpory klienta školení o zabezpečení KnowBe4](mailto:support@KnowBe4.com).

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/knowbe4-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/knowbe4-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/knowbe4-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/knowbe4-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>Vytvoření zkušebního uživatele KnowBe4 školení o zabezpečení

Cílem této části je vytvořte uživatele Britta Simon v KnowBe4 školení o zabezpečení. Školení o zabezpečení KnowBe4 podporuje just-in-time zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k KnowBe4 školení o zabezpečení, pokud ještě neexistuje. 

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory školení povědomí o zabezpečení KnowBe4](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke školením povědomí o zabezpečení KnowBe4.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit KnowBe4 školení o zabezpečení, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **školení povědomí o zabezpečení KnowBe4**.

    ![Školení o zabezpečení KnowBe4 odkaz v seznamu aplikací](./media/knowbe4-tutorial/tutorial_knowbe4_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.
  
Po kliknutí na dlaždici KnowBe4 školení o zabezpečení na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci KnowBe4 školení o zabezpečení. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/knowbe4-tutorial/tutorial_general_203.png

