---
title: 'Kurz: Azure Active Directory integrace s Splunk Enterprise a Splunk cloudu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Splunk Enterprise a Splunk cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: 8b06680c872f567cef3fd2821e40a814b834c3a3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350572"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Kurz: Azure Active Directory integrace s Splunk Enterprise a Splunk cloudu

V tomto kurzu zjistěte, jak integrovat Splunk Enterprise a Splunk cloudu s Azure Active Directory (Azure AD).

Integrace Splunk Enterprise a Splunk cloudu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k podnikové Splunk a Splunk cloudu
- Můžete povolit uživatelům, aby automaticky získat přihlášeného Splunk Enterprise a Splunk cloudu (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Splunk Enterprise a Splunk cloudu, potřebujete následující položky:

- Předplatné služby Azure AD
- Splunk Enterprise a Splunk cloudu jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Splunk Enterprise a Splunk cloudu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Přidání Splunk Enterprise a Splunk cloudu z Galerie
Při konfiguraci integrace Splunk Enterprise a Splunk cloudu do služby Azure AD, potřebujete přidat Splunk Enterprise a Splunk cloudu z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat Splunk Enterprise a Splunk cloudu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Splunk Enterprise a Splunk cloudu**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_search.png)

5. Na panelu výsledků vyberte **Splunk Enterprise a cloudu Splunk**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotného přihlašování k Splunk organizace a Splunk cloudu podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v Splunk Enterprise a Splunk cloudu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Splunk Enterprise a Splunk cloudu musí navázat.

V Splunk Enterprise a Splunk cloudu přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Splunk Enterprise a Splunk cloudu, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Splunk Enterprise a cloudu Splunk](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**  – Pokud chcete mít protějšku Britta Simon ve Splunk podniku a Splunk cloudu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Splunk Enterprise a Splunk cloudu.

**Ke konfiguraci Azure AD jednotné přihlašování s Splunk Enterprise a Splunk cloudu, proveďte následující kroky:**

1. Na portálu Azure na **Splunk Enterprise a cloudu Splunk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_samlbase.png)

3. Na **Splunk Enterprise a Splunk cloudové domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<splunkserverUrl>/en-US/app/launcher/home`
    
    b. V **identifikátor** textovému poli, zadejte adresu URL serveru Splunk.

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<splunkserver>/saml/acs`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Zde, doporučujeme vám použít jedinečnou hodnotu řetězce v identifikátoru. Obraťte se na [Splunk Enterprise a klient Cloud Splunk tým podpory](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **Splunk Enterprise a cloudu Splunk** straně, budete muset odeslat stažené **soubor XML s metadaty** k [Splunk Enterprise a Splunk cloudu podporovat team](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Vytvoření zkušebního uživatele Splunk Enterprise a Splunk cloudu

V této části vytvoříte uživatele volat Britta Simon ve Splunk podniku a Splunk cloudu. Práce s [Splunk Enterprise a Splunk cloudu tým podpory](https://www.splunk.com/content/splunkcom/en_us/about-us/contact.html#tabs/customer-support) přidat uživatele do Splunk Enterprise a Splunk cloudové platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Splunk Enterprise a Splunk cloudu.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon do firemní sítě Splunk a Splunk cloudu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Splunk Enterprise a Splunk cloudu**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_splunkenterpriseandsplunkcloud_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části je otestovat vaše Azure AD SSOonfiguration pomocí přístupového panelu.

Když kliknete na tlačítko Splunk podniku a Splunk cloudu dlaždici na přístupovém panelu, jste měli získat automaticky přihlášení k Splunk Enterprise a Splunk cloudové aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-splunkenterpriseandsplunkcloud-tutorial/tutorial_general_203.png

