---
title: 'Kurz: Azure Active Directory integrace s přední | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a popředí.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 7d70a04535c769bc2fa754887d79f474bc4ced0c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214116"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Kurz: Azure Active Directory integrace s popředí

V tomto kurzu zjistěte, jak integrovat před službou Azure Active Directory (Azure AD).

Integrace přední s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do popředí.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného do popředí (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přední, potřebujete následující položky:

- Předplatné služby Azure AD
- Popředí jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání přední z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-front-from-the-gallery"></a>Přidání přední z Galerie
Při konfiguraci integrace přední do služby Azure AD potřebujete přidat před z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat před z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Front**, vyberte **Front** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Přední v seznamu výsledků](./media/front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s přední podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku vpředu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské vpředu musí navázat.

Vpředu, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s přední, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele před](#create-a-front-test-user)**  – Pokud chcete mít protějškem Britta Simon vpředu propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci popředí.

**Ke konfiguraci Azure AD jednotné přihlašování s přední, proveďte následující kroky:**

1. Na portálu Azure na **Front** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/front-tutorial/tutorial_front_samlbase.png)

3. Na **Front domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/front-tutorial/tutorial_front_url1.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.frontapp.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.frontapp.com/sso/saml/callback`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat se skutečným identifikátorem a adresa URL odpovědi který je popsán později v kurzu nebo kontaktujte [tým podpory Front klienta](mailto:support@frontapp.com) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/front-tutorial/tutorial_front_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/front-tutorial/tutorial_general_400.png)
    
6. Na **Front konfigurace** klikněte na tlačítko **nakonfigurovat Front** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/front-tutorial/tutorial_front_configure.png) 

7. Přihlášení ke klientovi přední jako správce.

8. Přejděte na **nastavení (ozubené kolo ikona v dolní části na levém bočním panelu) > Předvolby**.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/front-tutorial/tutorial_front_000.png)

9. Klikněte na tlačítko **jednotné přihlašování** odkaz.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/front-tutorial/tutorial_front_001.png)

10. Vyberte **SAML** v rozevíracím seznamu **jednotné přihlašování**.
   
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/front-tutorial/tutorial_front_002.png)

11. V **vstupní bod** textbox vložte hodnotu **jeden přihlašování adresa URL služby** z Průvodce konfigurací aplikace Azure AD.
    
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/front-tutorial/tutorial_front_003.png)

12. Otevřete váš stažené **Certificate(Base64)** souboru v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte jej do **podpisového certifikátu** textové pole.
    
    ![Konfigurace straně jediné přihlášení na aplikaci](./media/front-tutorial/tutorial_front_004.png)

13. Na **nastavení poskytovatele služby** část, proveďte následující kroky:

    ![Konfigurace straně jediné přihlášení na aplikaci](./media/front-tutorial/tutorial_front_005.png)

    a. Zkopírujte hodnotu **Entity ID** a vložte ji do **identifikátor** textového pole v **Front domény a adresy URL** části na portálu Azure.

    b. Zkopírujte hodnotu **adresa URL služby ACS** a vložte ji do **adresa URL odpovědi** textového pole v **Front domény a adresy URL** části na portálu Azure.
    
14. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/front-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/front-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/front-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/front-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-front-test-user"></a>Vytvoření zkušebního uživatele před

V této části vytvoříte uživatele volat Britta Simon vpředu. Práce s [tým podpory Front klienta](mailto:support@frontapp.com) přidat uživatele do přední platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu do popředí.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon dopředu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Front**.

    ![V seznamu aplikací na přední odkaz](./media/front-tutorial/tutorial_front_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je pro testování vaší Azure AD SSOconfiguration pomocí přístupového panelu.

Když kliknete na dlaždici přední na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci před. 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/front-tutorial/tutorial_general_01.png
[2]: ./media/front-tutorial/tutorial_general_02.png
[3]: ./media/front-tutorial/tutorial_general_03.png
[4]: ./media/front-tutorial/tutorial_general_04.png

[100]: ./media/front-tutorial/tutorial_general_100.png

[200]: ./media/front-tutorial/tutorial_general_200.png
[201]: ./media/front-tutorial/tutorial_general_201.png
[202]: ./media/front-tutorial/tutorial_general_202.png
[203]: ./media/front-tutorial/tutorial_general_203.png

