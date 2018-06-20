---
title: 'Kurz: Azure Active Directory integrace s tajný klíč serveru (místní) | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tajný klíč serveru (místní).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: c9229afd7bd8ebad85ce9e329fb11f992236bce0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220097"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Kurz: Azure Active Directory integrace s tajný klíč serveru (místní)

V tomto kurzu zjistěte, jak integrovat tajný klíč serveru (místní) s Azure Active Directory (Azure AD).

Integrace tajný klíč serveru (místní) s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k tajný klíč serveru (místní).
- Můžete povolit uživatelům, aby automaticky získat přihlášení k tajný klíč serveru (místní) (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s tajný klíč serveru (místní), potřebujete následující položky:

- Předplatné služby Azure AD
- Tajný klíč serveru (místní) jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tajný klíč serveru (místní) z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Přidání tajný klíč serveru (místní) z Galerie
Při konfiguraci integrace tajný klíč serveru (místní) do služby Azure AD, musíte přidat tajný klíč serveru (místní) z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat tajný klíč serveru (místní) z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **tajný klíč serveru (místní)**, vyberte **tajný klíč serveru (místní)** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![V seznamu výsledků tajný Server (místní)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s tajný klíč serveru (místní) podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v tajný klíč serveru (místní) je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské tajný klíč serveru (místní).

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s tajný klíč serveru (místní), je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele tajný klíč serveru (místní)](#create-a-secret-server-on-premises-test-user)**  – Pokud chcete mít protějšek Britta Simon v tajný klíč serveru (místní), který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci tajný klíč serveru (místní).

**Ke konfiguraci Azure AD jednotné přihlašování s tajný klíč serveru (místní), proveďte následující kroky:**

1. Na portálu Azure na **tajný klíč serveru (místní)** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

3. Na **tajný klíč serveru (místní) domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Tajný serveru (místní) domény a adresy URL jednotné přihlašování informace](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. V **identifikátor** textovému poli, zadejte uživatele vybrali hodnotu jako příklad: `https://secretserveronpremises.azure`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `

    > [!NOTE]
    > ID Entity uvedené výše je příklad pouze a můžete zvolit jakoukoli jedinečnou hodnotu, která určuje vaší instanci tajný klíč serveru ve službě Azure AD. Budete muset odeslat toto ID Entity [tým podpory klienta tajný klíč serveru (místní)](https://thycotic.force.com/support/s/) a jejich konfigurace na jejich straně. Další podrobnosti, přečtěte si prosím [v tomto článku](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Tajný serveru (místní) domény a adresy URL jednotné přihlašování informace](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory klienta tajný klíč serveru (místní)](https://thycotic.force.com/support/s/) získat tyto hodnoty.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

6. Zkontrolujte **zobrazit upřesňující nastavení podpisový certifikát** a vyberte **podepisování možnost** jako **odpověď přihlašování SAML a assertion**.

    ![Možnosti podpisu](./media/secretserver-on-premises-tutorial/signing.png)

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
8. Na **konfigurace tajný klíč serveru (místní)** klikněte na tlačítko **konfigurace tajný klíč serveru (místní)** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace tajný serveru (místní)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

9. Konfigurace jednotného přihlašování na **tajný klíč serveru (místní)** straně, budete muset odeslat stažené **Certificate(Base64) Sign-Out URL SAML jeden přihlašování adresa URL služby**, a **SAML Entity ID** k [tým podpory tajný klíč serveru (místní)](https://thycotic.force.com/support/s/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Vytvoření zkušebního uživatele tajný klíč serveru (místní)

V této části vytvoříte uživatele volat Britta Simon tajný klíč serveru (místní). Práce s [tým podpory tajný klíč serveru (místní)](https://thycotic.force.com/support/s/) přidat uživatele do platforma tajný klíč serveru (místní). Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k tajný klíč serveru (místní).

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon tajný klíč serveru (místní), proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **tajný klíč serveru (místní)**.

    ![V seznamu aplikací na odkaz tajný klíč serveru (místní)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici tajný klíč serveru (místní) na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci tajný klíč serveru (místní).
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

