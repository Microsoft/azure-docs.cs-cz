---
title: 'Kurz: Azure Active Directory integrace s síti na pracovišti Soonr | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Soonr síti na pracovišti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.openlocfilehash: 1725ebd39a5d976fee061ed92ddcc686fa7a1acd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352901"
---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>Kurz: Azure Active Directory integrace s Soonr síti na pracovišti

V tomto kurzu zjistěte, jak k síti na pracovišti Soonr integraci s Azure Active Directory (Azure AD).

Integrace Soonr síti na pracovišti s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti Soonr
- Můžete povolit uživatelům, aby automaticky získat přihlášení k síti na pracovišti Soonr (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Soonr síti na pracovišti, potřebujete následující položky:

- Předplatné služby Azure AD
- Síti na pracovišti Soonr jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Soonr síti na pracovišti z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-soonr-workplace-from-the-gallery"></a>Přidání Soonr síti na pracovišti z Galerie
Při konfiguraci integrace síti na pracovišti Soonr do služby Azure AD potřebujete přidat Soonr síti na pracovišti z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Soonr síti na pracovišti z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Soonr síti na pracovišti**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_search.png)

5. Na panelu výsledků vyberte **síti na pracovišti Soonr**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování se Soonr pracovišti podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v síti na pracovišti Soonr je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživateli v síti na pracovišti Soonr musí navázat.

V síti na pracovišti Soonr, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Soonr síti na pracovišti, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele síti na pracovišti Soonr](#creating-a-soonr-workplace-test-user)**  – Pokud chcete mít protějšek Britta Simon v síti na pracovišti Soonr propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Soonr síti na pracovišti.

**Ke konfiguraci Azure AD jednotné přihlašování s Soonr síti na pracovišti, proveďte následující kroky:**

1. Na portálu Azure na **síti na pracovišti Soonr** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_samlbase.png)

3. Na **Soonr síti na pracovišti domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<servername>.soonr.com/singlesignon/saml/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<servername>.soonr.com/singlesignon/saml/SSO`

4. Na **Soonr síti na pracovišti domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_url1.png)

    a. Klikněte na **zobrazit upřesňující nastavení adresy URL**.

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<servername>.soonr.com/singlesignon/saml/SSO`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat skutečným identifikátorem přihlášení adresy URL a adresa URL odpovědi. Obraťte se na [tým podpory síti na pracovišti Soonr](https://awp.autotask.net/help/) k získání těchto hodnot.
 
5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_general_400.png)

7. Na **Soonr síti na pracovišti konfigurace** klikněte na tlačítko **konfigurace v síti na pracovišti Soonr** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_configure.png) 

8. Konfigurace jednotného přihlašování na **síti na pracovišti Soonr** straně, budete muset odeslat stažené **soubor XML s metadaty**, **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [tým podpory síti na pracovišti Soonr](https://awp.autotask.net/help/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

    >[!Note]
    >Pokud potřebujete pomoc s konfigurací Autotask síti na pracovišti, najdete v tématu [tuto stránku](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) získat pomoc s pracovního účtu.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-soonr-workplace-test-user"></a>Vytvoření zkušebního uživatele Soonr síti na pracovišti

Cílem této části je vytvoření uživatele v síti na pracovišti Soonr nazývá Britta Simon. Práce s [tým podpory síti na pracovišti Soonr](https://awp.autotask.net/help/) a vytvořte uživatele v platformu. Můžete zvýšit lístku podpory s Soonr z <a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">zde</a>.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k síti na pracovišti Soonr.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k síti na pracovišti Soonr, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Soonr síti na pracovišti**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  

Když kliknete na dlaždici Soonr síti na pracovišti na přístupovém panelu, můžete by měl získat automaticky přihlášení k síti na pracovišti Soonr aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png

