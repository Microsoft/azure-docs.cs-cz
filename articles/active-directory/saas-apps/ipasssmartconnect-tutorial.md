---
title: 'Kurz: Integrace Azure Active Directory s iPass SmartConnect | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a68a208496904fcc8bfe13a227c61bf313214f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198163"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Kurz: Integrace Azure Active Directory s iPass SmartConnect

V tomto kurzu se dozvíte, jak integrovat iPass SmartConnect s Azure Active Directory (Azure AD).

IPass SmartConnect integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k iPass SmartConnect.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iPass SmartConnect (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iPass SmartConnect, potřebujete následující položky:

- Předplatné Azure AD
- IPass SmartConnect jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iPass SmartConnect z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Přidání iPass SmartConnect z Galerie
Konfigurace integrace iPass SmartConnect do služby Azure AD, budete muset přidat iPass SmartConnect z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iPass SmartConnect z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **iPass SmartConnect**vyberte **iPass SmartConnect** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![iPass SmartConnect v seznamu výsledků](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s iPass SmartConnect založená na uživateli test "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v iPass SmartConnect je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iPass SmartConnect musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iPass SmartConnect, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele SmartConnect iPass](#create-an-ipass-smartconnect-test-user)**  – Pokud chcete mít protějšek Britta Simon v iPass SmartConnect, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich iPass SmartConnect aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s iPass SmartConnect, proveďte následující kroky:**

1. Na webu Azure Portal na **iPass SmartConnect** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. Na **iPass SmartConnect domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, není nutné provádět žádné kroky.

    ![iPass SmartConnect domény a adresy URL jednotného přihlašování – informace](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Zkontrolujte zobrazit pokročilé nastavení URL a proveďte následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![iPass SmartConnect domény a adresy URL jednotného přihlašování – informace](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    V textovém poli přihlašovací adresa URL zadejte adresu URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. iPass SmartConnect aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/ipasssmartconnect-tutorial/attribute.png)

1. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtávací políčko ve **atributy uživatele** rozbalte atributy. Proveďte následující kroky na všechny zobrazené atributy-

    | Název atributu | Hodnota atributu | Hodnota Namespace|
    | ---------------| --------------- |----------------|
    | Jméno | user.givenname |   |
    | Příjmení | user.surname | |
    | e-mail | user.userprincipalname | |
    | uživatelské jméno | user.userprincipalname | |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechejte prázdné. pro tento řádek hodnoty oboru názvů.

    e. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **iPass SmartConnect** straně, je nutné odeslat na stažený **soubor XML s metadaty** a **název domény** k [iPass SmartConnect tým podpory](mailto:help@ipass.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Vytvoření iPass SmartConnect testovacího uživatele

V této části vytvoříte uživatele v iPass SmartConnect jako Britta Simon. Práce s [tým podpory iPass SmartConnect](mailto:help@ipass.com) přidat uživatele nebo domény, který je nutný na seznamu povolených iPass SmartConnect platformy. Pokud je doména přidána týmem, bude získat uživatelé automaticky přiřazeni k platformě SmartConnect iPass. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iPass SmartConnect.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit iPass SmartConnect, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **iPass SmartConnect**.

    ![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

**K otestování aplikace ve službě flow iniciované SP, proveďte následující kroky:**

a. Stáhněte si windows iPass SmartConnect klienta [tady](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing3.png)

b. Instalace klienta a spuštění.

c. Klikněte na **Začínáme**.

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Zadejte jméno uživatele Azure s doménou. Klikněte na **pokračovat**. Budete přesměrováni na stránku pro přihlášení k Azure

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Po úspěšném ověření se spustí aktivace klienta. Klient bude aktivovat.

**K otestování aplikace ve službě flow zahájené pomocí IdP, proveďte následující kroky:**

a. Přihlaste se k [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Klikněte na iPass SmartConnect aplikace.

c. Spustí SSA stránky, klikněte na **stáhnout aplikaci pro Windows** instalace iPass SmartConnect klienta.

![IPass SmartConnect odkaz v seznamu aplikací](./media/ipasssmartconnect-tutorial/testing4.png)

d. Po instalaci klienta při prvním spuštění se automaticky spustí aktivaci po přijetí podmínek a ujednání.

e. Pokud se aktivace nepodaří spustit, klikněte na tlačítko aktivovat na stránce SSA zahájí aktivaci.

f. Klient bude aktivovat.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

