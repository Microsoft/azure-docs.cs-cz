---
title: 'Kurz: Azure Active Directory integrace s vxMaintain | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vxMaintain.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c30d59a2bd903a5578caf376957a22bb270455f9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346107"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Kurz: Azure Active Directory integrace s vxMaintain

V tomto kurzu zjistěte, jak integrovat vxMaintain s Azure Active Directory (Azure AD).

Tato integrace poskytuje několik výhod důležité. Můžete:

- Řízení ve službě Azure AD, který má přístup k vxMaintain.
- Povolte uživatelům automaticky se přihlaste k vxMaintain s jednotné přihlašování (SSO) pomocí svých účtů Azure AD.
- Spravovat účty v jednom centrálním místě: portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vxMaintain, potřebujete následující položky:

- Předplatné služby Azure AD
- VxMaintain předplatné povolené jednotné přihlašování

> [!NOTE]
> Při testování kroky v tomto kurzu, doporučujeme vám, nepoužívejte provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénář, který tento kurz popisuje se skládá ze dvou hlavních stavebních bloků:

* Přidání vxMaintain z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-vxmaintain-from-the-gallery"></a>Přidat vxMaintain z Galerie
Chcete-li nakonfigurovat integraci vxMaintain s Azure AD, přidejte vxMaintain z Galerie si na seznam spravovaných aplikací SaaS.

Pokud chcete přidat vxMaintain z galerie, postupujte takto:

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** tlačítko. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![V podokně "Podnikové aplikace"][2]
    
3. Chcete-li přidat aplikaci, v **všechny aplikace** dialogové okno, vyberte **novou aplikaci**.

    !["Nová aplikace" tlačítko][3]

4. Do vyhledávacího pole zadejte **vxMaintain**.

    ![Rozevíracím seznamu "Jednoho přihlašování v režimu"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. V seznamu výsledků vyberte **vxMaintain**a potom vyberte **přidat**.

    ![Odkaz vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD SSO pomocí vxMaintain, podle testovacího uživatele názvem "Britta Simon."

Pro jednotné přihlašování pro práci Azure AD musí znát vxMaintain protějškem uživatele Azure AD. To znamená je potřeba vytvořit vztah propojení mezi uživatele Azure AD a odpovídající vxMaintain uživatele.

Vytvořit vztah odkaz, přiřaďte mu vxMaintain **uživatelské jméno** hodnotu jako Azure AD **uživatelské jméno** hodnotu.

Nakonfigurovat a otestovat Azure AD SSO pomocí vxMaintain, dokončete následující stavební bloky.

### <a name="configure-azure-ad-sso"></a>Konfigurovat Azure AD jednotného přihlašování

V této části můžete povolit jednotné přihlašování Azure AD na portálu Azure i nakonfigurovat jednotné přihlašování v aplikaci vxMaintain následujícím způsobem:

1. Na portálu Azure na **vxMaintain** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Příkaz "Jednotného přihlašování"][4]

2. Pro povolení jednotného přihlašování, v **režimu přihlašování** rozevíracího seznamu vyberte **na základě SAML přihlašování**.
 
    ![Příkaz "na základě SAML přihlášení"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. V části **vxMaintain domény a adresy URL**, postupujte takto:

    ![VxMaintain oddílu domény a adresy URL](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. V **identifikátor** zadejte adresu URL, která má následující syntaxi: `https://<company name>.verisae.com`

    b. V **adresa URL odpovědi** zadejte adresu URL, která má následující syntaxi: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Je aktualizovat skutečným identifikátorem a adresa URL odpovědi. Chcete-li získat hodnoty, obraťte se [tým podpory vxMaintain](http://www.verisae.com/contact-us).
 
4. V části **SAML podpisový certifikát**, vyberte **soubor XML s metadaty**a potom uložte soubor metadat pro váš počítač.

    ![V části "SAML podpisový certifikát"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Ke konfiguraci **vxMaintain** jednotné přihlašování, odesílání stažené **soubor XML s metadaty** do souboru [tým podpory vxMaintain](http://www.verisae.com/contact-us).

> [!TIP]
> Jak nastavit aplikaci si můžete přečíst stručným verzi podle předchozích pokynů v [portál Azure](https://portal.azure.com). Po přidání aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a potom přejdete embedded dokumentace z **konfigurace** části. 
>
>Další informace o funkci embedded dokumentaci najdete v tématu [Správa jednotného přihlašování pro podnikové aplikace](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
V této části vytvoříte testovacího uživatele Britta Simon na portálu Azure, následujícím způsobem:

![Testovací uživatele Azure AD][100]

1. V **portál Azure**, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko "Azure Active Directory"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** > **všichni uživatelé**.
    
    ![Na odkaz "Všichni uživatelé"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    **Všichni uživatelé** otevře se dialogové okno. 

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat**.
 
    ![Tlačítko Přidat](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, postupujte takto:
 
    ![Dialogové okno uživatele](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu testovacího uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a poznamenejte si hodnotu, která byla vygenerována v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-vxmaintain-test-user"></a>Vytvoření zkušebního uživatele vxMaintain

V této části vytvoříte testovacího uživatele Britta Simon v vxMaintain. Přidat uživatele v platformě vxMaintain, pracovat [tým podpory vxMaintain](http://www.verisae.com/contact-us). Před použitím jednotného přihlašování k vytvoření a aktivace uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte testovacího uživatele Britta Simon chcete použít k udělení přístupu k vxMaintain jednotného přihlašování k Azure. Chcete-li to provést, postupujte takto:

![Testovací uživatel v seznamu zobrazovaný název][200] 

1. Na portálu Azure **aplikace** zobrazení, přejděte na **Directory** zobrazení > **podnikové aplikace, které** > **všechny aplikace**.

    ![Na odkaz "Všechny aplikace"][201] 

2. V **aplikace** seznamu, vyberte **vxMaintain**.

    ![Odkaz vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Vyberte **přidat** a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][203]

5. V **uživatelů a skupin** v dialogovém **uživatelé** seznamu, vyberte **Britta Simon**a pak vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testování vaší služby Azure AD jednotné přihlašování

V této části otestovat konfiguraci Azure AD jednotného přihlašování pomocí přístupového panelu.

Výběr **vxMaintain** dlaždice na přístupovém panelu musí přihlásit do aplikace vxMaintain automaticky.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Další postup

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

