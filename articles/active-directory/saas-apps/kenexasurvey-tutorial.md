---
title: 'Kurz: Integrace Azure Active Directory s IBM Kenexa průzkumu Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IBM Kenexa průzkumu Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67b24ca0008a03474b54a1bf226261c3f395fec
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183220"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Kurz: Integrace Azure Active Directory s IBM Kenexa průzkumu Enterprise

V tomto kurzu se dozvíte, jak integrovat IBM Kenexa průzkumu organizace Azure Active Directory (Azure AD).

IBM Kenexa průzkumu podnikové integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do firemní IBM Kenexa zjišťování sítě.
- Můžete povolit uživatelům přihlásit se automaticky na IBM Kenexa průzkumu Enterprise pomocí jednotného přihlašování (SSO) s jejich účty Azure AD.
- Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Pokud chcete získat další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IBM Kenexa průzkumu Enterprise, budete potřebovat následující položky:

- Předplatné Azure AD
- Předplatné podporující IBM Kenexa průzkumu podnikové jednotné přihlašování

> [!NOTE]
> Při testování kroky v tomto kurzu doporučujeme nepoužívejte produkčním prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání organizace průzkumu Kenexa IBM z Galerie
* Konfigurace a testování jednotného přihlašování k Azure AD

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Přidání organizace průzkumu Kenexa IBM z Galerie
Pokud chcete nakonfigurovat integraci IBM Kenexa průzkumu organizace do služby Azure AD, přidejte z Galerie na váš seznam spravovaných aplikací SaaS IBM Kenexa průzkumu Enterprise.

Přidání organizace průzkumu Kenexa IBM z galerie, postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně klikněte **Azure Active Directory** tlačítko. 

    ![Tlačítko Azure Active Directory][1]

1. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **IBM Kenexa průzkumu Enterprise**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

1. V seznamu výsledků vyberte **IBM Kenexa průzkumu Enterprise**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![IBM Kenexa průzkumu Enterprise v seznamu výsledků](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat jednotné přihlašování služby Azure AD IBM Kenexa průzkumu Enterprise na základě testovací uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje k identifikaci uživatele protějšek IBM Kenexa průzkumu Enterprise ve službě Azure AD. Jinými slovy Azure AD, musíte vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatelské v IBM Kenexa průzkumu Enterprise.

K navázání vztahu odkazu, přiřaďte hodnotu **uživatelské jméno** v IBM Kenexa průzkumu Enterprise jako hodnotu **uživatelské jméno** ve službě Azure AD.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování služby Azure AD IBM Kenexa průzkumu Enterprise, proveďte stavební bloky v následujících dvou částech.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

V této části Povolení jednotného přihlašování Azure AD na webu Azure Portal a konfigurace jednotného přihlašování v aplikaci IBM Kenexa průzkumu Enterprise následujícím způsobem:

1. Na webu Azure Portal na **IBM Kenexa průzkumu Enterprise** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![IBM Kenexa průzkumu Enterprise nakonfigurovat jednotné přihlašování odkaz][4]

1. V **jednotného přihlašování** v dialogu **režimu** vyberte **přihlašování na základě SAML** pro povolení jednotného přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

1. V **IBM Kenexa průzkumu podnikové domény a adresy URL** části, proveďte následující kroky:

    ![IBM Kenexa průzkumu podnikové domény a adresy URL jednotného přihlašování – informace](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL s následujícím vzorem: `https://surveys.kenexa.com/<companycode>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL s následujícím vzorem: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečný. Aktualizujte identifikátor skutečné a adresa URL odpovědi. A získat tak skutečné hodnoty, obraťte se [tým podpory IBM Kenexa průzkumu Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

1. V části **podpisový certifikát SAML**, klikněte na tlačítko **certifikát (Base64)** a potom uložte certifikát, který do vašeho počítače.

    ![Odkaz ke stažení certifikát (Base64)](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa průzkumu podniková aplikace očekává kontrolní výrazy zabezpečení kontrolní výrazy SAML (Markup Language) v určitém formátu, který je potřeba přidat mapování vlastních atributů ke konfiguraci vaší atributy tokenu SAML. Hodnota deklarace identity identifikátor uživatele v odpovědi musí odpovídat ID jednotného přihlašování, který je nakonfigurovaný v Kenexa systému. Pokud chcete namapovat identifikátor odpovídajícího uživatele ve vaší organizaci jako jednotné přihlašování Internet Datagram Protocol (IDP), pracovat s [tým podpory IBM Kenexa průzkumu Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    Ve výchozím nastavení Azure AD Nastaví uživatelský identifikátor jako hodnotu hlavního názvu (UPN) uživatele. Tuto hodnotu můžete změnit na **atribut** kartu, jak je znázorněno na následujícím snímku obrazovky. Integrace funguje pouze po dokončení mapování správně.
    
    ![Dialogové okno atributy uživatele](./media/kenexasurvey-tutorial/tutorial_attribute.png) 

1. Klikněte na **Uložit**.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/kenexasurvey-tutorial/tutorial_general_400.png)

1. Otevřete **nakonfigurovat přihlašování** okně v části **IBM Kenexa průzkumu podniková konfigurace**, klikněte na tlačítko **konfigurace IBM Kenexa průzkumu Enterprise**. 
 
    ![Konfigurace zjišťování Enterprise Kenexa IBM odkaz](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

1. Kopírovat **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** hodnoty z **Stručná referenční příručka** oddílu.

1. V **nakonfigurovat přihlašování** okně v části **Stručná referenční příručka**, kopie **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** hodnoty.

1. Konfigurace jednotného přihlašování na **IBM Kenexa průzkumu Enterprise** straně, odeslat na stažený **certifikát (Base64)**, **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** hodnoty [tým podpory IBM Kenexa průzkumu Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Můžete se podívat do stručné verzi těchto pokynů v [webu Azure portal](https://portal.azure.com) při nastavení aplikace. Po přidání aplikace ze **služby Active Directory** > **podnikové aplikace** jednoduše klikněte na **jednotného přihlašování** kartu a potom přejdete dokumentace ke službě prostřednictvím Embedded **konfigurace** na konci. Další informace o funkci vložený dokumentaci najdete v tématu [dokumentace ke službě Azure AD embedded](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
V této části můžete vytvořit testovacího uživatele Britta Simon na webu Azure Portal následujícím způsobem:

![Vytvořit testovacího uživatele Azure AD][100]

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/kenexasurvey-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/kenexasurvey-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.
 
    ![Tlačítko Přidat](./media/kenexasurvey-tutorial/create_aaduser_03.png) 

1. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/kenexasurvey-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Vytvořit testovacího uživatele IBM Kenexa průzkumu Enterprise

V této části vytvořte uživatele Britta Simon v IBM Kenexa průzkumu Enterprise. 

Vytvořit uživatele v systému IBM Kenexa průzkumu Enterprise a mapování ID jednotného přihlašování pro ně, můžete pracovat [tým podpory IBM Kenexa průzkumu Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Tato hodnota ID jednotné přihlašování musí být také mapováno na hodnotu identifikátoru uživatele ze služby Azure AD. Toto výchozí nastavení můžete změnit na **atribut** kartu.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon k udělení přístupu k podnikové průzkumu Kenexa IBM použití jednotného přihlašování k Azure.

![Přiřazení role uživatele][200] 

Přiřadit uživatele Britta Simon IBM Kenexa průzkumu organizace, postupujte takto:

1. Na webu Azure Portal, otevřete **aplikací** zobrazení, přejděte na **Directory** zobrazit, vyberte možnost **podnikové aplikace**a potom klikněte na tlačítko **všechny aplikace** .

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

1. V **aplikací** seznamu vyberte **IBM Kenexa průzkumu Enterprise**.

    ![IBM Kenexa průzkumu Enterprise odkaz v seznamu aplikací](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

1. V levém podokně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Klikněte na tlačítko **přidat** tlačítko a pak na **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** v dialogu **uživatelé** seznamu vyberte **Britta Simon**.

1. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko.

1. V **přidat přiřazení** dialogové okno, klikněte na tlačítko **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části testování konfigurace jednotného přihlašování k Azure AD s použitím na přístupovém panelu.

Když kliknete **IBM Kenexa průzkumu Enterprise** dlaždici na přístupovém panelu, které by měl být automaticky přihlášeni k své IBM Kenexa průzkumu podnikové aplikace.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/kenexasurvey-tutorial/tutorial_general_203.png

 
