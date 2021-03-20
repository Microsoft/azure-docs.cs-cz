---
title: 'Kurz: Azure Active Directory Integration with E Sales Manager Remix | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a E Sales Managerem Remix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995637"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrace Azure Active Directory s E-Remix manažerem E Sales

V tomto kurzu se naučíte, jak integrovat Azure Active Directory (Azure AD) s E Sales Managerem Remix.

Integrací služby Azure AD s E-Remix Sales Managerem získáte následující výhody:

- Můžete kontrolovat v Azure AD, kteří mají přístup k E Sales manažerům Remix.
- Uživatelům můžete povolit, aby se k E Sales Manageru Remix (jednotné přihlašování nebo SSO) přihlásili automaticky pomocí svých účtů Azure AD.
- Účty můžete spravovat v jednom centrálním umístění, Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s E-Remix Sales managerem, potřebujete následující položky:

- Předplatné Azure AD
- Předplatné E Sales Manageru Remix SSO – s podporou jednotného přihlašování

> [!NOTE]
> Když testujete kroky v tomto kurzu, doporučujeme *Nepoužívat produkční* prostředí.

K otestování kroků v tomto kurzu použijte tato doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu otestujete jednotné přihlašování Azure AD v testovacím prostředí. 

Scénář, který je popsaný v tomto kurzu, se skládá ze dvou hlavních stavebních bloků:

* Přidání E-Remix Sales Manageru z Galerie
* Konfigurace a testování jednotného přihlašování Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Přidat Remix E Sales Manageru z Galerie
Pokud chcete nakonfigurovat integraci Azure AD s E-Remix Sales managerem, přidejte E Sales Manager Remix z Galerie do svého seznamu spravovaných aplikací pro SaaS pomocí následujícího postupu:

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte možnost **Azure Active Directory**. 

    ![Tlačítko Azure Active Directory][1]

1. Vyberte **podnikové aplikace**  >  **všechny aplikace**.

    ![Okno podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, vyberte v horní části okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace][3]

1. Do vyhledávacího pole zadejte **e Sales Manager Remix**, v seznamu výsledků vyberte **e Sales Manager Remix** a pak vyberte **Přidat**.

    ![Remix E Sales Manageru v seznamu výsledků](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí E Sales Manageru Remix, a to na základě testovacího uživatele s názvem "Britta Simon".

Aby bylo jednotné přihlašování fungovat, musí Azure AD identifikovat uživatele Remix E Sales Manageru a jeho protějšek ve službě Azure AD. Jinými slovy se musí zřídit vztah propojení mezi uživatelem služby Azure AD a stejným uživatelem v E-Remix.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí E Sales Manageru Remix, dokončete stavební bloky v následujících pěti částech:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

Povolte jednotné přihlašování Azure AD v Azure Portal a nakonfigurujte jednotné přihlašování v aplikaci E Sales Manager Remix pomocí následujícího postupu:

1. V Azure Portal na stránce E- **Remix aplikace Insales Manageru** pro integraci aplikací vyberte **jednotné přihlašování**.

    ![Odkaz jednotného přihlašování][4]

1. V okně **jednotného přihlašování** vyberte v poli **režim jednotného přihlašování** možnost **přihlašování založené na SAML**.
 
    ![Okno jednotné přihlašování](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. V části **E Sales Manager Remix doména a adresy URL** proveďte tyto kroky:

    ![E Sales Manager Remix informace o jednotném přihlašování v doméně a adresách URL](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. Do pole **Adresa URL pro přihlášení** zadejte adresu URL v následujícím formátu: *https:// \<Server-Based-URL> / \<sub-domain> /eSales-PC*.

    b. Do pole **identifikátor** zadejte adresu URL v následujícím formátu: *https:// \<Server-Based-URL> / \<sub-domain> /*.

    c. Poznamenejte si hodnotu **identifikátoru** pro pozdější použití v tomto kurzu.
    
    > [!NOTE] 
    > Předchozí hodnoty nejsou reálné. Aktualizujte je pomocí skutečné přihlašovací adresy URL a identifikátoru. Chcete-li získat hodnoty, obraťte se na [tým podpory E Sales Manager Remix Client support](mailto:esupport@softbrain.co.jp).

1. V části **podpisový certifikát SAML** vyberte **certifikát (Base64)** a pak soubor certifikátu uložte do počítače.

    ![Odkaz na stažení certifikátu (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Zaškrtněte políčko **Zobrazit a upravit všechny ostatní atributy uživatele** a pak vyberte atribut **EmailAddress** .
    
    ![Okno atributů uživatele](./media/esalesmanagerremix-tutorial/configure1.png)

    Otevře se okno **Upravit atribut** .

1. Zkopírujte **obor názvů** a hodnoty **názvu** . Vygenerujte hodnotu ve vzoru *\<Namespace>/\<Name>* a uložte ji pro pozdější použití v tomto kurzu.

    ![Okno Upravit atribut](./media/esalesmanagerremix-tutorial/configure2.png)

1. V části **E-Remix konfigurace aplikace Sales Manager** vyberte **Konfigurovat E Sales Manager Remix**.

    ![Snímek obrazovky, který zobrazuje oddíl "E Sales Remix Configuration Manager" s vybranou možnost konfigurovat E Sales Manager Remix.](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Otevře se okno **Konfigurovat přihlašování** .

1. V části **rychlý odkaz** zkopírujte adresu URL pro odhlášení a adresu URL služby jednotného přihlašování SAML.

1. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Přihlaste se k aplikaci E Sales Manager Remix jako správce.

1. V pravém horním rohu vyberte **do nabídky Správce**.

    ![Příkaz "do nabídky Správce"](./media/esalesmanagerremix-tutorial/configure4.png)

1. V levém podokně vyberte možnost **nastavení systému**  >  **spolupráce s externím systémem**.

    ![Odkazy "Systémová nastavení" a "spolupráce s externím systémem"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. V okně **spolupráce s externím systémem** vyberte **SAML**.

    ![Okno spolupráce s externím systémem](./media/esalesmanagerremix-tutorial/configure6.png)

1. V části **nastavení ověřování SAML** udělejte toto:

    ![Oddíl nastavení ověřování SAML](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Zaškrtněte políčko **verze počítače** .
    
    b. V části **položka pro spolupráci** vyberte v rozevíracím seznamu možnost **e-mail**.

    c. Do pole **položka pro spolupráci** vložte hodnotu deklarace identity, kterou jste zkopírovali dříve z Azure Portal (tj **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .).

    d. Do pole **Vystavitel (ID entity)** vložte hodnotu identifikátoru, kterou jste zkopírovali dříve z části **E Sales Manager Remix doména a adresy URL** v Azure Portal.

    e. Pokud chcete nahrát stažený certifikát z Azure Portal, vyberte **Výběr souboru**.

    f. Do pole **Adresa URL pro přihlášení poskytovatele ID** vložte adresu URL služby jednotného přihlašování SAML, kterou jste zkopírovali dříve v Azure Portal.

    například V poli **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu URL pro odhlášení, kterou jste zkopírovali dříve v Azure Portal.

    h. Vyberte **nastavení dokončeno**.

> [!TIP]
> Při nastavování aplikace si můžete přečíst stručnou verzi předchozích pokynů v [Azure Portal](https://portal.azure.com). Po přidání aplikace v části **Active Directory**  >  **Enterprise Applications** vyberte kartu **jednotné přihlašování** a pak přejděte do vložené dokumentace v části **Konfigurace** v dolní části. Další informace o funkci integrované dokumentace najdete v [dokumentaci k Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele Britta Simon v Azure Portal následujícím způsobem:

![Vytvoření testovacího uživatele Azure AD][100]

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**.

    ![Odkaz na Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam aktuálních uživatelů, vyberte možnost **Uživatelé a skupiny**  >  **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. V horní části okna **Všichni uživatelé** vyberte **Přidat**.

    ![Tlačítko Přidat](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Otevře se okno **uživatel** .

1. V okně **uživatel** proveďte následující akce:

    ![Okno uživatele](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Do pole **název** zadejte **BrittaSimon**.

    b. Do pole **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli **heslo** .

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Vytvoření testovacího uživatele Remix E Sales Manageru

1. Přihlaste se k aplikaci E Sales Manager Remix jako správce.

1. V nabídce vpravo nahoře vyberte **nabídku správce** .

    ![Remix konfigurace E Sales Manageru](./media/esalesmanagerremix-tutorial/configure4.png)

1. Vyberte **nastavení vaší společnosti**  >  **Údržba oddělení a zaměstnanci** a pak vyberte **zaregistrované zaměstnance**.

    ![Karta registrované zaměstnanci](./media/esalesmanagerremix-tutorial/user1.png)

1. V části **Nová registrace zaměstnanců** proveďte následující kroky:
    
    ![Oddíl "nové registrace zaměstnanců"](./media/esalesmanagerremix-tutorial/user2.png)

    a. Do pole **název zaměstnance** zadejte jméno uživatele (například **Britta**).

    b. Vyplňte zbývající požadovaná pole.
    
    c. Pokud povolíte SAML, správce se nemůže přihlásit na přihlašovací stránce. Udělte uživateli oprávnění k přihlášení, a to tak, že zaškrtne políčko **přihlášení správce** .

    d. Vyberte možnost **registrace**.

1. V budoucnu se přihlaste jako správce a přihlaste se jako uživatel, který má oprávnění správce, a pak v pravém horním rohu vyberte **nabídku správce**.

    ![Příkaz "do nabídky Správce"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte, aby uživatel Britta Simon pomocí jednotného přihlašování Azure udělil přístup k E-Remix k E Sales manažerovi. Uděláte to takto: 

![Přiřazení role uživatele][200] 

1. V Azure Portal otevřete zobrazení **aplikace** , do zobrazení **adresář** a pak vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.

    ![Odkazy "podnikové aplikace" a "všechny aplikace"][201] 

1. V seznamu **aplikace** vyberte **E Sales Manager Remix**.

    ![Remix odkaz E Sales Manageru](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. V levém podokně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny][202]

1. Vyberte **Přidat** a pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Podokno přidat přiřazení][203]

1. V okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **Britta Simon**.

1. Vyberte tlačítko **Vybrat**.

1. V okně **Přidat přiřazení** vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po výběru dlaždice E Sales Manager Remix na přístupovém panelu byste měli být přihlášeni automaticky k aplikaci E Sales Manager Remix.

Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md). 

## <a name="additional-resources"></a>Další zdroje informací

* [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](tutorial-list.md)
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