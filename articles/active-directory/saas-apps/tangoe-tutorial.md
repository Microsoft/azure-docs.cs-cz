---
title: 'Kurz: Integrace Azure Active Directory s Tangoe příkaz Premium Mobile | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tangoe příkaz Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 2a477d8e10b9be4aa90cc80341c787facaabc520
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438745"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Kurz: Integrace Azure Active Directory s Tangoe příkaz Premium Mobile

V tomto kurzu se dozvíte, jak integrovat Tangoe příkaz Premium mobilní služby Azure Active Directory (Azure AD).

Integrace Mobile Tangoe příkaz Premium s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Tangoe příkaz Premium Mobile
- Uživatele, aby automaticky získat přihlášení k Tangoe příkaz Premium Mobile (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tangoe příkaz Premium Mobile, potřebujete následující položky:

- S předplatným služby Azure AD
- Mobile Premium příkaz Tangoe jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mobile Premium Tangoe příkaz z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Přidání Mobile Premium Tangoe příkaz z Galerie
Pokud chcete nakonfigurovat integraci Tangoe příkaz Premium mobilní zařízení do služby Azure AD, potřebujete přidat Tangoe příkaz Premium Mobile z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Mobile Premium Tangoe příkaz z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Tangoe příkaz Premium Mobile**vyberte **Tangoe příkaz Premium Mobile** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Přidání Mobile Premium Tangoe příkaz z Galerie ](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Tangoe příkaz Premium mobilní zařízení podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Tangoe příkaz Premium Mobile je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v Mobile Premium Tangoe příkazu je potřeba navázat.

V modulu snap-in Mobile Premium příkaz Tangoe přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování do mobilní Tangoe příkaz Premium, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Tangoe příkaz Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tangoe příkaz Premium Mobile, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Mobile Tangoe příkaz Premium.

**Ke konfiguraci Azure AD jednotné přihlašování s Tangoe příkaz Premium mobilní, proveďte následující kroky:**

1. Na webu Azure Portal na **Tangoe příkaz Premium Mobile** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Přihlašování založené na SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. Na **Tangoe příkaz Premium Mobile domény a adresy URL** části, proveďte následující kroky:

    ![Příkaz Tangoe Premium mobilní domény a adresy URL](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Tangoe příkaz Premium mobilního klienta](https://www.tangoe.com/contact-us/) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Části podpisový certifikát SAML](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. Na **Tangoe příkaz Premium Mobile konfigurace** klikněte na tlačítko **nakonfigurovat mobilní Premium Tangoe příkaz** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Tangoe příkaz Premium mobilní konfigurační oddíl](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, kontaktujte vašeho [tým podpory Tangoe příkaz Premium mobilního klienta](https://www.tangoe.com/contact-us/) a poskytněte mu tyto:

   - Soubor stažený metadat
   - **SAML Entity ID**
   - **Adresu URL služby jednotného přihlašování SAML**
   - **Adresa URL pro odhlášení**

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tangoe-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Všichni uživatelé -> uživatele a skupiny](./media/tangoe-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Přidání uživatele](./media/tangoe-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno stránky uživatele](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Vytvoření zkušebního uživatele Tangoe příkaz Premium Mobile

V této části vytvořte uživatele Britta Simon v Tangoe příkaz Premium Mobile. 

Všichni uživatelé, které se mají zřídit v aplikaci před provedením Single Sign On, musí Tangoe příkaz Premium mobilní aplikace. Proto prosím práci s [tým podpory Tangoe příkaz Premium mobilního klienta](https://www.tangoe.com/contact-us/) zřídit všechny tyto uživatele do aplikace. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Tangoe příkaz Premium Mobile.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Tangoe příkaz Premium Mobile, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Tangoe příkaz Premium Mobile**.

    ![Tangoe příkaz Premium mobilní zařízení v seznamu aplikací](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Tangoe příkaz Premium Mobile na přístupovém panelu, vám by měl získat automaticky přihlášení k Tangoe příkaz Premium mobilní aplikace. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

