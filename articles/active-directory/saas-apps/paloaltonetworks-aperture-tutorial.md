---
title: 'Kurz: Azure Active Directory integrace se sítěmi Palo Alto - otvoru | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks - otvoru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 0b8e5dd58736c0ad27ea45bd09e21acbdb0a27ac
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35898988"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Kurz: Azure Active Directory integrace se sítěmi Palo Alto - otvoru

V tomto kurzu zjistěte, jak integrovat Palo Alto Networks - otvoru službou Azure Active Directory (Azure AD).

Integrace Palo Alto sítí - otvoru s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k sítím Palo Alto - otvoru.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k sítím Palo Alto - otvoru (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se sítěmi Palo Alto - otvoru, potřebujete následující položky:

- Předplatné služby Azure AD
- Palo Alto sítě - otvoru jednotné přihlašování povoleno předplatného

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidávání sítí Palo Alto - otvoru z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Přidávání sítí Palo Alto - otvoru z Galerie
Při konfiguraci integrace Palo Alto sítí - otvoru do služby Azure AD, potřebujete přidat Palo Alto Networks - otvor z Galerie pro váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Palo Alto Networks - otvoru z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Palo Alto Networks - otvoru**, vyberte **Palo Alto Networks - otvoru** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Palo Alto sítě - otvoru v seznamu výsledků](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování se sítěmi Palo Alto – otvoru podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v sítích Palo Alto - otvoru je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v sítích Palo Alto - otvoru musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se sítěmi Palo Alto - otvoru, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořte sítě Palo Alto - otvoru testovacího uživatele](#create-a-palo-alto-networks---aperture-test-user)**  – Pokud chcete mít protějšek Britta Simon v sítích Palo Alto - otvoru propojeném s Azure AD reprezentace uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování ve svých sítích Palo Alto - otvoru aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování se sítěmi Palo Alto - otvoru, proveďte následující kroky:**

1. Na portálu Azure na **Palo Alto Networks - otvoru** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. Na **Palo Alto sítě - otvoru domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Jednotné přihlašování informace Palo Alto sítě - otvoru domény a adresy URL](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Jednotné přihlašování informace Palo Alto sítě - otvoru domény a adresy URL](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [Palo Alto Networks - tým podpory otvoru klienta](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. Na **Palo Alto Networks - otvoru konfigurace** klikněte na tlačítko **konfigurace sítě Palo Alto - otvoru** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace propojení](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. V okně prohlížeče jiný web a přihlášení k sítím Palo Alto - otvoru jako správce.

9. Na panelu horní nabídce klikněte na tlačítko **nastavení**.

    ![Karta nastavení](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Přejděte na **aplikace** klikněte na část **ověřování** formuláři na levé straně nabídky.

    ![Na kartě ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. Na **ověřování** stránky proveďte následující kroky:
    
    ![Na kartě ověřování](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Zkontrolujte **povolit jeden znak-On(Supported SSP Providers are Okta, Onelogin)** z **jednotné přihlašování** pole.

    b. V **ID zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    c. Klikněte na tlačítko **zvolit soubor** na kterou odešlete certifikát stažený z Azure AD v **certifikát zprostředkovatele Identity** pole.

    d. V **adresa URL jednotné přihlašování zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    e. Zkontrolovat informace o rozšíření IdP z **otvoru informace** části a stáhněte certifikát z **otvoru klíč** pole.

    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Vytvořte sítě Palo Alto - otvoru testovacího uživatele

V této části vytvoříte uživatele volat Britta Simon v sítích Palo Alto - otvoru. Práce s [Palo Alto Networks - tým podpory otvoru klienta](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) přidejte uživatele, v sítích Palo Alto - otvoru platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup k sítím Palo Alto - otvoru Azure jednotné přihlašování.

![Přiřadit role uživatele][200] 

**Britta Simon přiřadit Palo Alto Networks - otvoru, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Palo Alto Networks - otvoru**.

    ![Sítě Palo Alto - otvoru odkaz v seznamu aplikací](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko Palo Alto Networks - otvoru dlaždice na přístupovém panelu jste měli získat automaticky přihlášení k Palo Alto Networks - otvoru aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

