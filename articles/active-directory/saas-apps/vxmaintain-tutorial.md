---
title: 'Kurz: Integrace Azure Active Directory s vxMaintain | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vxMaintain.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 590c7961444a36c8958fd82aaf67b05ee2213e74
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819896"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Kurz: Integrace Azure Active Directory s vxMaintain

V tomto kurzu se dozvíte, jak integrovat vxMaintain s Azure Active Directory (Azure AD).

Tato integrace poskytuje několik výhod důležité. Můžete:

- Ovládací prvek ve službě Azure AD, který má přístup k vxMaintain.
- Umožní vašim uživatelům přihlásit se automaticky do vxMaintain s jednotným přihlašováním (SSO) s použitím svých účtů služby Azure AD.
- Správa účtů v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vxMaintain, potřebujete následující položky:

- Předplatné Azure AD
- VxMaintain podporou jednotného přihlašování k odběru

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme nepoužívejte produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. 

Scénáře, který se popisuje v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání vxMaintain z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-vxmaintain-from-the-gallery"></a>Přidání vxMaintain z Galerie
Konfigurace integrace vxMaintain s Azure AD, budete muset přidat vxMaintain z Galerie na váš seznam spravovaných aplikací SaaS.

Chcete-li přidat vxMaintain z galerie, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** tlačítko. 

    ![Tlačítko Azure Active Directory][1]

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně "Podnikové aplikace"][2]
    
1. V aplikaci, přidáte **všechny aplikace** dialogovém okně vyberte **novou aplikaci**.

    !["Nové aplikace" tlačítko][3]

1. Do vyhledávacího pole zadejte **vxMaintain**.

    !["Jeden režim přihlašování" rozevíracího seznamu](./media/vxmaintain-tutorial/tutorial_vxmaintain_search.png)

1. V seznamu výsledků vyberte **vxMaintain**a pak vyberte **přidat**.

    ![Odkaz vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat jednotné přihlašování služby Azure AD pomocí vxMaintain podle testovacího uživatele nazývá "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět vxMaintain protějškem uživatele Azure AD. To znamená je potřeba vytvořit vztah odkazu mezi uživatele Azure AD a odpovídající vxMaintain uživatele.

K navázání vztahu odkazu, přiřaďte vxMaintain **uživatelské jméno** hodnotu jako Azure AD **uživatelské jméno** hodnotu.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s použitím vxMaintain, dokončete následující stavební bloky.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

V této části můžete jak povolit jednotné přihlašování služby Azure AD na webu Azure Portal a konfigurace jednotného přihlašování v aplikaci vxMaintain následujícím způsobem:

1. Na webu Azure Portal na **vxMaintain** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Příkaz "Jednotné přihlašování"][4]

1. Chcete povolit jednotné přihlašování, v **režim jednotného přihlašování** rozevíracího seznamu vyberte **přihlašování na základě SAML**.
 
    ![Příkaz "založené na SAML Sign-on"](./media/vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

1. V části **vxMaintain domény a adresy URL**, postupujte takto:

    ![VxMaintain části domény a adresy URL](./media/vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. V **identifikátor** zadejte adresu URL, která má následující syntaxi: `https://<company name>.verisae.com`

    b. V **adresy URL odpovědi** zadejte adresu URL, která má následující syntaxi: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečný. Aktualizujte identifikátor skutečné a adresa URL odpovědi. K získání hodnot, obraťte se [tým podpory vxMaintain](https://www.hubspot.com/company/contact).
 
1. V části **podpisový certifikát SAML**vyberte **soubor XML s metadaty**a poté uložte soubor metadat do počítače.

    ![V části "Podpisový certifikát SAML"](./media/vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

1. Vyberte **Uložit**.

    ![Tlačítko Uložit](./media/vxmaintain-tutorial/tutorial_general_400.png)

1. Ke konfiguraci **vxMaintain** jednotného přihlašování, odeslat na stažený **soubor XML s metadaty** do souboru [tým podpory vxMaintain](https://www.hubspot.com/company/contact).

> [!TIP]
> Jak nastavit aplikaci si můžete přečíst stručné verzi podle předchozích pokynů v [webu Azure portal](https://portal.azure.com). Po přidání aplikace **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a potom přejdete na vložené dokumentace ke službě z **konfigurace** oddílu. 
>
>Další informace o funkci vložený dokumentaci najdete v tématu [Správa jednotného přihlašování pro podnikové aplikace](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal následujícím způsobem:

![Testovacího uživatele Azure AD][100]

1. V **webu Azure portal**, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko "Azure Active Directory"](./media/vxmaintain-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** > **všichni uživatelé**.
    
    ![Odkaz "Všichni uživatelé"](./media/vxmaintain-tutorial/create_aaduser_02.png)  
    **Všichni uživatelé** zobrazí se dialogové okno. 

1. Chcete-li otevřít **uživatele** dialogu **přidat**.
 
    ![Tlačítko Přidat](./media/vxmaintain-tutorial/create_aaduser_03.png) 

1. V **uživatele** dialogové okno pole, postupujte takto:
 
    ![Dialogové okno uživatele](./media/vxmaintain-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu testovacího uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a potom si poznamenejte hodnotu, která byla vygenerována **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-vxmaintain-test-user"></a>Vytvoření zkušebního uživatele vxMaintain

V této části můžete vytvořit testovacího uživatele Britta Simon v vxMaintain. Přidat uživatele na platformě vxMaintain, pracovat [tým podpory vxMaintain](https://www.hubspot.com/company/contact). Před použitím jednotného přihlašování k vytvoření a aktivace uživatele.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte testovacího uživatele Britta Simon k udělení přístupu k vxMaintain použití jednotného přihlašování k Azure. Chcete-li to provést, postupujte takto:

![V seznamu zobrazovaný název testovacího uživatele][200] 

1. Na webu Azure Portal **aplikací** zobrazení, přejděte na **Directory** zobrazení > **podnikové aplikace** > **všechny aplikace**.

    ![Odkaz "Všechny aplikace"][201] 

1. V **aplikací** seznamu vyberte **vxMaintain**.

    ![Odkaz vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

1. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Vyberte **přidat** a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][203]

1. V **uživatelů a skupin** v dialogu **uživatelé** seznamu vyberte **Britta Simon**a pak vyberte **vyberte** tlačítko.

1. V **přidat přiřazení** dialogu **přiřadit**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testování vaší služby Azure AD jednotného přihlašování

V této části testování konfigurace jednotného přihlašování k Azure AD s použitím na přístupovém panelu.

Výběr **vxMaintain** dlaždici na přístupovém panelu by vás přihlásit do aplikace vxMaintain automaticky.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Další postup

* [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/vxmaintain-tutorial/tutorial_general_203.png

