---
title: 'Kurz: Integrace služby Azure Active Directory s remixem E Sales Manager | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a remixem E Sales Manager.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65406955"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrace služby Azure Active Directory s remixem E Sales Manager

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) s E Sales Manager Remix.

Integrací Azure AD s E Sales Manager Remix, získáte následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Remix E Sales Manager.
- Uživatelům můžete povolit automatické přihlášení k Remixu E Sales Manager (jednotné přihlašování nebo jednotnépřihlašování) pomocí svých účtů Azure AD.
- Účty můžete spravovat v jednom centrálním umístění, na webu Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s E Sales Manager Remix, budete potřebovat následující položky:

- Předplatné Azure AD
- Předplatné s podporou SSO s podporou E Sales Manager

> [!NOTE]
> Při testování kroků v tomto kurzu doporučujeme *nepoužívat* produkční prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkční prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání E Sales Manager Remix z galerie
* Konfigurace a testování jednotného přihlašování azure ad

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Přidat E Sales Manager Remix z galerie
Pokud chcete nakonfigurovat integraci Azure AD s Remixem E Sales Manager, přidejte Remix E Sales Manager remix z galerie do seznamu spravovaných aplikací SaaS následujícím způsobem:

1. Na [webu Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

1. Vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Okno "Podnikové aplikace"][2]
    
1. Chcete-li přidat novou aplikaci, vyberte v horní části okna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace][3]

1. Do vyhledávacího pole zadejte **E Sales Manager Remix**, vyberte v seznamu výsledků **položku E Sales Manager Remix** a pak vyberte **Přidat**.

    ![E Sales Manager Remix v seznamu výsledků](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí remixu E Sales Manager, který je založen na testovacím uživateli s názvem "Britta Simon".

Aby jednotné přihlašování fungovalo, azure ad potřebuje identifikovat uživatele Remix E Sales Manager a jeho protějšek ve službě Azure AD. Jinými slovy vztah propojení mezi uživatelem Azure AD a stejným uživatelem v Remix e Sales Manager musí být vytvořen.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí remixu E Sales Manager, vyplňte stavební bloky v následujících pěti částech:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

Povolte jednotné přihlašování Azure AD na webu Azure Portal a nakonfigurujte jednotné přihlašování v aplikaci E Sales Manager Remix následujícím způsobem:

1. Na portálu Azure na stránce integrace aplikací **Remix e Sales Manager** vyberte **Jednotné přihlašování**.

    ![Odkaz "Jednotné přihlašování"][4]

1. V okně **Jednotné přihlašování** vyberte v poli **Režim jednotného přihlašování** **přihlašování možnost Přihlašování na saml**.
 
    ![Okno "Jednotné přihlašování"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. V části **E Sales Manager Remix Domain and URL**postupujte takto:

    ![E Sales Manager Remix Domény a adresy URL jednotné přihlašovací informace](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Do pole **Přihlašovací adresa URL** zadejte adresu URL v následujícím formátu: https:// *\<> na serveru/>/esales-pc\<*.

    b. Do pole **Identifikátor** zadejte adresu URL v následujícím formátu: *\<https://\<>/ subdoménu https:// serverová adresa URL>/ subdoména*.

    c. Všimněte si hodnoty **Identifikátor** pro pozdější použití v tomto kurzu.
    
    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Aktualizujte je skutečnou přihlašovací adresou URL a identifikátorem. Chcete-li získat hodnoty, obraťte se na [E Sales Manager Remix týmu podpory klienta](mailto:esupport@softbrain.co.jp).

1. V části **PODPISOVÝ Certifikát SAML**vyberte **certifikát (Base64)** a uložte soubor certifikátu do počítače.

    ![Odkaz ke stažení certifikátu (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Zaškrtněte políčko **Zobrazit a upravte všechny ostatní atributy uživatele** a vyberte atribut **e-mailové adresy.**
    
    ![Okno Uživatelské atributy](./media/esalesmanagerremix-tutorial/configure1.png)

    Otevře se okno **Upravit atribut.**

1. Zkopírujte hodnoty **Obor názvů** **a Název.** Vygenerujte hodnotu ve vzoru * \<Obor názvů>/\<Název>* a uložte ji pro pozdější použití v tomto kurzu.

    ![Okno Upravit atribut](./media/esalesmanagerremix-tutorial/configure2.png)

1. V části **E Sales Manager Remix Configuration**vyberte Konfigurovat **remix E Sales Manager .**

    ![Konfigurace remixu e manažera prodeje](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Otevře se okno **Konfigurovat přihlášení.**

1. V části **Stručný odkaz** zkopírujte adresu URL pro odhlášení a adresu URL služby jednotného přihlášení SAML.

1. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Přihlaste se k aplikaci E Sales Manager Remix jako správce.

1. Vpravo nahoře vyberte **Nabídku Správce**.

    ![Příkaz "Nabídka správce"](./media/esalesmanagerremix-tutorial/configure4.png)

1. V levém podokně vyberte **Nastavení** > systému Spolupráce s**externím systémem**.

    ![Odkazy "Nastavení systému" a "Spolupráce s externím systémem"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. V okně **Spolupráce s externím systémem** vyberte možnost **SAML**.

    ![Okno "Spolupráce s externím systémem"](./media/esalesmanagerremix-tutorial/configure6.png)

1. V části **nastavení ověřování SAML**postupujte takto:

    ![Oddíl Nastavení ověřování SAML](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Zaškrtněte políčko **Verze počítače.**
    
    b. V části **Položka Spolupráce** vyberte v rozevíracím seznamu **e-mail**.

    c. V poli **Položka Spolupráce** vložte hodnotu deklarace, kterou jste zkopírovali dříve z portálu Azure **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**(tj.

    d. Do pole **Vydavatel (ID entity)** vložte hodnotu identifikátoru, kterou jste zkopírovali dříve z části **E Sales Manager Remix Domain and URL** na webu Azure Portal.

    e. Pokud chcete stažený certifikát nahrát z webu Azure Portal, vyberte **Výběr souborů**.

    f. Do pole **přihlašovací adresa URL zprostředkovatele ID** vložte adresu URL služby jednotného přihlašování SAML, kterou jste zkopírovali dříve na webu Azure Portal.

    g. V poli **Adresa URL odhlášení zprostředkovatele identity** vložte hodnotu adresy URL odhlášení, kterou jste zkopírovali dříve na webu Azure Portal.

    h. Vyberte **Možnost Nastavení dokončeno**.

> [!TIP]
> Při nastavování aplikace si můžete přečíst stručnou verzi předchozích pokynů na [webu Azure Portal](https://portal.azure.com). Po přidání aplikace do části **Aplikace Active Directory** > **Enterprise Applications** vyberte kartu Jednotné **přihlašování** a pak získejte přístup k vložené dokumentaci v části **Konfigurace** v dolní části. Další informace o funkci vložené dokumentace naleznete v [tématu Integrovaná dokumentace služby Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovací uživatele Britta Simon na webu Azure portal následujícím způsobem:

![Vytvoření testovacího uživatele Azure AD][100]

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**.

    ![Propojení služby Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam aktuálních uživatelů, vyberte **možnost Uživatelé a skupiny** > **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. V horní části okna **Všichni uživatelé** vyberte **Přidat**.

    ![Tlačítko Přidat](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Otevře se okno **Uživatel.**

1. V okně **Uživatel** postupujte takto:

    ![Okno Uživatel](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Do pole **Název** zadejte **BrittaSimon**.

    b. Do pole **Uživatelské jméno** zadejte e-mailovou adresu uživatele Britty Simonové.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli **Heslo.**

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Vytvoření testovacího uživatele Remixu e Sales Managera

1. Přihlaste se k aplikaci E Sales Manager Remix jako správce.

1. V nabídce vpravo nahoře vyberte **Nabídku Správce.**

    ![Konfigurace remixu e manažera prodeje](./media/esalesmanagerremix-tutorial/configure4.png)

1. Vyberte **nastavení** > společnosti**Údržba oddělení a zaměstnanců**a pak vyberte **Zaměstnanci registrovaní**.

    ![Karta "Registrovaní zaměstnanci"](./media/esalesmanagerremix-tutorial/user1.png)

1. V části **Registrace nového zaměstnance** postupujte takto:
    
    ![Oddíl "Registrace nového zaměstnance"](./media/esalesmanagerremix-tutorial/user2.png)

    a. Do pole **Jméno zaměstnance** zadejte jméno uživatele (například **Britta**).

    b. Vyplňte zbývající povinná pole.
    
    c. Pokud povolíte SAML, správce se nemůže přihlásit ze přihlašovací stránky. Udělte uživateli oprávnění k přihlášení správce zaškrtnutím políčka **Přihlášení správce.**

    d. Vyberte **možnost Registrace**.

1. V budoucnu se chcete přihlásit jako správce, přihlásit se jako uživatel, který má oprávnění správce, a pak v pravém horním rohu vybrat **nabídku Správce**.

    ![Příkaz "Nabídka správce"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte uživateli Britta Simon používat Azure jednotné přihlášení udělením přístupu k E Sales Manager Remix. Chcete-li tak učinit, postupujte takto: 

![Přiřazení role uživatele][200] 

1. Na webu Azure Portal otevřete zobrazení **Aplikace,** přejděte do zobrazení **Adresář** a vyberte **podnikové aplikace** > **Všechny aplikace**.

    ![Odkazy "Podnikové aplikace" a "Všechny aplikace"][201] 

1. V seznamu **Aplikace** vyberte **možnost E Sales Manager Remix**.

    ![Odkaz Remix manažera prodeje E](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. V levém podokně vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"][202]

1. Vyberte **Přidat** a potom v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno Přidat přiřazení][203]

1. V okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** **Brittu Simonovou**.

1. Vyberte tlačítko **Vybrat.**

1. V okně **Přidat přiřazení** vyberte **Přiřadit**.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Remix e Sales Manager, měli byste být automaticky přihlášeni k aplikaci E Sales Manager Remix.

Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje

* [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
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

