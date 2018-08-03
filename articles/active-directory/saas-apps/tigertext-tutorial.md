---
title: 'Kurz: Integrace Azure Active Directory s TigerText zabezpečení Messenger | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TigerText zabezpečení Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: e74a1a17638a3d968f2216950f6310205fce86f5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422480"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Kurz: Integrace Azure Active Directory s TigerText zabezpečení Messenger

V tomto kurzu se dozvíte, jak integrovat Messenger TigerText zabezpečení Azure Active Directory (Azure AD).

Integrace zabezpečení Messenger TigerText s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke službě Messenger zabezpečení TigerText
- Uživatele, aby automaticky získat přihlášení k TigerText zabezpečení Messenger (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TigerText zabezpečení Messenger, potřebujete následující položky:

- S předplatným služby Azure AD
- Zabezpečení Messenger TigerText jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání zabezpečení Messenger TigerText z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-tigertext-secure-messenger-from-the-gallery"></a>Přidání zabezpečení Messenger TigerText z Galerie
Pokud chcete nakonfigurovat integraci Messenger TigerText zabezpečení do služby Azure AD, budete muset TigerText zabezpečení Messenger přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Pro přidání zabezpečení Messenger TigerText z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **TigerText zabezpečení Messenger**vyberte **TigerText zabezpečení Messenger** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Přidat z Galerie](./media/tigertext-tutorial/tutorial_tigertext_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Messenger TigerText zabezpečení na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatele ve službě Messenger zabezpečení TigerText je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě Messenger zabezpečení TigerText potřeba navázat.

V zabezpečení Messenger TigerText přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Messenger TigerText zabezpečení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele TigerText zabezpečení Messenger](#create-a-tigertext-secure-messenger-test-user)**  – Pokud chcete mít protějšek Britta Simon ve TigerText zabezpečené službě Messenger, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci TigerText zabezpečení Messenger.

**Ke konfiguraci Azure AD jednotné přihlašování s TigerText zabezpečení Messenger, proveďte následující kroky:**

1. Na webu Azure Portal na **TigerText zabezpečení Messenger** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Přihlašování založené na SAML](./media/tigertext-tutorial/tutorial_tigertext_samlbase.png)

1. Na **TigerText zabezpečení Messenger domény a adresy URL** části, proveďte následující kroky:

    ![Část TigerText zabezpečení Messenger domény a adresy URL](./media/tigertext-tutorial/tutorial_tigertext_url.png)

    a. V **přihlašovací adresa URL** textového pole zadat adresu URL jako: `https://home.tigertext.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://saml-lb.tigertext.me/v1/organization/<instance Id>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné identifikátor. Kontakt [tým podpory klient Messenger zabezpečení TigerText](mailTo:prosupport@tigertext.com) tuto výhodu získáte. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Části podpisový certifikát SAML](./media/tigertext-tutorial/tutorial_tigertext_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/tigertext-tutorial/tutorial_general_400.png)

1. Získat jednotné přihlašování nakonfigurované pro vaši aplikaci, obraťte se na [tým podpory zabezpečení Messenger TigerText](mailTo:prosupport@tigertext.com) a poskytování přesných **stažené metadat**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/tigertext-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Všichni uživatelé -> uživatele a skupiny](./media/tigertext-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Přidání tlačítka](./media/tigertext-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/tigertext-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-tigertext-secure-messenger-test-user"></a>Vytvoření zkušebního uživatele TigerText zabezpečení Messenger

V této části vytvoříte uživatele v TigerText jako Britta Simon. Kontaktujte prosím [tým podpory klient Messenger zabezpečení TigerText](mailTo:prosupport@tigertext.com) přidat uživatele na platformě TigerText.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke službě Messenger zabezpečení TigerText.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit TigerText zabezpečení Messenger, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **TigerText zabezpečení Messenger**.

    ![TigerText zabezpečení Messenger v seznamu aplikací](./media/tigertext-tutorial/tutorial_tigertext_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TigerText na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci TigerText. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tigertext-tutorial/tutorial_general_01.png
[2]: ./media/tigertext-tutorial/tutorial_general_02.png
[3]: ./media/tigertext-tutorial/tutorial_general_03.png
[4]: ./media/tigertext-tutorial/tutorial_general_04.png

[100]: ./media/tigertext-tutorial/tutorial_general_100.png

[200]: ./media/tigertext-tutorial/tutorial_general_200.png
[201]: ./media/tigertext-tutorial/tutorial_general_201.png
[202]: ./media/tigertext-tutorial/tutorial_general_202.png
[203]: ./media/tigertext-tutorial/tutorial_general_203.png

