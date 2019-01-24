---
title: 'Kurz: Integrace Azure Active Directory Mozy Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: a5730b518ab19591188f0a7f56b706d8cf29e6f1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816581"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Kurz: Integrace Azure Active Directory Mozy Enterprise

V tomto kurzu se dozvíte, jak integrovat Mozy organizace Azure Active Directory (Azure AD).

Mozy podnikové integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do firemní sítě Mozy
- Uživatele, aby automaticky získat přihlášení k Mozy Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Mozy Enterprise, budete potřebovat následující položky:

- Předplatné Azure AD
- Mozy podnikové jednotné přihlašování povoleno předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání organizace Mozy z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Přidání organizace Mozy z Galerie
Ke konfiguraci integrace Mozy organizace do služby Azure AD, budete muset přidat Mozy Enterprise na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání organizace Mozy z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Mozy Enterprise**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

1. Na panelu výsledků vyberte **Mozy Enterprise**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s Mozy Enterprise podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Mozy Enterprise je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v Mozy organizace je potřeba navázat.

V podnikových Mozy přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Mozy organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele organizace Mozy](#creating-a-mozy-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon Mozy Enterprise, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Mozy Enterprise.

**Ke konfiguraci Azure AD jednotné přihlašování s Mozy organizace, proveďte následující kroky:**

1. Na webu Azure Portal na **Mozy Enterprise** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

1. Na **Mozy podnikové domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Mozy Enterprise Client](http://support.mozy.com/) tuto výhodu získáte.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/mozy-enterprise-tutorial/tutorial_general_400.png)

1. Na **Mozy podniková konfigurace** klikněte na tlačítko **konfigurace organizace Mozy** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Mozy Enterprise.

1. V **konfigurace** klikněte na tlačítko **zásady ověřování**.
   
   ![Zásady ověřování](./media/mozy-enterprise-tutorial/ic777314.png "zásady ověřování")

1. Na **zásady ověřování** části, proveďte následující kroky:
   
   ![Zásady ověřování](./media/mozy-enterprise-tutorial/ic777315.png "zásady ověřování")
   
   a. Vyberte **adresářová služba** jako **poskytovatele**.
   
   b. Vyberte **využití nabízené instalace LDAP**.
   
   c. Klikněte na tlačítko **ověřování SAML** kartu.
   
   d. Vložit **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal do **adresy URL ověřování** textového pole.
   
   e. Vložit **SAML Entity ID**, který jste zkopírovali z portálu Azure portal do **koncový bod SAML** textového pole.
   
   f. Otevřete stažený certifikát kódováním base-64 v poznámkovém bloku, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikát SAML** textového pole.
   
   g. Vyberte **povolit jednotné přihlašování pro správce, které se chcete přihlásit pomocí svých přihlašovacích údajů sítě**.
   
   h. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Vytvoření zkušebního uživatele Mozy Enterprise

Chcete-li povolit uživatele Azure AD k přihlášení do Mozy organizace, musí být poskytnuty do Mozy Enterprise. V případě Mozy Enterprise zřizování je ruční úloha.

>[!NOTE]
>Můžete použít jakékoli jiné Mozy podnikové uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Mozy Enterprise uživatelským účtům, zřídit AAD.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Mozy Enterprise** tenanta.

1. Klikněte na tlačítko **uživatelé**a potom klikněte na tlačítko **Add New User**.
   
   ![Uživatelé](./media/mozy-enterprise-tutorial/ic777317.png "uživatelů")
   
   >[!NOTE]
   >**Přidat nového uživatele** možnost se zobrazí pouze pouze v případě **Mozy** je zvolen jako poskytovatele v rámci **zásady ověřování**. Pokud je nakonfigurované ověřování SAML, pak uživatelé jsou přidány automaticky při jejich první přihlášení pomocí jednotného přihlašování na.
    
1. V dialogovém okně Nový uživatel proveďte následující kroky:
   
   ![Přidání uživatelů](./media/mozy-enterprise-tutorial/ic777318.png "přidat uživatele")
   
   a. Z **zvolte skupinu** vyberte skupinu, ze seznamu.
   
   b. Z **jaký typ uživatelů** vyberte typ, ze seznamu.
   
   c. V **uživatelské jméno** textového pole zadejte jméno uživatele Azure AD.
   
   d. V **e-mailu** textového pole zadejte e-mailová adresa uživatele Azure AD.
   
   e. Vyberte **odeslání e-mailu uživatele instrukce**.
   
   f. Klikněte na tlačítko **přidat uživatele**.

     >[!NOTE]
     > Po vytvoření uživatele se odešle e-mail pro uživatele Azure AD, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do firemní sítě Mozy.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Mozy organizace, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Mozy Enterprise**.

    ![Konfigurace jednotného přihlašování](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Mozy Enterprise na přístupovém panelu, měli byste obdržet přihlašovací stránku Mozy podnikovou aplikaci.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/mozy-enterprise-tutorial/tutorial_general_203.png

