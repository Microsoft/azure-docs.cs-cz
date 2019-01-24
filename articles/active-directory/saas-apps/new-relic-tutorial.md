---
title: 'Kurz: Integrace Azure Active Directory s nástrojem New Relic | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a New Relic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 6aade578aef40b6f009ed42b4798bc9cefce5793
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828328"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Kurz: Integrace Azure Active Directory s nástrojem New Relic

V tomto kurzu se dozvíte, jak integrovat nástroj společnosti New Relic s Azure Active Directory (Azure AD).

Špičkové funkce New Relicu integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k New Relic.
- Uživatele, aby automaticky získat přihlášení k špičkové funkce New Relicu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s nástrojem New Relic, potřebujete následující položky:

- Předplatné Azure AD
- Nástrojem New Relic jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat špičkové funkce New Relicu z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-new-relic-from-the-gallery"></a>Přidat špičkové funkce New Relicu z Galerie
Pokud chcete nakonfigurovat integraci New Relic do služby Azure AD, budete muset přidat špičkové funkce New Relicu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat špičkové funkce New Relicu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **špičkové funkce New Relicu**vyberte **špičkové funkce New Relicu** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Nástroj společnosti New Relic v seznamu výsledků](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s nástrojem New Relic na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v New Relic je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v New Relic je potřeba navázat.

V New Relic, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s nástrojem New Relic, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele špičkové funkce New Relicu](#create-a-new-relic-test-user)**  – Pokud chcete mít protějšek Britta Simon v New Relic, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci New Relic.

**Ke konfiguraci Azure AD jednotné přihlašování s nástrojem New Relic, proveďte následující kroky:**

1. Na webu Azure Portal na **špičkové funkce New Relicu** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

1. Na **nové Relic domény a adresy URL** části, proveďte následující kroky:

    ![Nové Relic domény a adresy URL jednotného přihlašování – informace](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – je potřeba nahradit vlastním ID účet New Relic.

    b. V **identifikátor** textového pole zadejte hodnotu: `rpm.newrelic.com`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/new-relic-tutorial/tutorial_general_400.png)

1. Na **novou konfiguraci Relic** klikněte na tlačítko **konfigurace špičkové funkce New Relicu** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Nová konfigurace Relic](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

1. V okně jiné webové prohlížeče, přihlaste se k vaší **špičkové funkce New Relicu** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení účtu**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797036.png "nastavení účtu")

1. Klikněte na tlačítko **zabezpečení a ověřování** kartu a potom klikněte na tlačítko **jednotné přihlašování** kartu.
   
    ![Jednotné přihlašování](./media/new-relic-tutorial/ic797037.png "jednotného přihlašování")

1. Na stránce dialogového okna SAML proveďte následující kroky:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. Klikněte na tlačítko **zvolit soubor** chcete nahrát svůj certifikát stažený Azure Active Directory.

   b. V **adresu URL pro vzdálené přihlášení** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
   
   c. V **odhlašovací adresa URL úvodní** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

   d. Klikněte na tlačítko **uložit moje změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/new-relic-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/new-relic-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/new-relic-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/new-relic-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-new-relic-test-user"></a>Vytvoření zkušebního uživatele New Relic

Chcete-li povolit uživatelů Azure Active Directory pro přihlášení k doplňku New Relic, musí být poskytnuty do New Relic. V případě špičkové funkce New Relicu zřizování je ruční úloha.

**Ke zřízení uživatelského účtu pro nástroj společnosti New Relic, proveďte následující kroky:**

1. Přihlaste se k vaší **špičkové funkce New Relicu** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení účtu**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797040.png "nastavení účtu")

1. V **účet** podokna na levé straně klikněte na tlačítko **Souhrn**a potom klikněte na tlačítko **přidat uživatele**.
   
    ![Nastavení účtu](./media/new-relic-tutorial/ic797041.png "nastavení účtu")

1. Na **aktivní uživatelé** dialogového okna, proveďte následující kroky:
   
    ![Aktivní uživatelé](./media/new-relic-tutorial/ic797042.png "aktivních uživatelů")
   
    a. V **e-mailu** textového pole zadejte e-mailovou adresu platného uživatele Azure Active Directory, které chcete zřídit.

    b. Jako **Role** vyberte **uživatele**.

    c. Klikněte na tlačítko **přidat tohoto uživatele**.

>[!NOTE]
>Můžete použít jakékoli další špičkové funkce New Relicu uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytuje špičkové funkce New Relicu uživatelským účtům, zřídit AAD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k New Relic.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit New Relic, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **špičkové funkce New Relicu**.

    ![Nástroj společnosti New Relic odkaz v seznamu aplikací](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici New Relic na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci New Relic.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/new-relic-tutorial/tutorial_general_01.png
[2]: ./media/new-relic-tutorial/tutorial_general_02.png
[3]: ./media/new-relic-tutorial/tutorial_general_03.png
[4]: ./media/new-relic-tutorial/tutorial_general_04.png

[100]: ./media/new-relic-tutorial/tutorial_general_100.png

[200]: ./media/new-relic-tutorial/tutorial_general_200.png
[201]: ./media/new-relic-tutorial/tutorial_general_201.png
[202]: ./media/new-relic-tutorial/tutorial_general_202.png
[203]: ./media/new-relic-tutorial/tutorial_general_203.png

