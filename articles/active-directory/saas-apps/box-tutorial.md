---
title: 'Kurz: Integrace Azure Active Directory s polem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pole.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 535f59b1b0dc56b183c8a019d101b4fd4f1bfad6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116121"
---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>Kurz: Integrace Azure Active Directory s poli

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) pole.

Pole integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k poli.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k poli (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s polem, potřebujete následující položky:

- Předplatné Azure AD
- Do pole jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání pole z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-box-from-the-gallery"></a>Přidání pole z Galerie
Pokud chcete nakonfigurovat integraci pole do služby Azure AD, budete muset přidat pole do seznamu spravovaných aplikací SaaS z galerie.

**Pokud chcete přidat pole z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/box-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/box-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/box-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **pole**vyberte **pole** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/box-tutorial/tutorial_Box_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí pole na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek pole je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v poli je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s polem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele pole](#create-a-box-test-user)**  – Pokud chcete mít protějšek Britta Simon pole, které souvisí s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci pole.

**Ke konfiguraci Azure AD jednotné přihlašování s polem, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **pole** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/box-tutorial/b1_b2_select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/box-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/box-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.account.box.com`

    b. V **identifikátor** textového pole zadejte adresu URL:`box.net`

    ![image](./media/box-tutorial/tutorial_box_url.png)

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [pole tým podpory](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) má být získána hodnota.
 
5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  a uložte ho do počítače.

    ![image](./media/box-tutorial/tutorial_Box_certificate.png)

6. Pokud chcete nakonfigurovat jednotné přihlašování pro vaši aplikaci, postupujte podle pokynů v [nastavit jednotné přihlašování na vlastní](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown). 

>[!NOTE]
>Pokud se nemůžete ke konfiguraci nastavení jednotného přihlašování pro svůj účet boxu, budete muset odeslat na stažený **kód XML metadat federace** k [pole tým podpory](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![image](./media/box-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/box-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/box-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-box-test-user"></a>Vytvoření zkušebního uživatele pole

Cílem této části je vytvořte uživatele Britta Simon pole. Pole podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k poli, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [pole tým podpory](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k poli.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/box-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **pole**.

    ![image](./media/box-tutorial/tutorial_Box_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/box-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/box-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete **pole** dlaždici na přístupovém panelu, zobrazí se přihlašovací stránku pro přihlášení k aplikaci Box.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](box-userprovisioning-tutorial.md)


