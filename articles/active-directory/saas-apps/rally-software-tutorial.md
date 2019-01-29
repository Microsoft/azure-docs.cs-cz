---
title: 'Kurz: Integrace Azure Active Directory pomocí technologie Rally softwaru | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a technologie Rally softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 5898f084ea3de4e0f947283100bae55d4499d382
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55166879"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Kurz: Integrace Azure Active Directory pomocí technologie Rally softwaru

V tomto kurzu se dozvíte, jak integrovat technologie Rally softwaru se službou Azure Active Directory (Azure AD).

Integrace technologie Rally Software s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k technologie Rally softwaru.
- Uživatele, aby automaticky získat přihlášení k technologie Rally softwaru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí technologie Rally softwaru, potřebujete následující položky:

- Předplatné Azure AD
- Technologie Rally jednotné přihlašování povoleno předplatné softwaru

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání technologie Rally softwaru z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rally-software-from-the-gallery"></a>Přidání technologie Rally softwaru z Galerie
Konfigurace integrace technologie Rally softwaru do služby Azure AD, budete muset přidat technologie Rally Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat technologie Rally softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **technologie Rally softwaru**vyberte **technologie Rally softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Technologie Rally Software v seznamu výsledků](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí technologie Rally Software založený na uživateli test "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek technologie Rally softwaru je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské technologie Rally softwaru je potřeba navázat.

V softwaru technologie Rally přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí technologie Rally softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele technologie Rally softwaru](#create-a-rally-software-test-user)**  – Pokud chcete mít protějšek Britta Simon technologie Rally Software, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci technologie Rally softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí technologie Rally softwaru, proveďte následující kroky:**

1. Na webu Azure Portal na **technologie Rally softwaru** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

1. Na **technologie Rally softwaru domény a adresy URL** části, proveďte následující kroky:

    ![Technologie Rally softwaru domény a adresy URL jednotné přihlašování – informace](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.rally.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory technologie Rally softwarového klienta](https://help.rallydev.com/) k získání těchto hodnot. 
 


1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/rally-software-tutorial/tutorial_general_400.png)

1. Na **technologie Rally konfigurace softwaru** klikněte na tlačítko **konfigurace technologie Rally softwaru** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Technologie Rally konfigurace softwaru](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

1. Přihlaste se k vaší **technologie Rally softwaru** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a pak vyberte **předplatné**.
   
    ![Předplatné](./media/rally-software-tutorial/ic769531.png "předplatného")

1. Klikněte na tlačítko **akce** tlačítko. Vyberte **upravit předplatné** v horní pravé straně panelu nástrojů.

1. Na **předplatné** dialogového okna stránky, proveďte následující kroky a pak klikněte na **uložit a zavřít**:
   
    ![Ověřování](./media/rally-software-tutorial/ic769542.png "ověřování")
   
    a. Vyberte **technologie Rally nebo jednotného přihlašování ověřování** z rozevíracího seznamu ověřování.

    b. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 

    c. V **jednotné odhlašování** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/rally-software-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/rally-software-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/rally-software-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/rally-software-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-rally-software-test-user"></a>Vytvoření zkušebního uživatele technologie Rally softwaru

Pro uživatele Azure AD bude moct přihlásit musí být poskytnuty k technologie Rally softwarová aplikace pomocí jejich uživatelská jména Azure Active Directory.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vašemu tenantovi technologie Rally softwaru.

1. Přejděte na **nastavení \> uživatelé**a potom klikněte na tlačítko **+ přidat nový**.
   
    ![Uživatelé](./media/rally-software-tutorial/ic781039.png "uživatelů")

1. Zadejte název do textového pole nového uživatele a potom klikněte na tlačítko **přidat s podrobnostmi**.

1. V **vytvořit uživatele** části, proveďte následující kroky:
   
    ![Vytvoření uživatele](./media/rally-software-tutorial/ic781040.png "vytvoření uživatele")

    a. V **uživatelské jméno** textového pole, zadejte jméno uživatele, jako jsou **Brittsimon**.
   
    b. V **e-mailovou adresu** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    c. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    d. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **Simon**.

    e. Klikněte na tlačítko **uložit a zavřít**.

   >[!NOTE]
   >Další nástroje pro tvorbu technologie Rally softwaru uživatelského účtu nebo rozhraní API poskytovaných technologie Rally softwaru můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k technologie Rally softwaru.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit technologie Rally softwaru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **technologie Rally softwaru**.

    ![Technologie Rally softwaru odkaz v seznamu aplikací](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici technologie Rally softwaru na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci technologie Rally softwaru.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png

