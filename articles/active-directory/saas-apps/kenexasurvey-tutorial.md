---
title: 'Kurz: Azure Active Directory integrace s IBM Kenexa průzkum Enterprise | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IBM Kenexa průzkum Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: bca5d5111f5cfc3bcf2720af855f44d1928bcfd6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221012"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Kurz: Azure Active Directory integrace s IBM Kenexa průzkum Enterprise

V tomto kurzu zjistěte, jak integrovat IBM Kenexa průzkum Enterprise s Azure Active Directory (Azure AD).

Integrace IBM Kenexa průzkum Enterprise s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do firemní IBM Kenexa zjišťování sítě.
- Můžete povolit uživatelům automaticky se přihlaste k IBM Kenexa průzkum Enterprise pomocí jednotného přihlašování (SSO) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě: portál Azure.

Pokud chcete získat další informace o softwaru jako integraci aplikace služby (SaaS) s Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IBM Kenexa průzkum Enterprise, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné služby IBM Kenexa průzkum Enterprise jednotného přihlašování

> [!NOTE]
> Při testování kroky v tomto kurzu, doporučujeme vám, nepoužívejte provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu Azure AD jednotného přihlašování k testování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání IBM Kenexa průzkum Enterprise z Galerie
* Konfigurace a testování Azure AD SSO

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Přidat IBM Kenexa průzkum Enterprise z Galerie
Při konfiguraci integrace systému IBM Kenexa průzkum Enterprise do služby Azure AD, přidejte IBM Kenexa průzkum Enterprise z Galerie si na seznam spravovaných aplikací SaaS.

Pokud chcete přidat IBM Kenexa průzkum Enterprise z galerie, postupujte takto:

1. V [portál Azure](https://portal.azure.com), v levém podokně klikněte **Azure Active Directory** tlačítko. 

    ![Tlačítko Azure Active Directory][1]

2. Vyberte **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **IBM Kenexa průzkum Enterprise**.

    ![Vytváření testovacího uživatele Azure AD](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Vyberte v seznamu výsledků **IBM Kenexa průzkum Enterprise**a klikněte **přidat** tlačítko Přidat aplikaci.

    ![IBM Kenexa průzkum Enterprise v seznamu výsledků](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD přihlášení SSO se IBM Kenexa průzkum Enterprise podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí identifikovat příslušného uživatele IBM Kenexa průzkum Enterprise ve službě Azure AD. Jinými slovy Azure AD potřeba vytvořit vztah propojení mezi uživatele Azure AD a související uživatelské ve IBM Kenexa průzkum podniku.

K navázání vztahu odkaz, přiřadit hodnotu **uživatelské jméno** ve IBM Kenexa průzkum podniku jako hodnotu **uživatelské jméno** ve službě Azure AD.

Nakonfigurovat a otestovat Azure AD přihlášení SSO se IBM Kenexa průzkum Enterprise, dokončete stavebních bloků v následujících dvou částech.

### <a name="configure-azure-ad-sso"></a>Konfigurovat Azure AD jednotného přihlašování

V této části Povolení jednotného přihlašování Azure AD na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci IBM Kenexa průzkum Enterprise následujícím způsobem:

1. Na portálu Azure na **IBM Kenexa průzkum Enterprise** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![IBM Kenexa průzkum Enterprise nakonfigurovat jeden přihlašování odkaz][4]

2. V **jednotného přihlašování** v dialogovém **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. V **IBM Kenexa průzkum podnikové domény a adresy URL** část, proveďte následující kroky:

    ![IBM Kenexa průzkum podnikové domény a adresy URL jednotné přihlašování informace](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL s vzoru následující: `https://surveys.kenexa.com/<companycode>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL s vzoru následující: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > Předchozí hodnoty nejsou skutečné. Je aktualizovat skutečným identifikátorem a adresa URL odpovědi. Chcete-li získat skutečnými hodnotami, obraťte se [tým podpory IBM Kenexa průzkum Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

4. V části **SAML podpisový certifikát**, klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu do počítače.

    ![Odkaz ke stažení certifikátu (Base64)](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa průzkum podniková aplikace očekává kontrolní výrazy zabezpečení kontrolní výrazy Markup Language (SAML) v určitém formátu, který vyžaduje, můžete přidat mapování vlastních atributů ke konfiguraci vaší atributů tokenu SAML. Hodnota deklarace identity identifikátor uživatele v odpovědi musí odpovídat ID jednotné přihlašování, který je nakonfigurovaný v Kenexa systému. Pro mapování identifikátor příslušné uživatele ve vaší organizaci jako jednotného přihlašování k Internetu Datagram Protocol (IDP), pracovat [tým podpory IBM Kenexa průzkum Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    Ve výchozím nastavení Azure AD Nastaví uživatelský identifikátor jako hodnotu uživatelského jména (UPN). Tuto hodnotu můžete změnit na **atribut** kartě, jak je znázorněno na následujícím snímku obrazovky. Integrace funguje pouze po dokončení mapování správně.
    
    ![Dialogové okno atributy uživatele](./media/kenexasurvey-tutorial/tutorial_attribute.png) 

5. Klikněte na **Uložit**.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/kenexasurvey-tutorial/tutorial_general_400.png)

6. Otevřete **konfigurovat přihlášení** okno, v části **IBM Kenexa průzkum podniková konfigurace**, klikněte na tlačítko **konfigurace IBM Kenexa průzkum Enterprise**. 
 
    ![Odkaz Konfigurace IBM Kenexa průzkum Enterprise](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Kopírování **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašovací adresa URL služby** hodnoty z **Stručná referenční příručka** části.

8. V **konfigurovat přihlášení** okno, v části **Stručná referenční příručka**, kopie **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašovací adresa URL služby** hodnoty.

9. Konfigurace jednotného přihlašování na **IBM Kenexa průzkum Enterprise** straně, odesílat stažené **certifikátu (Base64)**, **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašovací adresa URL služby** hodnoty k [tým podpory IBM Kenexa průzkum Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Můžete se podívat do stručným verzi tyto pokyny v [portál Azure](https://portal.azure.com) při k nastavení aplikace. Po přidání aplikace z **služby Active Directory** > **podnikové aplikace, které** jednoduše klikněte na položku **jednotného přihlašování** kartě a potom přejdete na vložené dokumentace prostřednictvím **konfigurace** na konci. Další informace o funkci embedded dokumentaci najdete v tématu [Azure AD vložených dokumentaci](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
V této části vytvoříte testovacího uživatele Britta Simon na portálu Azure, následujícím způsobem:

![Vytvořit testovací uživatele Azure AD][100]

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/kenexasurvey-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/kenexasurvey-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.
 
    ![Tlačítko Přidat](./media/kenexasurvey-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/kenexasurvey-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Vytvořit testovací uživatele s IBM Kenexa průzkum Enterprise

V této části vytvoříte uživatele volal Britta Simon v IBM Kenexa průzkum Enterprise. 

K vytvoření uživatele v systému IBM Kenexa průzkum Enterprise a mapování ID jednotného přihlašování pro ně, můžete pracovat [tým podpory IBM Kenexa průzkum Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Tato hodnota ID jednotné přihlašování musí taky namapovaný na hodnota identifikátoru uživatele z Azure AD. Toto výchozí nastavení můžete změnit na **atribut** kartě.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotného přihlašování k Azure tak, že udělíte přístup do firemní IBM Kenexa zjišťování sítě.

![Přiřadit role uživatele][200] 

Pokud chcete přiřadit uživatele Britta Simon IBM Kenexa průzkum Enterprise, postupujte takto:

1. Na portálu Azure otevřete **aplikace** zobrazení, přejděte na **Directory** zobrazit, vyberte možnost **podnikové aplikace, které**a potom klikněte na **všechny aplikace**.

    !["Podnikové aplikace" a "Všechny aplikace" odkazy][201] 

2. V **aplikace** seznamu, vyberte **IBM Kenexa průzkum Enterprise**.

    ![IBM Kenexa průzkum Enterprise odkaz v seznamu aplikací](./media/kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. V levém podokně klikněte na **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Klikněte na tlačítko **přidat** tlačítko a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** v dialogovém **uživatelé** seznamu, vyberte **Britta Simon**.

6. V **uživatelů a skupin** dialogové okno, klikněte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, klikněte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat konfiguraci Azure AD jednotného přihlašování pomocí přístupového panelu.

Když kliknete **IBM Kenexa průzkum Enterprise** dlaždici na přístupovém panelu můžete by měl být automaticky přihlášeni k vaší aplikaci IBM Kenexa průzkum Enterprise.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

 
