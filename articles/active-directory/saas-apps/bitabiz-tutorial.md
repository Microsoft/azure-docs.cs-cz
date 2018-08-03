---
title: 'Kurz: Integrace Azure Active Directory se službou BitaBIZ | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 2a05a4f1b9162a69e074bf6243236df48c8ce536
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429658"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Kurz: Integrace Azure Active Directory se službou BitaBIZ

V tomto kurzu se dozvíte, jak integrovat BitaBIZ s Azure Active Directory (Azure AD).

BitaBIZ integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k BitaBIZ.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k BitaBIZ (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s BitaBIZ, potřebujete následující položky:

- S předplatným služby Azure AD
- BitaBIZ jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání BitaBIZ z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bitabiz-from-the-gallery"></a>Přidání BitaBIZ z Galerie
Konfigurace integrace BitaBIZ do služby Azure AD, budete muset přidat BitaBIZ z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BitaBIZ z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **BitaBIZ**vyberte **BitaBIZ** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![BitaBIZ v seznamu výsledků](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí BitaBIZ podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v BitaBIZ je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v BitaBIZ potřeba navázat.

V BitaBIZ, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s BitaBIZ, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele BitaBIZ](#create-a-bitabiz-test-user)**  – Pokud chcete mít protějšek Britta Simon BitaBIZ, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci BitaBIZ.

**Ke konfiguraci Azure AD jednotné přihlašování s BitaBIZ, proveďte následující kroky:**

1. Na webu Azure Portal na **BitaBIZ** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

1. Na **BitaBIZ domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![BitaBIZ domény a adresy URL jednotného přihlašování – informace](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Hodnota v výše zobrazenou adresu URL je pouze ukázku. Aktualizujte hodnotu s skutečné identifikátor, který je vysvětlen později v tomto kurzu.

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![BitaBIZ domény a adresy URL jednotného přihlašování – informace](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://www.bitabiz.com/dashboard`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/bitabiz-tutorial/tutorial_general_400.png)
    
1. Na **BitaBIZ konfigurace** klikněte na tlačítko **nakonfigurovat BitaBIZ** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi BitaBIZ jako správce.

1. Klikněte na **nastavení správce**.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings1.png)

1. Klikněte na **integrace Microsoft** pod **vyšší zhodnocení** oddílu.

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings2.png)

1. Přejděte dolů do části **Microsoft Azure AD (povolit jednotné přihlašování)** a proveďte následující kroky:

    ![Konfigurace BitaBIZ](./media/bitabiz-tutorial/settings3.png)

    a. Zkopírujte hodnotu z **ID Entity ("identifikátor" ve službě Azure AD)** textového pole a vložte ho do **identifikátor** textového pole na **BitaBIZ domény a adresy URL** části webu Azure Portal. 
    
    b. V **Azure AD jednotné přihlašování – adresa URL služby** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
    
    c. V **ID Entity Azure AD SAML** vložit do textového pole **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    d. Otevřete váš stažené **Certificate(Base64)** v poznámkovém bloku soubor, zkopírujte obsah ho do schránky a vložte ho do **Azure AD podpisový certifikát (základ64 kódovaný)** textového pole.

    e. Přidání e-mailové domény firmy název tedy mycompany.com v **název domény** textové pole, které chcete přiřadit uživatelům ve vaší společnosti pomocí tohoto e-mailovou doménu jednotné přihlašování (nepovinné).
    
    f. Označit **povoleno jednotné přihlašování** BitaBIZ účtu.
    
    g. Klikněte na tlačítko **uložit konfiguraci Azure AD** uložit a aktivovat Konfigurace jednotného přihlašování.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/bitabiz-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/bitabiz-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/bitabiz-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-bitabiz-test-user"></a>Vytvoření zkušebního uživatele BitaBIZ

Přihlaste se k BitaBIZ Azure AD uživatelům umožnit, musí být poskytnuty do BitaBIZ.  
V případě BitaBIZ zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti BitaBIZ jako správce.

1. Klikněte na **nastavení správce**.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/settings1.png)

1. Klikněte na **přidat uživatele** pod **organizace** oddílu.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user1.png)

1. Klikněte na tlačítko **přidat nového zaměstnance**.

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user2.png)

1. Na **"Přidat nového zaměstnance"** dialogového okna stránky, proveďte následující kroky:

    ![BitaBIZ přidat uživatele](./media/bitabiz-tutorial/user3.png)

    a. V **křestní jméno** textového pole Název typu prvního uživatele, jako je Britta.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.

    c. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    d. Vyberte datum v **datum zaměstnání**.

    e. Existují ostatní atributy uživatele nepovinné, které lze nastavit pro daného uživatele. Najdete [zaměstnance nastavení Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) další podrobnosti.    
    
    f. Klikněte na tlačítko **uložit zaměstnance**.
    
    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.
    
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k BitaBIZ použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit BitaBIZ Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **BitaBIZ**.

    ![Odkaz BitaBIZ v seznamu aplikací](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici BitaBIZ na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci BitaBIZ.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

