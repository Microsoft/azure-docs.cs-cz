---
title: 'Kurz: Azure Active Directory integrace s Cisco cloudu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db1cea1d-ff0a-4f0d-b5fd-50ca32702d56
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 7a0f9e54b920ee2c16a141ee62d22784f75c0af6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224149"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-cloud"></a>Kurz: Azure Active Directory integrace s Cisco cloudu

V tomto kurzu zjistěte, jak integrovat Cisco cloudu s Azure Active Directory (Azure AD).

Integrace Cisco cloudu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do cloudu Cisco.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Cisco cloudu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudem Cisco, potřebujete následující položky:

- Předplatné služby Azure AD
- Cloudu Cisco jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cisco cloudu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cisco-cloud-from-the-gallery"></a>Přidání Cisco cloudu z Galerie
Při konfiguraci integrace Cisco cloudu do služby Azure AD, potřebujete přidat Cisco cloudu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Cisco cloudu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco cloudu**, vyberte **Cisco cloudu** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Cisco cloudu v seznamu výsledků](./media/ciscocloud-tutorial/tutorial_ciscocloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco cloudu podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v cloudu Cisco je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v cloudu Cisco musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco cloudu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Cisco cloudu](#create-a-cisco-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco cloudu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Cisco cloudu.

**Ke konfiguraci Azure AD jednotné přihlašování s Cisco cloudu, proveďte následující kroky:**

1. Na portálu Azure na **Cisco cloudu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/ciscocloud-tutorial/tutorial_ciscocloud_samlbase.png)

3. Na **Cisco cloudové domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Cisco cloudové domény a adresy URL jednotné přihlašování informace](./media/ciscocloud-tutorial/tutorial_ciscocloud_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `<subdomain>.cisco.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.cisco.com/sp/ACS.saml2`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Cisco cloudové domény a adresy URL jednotné přihlašování informace](./media/ciscocloud-tutorial/tutorial_ciscocloud_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://<subdomain>.cloudapps.cisco.com`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a adresa URL přihlašování. Obraťte se na [tým podpory klient Cloud Cisco](mailto:cpr-ops@cisco.com) k získání těchto hodnot.

5. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/ciscocloud-tutorial/tutorial_ciscocloud_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/ciscocloud-tutorial/tutorial_general_400.png)

7. Konfigurace jednotného přihlašování na **Cisco cloudu** straně, budete muset odeslat **adresu Url aplikace federační Metadata** k [tým podpory Cisco cloudu](mailto:cpr-ops@cisco.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/ciscocloud-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/ciscocloud-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/ciscocloud-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/ciscocloud-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-cisco-cloud-test-user"></a>Vytvoření zkušebního uživatele Cisco cloudu

V této části vytvoříte uživatele volat Britta Simon v cloudu Cisco. Práce s [tým podpory Cisco cloudu](mailto:cpr-ops@cisco.com) přidat uživatele do Cisco Cloudová platforma. Uživatelé musí být vytvořen a aktivovat před použitím jednotného přihlašování

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu do cloudu Cisco.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit do cloudu Cisco, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Cisco cloudu**.

    ![V seznamu aplikací na odkaz Cisco cloudu](./media/ciscocloud-tutorial/tutorial_ciscocloud_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Cisco cloudu na přístupovém panelu, jste měli získat automaticky přihlášení k Cisco cloudové aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ciscocloud-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloud-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloud-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloud-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloud-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloud-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloud-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloud-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloud-tutorial/tutorial_general_203.png

