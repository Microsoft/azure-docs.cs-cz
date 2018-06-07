---
title: 'Kurz: Azure Active Directory integrace s Adobe Creative cloudu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Creative cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeedes
ms.openlocfilehash: 53513b2d07f75e917845ad307a2833a5149ab677
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590007"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Kurz: Azure Active Directory integrace s Adobe Creative cloudu

V tomto kurzu zjistěte, jak integrovat Adobe Creative cloudu s Azure Active Directory (Azure AD).

Integrace Adobe Creative cloudu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup do cloudu tvůrčí Adobe.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Adobe Creative cloudu (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Adobe Creative cloudu, potřebujete následující položky:

- Předplatné služby Azure AD
- Adobe Creative cloudu jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Adobe Creative cloudu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Přidání Adobe Creative cloudu z Galerie
Při konfiguraci Integrace Adobe Creative cloudu do služby Azure AD, potřebujete přidat Adobe Creative cloudu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Adobe Creative cloudu z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Adobe Creative cloudu**, vyberte **Adobe Creative cloudu** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Adobe Creative cloudu v seznamu výsledků](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe Creative cloudu podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v cloudu tvůrčí Adobe je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v cloudu tvůrčí Adobe musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Adobe Creative cloudu, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Adobe Creative cloudu](#create-an-adobe-creative-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Adobe Creative cloudu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Adobe Creative cloudu.

**Ke konfiguraci Azure AD jednotné přihlašování s Adobe Creative cloudu, proveďte následující kroky:**

1. Na portálu Azure na **Adobe Creative cloudu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. Na **Adobe Creative cloudové domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu rozšíření IDP iniciované:

    ![Adobe Creative cloudové domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.okta.com/saml2/service-provider/<token>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory Adobe Creative klient Cloud](https://helpx.adobe.com/in/contact/support.html) k získání těchto hodnot.

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Adobe Creative cloudové domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte hodnotu jako: `https://adobe.com`

5. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative cloudových aplikací očekává SAML kontrolní výrazy ve specifickém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z **atribut uživatele** aplikace. Následující snímek obrazovky ukazuje příklad pro tento.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | ---------------| ----------------|
    | FirstName |user.givenname |
    | Příjmení |user.surname |
    | Email |User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d. Klikněte na tlačítko **OK**.

    > [!NOTE]
    > Uživatelé musí mít Office 365 ExO platnou licenci pro e-mailu hodnoty poskytovanými v odpověď SAML deklarace identity.

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)

9. Na **Adobe Creative konfigurace cloudu** klikněte na tlačítko **konfigurace cloudu tvůrčí Adobe** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části**.

    ![Konfigurace Adobe Creative cloudu](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)

10. V okně prohlížeče jiný web, přihlaste se do [konzoly pro správu Adobe](https://adminconsole.adobe.com) jako správce.

11. Přejděte na **nastavení** v horním navigačním panelu a potom zvolte **Identity**. Otevře se seznam domén. Klikněte na tlačítko **konfigurace** odkaz vůči vaší doméně. Potom proveďte následující kroky na **jeden znak na konfigurace požadované** části. Další informace najdete v tématu [nastavení domény](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Nastavení](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "nastavení")

    a. Klikněte na tlačítko **Procházet** na kterou odešlete certifikát stažený z Azure AD **IDP certifikát**.

    b. V **IDP vystavitele** textovému poli, vložte hodnotu **SAML Entity Id** který jste zkopírovali ze **konfigurovat přihlášení** části na portálu Azure.

    c. V **IDP přihlašovací adresa URL** textovému poli, vložte hodnotu **adresa URL služby Jednotné přihlašování SAML** který jste zkopírovali ze **konfigurovat přihlášení** části na portálu Azure.

    d. Vyberte **HTTP - přesměrování** jako **IDP vazby**.

    e. Vyberte **e-mailová adresa** jako **nastavení přihlášení uživatele**.

    f. Klikněte na tlačítko **Uložit** tlačítko.

12. Řídicí panel bude nyní k dispozici soubor XML **"Stáhnout Metadata"** souboru. Obsahuje EntityDescriptor adresy URL a adresy URL AssertionConsumerService na Adobe. Otevřete soubor a nakonfigurujete je v aplikaci Azure AD.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Použijte hodnotu EntityDescriptor Adobe zadaný pro **identifikátor** na **nakonfigurovat nastavení aplikace** dialogové okno.

    b. Použijte hodnotu AssertionConsumerService Adobe zadaný pro **adresa URL odpovědi** na **nakonfigurovat nastavení aplikace** dialogové okno.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Vytvořit testovací uživatele s Adobe Creative cloudu

Pokud chcete povolit uživatelům Azure AD přihlášení do cloudu tvůrčí Adobe, musí být zřízená do Adobe Creative cloudu. V případě Adobe Creative cloudu zřizování je ruční úloha.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Ke zřízení uživatelských účtů, proveďte následující kroky:

1. Přihlaste se k [konzoly pro správu Adobe](https://adminconsole.adobe.com) lokality jako správce.

2. Přidejte uživatele v rámci konzoly na Adobe jako federované ID a přiřadit profil produktu. Podrobné informace o přidávání uživatelů naleznete v tématu [přidat uživatele v konzole pro správu Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. V tuto chvíli zadejte e-mailovou adresu nebo upn do formuláře přihlášení Adobe, stisknutím klávesy tab a jste měli federovaný zpět do Azure AD:
    * Webový přístup: www.adobe.com > přihlášení
    * V rámci nástroje aplikace na ploše > přihlášení
    * V aplikaci > Nápověda > přihlášení

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Adobe Creative cloudu.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon Adobe Creative cloudu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Adobe Creative cloudu**.

    ![Odkaz Adobe Creative cloudu v seznamu aplikací](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Adobe Creative cloudu na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Adobe Creative cloudu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Nastavení domény (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Konfigurace Azure pro použití s Adobe jednotné přihlašování (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png
