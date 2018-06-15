---
title: 'Kurz: Azure Active Directory integrace s TimeLive | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TimeLive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 34123629-4ad5-465c-a4c1-8299f857e720
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: e25ded9097a8250206f5bec3c76e872044ba597d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351592"
---
# <a name="tutorial-azure-active-directory-integration-with-timelive"></a>Kurz: Azure Active Directory integrace s TimeLive

V tomto kurzu zjistěte, jak integrovat TimeLive s Azure Active Directory (Azure AD).

Integrace TimeLive s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k TimeLive.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TimeLive (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TimeLive, potřebujete následující položky:

- Předplatné služby Azure AD
- TimeLive jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TimeLive z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-timelive-from-the-gallery"></a>Přidání TimeLive z Galerie
Při konfiguraci integrace TimeLive do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS TimeLive z galerie.

**Pokud chcete přidat TimeLive z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **TimeLive**, vyberte **TimeLive** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![TimeLive v seznamu výsledků](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s TimeLive podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v TimeLive je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v TimeLive musí navázat.

V TimeLive, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TimeLive, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele TimeLive](#create-a-timelive-test-user)**  – Pokud chcete mít protějšek Britta Simon v TimeLive propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci TimeLive.

**Ke konfiguraci Azure AD jednotné přihlašování s TimeLive, proveďte následující kroky:**

1. Na portálu Azure na **TimeLive** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_samlbase.png)

3. Na **TimeLive domény a adresy URL** část, proveďte následující kroky:

    ![TimeLive domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://domainname.livetecs.com/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://domainname.livetecs.com/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a přihlašovací adresa URL. Obraťte se na [tým podpory TimeLive klienta](mailto:support@livetecs.com) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-timelive-tutorial/tutorial_general_400.png)
    
6. Na **TimeLive konfigurace** klikněte na tlačítko **konfigurace TimeLive** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL a SAML jeden přihlašování služby URL** z **Stručná referenční příručka části.**

    ![Konfigurace TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_configure.png)

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti TimeLive jako správce.

8. Vyberte **Předvolby** pod **možnosti správce**.

    ![Konfigurace TimeLive](./media/active-directory-saas-timelive-tutorial/configure1.png)

9. V **předvoleb aplikace** část, proveďte následující kroky:
    
    ![Konfigurace TimeLive](./media/active-directory-saas-timelive-tutorial/configure2.png)

    a. Vyberte **zabezpečení** kartě.

    b. Zkontrolujte **povolit jednotné přihlašování na (SSO)** zaškrtávací políčko.

    c. Vyberte **SAML** z rozevírací nabídky s názvem **přihlásit pomocí jednotné přihlašování (SSO) s**.

    d. V **URL jednotné přihlašování SAML**, vložte **SAML jeden přihlašování adresa URL služby** hodnotu, která jste zkopírovali formuláři na portálu Azure.

    e. V **adresy URL odhlašovací vzdálené**, vložte **Sign-Out URL** hodnotu, která jste zkopírovali formuláři na portálu Azure.

    f. Otevřete stažené **certifikátu s kódováním base-64** z portálu Azure v poznámkovém bloku, kopírovat obsah a vložte ji do **certifikát X.509** textové pole.

    g. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-timelive-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-timelive-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-timelive-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-timelive-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-timelive-test-user"></a>Vytvoření zkušebního uživatele TimeLive

Cílem této části je vytvoření uživatele v TimeLive nazývá Britta Simon. TimeLive podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k TimeLive, pokud ještě neexistuje.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu TimeLive.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon TimeLive, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **TimeLive**.

    ![V seznamu aplikací na TimeLive odkaz](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici TimeLive na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci TimeLive.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_203.png

