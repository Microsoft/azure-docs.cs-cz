---
title: 'Kurz: Integrace Azure Active Directory s účastí Management Services | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a služby správy měřítka.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: bcbb3d5d25fdc3de71045cedfd4634e47b3947bf
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811950"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Kurz: Integrace Azure Active Directory službami pro správu měřítka

V tomto kurzu se dozvíte, jak integrovat účast správy služby Azure Active Directory (Azure AD).

Integrace služby správy účast s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k účasti Management Services.
- Uživatele, aby automaticky získat přihlášení k účasti Management Services (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s účastí Management Services, potřebujete následující položky:

- Předplatné Azure AD
- Účasti Management Services jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání služby pro účast v galerii
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-attendance-management-services-from-the-gallery"></a>Přidání služby pro účast v galerii
Pokud chcete nakonfigurovat integraci služeb účast správy do služby Azure AD, musíte na přidání služeb správy účast v galerii na váš seznam spravovaných aplikací SaaS.

**Přidání služby pro účast z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **účast Management Services**vyberte **účast Management Services** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Služby pro účast v seznamu výsledků](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí služby správy účast na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek v účasti Management Services je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v účasti Management Services je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s účastí Management Services, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **Vytvořit testovacího uživatele účasti Management Services** – Pokud chcete mít protějšek Britta Simon v účasti Management Services, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci služby pro účast.

**Ke konfiguraci Azure AD jednotné přihlašování s účastí Management Services, postupujte následovně:**

1. Na webu Azure Portal na **účast Management Services** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

1. Na **účast správy služeb domény a adresy URL** části, proveďte následující kroky:

    ![Účast na správu služeb domény a adresy URL jednotného přihlašování – informace](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://id.obc.jp/<tenant information >/`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory účast Management Services Client](https://www.obcnet.jp/) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

1. Na **konfigurace služby správy účast** klikněte na tlačítko **konfigurace služby pro účast** otevřete **nakonfigurovat přihlašování** okno. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace služby správy měřítka](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

1. V jiném okně prohlížeče přihlašování k webu služby správy účast společnosti jako správce.

1. Klikněte na **ověřování SAML** pod **části Správa zabezpečení**.

    ![Konfigurace služby správy měřítka](./media/attendancemanagementservices-tutorial/user1.png)

1. Proveďte následující kroky:

    ![Konfigurace služby správy měřítka](./media/attendancemanagementservices-tutorial/user2.png)

    a. Vyberte **ověřování pomocí SAML**.

    b. V **identifikátor** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 

    c. V **adresu URL koncového bodu ověřování** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **vyberte soubor** se nahrát certifikát, který jste si stáhli z Azure AD.

    e. Vyberte **zakažte ověřování hesla**.

    f. Klikněte na tlačítko **registrace**

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace! Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Vytvořit testovacího uživatele účasti Management Services

Povolit uživatele Azure AD pro přihlášení k účasti Management Services, musí být poskytnuty do služby správy účast. V případě služby pro účast zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Připojte se k webu služby správy účast společnosti jako správce.

1. Klikněte na **Správa uživatelů** pod **části Správa zabezpečení**.

    ![Přidat zaměstnance](./media/attendancemanagementservices-tutorial/user5.png)

1. Klikněte na tlačítko **nového přihlašovacího jména pravidla**.

    ![Přidat zaměstnance](./media/attendancemanagementservices-tutorial/user3.png)

1. V **OBCiD informace** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/attendancemanagementservices-tutorial/user4.png)

    a. V **OBCiD** , jako je textové pole, typ e-mailu uživatele **BrittaSimon@contoso.com**.

    b. V **heslo** textového pole zadejte heslo uživatele.

    c. Klikněte na tlačítko **registrace**


### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k účasti Management Services.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon účast Management Services, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **účast Management Services**.

    ![Propojení služby pro účast v seznamu aplikací](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici služby pro účast na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci služby pro účast.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png

