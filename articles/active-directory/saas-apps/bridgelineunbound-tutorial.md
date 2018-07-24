---
title: 'Kurz: Integrace Azure Active Directory s Bridgeline nevázaného | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Bridgeline nevázaných.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b018472f-c8b3-403d-ae66-9ed26a35f413
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: c429afa12bc11db68d041fef96f66b3f4c7f0b1b
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206099"
---
# <a name="tutorial-azure-active-directory-integration-with-bridgeline-unbound"></a>Kurz: Integrace Azure Active Directory s Bridgeline nevázaného

V tomto kurzu se dozvíte, jak integrovat Bridgeline nevázaného s Azure Active Directory (Azure AD).

Integrace s Azure AD Bridgeline nevázaného poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Bridgeline nevázaného.
- Uživatele, aby automaticky získat přihlášení k Bridgeline nevázaného (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Bridgeline Odvázat, potřebujete následující položky:

- S předplatným služby Azure AD
- Nepřipojeného Bridgeline jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání nevázaných Bridgeline z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-bridgeline-unbound-from-the-gallery"></a>Přidání nevázaných Bridgeline z Galerie
Pokud chcete nakonfigurovat integraci Bridgeline nevázaného do služby Azure AD, potřebujete přidat Bridgeline nevázaného z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání nevázaných Bridgeline z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Bridgeline nevázaného**vyberte **Bridgeline nevázaného** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Bridgeline nevázaného v seznamu výsledků](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Bridgeline nevázaného podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Bridgeline nevázaný je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Bridgeline nevázaného potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bridgeline Odvázat, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Bridgeline nevázaného](#create-a-bridgeline-unbound-test-user)**  – Pokud chcete mít protějšek Britta Simon Bridgeline Odvázat, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Bridgeline nevázaného.

**Ke konfiguraci Azure AD jednotné přihlašování s Bridgeline Odvázat, proveďte následující kroky:**

1. Na webu Azure Portal na **Bridgeline nevázaného** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_samlbase.png)
 
3. Na **Bridgeline nevázaného domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Bridgeline domény a adresy URL jednotného přihlašování – informace](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `iApps_UPSTT_<ENVIRONMENTNAME>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.iapps.com/SAMLAssertionService.aspx`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Bridgeline domény a adresy URL jednotného přihlašování – informace](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<SUBDOMAIN>.iapps.com/CommonLogin/login?<INSTANCENAME>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory nevázaného klienta Bridgeline](mailto:support@iapps.com) k získání těchto hodnot. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/bridgelineunbound-tutorial/tutorial_general_400.png)

6. Na **Bridgeline nevázaného konfigurace** klikněte na tlačítko **konfigurace Bridgeline nevázaného** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Bridgeline nevázaného konfigurace](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_configure.png) 

7. Ke konfiguraci jednotného přihlašování na **Bridgeline nevázaného** straně, je nutné odeslat na stažený **certifikát (Base64)**, **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** k [Bridgeline nevázaného tým podpory](mailto:support@iapps.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/bridgelineunbound-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a grobridgelineinbound**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatelů a grobridgelineinbound" a "Všechny uživatele" odkazy](./media/bridgelineunbound-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/bridgelineunbound-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/bridgelineunbound-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-bridgeline-unbound-test-user"></a>Vytvoření nevázaného Bridgeline testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Bridgeline nevázaného. Bridgeline nevázaného podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Bridgeline Odvázat, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [Bridgeline nevázaného tým podpory](mailto:support@iapps.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do nepřipojeného Bridgeline.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon Bridgeline Odvázat, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Bridgeline nevázaného**.

    ![Bridgeline nevázaný odkaz v seznamu aplikací](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a grobridgelineinbound**.

    ![Odkaz "Uživatelů a grobridgelineinbound"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a grobridgelineinbound** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a grobridgelineinbound** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a grobridgelineinbound** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Bridgeline nevázaného na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Bridgeline nevázaného.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bridgelineunbound-tutorial/tutorial_general_01.png
[2]: ./media/bridgelineunbound-tutorial/tutorial_general_02.png
[3]: ./media/bridgelineunbound-tutorial/tutorial_general_03.png
[4]: ./media/bridgelineunbound-tutorial/tutorial_general_04.png

[100]: ./media/bridgelineunbound-tutorial/tutorial_general_100.png

[200]: ./media/bridgelineunbound-tutorial/tutorial_general_200.png
[201]: ./media/bridgelineunbound-tutorial/tutorial_general_201.png
[202]: ./media/bridgelineunbound-tutorial/tutorial_general_202.png
[203]: ./media/bridgelineunbound-tutorial/tutorial_general_203.png

