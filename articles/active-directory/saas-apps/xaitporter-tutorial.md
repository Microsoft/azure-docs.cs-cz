---
title: 'Kurz: Integrace Azure Active Directory s XaitPorter | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd4f9e561a55c010273b48fb4232348baad3582
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200934"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Kurz: Integrace Azure Active Directory s XaitPorter

V tomto kurzu se dozvíte, jak integrovat XaitPorter s Azure Active Directory (Azure AD).

XaitPorter integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k XaitPorter.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k XaitPorter (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s XaitPorter, potřebujete následující položky:

- Předplatné Azure AD
- XaitPorter jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání XaitPorter z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-xaitporter-from-the-gallery"></a>Přidání XaitPorter z Galerie
Konfigurace integrace XaitPorter do služby Azure AD, budete muset přidat XaitPorter z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat XaitPorter z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **XaitPorter**vyberte **XaitPorter** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![XaitPorter v seznamu výsledků](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí XaitPorter podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v XaitPorter je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v XaitPorter potřeba navázat.

V XaitPorter, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s XaitPorter, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele XaitPorter](#create-a-xaitporter-test-user)**  – Pokud chcete mít protějšek Britta Simon XaitPorter, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci XaitPorter.

**Ke konfiguraci Azure AD jednotné přihlašování s XaitPorter, proveďte následující kroky:**

1. Na webu Azure Portal na **XaitPorter** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. Na **XaitPorter domény a adresy URL** části, proveďte následující kroky:

    ![XaitPorter domény a adresy URL jednotného přihlašování – informace](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.xaitporter.com/saml/login`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory XaitPorter klienta](https://www.xait.com/support/) k získání těchto hodnot.
     
1. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku. 

    ![Odkaz ke stažení certifikátu](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/xaitporter-tutorial/tutorial_general_400.png)

1. Zadejte **IP adresu** nebo **adresa Url federačních metadat aplikace** k [SmartRecruiters tým podpory](https://www.smartrecruiters.com/about-us/contact-us/)tak, aby XaitPorter můžete zajistit, že IP adresa je dostupný z vašeho Konfigurace seznamu povolených IP adres na své straně XaitPorter instance. 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti XaitPorter jako správce.

1. Klikněte na **správce**.

    ![Konfigurace jednotného přihlašování](./media/xaitporter-tutorial/user1.png)

1. Vyberte **spravovat Single Sign-On** z **nastavení systému** rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/xaitporter-tutorial/user2.png)

1. V **SPRAVOVAT jednotné přihlašování** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/xaitporter-tutorial/user3.png)

    a. Vyberte **povolit jednotné přihlašování, ověřování**.

    b. V **nastavení zprostředkovatele Identity** vložit do textového pole **adresa Url federačních metadat aplikace** zkopírovanou z webu Azure portal a klikněte na tlačítko **načíst**.

    c. Vyberte **povolit automatické vytvoření uživatelů**.

    d. Klikněte na **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/xaitporter-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/xaitporter-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-xaitporter-test-user"></a>Vytvoření zkušebního uživatele XaitPorter

V této části vytvoříte uživatele v XaitPorter jako Britta Simon. Práce s [tým podpory XaitPorter klienta](https://www.xait.com/support/) přidat uživatele na platformě XaitPorter. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k XaitPorter použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit XaitPorter Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **XaitPorter**.

    ![Odkaz XaitPorter v seznamu aplikací](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici XaitPorter na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci XaitPorter.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

