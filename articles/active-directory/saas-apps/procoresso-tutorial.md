---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Procore | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Procore jednotného přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 871fde799f064f52b11b75e190b9b7c8a73a843d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445197"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Procore

V tomto kurzu se dozvíte, jak integrovat Procore jednotného přihlašování s Azure Active Directory (Azure AD).

Integrace Procore jednotného přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Procore jednotného přihlašování
- Uživatele, aby automaticky získat přihlášení k Procore jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Procore, potřebujete následující položky:

- S předplatným služby Azure AD
- Procore jednotného přihlašování jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování k Procore z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-procore-sso-from-the-gallery"></a>Přidání jednotného přihlašování k Procore z Galerie
Konfigurace integrace Procore jednotné přihlašování do služby Azure AD, budete muset přidat Procore jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Procore jednotné přihlašování z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Procore jednotného přihlašování**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/procoresso-tutorial/tutorial_procoresso_search.png)

1. Na panelu výsledků vyberte **Procore jednotného přihlašování**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Procore jednotného přihlašování na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Procore jednotného přihlašování je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Procore jednotného přihlašování je potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Procore jednotného přihlašování.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Procore jednotného přihlašování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Procore jednotného přihlašování](#creating-a-procore-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Procore jednotného přihlašování, který je propojený s Azure AD reprezentace jí.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci Procore jednotného přihlašování.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Procore, proveďte následující kroky:**

1. V portálu pro správu Azure na **Procore jednotného přihlašování** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_procoresso_samlbase.png)

1. Na **Procore jednotného přihlašování k doméně a adresy URL** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_procoresso_url.png)

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_general_400.png)

1. Na **Procore Konfigurace jednotného přihlašování** klikněte na tlačítko **konfigurace Procore jednotného přihlašování** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_procoresso_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Procore jednotného přihlašování** straně, přihlaste se k webu procore společnosti jako správce.

1. Ze seznamu nástrojů dolů, klikněte na **správce** otevřete stránku nastavení jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_tool_admin.png)

1. Vložení hodnoty do polí, jak je popsáno níže –

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. V **jednotné přihlašování vystavitele adresa URL** pole, vložte SAML Entity ID zkopírovali z portálu Azure portal.

    b. V **SAML znak v cílové adrese URL** pole, vložit SAML jednotné přihlašování – adresa URL služby zkopírovali z portálu Azure portal.

    c. Nyní otevřete **soubor XML s metadaty** nad stáhnout z webu Azure portal a zkopírovat ověření ve značce s názvem **certifikátu x 509**. Vložte zkopírovaný hodnotu do **Single Sign On x509 certifikát** pole.

1. Klikněte na **uložit změny**.

1. Po nastavení, musí odeslat **název domény** (např. **contoso.com**) prostřednictvím které se přihlašujete do Procore k [tým Procore podpory](https://support.procore.com/) ty budou Aktivujte federovaného jednotného přihlašování pro tuto doménu.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/procoresso-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/procoresso-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/procoresso-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/procoresso-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-procore-sso-test-user"></a>Vytvoření zkušebního uživatele Procore jednotného přihlašování

Postupujte prosím podle následujících kroků vytvořte na své straně Procore testovacího uživatele.

1. Přihlaste se k webu procore společnosti jako správce.  

1. Ze seznamu nástrojů dolů, klikněte na **Directory** otevřete stránku directory společnosti.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_sso_directory.png)

1. Klikněte na **přidat uživatele** provádět možnost otevřete formulář a zadejte následující možnosti -

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_add.png)

    a. V **křestní jméno** textového pole jméno typ uživatele jako **Britta**.

    b. V **příjmení** textové pole, příjmení typ uživatele jako **Simon**.

    c. V **e-mailovou adresu** , jako je textové pole, typ uživatele e-mailovou adresu **BrittaSimon@contoso.com**.

    d. Vyberte **šablona oprávnění** jako **později použít šablonu oprávnění**.

    e. Klikněte na možnost **Vytvořit**.

1. Zkontrolujte a aktualizujte podrobnosti o nově přidaných kontaktu.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_check.png)

1. Klikněte na **uložit a odeslat Invitiation** (Pokud pozvání prostřednictvím e-mailu se vyžaduje) nebo **Uložit** (uložit přímo) k dokončení registrace uživatele.
    
    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_save.png)  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Procore jednotného přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Procore jednotného přihlašování, postupujte následovně:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Procore jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_procoresso_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu. Další podrobnosti o přístupový Panel najdete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). Po kliknutí na dlaždici Procore jednotného přihlašování na přístupovém panelu, které by měl získat automaticky přihlášení k Procore jednotného přihlašování aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/procoresso-tutorial/tutorial_general_01.png
[2]: ./media/procoresso-tutorial/tutorial_general_02.png
[3]: ./media/procoresso-tutorial/tutorial_general_03.png
[4]: ./media/procoresso-tutorial/tutorial_general_04.png

[100]: ./media/procoresso-tutorial/tutorial_general_100.png

[200]: ./media/procoresso-tutorial/tutorial_general_200.png
[201]: ./media/procoresso-tutorial/tutorial_general_201.png
[202]: ./media/procoresso-tutorial/tutorial_general_202.png
[203]: ./media/procoresso-tutorial/tutorial_general_203.png

