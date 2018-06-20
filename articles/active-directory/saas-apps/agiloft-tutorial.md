---
title: 'Kurz: Azure Active Directory integrace s Agiloft | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Agiloft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: aca13814-cdbd-46b8-93dc-1578099c5ee4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: jeedes
ms.openlocfilehash: 2eddeae47e8279e9eb4d6bbb8386cbbe7c53a366
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214232"
---
# <a name="tutorial-azure-active-directory-integration-with-agiloft"></a>Kurz: Azure Active Directory integrace s Agiloft

V tomto kurzu zjistěte, jak integrovat Agiloft s Azure Active Directory (Azure AD).

Integrace Agiloft s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Agiloft.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Agiloft (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Agiloft, potřebujete následující položky:

- Předplatné služby Azure AD
- Agiloft jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Agiloft z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-agiloft-from-the-gallery"></a>Přidání Agiloft z Galerie
Při konfiguraci integrace Agiloft do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Agiloft z galerie.

**Pokud chcete přidat Agiloft z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Agiloft**, vyberte **Agiloft** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Agiloft v seznamu výsledků](./media/agiloft-tutorial/tutorial_agiloft_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Agiloft podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Agiloft je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Agiloft musí navázat.

V Agiloft, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Agiloft, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Agiloft](#create-an-agiloft-test-user)**  – Pokud chcete mít protějšek Britta Simon v Agiloft propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Agiloft.

**Ke konfiguraci Azure AD jednotné přihlašování s Agiloft, proveďte následující kroky:**

1. Na portálu Azure na **Agiloft** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/agiloft-tutorial/tutorial_agiloft_samlbase.png)

3. Na **Agiloft domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![Agiloft domény a adresy URL jednotné přihlašování informace](./media/agiloft-tutorial/tutorial_agiloft_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/project/<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/project/<KB_NAME>` |

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com:443/gui2/spsamlsso?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com:443/gui2/spsamlsso?project=<KB_NAME>` |

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Agiloft domény a adresy URL jednotné přihlašování informace](./media/agiloft-tutorial/tutorial_agiloft_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    | |
    |-|-|
    | `https://<subdomain>.saas.enterprisewizard.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
    | `https://<subdomain>.agiloft.com/gui2/samlssologin.jsp?project=<KB_NAME>` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Agiloft klienta](https://www.agiloft.com/support-login.htm) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/agiloft-tutorial/tutorial_agiloft_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/agiloft-tutorial/tutorial_general_400.png)
    
7. Na **Agiloft konfigurace** klikněte na tlačítko **konfigurace Agiloft** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Agiloft](./media/agiloft-tutorial/tutorial_agiloft_configure.png) 

8. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Agiloft jako správce.

9. Klikněte na **instalace** (v levém podokně) a potom **přístup**.

    ![Konfigurace Agiloft](./media/agiloft-tutorial/setup1.png) 

10. Kliknutím na tlačítko **"Konfigurace SAML 2.0 Single Sign-On"**. 
    
    ![Konfigurace Agiloft](./media/agiloft-tutorial/setup2.png) 

11. Zobrazí se dialogové okno průvodce. V dialogovém okně klikněte na kartu **"Podrobnosti zprostředkovatele Identity"** a vyplňte následující pole:  
    
    ![Konfigurace Agiloft](./media/agiloft-tutorial/setup4.png) 

    a. V **IdP Entity Id / vystavitele** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    b. V **IdP přihlašovací adresa URL** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    c. V **adresy URL odhlašovací IdP** textovému poli, vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.

    d. Otevřete váš **certifikátu s kódováním base-64** v poznámkovém bloku stáhli z portálu Azure, kopírovat obsah ho do schránky a vložte jej do **obsah certifikátu X.509 poskytuje IdP** textové pole.

    e. Klikněte na **Dokončit**.


> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/agiloft-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/agiloft-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/agiloft-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/agiloft-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-agiloft-test-user"></a>Vytvořit uživatele s Agiloft testu

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele automaticky se vytvoří v aplikaci. Neexistuje žádná akce pro vás v této části.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Agiloft.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Agiloft, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Agiloft**.

    ![V seznamu aplikací na Agiloft odkaz](./media/agiloft-tutorial/tutorial_agiloft_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Agiloft na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Agiloft.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/agiloft-tutorial/tutorial_general_01.png
[2]: ./media/agiloft-tutorial/tutorial_general_02.png
[3]: ./media/agiloft-tutorial/tutorial_general_03.png
[4]: ./media/agiloft-tutorial/tutorial_general_04.png

[100]: ./media/agiloft-tutorial/tutorial_general_100.png

[200]: ./media/agiloft-tutorial/tutorial_general_200.png
[201]: ./media/agiloft-tutorial/tutorial_general_201.png
[202]: ./media/agiloft-tutorial/tutorial_general_202.png
[203]: ./media/agiloft-tutorial/tutorial_general_203.png

