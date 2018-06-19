---
title: 'Kurz: Azure Active Directory integrace s vyrovnání Hudba | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Settling Hudba.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6f86a8a2-4bd0-40cc-b1b4-752fce123328
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: jeedes
ms.openlocfilehash: 4c74e4b23ad753ad3e9ff7503080b7a3190bc4b5
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899360"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Kurz: Azure Active Directory integrace s vyrovnání Hudba

V tomto kurzu zjistěte, jak integrovat Settling Hudba s Azure Active Directory (Azure AD).

Integrace Settling Hudba s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k vyrovnání Hudba.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k vyrovnání Hudba (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vyrovnání Hudba, potřebujete následující položky:

- Předplatné služby Azure AD
- Settling Hudba jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat hudební Settling z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-settling-music-from-the-gallery"></a>Přidat hudební Settling z Galerie
Při konfiguraci integrace Settling hudby do služby Azure AD, musíte přidat hudební Settling z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat hudební Settling z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **vyrovnání Hudba**, vyberte **vyrovnání Hudba** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Vyrovnání Hudba v seznamu výsledků](./media/settlingmusic-tutorial/tutorial_settlingmusic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s vyrovnání Hudba podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Settling Hudba je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské likvidaci Hudba musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Settling Hudba, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Hudba Settling](#create-a-settling-music-test-user)**  – Pokud chcete mít protějšek Britta Simon likvidaci Hudba, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Hudba Settling.

**Ke konfiguraci Azure AD jednotné přihlašování s vyrovnání Hudba, proveďte následující kroky:**

1. Na portálu Azure na **vyrovnání Hudba** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/settlingmusic-tutorial/tutorial_settlingmusic_samlbase.png)

3. Na **vyrovnání Hudba domény a adresy URL** část, proveďte následující kroky:

    ![Vyrovnání Hudba domény a adresy URL jeden přihlašování informace](./media/settlingmusic-tutorial/tutorial_settlingmusic_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [vyrovnání tým podpory klienta Hudba](https://rakurakuseisan.jp/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/settlingmusic-tutorial/tutorial_settlingmusic_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/settlingmusic-tutorial/tutorial_general_400.png)

6. Na **vyrovnání Hudba konfigurace** klikněte na tlačítko **nakonfigurovat vyrovnání Hudba** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Vyrovnání Hudba konfigurace](./media/settlingmusic-tutorial/tutorial_settlingmusic_configure.png) 

7. V okně prohlížeče jiný web a přihlášení k vyrovnání Hudba jako správce zabezpečení.

8. Na stránce klikněte na tlačítko **správy** kartě.

    ![Vyrovnání Hudba krok 1](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

9. Klikněte na **nastavení systému** kartě.

    ![Vyrovnání Hudba krok 2](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

10. Přepnout na **zabezpečení** kartě.

    ![Vyrovnání hudba ke kroku 3](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

11. Na **jednotné přihlašování v nastavení** část, proveďte následující kroky:

    ![Vyrovnání step5 Hudba](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Klikněte na tlačítko **povolit**.

    b. V **přihlašovací adresa URL poskytovatele ID** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. V **adresy URL odhlašovací ID zprostředkovatele** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.

    d. Klikněte na tlačítko **zvolit soubor** nahrát **certifikátu (Base64)** který jste si stáhli formuláři portálu Azure.

    e. Klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/settlingmusic-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/settlingmusic-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/settlingmusic-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/settlingmusic-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-settling-music-test-user"></a>Vytvoření zkušebního uživatele Settling Hudba

V této části vytvoříte volal Britta Simon v Settling Hudba uživatele. Práce s [vyrovnání tým podpory klienta Hudba](https://rakurakuseisan.jp/) přidat uživatele do platformy Hudba Settling. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k vyrovnání Hudba.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Settling Hudba, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **vyrovnání Hudba**.

    ![Odkaz Settling Hudba v seznamu aplikací](./media/settlingmusic-tutorial/tutorial_settlingmusic_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Hudba Settling na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Hudba Settling.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/settlingmusic-tutorial/tutorial_general_01.png
[2]: ./media/settlingmusic-tutorial/tutorial_general_02.png
[3]: ./media/settlingmusic-tutorial/tutorial_general_03.png
[4]: ./media/settlingmusic-tutorial/tutorial_general_04.png

[100]: ./media/settlingmusic-tutorial/tutorial_general_100.png

[200]: ./media/settlingmusic-tutorial/tutorial_general_200.png
[201]: ./media/settlingmusic-tutorial/tutorial_general_201.png
[202]: ./media/settlingmusic-tutorial/tutorial_general_202.png
[203]: ./media/settlingmusic-tutorial/tutorial_general_203.png

