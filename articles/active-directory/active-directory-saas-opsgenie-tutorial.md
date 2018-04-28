---
title: 'Kurz: Azure Active Directory integrace s OpsGenie | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: d57c80e8e965e15d92736ec482247f02064f6009
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Kurz: Azure Active Directory integrace s OpsGenie

V tomto kurzu zjistěte, jak integrovat OpsGenie s Azure Active Directory (Azure AD).

Integrace OpsGenie s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k OpsGenie
- Můžete povolit uživatelům, aby automaticky získat přihlášení k OpsGenie (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OpsGenie, potřebujete následující položky:

- Předplatné služby Azure AD
- OpsGenie jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání OpsGenie z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-opsgenie-from-the-gallery"></a>Přidání OpsGenie z Galerie
Při konfiguraci integrace OpsGenie do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS OpsGenie z galerie.

**Pokud chcete přidat OpsGenie z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **OpsGenie**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_search.png)

5. Na panelu výsledků vyberte **OpsGenie**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s OpsGenie podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v OpsGenie je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v OpsGenie musí navázat.

V OpsGenie, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OpsGenie, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele OpsGenie](#creating-a-opsgenie-test-user)**  – Pokud chcete mít protějšek Britta Simon v OpsGenie propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci OpsGenie.

**Ke konfiguraci Azure AD jednotné přihlašování s OpsGenie, proveďte následující kroky:**

1. Na portálu Azure na **OpsGenie** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_samlbase.png)

3. Na **OpsGenie domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://app.opsgenie.com/auth/login`

4. Na **SAML podpisový certifikát** části, klikněte na tlačítko Kopírovat kopírování **adresu Url aplikace federační Metadata** a vložte do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-opsgenie-tutorial/tutorial_general_400.png)

6. Na **OpsGenie konfigurace** klikněte na tlačítko **konfigurace OpsGenie** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_configure.png)

7. Otevřete jiná instance prohlížeče a pak přihlásit k OpsGenie jako správce.

8. Klikněte na tlačítko **nastavení**a klikněte **jednotné přihlašování** kartě.
   
    ![OpsGenie jednotné přihlášení](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png)

9. Chcete-li povolit jednotné přihlašování, vyberte **povoleno**.
   
    ![Nastavení OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 

10. V **zprostředkovatele** klikněte na položku **Azure Active Directory** kartě.
   
    ![Nastavení OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 

11. Na stránce dialogové okno služby Azure Active Directory proveďte následující kroky:
   
    ![Nastavení OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. V **SAML 2.0 Endpoint** textovému poli, vložte **jeden znak na adresu URL služby**hodnotu, která jste zkopírovali z portálu Azure.
    
    b. V **adresu Url metadat:** textovému poli, vložte **adresu Url aplikace federační Metadata** hodnotu, která jste zkopírovali z portálu Azure.
    
    c. Klikněte na tlačítko **uložit změny**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-opsgenie-test-user"></a>Vytvoření zkušebního uživatele OpsGenie

Cílem této části je vytvoření uživatele v OpsGenie nazývá Britta Simon. 

1. V okně webového prohlížeče Přihlaste se ke klientovi OpsGenie jako správce.

2. Přejděte do seznamu uživatelů kliknutím **uživatele** v levém panelu.
   
   ![Nastavení OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klikněte na tlačítko **přidat uživatele**.

4. Na **přidat uživatele** dialogové okno, proveďte následující kroky:
   
   ![Nastavení OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png)
   
   a. V **e-mailu** textovému poli, typ e-mailovou adresu BrittaSimon řešit v Azure Active Directory.
   
   b. V **úplný název** textovému poli, typ **Britta Simon**.
   
   c. Klikněte na **Uložit**. 

>[!NOTE]
>Britta získá e-mail s pokyny k nastavení svůj profil.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu OpsGenie.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon OpsGenie, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **OpsGenie**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici OpsGenie na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci OpsGenie.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png

