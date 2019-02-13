---
title: 'Kurz: Integrace Azure Active Directory s E Sales Manager Remix | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164145"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrace Azure Active Directory s prodejní manažer Remix E

V tomto kurzu se dozvíte, jak integrovat E Sales Manager Remix Azure Active Directory (Azure AD).

Díky integraci služby Azure AD s E Sales Manager Remix, získáte následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k E Sales Manager Remix.
- Můžete povolit uživatelům získat přihlášení automaticky na elektronické Sales Manager Remix (jednotné přihlašování a jednotné přihlašování) pomocí svých účtů služby Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s E Sales Manager Remix, potřebujete následující položky:

- Předplatné Azure AD
- Předplatné podporující E Sales Manager Remix SSO

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme, abyste udělali *není* použijte produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání E Sales Manager Remix z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Přidat E Sales Manager Remix z Galerie
Konfigurace integrace Azure AD s E Sales Manager Remix, přidejte E Sales Manager Remix z Galerie na váš seznam spravovaných aplikací SaaS následujícím způsobem:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V okně "Podnikové aplikace"][2]
    
1. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **E Sales Manager Remix**vyberte **E Sales Manager Remix** v seznamu výsledků a pak vyberte **přidat**.

    ![Elektronické Sales Manager Remix v seznamu výsledků](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s E Sales Manager Remix, na základě testovací uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje k identifikaci E Sales Manager Remix uživatele a jeho protějšek ve službě Azure AD. Jinými slovy musí vytvořit vztah odkazu mezi uživatele služby Azure AD a že jeden uživatel E Sales Manager Remix.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s E Sales Manager Remix, proveďte stavební bloky v příštích pěti oddílů:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci E Sales Manager Remix následujícím způsobem:

1. Na webu Azure Portal na **E Sales Manager Remix** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Odkaz "Jednotné přihlašování"][4]

1. V **jednotného přihlašování** okno v **režim jednotného přihlašování** vyberte **přihlašování na základě SAML**.
 
    ![V okně "Jednotné přihlašování"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. V části **E Sales Manager Remix domény a adresy URL**, postupujte takto:

    ![Elektronické Sales Manager Remix domény a adresy URL jednotného přihlašování – informace](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL v následujícím formátu: *https://\<– na základě – adresa URL serveru > /\<subdomény > / esales-pc*.

    b. V **identifikátor** pole, zadejte adresu URL v následujícím formátu: *https://\<– na základě – adresa URL serveru > /\<subdomény > /*.

    c. Poznámka: **identifikátor** hodnoty pro pozdější použití v tomto kurzu.
    
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečný. Aktualizujte je s skutečné přihlašovací adresu URL a identifikátorem. K získání hodnot, kontaktujte [tým podpory E Sales Manager Remix klienta](mailto:esupport@softbrain.co.jp).

1. V části **podpisový certifikát SAML**vyberte **certifikát (Base64)** a poté uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikát (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Vyberte **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtněte políčko a potom vyberte **emailaddress** atribut.
    
    ![V okně atributy uživatele](./media/esalesmanagerremix-tutorial/configure1.png)

    **Upravit atribut** otevře se okno.

1. Kopírovat **Namespace** a **název** hodnoty. Generování hodnoty ve vzoru  *\<Namespace > /\<Name >* a uložte jej pro pozdější použití v tomto kurzu.

    ![V okně Upravit atribut](./media/esalesmanagerremix-tutorial/configure2.png)

1. V části **E Sales Manager Remix konfigurace**vyberte **konfigurace Remix Sales Manager se E**.

    ![Konfigurace Remix Prodejní manažer E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **Nakonfigurovat přihlašování** otevře se okno.

1. V **Stručná referenční příručka** tématu, zkopírujte je odhlašovací adresa URL a adresy URL služby jednotného přihlašování SAML.

1. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Přihlaste se k aplikaci E Sales Manager Remix jako správce.

1. V pravém horním rohu, vyberte **nabídky správce**.

    ![Příkaz "Do nabídky Správce"](./media/esalesmanagerremix-tutorial/configure4.png)

1. V levém podokně vyberte **nastavení systému** > **spolupráci s externím systémem**.

    !["Nastavení systému" a "Spolupráci s externím systémem" odkazy](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. V **spolupráci s externím systémem** okně **SAML**.

    ![V okně "Spolupráci s externím systémem"](./media/esalesmanagerremix-tutorial/configure6.png)

1. V části **nastavení ověřování SAML**, postupujte takto:

    ![V části "Nastavení ověřování SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Vyberte **PC verze** zaškrtávací políčko.
    
    b. V **spolupráci položky** části, v rozevíracím seznamu vyberte **e-mailu**.

    c. V **spolupráci položky** vložte hodnotu deklarace identity, který jste si zkopírovali z portálu Azure portal (to znamená **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. V **vystavitele (entity ID)** vložte hodnotu identifikátoru, který jste si zkopírovali z **E Sales Manager Remix domény a adresy URL** části webu Azure portal.

    e. Pokud chcete nahrát svůj certifikát stažený z webu Azure portal, vyberte **File selection**.

    f. V **přihlašovací adresa URL zprostředkovatele ID** pole, vložte adresu URL služby jednotného přihlašování SAML, který jste zkopírovali dříve na portálu Azure portal.

    g. V **odhlašovací adresa URL zprostředkovatele Identity** okně vložte odhlašování hodnotu adresy URL, který jste zkopírovali dříve na portálu Azure portal.

    h. Vyberte **nastavení dokončení**.

> [!TIP]
> Jak při nastavování aplikace, můžete si přečíst stručné verzi podle předchozích pokynů v [webu Azure portal](https://portal.azure.com). Po přidání aplikace ve **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a potom přejdete dokumentace ke službě v Embedded **konfigurace** oblast v dolní části. Další informace o funkci vložený dokumentaci najdete v tématu [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal následujícím způsobem:

![Vytvořit testovacího uživatele Azure AD][100]

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**.

    ![Propojení služby Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam aktuálních uživatelů, vyberte **uživatelů a skupin** > **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. V horní části **všichni uživatelé** okně **přidat**.

    ![Tlačítko Přidat](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Uživatele** otevře se okno.

1. V **uživatele** okno, postupujte takto:

    ![V okně uživatele](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a potom si poznamenejte hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Vytvořit testovacího uživatele E Sales Manager Remix

1. Přihlaste se k aplikaci E Sales Manager Remix jako správce.

1. Vyberte **nabídky správce** v nabídce v pravé horní části.

    ![Konfigurace Remix Prodejní manažer E](./media/esalesmanagerremix-tutorial/configure4.png)

1. Vyberte **nastavení vaší společnosti** > **údržbu oddělení a zaměstnanci**a pak vyberte **zaměstnanci zaregistrovaný**.

    ![Na kartě "Zaměstnanci registrováno"](./media/esalesmanagerremix-tutorial/user1.png)

1. V **registrace nové zaměstnance** části, postupujte takto:
    
    !["Registrace nové zaměstnance" oddílu](./media/esalesmanagerremix-tutorial/user2.png)

    a. V **jméno zaměstnance** zadejte název uživatele (například **Britta**).

    b. Vyplňte zbývající požadovaná pole.
    
    c. Pokud povolíte SAML, správce nemůže přihlásit z přihlašovací stránky. Udělení oprávnění správce při přihlášení uživatele tak, že vyberete **přihlašovací jméno správce** zaškrtávací políčko.

    d. Vyberte **registrace**.

1. V budoucnu, přihlásit se jako správce, přihlaste se jako uživatel, který má oprávnění správce a pak v pravém horním rohu, vyberte **nabídky správce**.

    ![Příkaz "Do nabídky Správce"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon k udělení přístupu k E Sales Manager Remix použití Azure jednotného přihlašování. Chcete-li to provést, postupujte takto: 

![Přiřazení role uživatele][200] 

1. Na webu Azure Portal, otevřete **aplikací** zobrazení, přejděte na **Directory** zobrazit a pak vyberte **podnikové aplikace** > **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

1. V **aplikací** seznamu vyberte **E Sales Manager Remix**.

    ![Odkaz E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** okno v **uživatelé** seznamu vyberte **Britta Simon**.

1. Vyberte **vyberte** tlačítko.

1. V **přidat přiřazení** okně **přiřadit**.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici E Sales Manager Remix na přístupovém panelu, by měl být přihlásíte automaticky E Sales Manager Remix aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS se službou Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

