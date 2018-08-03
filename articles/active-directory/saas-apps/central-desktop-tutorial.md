---
title: 'Kurz: Integrace Azure Active Directory s centrální Desktop | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrální Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 82a6911c85dd1438aa8f60cb36194a2916bc91e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429042"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Kurz: Integrace Azure Active Directory s centrální Desktop

V tomto kurzu se dozvíte, jak integrovat centrální plochy s Azure Active Directory (Azure AD).

Integrace s Azure AD střed Desktop poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k ploše centrální.
- Můžete povolit uživatelům, aby automaticky získat přihlášení pro centrální Desktop pomocí svých účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s centrální Desktop, potřebujete následující položky:

- S předplatným služby Azure AD
- Předplatné centrální Desktopu jednotné přihlašování na povoleno

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Pokud ještě nemáte zkušební prostředí Azure AD, [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání centrální plochy z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-central-desktop-from-the-gallery"></a>Přidání centrální plochy z Galerie
Ke konfiguraci integrace centrální Desktopu do služby Azure AD, budete muset přidat centrální plochy z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat centrální plochy z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat nové aplikace, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **centrální Desktopu**. Vyberte **centrální Desktop** z panel výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Centrální Desktop v seznamu výsledků](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s centrální Desktopu podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo tento uživatel protějšky v centrální Desktopu je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatelské centrální plochy.

V centrální ploše zadejte **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dva uživatele.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí centrální plochy, které potřebujete k dokončení následujících stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvoření zkušebního uživatele centrální Desktopu](#create-a-central-desktop-test-user) mít protějšek Britta Simon v centrální Desktopu, který je propojený s Azure AD reprezentace uživatele.
1. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Centrální Desktop.

**Ke konfiguraci Azure AD jednotné přihlašování s centrální Desktop, proveďte následující kroky:**

1. Na webu Azure Portal na **centrální Desktop** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogu **režimu** rozevíracího seznamu vyberte **přihlašování na základě SAML**.
 
    ![Jednotné přihlašování – dialogové okno](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. V **centrální Desktopu domény a adresy URL** části, proveďte následující kroky:

    ![Centrální klasické pracovní plochy domény a adresy URL jednotného přihlašování – informace](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `https://<companyname>.centraldesktop.com`

    b. V **identifikátor** pole, zadejte adresu URL s následujícím vzorem:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat s identifikátorem skutečné odpovědi adresy URL a přihlašovací adresa URL. Obraťte se [tým podpory centrální plochy klienta](https://imeetcentral.com/contact-us) k získání těchto hodnot. 

1. V **podpisový certifikát SAML** vyberte **certifikát**. Uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. Vyberte tlačítko **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. V **centrální konfigurace plochy** vyberte **nakonfigurovat centrální Desktop** otevřít **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka** oddílu.

    ![Konfigurace centrálních klasické pracovní plochy](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. Přihlaste se k vaší **centrální Desktop** tenanta.

1. Přejděte na **nastavení**. Vyberte **Upřesnit**a pak vyberte **Single Sign On**.

    ![Instalační program – rozšířená](./media/central-desktop-tutorial/ic769563.png "instalační program – rozšířené")

1. Na **nastavení jednotného přihlašování** stránce, proveďte následující kroky:

    ![Jednotné přihlašování – nastavení](./media/central-desktop-tutorial/ic769564.png "nastavení jednotného přihlašování")
    
    a. Vyberte **povolit SAML v2 jednotného přihlašování**.
    
    b. V **adresu URL jednotného přihlašování** pole, vložte **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure portal.
    
    c. V **přihlašovací adresu URL pro jednotné přihlašování** pole, vložte **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal.
    
    d. V **jednotného přihlašování k odhlašovací adresa URL** pole, vložte **odhlašování URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

1. V **metodu ověření podpisu zpráva** části, proveďte následující kroky:

    ![Zpráva metodu ověření podpisu](./media/central-desktop-tutorial/ic769565.png "metodu ověření podpisu zpráva") . Vyberte **Certifikát**.
    
    b. V **certifikát jednotného přihlašování** seznamu vyberte **RSH SHA256**.
    
    c. Otevřete stažený certifikát v poznámkovém bloku. Potom zkopírujte obsah certifikát a vložte ho do **certifikát jednotného přihlašování** pole.
        
    d. Vyberte **zobrazení odkazu na vaší přihlašovací stránce SAMLv2**.
    
    e. Vyberte **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com) při nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a potom přejdete na vložené dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace na [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/central-desktop-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**. Potom vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/central-desktop-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/central-desktop-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-central-desktop-test-user"></a>Vytvoření zkušebního uživatele centrální Desktop

Pro uživatele Azure AD bude moct přihlásit musí být poskytnuty v aplikace centrální klasické pracovní plochy. Tato část popisuje, jak vytvořit uživatelské účty služby Azure AD v centrální Desktopu.

> [!NOTE]
> Ke zřízení uživatelských účtů služby Azure AD, můžete použít jiné nástroje pro tvorbu centrální plochy uživatele účtu nebo rozhraní API, která jsou k dispozici v centrální Desktop.

**Ke zřízení uživatelských účtů do centrální Desktopu:**

1. Přihlaste se k vašemu tenantovi centrální Desktop.

1. Přejděte na **lidé** > **vnitřní členy**.

1. Vyberte **přidat interní členy**.

    ![Lidé](./media/central-desktop-tutorial/ic781051.png "osoby")
    
1. V **e-mailovou adresu nové členy** zadejte účet služby Azure AD, které chcete zřídit a pak vyberte **Další**.

    ![E-mailové adresy nových členů](./media/central-desktop-tutorial/ic781052.png "e-mailové adresy nové členy")

1. Vyberte **přidejte interní členy**.

    ![Přidat vnitřní člena](./media/central-desktop-tutorial/ic781053.png "interní přidat člena")
   
   >[!NOTE]
   >Uživatelé, které přidáte dostanou e-mail, který obsahuje odkaz potvrzení pro aktivaci svých účtů.
   
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure tak, že jim udělíte přístup k ploše centrální.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon centrální plochu, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace. Přejděte do zobrazení adresáře a potom přejděte ke **podnikové aplikace**.

1. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **centrální Desktopu**.

    ![Centrální Desktopu odkaz v seznamu aplikací](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu.

1. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko.

1. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici centrální plochy na přístupovém panelu, můžete automaticky získat přihlášení k vaší aplikace centrální klasické pracovní plochy.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

