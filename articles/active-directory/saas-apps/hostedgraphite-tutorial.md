---
title: 'Kurz: Integrace Azure Active Directory s hostované grafitová | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hostované grafitová.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 8aefa0580e6a9a1e446dd4861a5627ea2a4d36ce
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811872"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Kurz: Integrace Azure Active Directory s hostované grafitová

V tomto kurzu se dozvíte, jak integrovat grafitová hostované službou Azure Active Directory (Azure AD).

Hostované grafitová integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k hostované grafitová
- Uživatele, aby automaticky získat přihlášení k hostované grafitová (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s hostované grafitová, potřebujete následující položky:

- Předplatné Azure AD
- Hostovaný grafitová jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání hostované grafitová z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-hosted-graphite-from-the-gallery"></a>Přidání hostované grafitová z Galerie
Pokud chcete nakonfigurovat integraci hostované grafitová do služby Azure AD, budete muset přidat hostované grafitová z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání hostované grafitová z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **hostované grafitová**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

1. Na panelu výsledků vyberte **hostované grafitová**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí grafitová hostované na základě testovací uživatele nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co uživatel protějšek v hostovaných grafitová je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v hostovaných grafitová potřeba navázat.

V hostované grafitová přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s hostované grafitová, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření testovacího uživatele hostované grafitová](#creating-a-hosted-graphite-test-user)**  – Pokud chcete mít protějšek Britta Simon v hostovaných grafitová, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci hostované grafitová.

**Ke konfiguraci Azure AD jednotné přihlašování s hostované grafitová, proveďte následující kroky:**

1. Na webu Azure Portal na **hostované grafitová** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

1. Na **hostované grafitová domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.hostedgraphite.com/metadata/<user id>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.hostedgraphite.com/complete/saml/<user id>`

1. Na **hostované grafitová domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **SP iniciované režimu**, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
    a. Klikněte na **zobrazit pokročilé nastavení URL** možnost

    b. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.hostedgraphite.com/login/saml/<user id>/`   

    > [!NOTE] 
    > Mějte prosím na paměti, že se nejedná skutečné hodnoty. Budete muset aktualizovat tyto hodnoty se skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. K získání těchto hodnot, můžete přejít na přístup -> Nastavení SAML na straně aplikace nebo kontaktujte [tým podpory hostovaných grafitová](mailto:help@hostedgraphite.com).
    >
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_general_400.png)

1. Na **hostované konfigurace grafitová** klikněte na **konfigurace hostované grafitová** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

1. Přihlašování k vašemu tenantovi hostované grafitová jako správce.

1. Přejděte **stránku nastavení SAML** na bočním panelu (**přístup -> Nastavení SAML**).
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

1. Potvrďte tyto adresy URL neodpovídá v konfiguraci **hostované grafitová domény a adresy URL** části webu Azure portal.
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

1. V **Entity nebo ID vystavitele** a **přihlašovací adresu URL pro jednotné přihlašování** textových polí, vložte hodnotu **SAML Entity ID** a **SAML jednotné přihlašování – adresa URL služby** který jste zkopírovali z portálu Azure portal. 
   
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

1. Vyberte "**jen pro čtení**" jako **výchozí Role uživatele**.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

1. Otevřete váš certifikát base-64 kódovaných v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a a vložte ho do **certifikát X.509** textového pole.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/hostedgraphite-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/hostedgraphite-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hostedgraphite-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/hostedgraphite-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Vytváření hostované grafitová testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v hostovaných grafitová. Hostované grafitová podporuje just-in-time zřizování, který je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k hostované grafitová, pokud ještě neexistuje.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat tým podpory hostovaných grafitová prostřednictvím <mailto:help@hostedgraphite.com>. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k hostované grafitová.

![Přiřadit uživatele][200] 

**Přiřadit hostované grafitová Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **hostované grafitová**.

    ![Konfigurace jednotného přihlašování](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici grafitová hostované na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci hostované grafitová.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/hostedgraphite-tutorial/tutorial_general_203.png

