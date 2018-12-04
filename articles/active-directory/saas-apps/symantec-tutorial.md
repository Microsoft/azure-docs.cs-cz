---
title: 'Kurz: Integrace Azure Active Directory s Symantec webové zabezpečení služby (WSS) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Symantec webové zabezpečení služby (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: b933bc5f5ecb39c3462e4e9bd300f1e07fd718c0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838767"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Kurz: Integrace Azure Active Directory s Symantec webové zabezpečení služby (WSS)

V tomto kurzu se dozvíte, jak integrovat účtu Symantec webové zabezpečení služby (WSS) pomocí svého účtu Azure Active Directory (Azure AD), tak, aby WSS může ověřovat koncový uživatel zřízenou ve službě Azure AD pomocí ověřování SAML a vynutit uživatele nebo pravidla úrovně zásad skupiny.

Integrace Symantec webové zabezpečení služby (WSS) s Azure AD poskytuje následující výhody:

- Spravujte všechny koncoví uživatelé a skupiny používané vaším účtem WSS z portálu Azure AD. 

- Povolit koncovým uživatelům sami ověření v WSS pomocí svých přihlašovacích údajů Azure AD.

- Povolit vynucení uživatele a skupiny zásadou na úrovni pravidel definovaných ve vašem účtu služby WSS.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Symantec webové zabezpečení služby (WSS), potřebujete následující položky:

- Předplatné Azure AD
- S účtem Symantec webové zabezpečení služby (WSS)

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití WSS účtu, který se aktuálně používá pro produkční účely.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte svůj WSS účet, který se aktuálně používá pro produkční účely tento test Pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu nakonfigurujete služby Azure AD umožňuje jednotné přihlašování do služby WSS pomocí přihlašovacích údajů koncového uživatele definované ve vašem účtu Azure AD.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidává se aplikace Symantec webové zabezpečení služby (WSS) z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Přidání Symantec webové zabezpečení služby (WSS) z Galerie
Konfigurace integrace nástroje Symantec webové zabezpečení služby (WSS) do služby Azure AD, budete muset přidat Symantec webové zabezpečení služby (WSS) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Symantec webové zabezpečení služby (WSS) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Symantec webové zabezpečení služby (WSS)** vyberte **Symantec webové zabezpečení služby (WSS)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Symantec webové zabezpečení služby (WSS) v seznamu výsledků](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (služby WSS) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Symantec webové zabezpečení služby (WSS) je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů ve společnosti Symantec webové zabezpečení služby (WSS) je potřeba navázat.

Ve společnosti Symantec webové zabezpečení služby (WSS) přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Symantec webové zabezpečení služby (WSS)](#create-a-symantec-web-security-service-wss-test-user)**  – Pokud chcete mít protějšek Britta Simon v Symantec webové zabezpečení služby (služby WSS), který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Symantec webové zabezpečení služby (WSS).

**Ke konfiguraci Azure AD jednotné přihlašování s Symantec webové zabezpečení služby (WSS), proveďte následující kroky:**

1. Na webu Azure Portal na **Symantec webové zabezpečení služby (WSS)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

1. Na **Symantec webové zabezpečení služby (WSS) domény a adresy URL** části, proveďte následující kroky:

    ![Symantec webové zabezpečení služby (WSS) domény a adresy URL jednotného přihlašování – informace](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Obraťte se prosím [tým podpory Symantec webové zabezpečení služby (WSS) klienta](https://www.symantec.com/contact-us) Pokud hodnoty **identifikátor** a **adresy URL odpovědi** nefungují z nějakého důvodu.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/symantec-tutorial/tutorial_general_400.png)
    
1. Pokud chcete nakonfigurovat jednotné přihlašování na straně Symantec webové zabezpečení služby (WSS), najdete v dokumentaci online WSS. Na stažený **soubor XML s metadaty** bude nutné importovat do portálu WSS soubor. Obraťte se [Symantec webové zabezpečení služby (WSS) tým podpory](https://www.symantec.com/contact-us) Pokud potřebujete pomoc s konfigurací na portálu služby WSS.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/symantec-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/symantec-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/symantec-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/symantec-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Vytvoření zkušebního uživatele Symantec webové zabezpečení služby (WSS)

V této části vytvoříte uživateli Britta Simon v Symantec webové zabezpečení služby (WSS). Odpovídající koncové uživatelské jméno je ručně vytvořit na portálu služby WSS nebo můžete počkat, uživatele nebo skupiny zřízenou ve službě Azure AD a budou synchronizovat na portálu služby WSS za pár minut (přibližně 15 minut). Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. Veřejnou IP adresu počítače koncového uživatele, který se použije k procházení websites je také potřeba ho zřídit na portálu společnosti Symantec webové zabezpečení služby (WSS).

> [!NOTE]
> Prosím [kliknutím sem](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) získat váš počítač je veřejná IP adresa.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Symantec webové zabezpečení služby (WSS).

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Symantec webové zabezpečení služby (WSS), postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Symantec webové zabezpečení služby (WSS)**.

    ![Odkaz Symantec webové zabezpečení služby (WSS) v seznamu aplikací](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části budete testovat funkce přihlašování – teď, když jste nakonfigurovali WSS účet pro použití služby Azure AD pro ověřování SAML.

Po nakonfigurování ve webovém prohlížeči proxy provoz na WSS, když otevřete webový prohlížeč a zkuste přejít na web potom budete přesměrováni na stránku Azure přihlašování. Zadání přihlašovacích údajů koncového uživatele testu, které se zřizují ve službě Azure AD (BrittaSimon) a přidružené heslo. Po ověření, budete moct přejít na web, který jste zvolili. Můžete vytvořit pravidlo zásad na straně služby WSS znemožní BrittaSimon procházení určité lokalitě, pak by měl naleznete na stránce bloku WSS, při pokusu o procházení k dané lokalitě jako uživatel BrittaSimon.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/symantec-tutorial/tutorial_general_01.png
[2]: ./media/symantec-tutorial/tutorial_general_02.png
[3]: ./media/symantec-tutorial/tutorial_general_03.png
[4]: ./media/symantec-tutorial/tutorial_general_04.png

[100]: ./media/symantec-tutorial/tutorial_general_100.png

[200]: ./media/symantec-tutorial/tutorial_general_200.png
[201]: ./media/symantec-tutorial/tutorial_general_201.png
[202]: ./media/symantec-tutorial/tutorial_general_202.png
[203]: ./media/symantec-tutorial/tutorial_general_203.png

