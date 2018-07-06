---
title: 'Kurz: Integrace Azure Active Directory se službou Intralinks | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Intralinks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: fbe3ceb3d26de90bd46ee02263d09c822ee90c29
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868843"
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Kurz: Integrace Azure Active Directory se službou Intralinks

V tomto kurzu se dozvíte, jak integrovat Intralinks s Azure Active Directory (Azure AD).

Intralinks integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Intralinks
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Intralinks (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Intralinks, potřebujete následující položky:

- S předplatným služby Azure AD
- Intralinks jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Intralinks z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-intralinks-from-the-gallery"></a>Přidání Intralinks z Galerie
Konfigurace integrace Intralinks do služby Azure AD, budete muset přidat Intralinks z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Intralinks z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Intralinks**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

5. Na panelu výsledků vyberte **Intralinks**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Intralinks podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Intralinks je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Intralinks potřeba navázat.

V Intralinks, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Intralinks, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno Intralinks](#creating-an-intralinks-test-user)**  – Pokud chcete mít protějšek Britta Simon Intralinks, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Intralinks.

**Ke konfiguraci Azure AD jednotné přihlašování s Intralinks, proveďte následující kroky:**

1. Na webu Azure Portal na **Intralinks** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_intralinks_samlbase.png)

3. Na **Intralinks domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_intralinks_url.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:  `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Intralinks klienta](https://www.intralinks.com/contact-1) tuto výhodu získáte. 
 
4. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_intralinks_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_general_400.png)

6. Ke konfiguraci jednotného přihlašování na **Intralinks** straně, je nutné odeslat na stažený **soubor XML s metadaty** [tým podpory Intralinks](https://www.intralinks.com/contact-1). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-intralinks-test-user"></a>Vytvoření Intralinks testovacího uživatele

V této části vytvoříte uživatele v Intralinks jako Britta Simon. Spojte se prosím s [tým podpory Intralinks](https://www.intralinks.com/contact-1) přidat uživatele na platformě Intralinks.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Intralinks použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Intralinks Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Intralinks**.

    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_intralinks_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="add-intralinks-via-or-elite-application"></a>Přidání Intralinks prostřednictvím nebo Elite aplikace

Intralinks používá stejnou identitu platformu jednotného přihlašování pro všechny ostatní aplikace Intralinks s výjimkou zakázky Nexus aplikace. Takže pokud máte v plánu používat jakékoli jiné aplikace Intralinks pak nejprve budete muset nakonfigurovat jednotné přihlašování pro jednu primární Intralinks aplikaci pomocí postupu popsaného výše.

Poté můžete sledovat následující postup pro přidání jiná Intralinks aplikace ve vašem tenantovi, který můžete využít tuto primární aplikaci pro jednotné přihlašování. 

>[!NOTE]
>Tato funkce je dostupná jenom pro zákazníky Azure AD Premium SKU a není k dispozici pro zákazníky, kteří Free nebo základní SKU.

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]


2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Intralinks**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/intralinks-tutorial/tutorial_intralinks_search.png)

5. Na **Intralinks přidat aplikaci** proveďte následující kroky:

    ![Přidává se aplikace Intralinks prostřednictvím nebo Elite](./media/intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. V **název** textového pole zadejte vhodný název aplikace, třeba **Intralinks Elite**.

    b. Klikněte na tlačítko **přidat** tlačítko.

6.  Na webu Azure Portal na **Intralinks** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

7. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **propojené přihlášení**.
 
    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

8. Získat SP iniciované URL jednotného přihlašování z [Intralinks týmu](https://www.intralinks.com/contact-1) pro jiné aplikace Intralinks a zadejte ho v **konfigurovat přihlašovací adresa URL** jak je znázorněno níže. 
    
     ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     V textovém poli přihlašovací adresu URL zadejte adresu URL používají vaši uživatelé k přihlašování do aplikace Intralinks pomocí následujícímu vzoru:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

9. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/intralinks-tutorial/tutorial_general_400.png)

10. Přiřazení aplikací do skupin uživatelů nebo jak je uvedeno v části  **[přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Intralinks na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Intralinks.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/intralinks-tutorial/tutorial_general_01.png
[2]: ./media/intralinks-tutorial/tutorial_general_02.png
[3]: ./media/intralinks-tutorial/tutorial_general_03.png
[4]: ./media/intralinks-tutorial/tutorial_general_04.png

[100]: ./media/intralinks-tutorial/tutorial_general_100.png

[200]: ./media/intralinks-tutorial/tutorial_general_200.png
[201]: ./media/intralinks-tutorial/tutorial_general_201.png
[202]: ./media/intralinks-tutorial/tutorial_general_202.png
[203]: ./media/intralinks-tutorial/tutorial_general_203.png

