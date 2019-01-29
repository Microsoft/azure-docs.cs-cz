---
title: 'Kurz: Integrace Azure Active Directory s Adobe Captivate Prime | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: aa20e4544fcd78330c0daa15b9aa058ba80af2d5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171945"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Kurz: Integrace Azure Active Directory s Adobe Captivate Prime

V tomto kurzu se dozvíte, jak integrovat Adobe Captivate Prime s Azure Active Directory (Azure AD).

Integrací Adobe Captivate Prime s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Adobe Captivate Prime.
- Uživatele, aby automaticky získat přihlášení k Adobe Captivate Prime (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Adobe Captivate Prime, potřebujete následující položky:

- Předplatné Azure AD
- Adobe Captivate Prime jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Adobe Captivate Prime z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Přidání Adobe Captivate Prime z Galerie
Pokud chcete nakonfigurovat integraci Adobe Captivate Prime do služby Azure AD, budete muset přidat Adobe Captivate Prime z Galerie na váš seznam spravovaných aplikací SaaS.

**Adobe Captivate Prime přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Adobe Captivate Prime**vyberte **Adobe Captivate Prime** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Adobe Captivate primární v seznamu výsledků](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Adobe Captivate Prime podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve primárního Captivate Adobe je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve primárního Captivate Adobe je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe Captivate Prime, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)**  – Pokud chcete mít protějšek Britta Simon Captivate Prime Adobe, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Adobe Captivate Prime.

**Ke konfiguraci Azure AD jednotné přihlašování s Adobe Captivate Prime, proveďte následující kroky:**

1. Na webu Azure Portal na **Adobe Captivate Prime** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. Na **Adobe Captivate Prime domény a adresy URL** části, proveďte následující kroky:

    ![Adobe Captivate Prime domény a adresy URL jednotného přihlašování – informace](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://captivateprime.adobe.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL: `https://captivateprime.adobe.com/saml/SSO`

4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Přejděte na **vlastnosti** kartu, zkopírujte **adresa URL portálu User access** a vložte ho do poznámkového bloku.

    ![Odkaz uživatelského přístupu](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Ke konfiguraci jednotného přihlašování na **Adobe Captivate Prime** straně, je nutné odeslat na stažený **soubor XML s metadaty** a zkopírovaný **adresa URL portálu User access** k [Adobe Tým podpory Prime captivate](mailto:captivateprimesupport@adobe.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Vytvoření aplikace Adobe Captivate Prime testovacího uživatele

V této části vytvořte uživatele Britta Simon ve primárního Captivate Adobe. Práce s [tým podpory Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) přidat uživatele na platformě Adobe Captivate Prime. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Adobe Captivate Prime.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Adobe Captivate Prime, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Adobe Captivate Prime**.

    ![Adobe Captivate Prime odkaz v seznamu aplikací](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Adobe Captivate Prime na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Adobe Captivate Prime.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

