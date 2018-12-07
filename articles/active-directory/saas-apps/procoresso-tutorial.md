---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Procore | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Procore jednotného přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 12e7a37c1d2811b44dbf943b460b7d620770d45e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017521"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Procore

V tomto kurzu se dozvíte, jak integrovat Procore jednotného přihlašování s Azure Active Directory (Azure AD).

Integrace Procore jednotného přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Procore jednotného přihlašování.
- Uživatele, aby automaticky získat přihlášení k Procore jednotné přihlašování (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Procore, potřebujete následující položky:

- Předplatné Azure AD
- Jednotné přihlašování Procore jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotného přihlašování k Procore z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-procore-sso-from-the-gallery"></a>Přidání jednotného přihlašování k Procore z Galerie

Konfigurace integrace Procore jednotné přihlašování do služby Azure AD, budete muset přidat Procore jednotného přihlašování na váš seznam spravovaných aplikací SaaS z galerie.

**Chcete-li přidat Procore jednotné přihlašování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Procore jednotného přihlašování**vyberte **Procore jednotného přihlašování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Procore jednotné přihlašování v seznamu výsledků](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Procore jednotného přihlašování na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Procore jednotného přihlašování je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Procore jednotného přihlašování je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Procore jednotného přihlašování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Procore jednotného přihlašování](#creating-a-procore-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Procore jednotného přihlašování, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Procore jednotného přihlašování.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Procore, proveďte následující kroky:**

1. Na webu Azure Portal na **Procore jednotného přihlašování** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části uživatel nemá k provedení všech kroků jako aplikace je už předem integrováno s Azure.

    ![Procore jednotného přihlašování k doméně a adresy URL jednotného přihlašování – informace](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Na **nastavit jednotné přihlašování Procore** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace procore jednotného přihlašování](common/configuresection.png)

7. Ke konfiguraci jednotného přihlašování na **Procore jednotného přihlašování** straně, přihlaste se k webu procore společnosti jako správce.

8. Ze seznamu nástrojů dolů, klikněte na **správce** otevřete stránku nastavení jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_tool_admin.png)

9. Vložení hodnoty do polí, jak je popsáno níže –

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. V **jednotné přihlašování vystavitele adresa URL** text vložte hodnotu **Azure AD identifikátor** který jste zkopírovali z portálu Azure portal.

    b. V **SAML znak v cílové adrese URL** vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    c. Nyní otevřete **kód XML metadat federace** nad stáhnout z webu Azure portal a zkopírujte certifikát ve značce s názvem **certifikátu x 509**. Vložte zkopírovaný hodnotu do **Single Sign On x509 certifikát** pole.

10. Klikněte na **uložit změny**.

11. Po nastavení, musí odeslat **název domény** (např. **contoso.com**) prostřednictvím které se přihlašujete do Procore k [tým Procore podpory](https://support.procore.com/) ty budou Aktivujte federovaného jednotného přihlašování pro tuto doménu.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
       Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-procore-sso-test-user"></a>Vytvoření zkušebního uživatele Procore jednotného přihlašování

Postupujte prosím podle následujících kroků vytvořte na straně Procore SSOc Procore testovacího uživatele.

1. Přihlaste se k webu procore společnosti jako správce.  

2. Ze seznamu nástrojů dolů, klikněte na **Directory** otevřete stránku directory společnosti.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klikněte na **přidat uživatele** provádět možnost otevřete formulář a zadejte následující možnosti -

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_add.png)

    a. V **křestní jméno** textového pole jméno typ uživatele jako **Britta**.

    b. V **příjmení** textové pole, příjmení typ uživatele jako **Simon**.

    c. V **e-mailovou adresu** , jako je textové pole, typ uživatele e-mailovou adresu **BrittaSimon@contoso.com**.

    d. Vyberte **šablona oprávnění** jako **později použít šablonu oprávnění**.

    e. Klikněte na možnost **Vytvořit**.

4. Zkontrolujte a aktualizujte podrobnosti o nově přidaných kontaktu.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_check.png)

5. Klikněte na **uložit a odeslat Invitiation** (Pokud pozvání prostřednictvím e-mailu se vyžaduje) nebo **Uložit** (uložit přímo) k dokončení registrace uživatele.
    
    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Procore jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Procore jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Procore jednotného přihlašování na přístupovém panelu, které by měl získat automaticky přihlášení k Procore jednotného přihlašování aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
