---
title: 'Kurz: Integrace Azure Active Directory s věrností NetBenefits | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetBenefits přesnosti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: d11164fafa3c05c8c61c352f4d6be6607fa52ebb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425251"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Kurz: Integrace Azure Active Directory s věrností NetBenefits

V tomto kurzu se dozvíte, jak integrovat věrnost NetBenefits s Azure Active Directory (Azure AD).

Věrnost NetBenefits integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k NetBenefits přesnosti.
- Uživatele, aby automaticky získat přihlášení k NetBenefits věrnost (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s věrností NetBenefits, potřebujete následující položky:

- S předplatným služby Azure AD
- Věrnost NetBenefits jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání věrnost NetBenefits z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Přidání věrnost NetBenefits z Galerie
Ke konfiguraci integrace věrnost NetBenefits do služby Azure AD, budete muset přidat z Galerie NetBenefits věrností na váš seznam spravovaných aplikací SaaS.

**Věrnost NetBenefits přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **věrnost NetBenefits**vyberte **věrnost NetBenefits** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Věrnost NetBenefits v seznamu výsledků](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí NetBenefits věrností na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v věrnost NetBenefits je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v věrnost NetBenefits potřeba navázat.

V věrnost NetBenefits **uživatele** mapování by mělo být provedeno s **uživatele Azure AD** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s věrností NetBenefits, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele věrnost NetBenefits](#create-a-fidelity-netbenefits-test-user)**  – Pokud chcete mít protějšek Britta Simon věrnost NetBenefits, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci NetBenefits přesnosti.

**Ke konfiguraci Azure AD jednotné přihlašování s věrností NetBenefits, proveďte následující kroky:**

1. Na webu Azure Portal na **věrnost NetBenefits** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

1. Na **věrnost NetBenefits domény a adresy URL** části, proveďte následující kroky:

    ![Věrnost NetBenefits domény a adresy URL jednotného přihlašování – informace](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL:

    Pro testování prostředí:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Pro produkční prostředí:  `urn:sp:fidelity:geninbndnbparts20`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL:

    Pro testování prostředí:  `https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    Pro produkční prostředí:  `https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
1. Věrnost NetBenefits aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Jsme změnili **identifikátor uživatele** s **user.userprincipalname**. Můžete ho namapovat s **employeeid** nebo jiné deklarace, které lze použít pro vaši organizaci jako **identifikátor uživatele**. Následující snímek obrazovky ukazuje uvedené jenom jako příklad pro tuto.

    ![Atribut NetBenefits věrnost](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Věrnost NetBenefits podporují statické a dynamické federace. Statické znamená, že jej nebude používat protokol SAML podle metody just-in čas uživatele zřizování a dynamické znamená, že se podporuje jenom v době zřizování uživatelů. Pomocí JIT na základě zřizování zákazníků muset přidat některé další deklarace identity v Azure AD, jako jsou datum narození uživatele atd. Tyto informace jsou poskytovány [tým podpory věrnost NetBenefits](mailto:SSOMaintenance@fmr.com) a mají k povolení této dynamické federace pro vaši instanci.
    
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/fidelitynetbenefits-tutorial/tutorial_general_400.png)

1. Na **věrnost NetBenefits konfigurace** klikněte na tlačítko **konfigurace NetBenefits věrnost** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Věrnost NetBenefits konfigurace](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **věrnost NetBenefits** straně, je nutné odeslat na stažený **soubor XML s metadaty**, **SAML jednotné přihlašování – adresa URL služby** a  **SAML Entity ID** k [tým podpory věrnost NetBenefits](mailto:SSOMaintenance@fmr.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/fidelitynetbenefits-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/fidelitynetbenefits-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/fidelitynetbenefits-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Vytvoření zkušebního uživatele NetBenefits věrnost

V této části vytvořte uživatele Britta Simon v NetBenefits přesnosti. Pokud vytváříte statické federace, prosím práci s [věrnost NetBenefits tým podpory](mailto:SSOMaintenance@fmr.com) vytvořit uživatele v věrnost NetBenefits platformy. Tito uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

Pro dynamické federace uživatelé se vytvoří JIT zřizování uživatelů. Pomocí JIT na základě zřizování zákazníků muset přidat některé další deklarace identity v Azure AD, jako jsou datum narození uživatele atd. Tyto informace jsou poskytovány [tým podpory věrnost NetBenefits](mailto:SSOMaintenance@fmr.com) a mají k povolení této dynamické federace pro vaši instanci.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k NetBenefits přesnosti.

![Přiřazení role uživatele][200] 

**Přiřadit přesné NetBenefits Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **věrnost NetBenefits**.

    ![Věrnost NetBenefits odkaz v seznamu aplikací](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici NetBenefits věrností na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci NetBenefits věrnost.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/fidelitynetbenefits-tutorial/tutorial_general_203.png

