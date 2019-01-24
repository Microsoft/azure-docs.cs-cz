---
title: 'Kurz: Integrace Azure Active Directory s UserVoice | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: f8a30ba7eee7653e4a098df706450c6a4328cce9
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819063"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Kurz: Integrace Azure Active Directory s UserVoice

V tomto kurzu se dozvíte, jak integrovat UserVoice s Azure Active Directory (Azure AD).

UserVoice integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k službě UserVoice.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k službě UserVoice (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s UserVoice, potřebujete následující položky:

- Předplatné Azure AD
- UserVoice jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání UserVoice z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-uservoice-from-the-gallery"></a>Přidání UserVoice z Galerie
Konfigurace integrace UserVoice do služby Azure AD, budete muset přidat UserVoice z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat UserVoice z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **UserVoice**vyberte **UserVoice** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![UserVoice v seznamu výsledků](./media/uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí služby UserVoice na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve službě UserVoice. je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě UserVoice.

Ve službě UserVoice, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s UserVoice, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele UserVoice](#create-a-uservoice-test-user)**  – Pokud chcete mít protějšek Britta Simon ve službě UserVoice, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci služby UserVoice.

**Ke konfiguraci Azure AD jednotné přihlašování s UserVoice, proveďte následující kroky:**

1. Na webu Azure Portal na **UserVoice** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/uservoice-tutorial/tutorial_uservoice_samlbase.png)

1. Na **UserVoice domény a adresy URL** části, proveďte následující kroky:

    ![UserVoice domény a adresy URL jednotného přihlašování – informace](./media/uservoice-tutorial/tutorial_uservoice_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.UserVoice.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory služby UserVoice klienta](https://www.uservoice.com/) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Odkaz ke stažení certifikátu](./media/uservoice-tutorial/tutorial_uservoice_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/uservoice-tutorial/tutorial_general_400.png)

1. Na **UserVoice konfigurace** klikněte na tlačítko **nakonfigurovat UserVoice** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace služby UserVoice](./media/uservoice-tutorial/tutorial_uservoice_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu UserVoice společnosti jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a pak vyberte **webový portál** z nabídky.
   
    ![Sekce nastavení na straně aplikace](./media/uservoice-tutorial/ic777519.png "nastavení")

1. Na **webový portál** kartě **ověřování uživatelů** klikněte na tlačítko **upravit** otevřete **upravit ověření uživatele** dialogového okna stránky.
   
    ![Webový portál kartu](./media/uservoice-tutorial/ic777520.png "webový portál")

1. Na **upravit ověření uživatele** dialogového okna stránky, proveďte následující kroky:
   
    ![Upravit ověření uživatele](./media/uservoice-tutorial/ic777521.png "ověřování uživatelů pro úpravy")
   
    a. Klikněte na tlačítko **jednotné přihlašování (SSO)**.
 
    b. Vložit **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal do **jednotného přihlašování vzdáleného přihlášení** textového pole.

    c. Vložit **odhlašování URL** hodnotu, kterou jste zkopírovali z portálu Azure portal do **jednotné odhlašování vzdálené textbox**.
 
    d. Vložit **kryptografický otisk** hodnotu, kterou jste zkopírovali z portálu Azure portal do **otisk aktuálního certifikátu SHA1** textového pole.
    
    e. Klikněte na tlačítko **uložit nastavení ověřování**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/uservoice-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/uservoice-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/uservoice-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/uservoice-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-uservoice-test-user"></a>Vytvoření zkušebního uživatele UserVoice

Pokud chcete povolit Azure AD uživatelům přihlášení k službě UserVoice, musí být poskytnuty do služby UserVoice. V případě UserVoice zřizování se ruční úlohy.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:
1. Přihlaste se k vaší **UserVoice** tenanta.

1. Přejděte na **nastavení**.
   
    ![Nastavení](./media/uservoice-tutorial/ic777811.png "nastavení")

1. Klikněte na tlačítko **Obecné**.

1. Klikněte na tlačítko **agenty a oprávnění**.
   
    ![Agenti a oprávnění](./media/uservoice-tutorial/ic777812.png "agenty a oprávnění")

1. Klikněte na tlačítko **mohli přidávat správce**.
   
    ![Přidat správce](./media/uservoice-tutorial/ic777813.png "přidat správce")

1. Na **pozvat správci** dialogového okna, proveďte následující kroky:
   
    ![Pozvat správci](./media/uservoice-tutorial/ic777814.png "pozvat správci")
   
    a. V textovém poli e-mailů, zadejte e-mailovou adresu účtu, které chcete zřídit a potom klikněte na tlačítko **přidat**.
   
    b. Klikněte na tlačítko **pozvat**.

> [!NOTE]
> Můžete použít jakékoli jiné UserVoice uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných UserVoice uživatelským účtům, zřídit AAD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k službě UserVoice.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit k službě UserVoice Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **UserVoice**.

    ![V seznamu aplikací na odkaz UserVoice](./media/uservoice-tutorial/tutorial_uservoice_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici služby UserVoice na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci UserVoice.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/uservoice-tutorial/tutorial_general_01.png
[2]: ./media/uservoice-tutorial/tutorial_general_02.png
[3]: ./media/uservoice-tutorial/tutorial_general_03.png
[4]: ./media/uservoice-tutorial/tutorial_general_04.png

[100]: ./media/uservoice-tutorial/tutorial_general_100.png

[200]: ./media/uservoice-tutorial/tutorial_general_200.png
[201]: ./media/uservoice-tutorial/tutorial_general_201.png
[202]: ./media/uservoice-tutorial/tutorial_general_202.png
[203]: ./media/uservoice-tutorial/tutorial_general_203.png

