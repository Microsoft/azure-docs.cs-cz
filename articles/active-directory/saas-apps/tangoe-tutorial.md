---
title: 'Kurz: Azure Active Directory integrace s Tangoe příkaz Premium Mobile | Microsoft Docs'
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
ms.openlocfilehash: c87b90e82b1300e147533256e3286421a73f08d2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218890"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Kurz: Azure Active Directory integrace s Tangoe příkaz Premium Mobile

V tomto kurzu zjistíte integrace Tangoe příkaz Premium mobilní služby Azure Active Directory (Azure AD).

Integrace Mobile Premium příkaz Tangoe s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Tangoe příkaz Premium Mobile
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Tangoe příkaz Premium Mobile (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tangoe příkaz Premium Mobile, potřebujete následující položky:

- Předplatné služby Azure AD
- Tangoe příkaz Premium Mobile jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mobile Premium Tangoe příkaz z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Přidání Mobile Premium Tangoe příkaz z Galerie
Při konfiguraci integrace Mobile Premium příkaz Tangoe do služby Azure AD potřebujete přidat mobilní Premium Tangoe příkaz z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat mobilní Premium Tangoe příkaz z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tangoe příkaz Premium Mobile**, vyberte **Tangoe příkaz Premium Mobile** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Přidání Mobile Premium Tangoe příkaz z Galerie ](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tangoe příkaz Premium Mobile podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Tangoe příkaz Premium Mobile je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Tangoe příkaz Premium Mobile musí navázat.

V modulu snap-in Mobile Premium příkaz Tangoe přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tangoe příkaz Premium Mobile, musíte dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Mobile Premium příkaz Tangoe](#create-a-tangoe-command-premium-mobile-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tangoe příkaz Premium Mobile, která je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v Tangoe příkaz Premium mobilní aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s Tangoe příkaz Premium Mobile, proveďte následující kroky:**

1. Na portálu Azure na **Tangoe příkaz Premium Mobile** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Přihlašování založené na SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

3. Na **Tangoe příkaz Premium Mobile domény a adresy URL** část, proveďte následující kroky:

    ![Příkaz Tangoe Premium mobilní domény a adresy URL](./media/tangoe-tutorial/tutorial_tangoe_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://sso.tangoe.com/sp/ACS.saml2`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory Tangoe příkaz Premium mobilního klienta](https://www.tangoe.com/contact-2/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Certifikát pro podpis SAML části](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/tangoe-tutorial/tutorial_general_400.png)
    
6. Na **Konfigurace mobilních Premium příkaz Tangoe** klikněte na tlačítko **konfigurace Tangoe příkaz Premium Mobile** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Tangoe příkaz Premium mobilní konfigurační oddíl](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

7. Chcete-li získat jednotné přihlašování, které jsou nakonfigurované pro vaše aplikace, obraťte se na vaše [tým podpory Tangoe příkaz Premium mobilního klienta](https://www.tangoe.com/contact-2/) a zadejte následující:

   - Soubor stažený metadat
   - **SAML Entity ID**
   - **Adresa URL služby jednotného přihlašování SAML**
   - **Odhlášení adresy URL**

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/tangoe-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Uživatelé a skupiny -> všichni uživatelé](./media/tangoe-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Přidání uživatele](./media/tangoe-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Dialogové okno stránka uživatel](./media/tangoe-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Vytvoření zkušebního uživatele Tangoe příkaz Premium Mobile

V této části vytvoříte uživatele názvem Britta Simon v Tangoe příkaz Premium Mobile. 

Tangoe příkaz Premium mobilní aplikace, musí všichni uživatelé zřídit v aplikaci před provedením jednotné přihlašování. Proto prosím práci s [tým podpory Tangoe příkaz Premium mobilního klienta](https://www.tangoe.com/contact-2/) ke zřízení tito uživatelé do aplikace. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Tangoe příkaz Premium Mobile.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Tangoe příkaz Premium Mobile, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tangoe příkaz Premium Mobile**.

    ![Tangoe příkaz Premium Mobile v seznamu aplikací](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete otestovat vaši konfiguraci Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici Tangoe příkaz Premium Mobile na přístupovém panelu, jste měli získat automaticky přihlášení k Tangoe příkaz Premium mobilní aplikace. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

