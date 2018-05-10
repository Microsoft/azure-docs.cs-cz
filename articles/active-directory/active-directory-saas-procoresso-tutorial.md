---
title: 'Kurz: Azure Active Directory integration Procore jednotného přihlašování k | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Procore jednotné přihlašování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 4cba8aa046b84fe63b17a662990824b1823c1572
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Kurz: Azure Active Directory integrace s Procore jednotného přihlašování

V tomto kurzu zjistěte, jak integrovat Procore jednotné přihlašování s Azure Active Directory (Azure AD).

Integrace Procore jednotné přihlašování s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Procore jednotného přihlašování
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Procore jednotné přihlašování (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu pro správu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Procore jednotné přihlašování, potřebujete následující položky:

- Předplatné služby Azure AD
- Procore SSO jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Provozním prostředí byste neměli používat, pokud je to nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Procore jednotného přihlašování z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-procore-sso-from-the-gallery"></a>Přidání Procore jednotného přihlašování z Galerie
Při konfiguraci integrace Procore přihlašování do služby Azure AD, potřebujete přidat Procore jednotného přihlašování z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Procore jednotného přihlašování z galerie, proveďte následující kroky:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Procore SSO**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. Na panelu výsledků vyberte **Procore SSO**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Procore jednotného přihlašování na základě testovací uživatele, nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku Procore SSO je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Procore SSO musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** Procore sso.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování služby Procore, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Procore SSO](#creating-a-procore-sso-test-user)**  – Pokud chcete mít protějšek Britta Simon Procore SSO propojeném s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotné přihlašování v portálu pro správu Azure a nakonfigurovat jednotné přihlašování v aplikaci Procore jednotné přihlašování.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí jednotného přihlašování služby Procore, proveďte následující kroky:**

1. Na portálu Azure Management portal na **Procore jednotného přihlašování k** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování na.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Na **Procore jednotného přihlašování k doméně a adresy URL** části uživatel nemusí provádět žádné kroky, protože aplikace je už předem integrováno s Azure.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. Na **Procore Konfigurace jednotného přihlašování k** klikněte na tlačítko **Konfigurace jednotného přihlašování k Procore** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Konfigurace jednotného přihlašování na **Procore SSO** straně, přihlášení k serveru vaší společnosti procore jako správce.

8. V rozevíracím sady nástrojů dolů, klikněte na **správce** otevřete stránku nastavení jednotného přihlašování.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Vložení hodnoty do polí, jak je popsáno níže-

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. V **jedné URL přihlašování na vystavitele** pole, vložte SAML Entity ID zkopírovaných z portálu Azure.

    b. V **SAML přihlašovací na cílová adresa URL** pole, vložte SAML jeden přihlašování adresa URL služby zkopírovaných z portálu Azure.

    c. Nyní otevřete **soubor XML s metadaty** výše si stáhli z portálu Azure a kopírovat ve značce s názvem certifikátu **certifikátu x 509**. Vložit zkopírovaný hodnotu do **jednotné přihlašování x509 certifikát** pole.

10. Klikněte na **uložit změny**.

11. Po tato nastavení, musí poslat **název domény** (např **contoso.com**) prostřednictvím které jsou do Procore k protokolování [tým podpory Procore](https://support.procore.com/) a aktivují federované jednotné přihlašování pro tuto doménu.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu správy Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portálu pro správu Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-procore-sso-test-user"></a>Vytvoření zkušebního uživatele Procore jednotného přihlašování

Postupujte níže uvedených pokynů pro vytvoření Procore testovací uživatele na jejich straně.

1. Přihlášení k serveru vaší společnosti procore jako správce.  

2. V rozevíracím sady nástrojů dolů, klikněte na **Directory** chcete otevřít stránku directory společnosti.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Klikněte na **přidat osobu** možnost Otevřít formulář a zadejte provést následující možnosti -

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. V **křestní jméno** textovému poli, křestní jméno uživatele typu jako **Britta**.

    b. V **příjmení** textovému poli, příjmení uživatele typu jako **Simon**.

    c. V **e-mailovou adresu** textovému poli, typu uživatele e-mailovou adresu jako **BrittaSimon@contoso.com**.

    d. Vyberte **šablona oprávnění** jako **později použít šablonu oprávnění**.

    e. Klikněte na možnost **Vytvořit**.

4. Zkontrolovat a aktualizovat podrobnosti nově přidané kontakt.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Klikněte na **uložit a odeslat Invitiation** (Pokud pozvání prostřednictvím e-mailu je vyžadována) nebo **Uložit** (uložit přímo) k dokončení registrace uživatele.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k Procore jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Procore jednotné přihlašování, proveďte následující kroky:**

1. V portálu pro správu Azure, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Procore SSO**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Pokud chcete testovat vaše nastavení jednotného přihlašování, otevřete Panel přístupu. Další podrobnosti o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586). Když kliknete na dlaždici Procore jednotného přihlašování na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Procore jednotné přihlašování.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

