---
title: 'Kurz: Azure Active Directory integrace s interaktivní příručky Yonyx | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a interaktivní příručky Yonyx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.openlocfilehash: 2dd2c20131df082a80a3be4a9ab7d1cac3c91691
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901669"
---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Kurz: Azure Active Directory integrace s Yonyx interaktivní příručky

V tomto kurzu zjistěte, jak integrovat Yonyx interaktivní Průvodce službou Azure Active Directory (Azure AD).

Integrace Yonyx interaktivní příručky s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vodítkům interaktivní Yonyx
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k vodítkům interaktivní Yonyx (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí Yonyx interaktivní příruček, potřebujete následující položky:

- Předplatné služby Azure AD
- Interaktivní příručky Yonyx jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání příručky interaktivní Yonyx z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Přidání příručky interaktivní Yonyx z Galerie
Při konfiguraci integrace interaktivní příručky Yonyx do služby Azure AD, potřebujete přidat interaktivní příručky Yonyx z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat interaktivní příručky Yonyx z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **interaktivní příručky Yonyx**, vyberte **interaktivní příručky Yonyx** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Interaktivní příručky Yonyx v seznamu výsledků](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Yonyx interaktivní příručky podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v příručkách interaktivní Yonyx je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v příručkách interaktivní Yonyx musí navázat.

V Yonyx interaktivní příručky, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Yonyx interaktivní příruček, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele interaktivní příručky Yonyx](#create-a-yonyx-interactive-guides-test-user)**  – Pokud chcete mít protějšek Britta Simon v příručkách interaktivní Yonyx, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Yonyx interaktivní příručky.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Yonyx interaktivní příruček, proveďte následující kroky:**

1. Na portálu Azure na **interaktivní příručky Yonyx** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_samlbase.png)

3. Na **Yonyx interaktivní příručky domény a adresy URL** část, proveďte následující kroky:

    ![Yonyx interaktivní příručky domény a adresy URL jednotné přihlašování informace](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.yonyx.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Yonyx interaktivní příručky klienta](mailto:support@yonyx.com) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/yonyx-tutorial/tutorial_general_400.png)

6. Na **Yonyx interaktivní příručky konfigurace** klikněte na tlačítko **konfigurace interaktivní příručky Yonyx** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Yonyx interaktivní příručky](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_configure.png) 

7. Konfigurace jednotného přihlašování na **Yonyx interaktivní příručky** straně, budete muset odeslat stažený **Certificate(Base64)**, **Sign-Out URL**, **SAML jeden přihlašování adresa URL služby** **SAML Entity ID** k [tým podpory interaktivní příručky Yonyx](mailto:support@yonyx.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

  ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/yonyx-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/yonyx-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Tlačítko Přidat](./media/yonyx-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/yonyx-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-yonyx-interactive-guides-test-user"></a>Vytvoření zkušebního uživatele Yonyx interaktivní příručky

Cílem této části je vytvoření uživatele volal Britta Simon v Yonyx interaktivní příručky. Interaktivní příručky Yonyx podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Yonyx interaktivní příručky, pokud ještě neexistuje.

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat tým podpory interaktivní příručky Yonyx prostřednictvím <mailto:support@yonyx.com>. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k vodítkům interaktivní Yonyx.

![Přiřadit role uživatele][200]

**Pokud chcete přiřadit Britta Simon Yonyx interaktivní příručky, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Yonyx interaktivní příručky**.

    ![Interaktivní příručky Yonyx odkaz v seznamu aplikací](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici interaktivní příručky Yonyx na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Yonyx interaktivní příručky.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yonyx-tutorial/tutorial_general_01.png
[2]: ./media/yonyx-tutorial/tutorial_general_02.png
[3]: ./media/yonyx-tutorial/tutorial_general_03.png
[4]: ./media/yonyx-tutorial/tutorial_general_04.png

[100]: ./media/yonyx-tutorial/tutorial_general_100.png

[200]: ./media/yonyx-tutorial/tutorial_general_200.png
[201]: ./media/yonyx-tutorial/tutorial_general_201.png
[202]: ./media/yonyx-tutorial/tutorial_general_202.png
[203]: ./media/yonyx-tutorial/tutorial_general_203.png

