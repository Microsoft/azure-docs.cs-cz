---
title: 'Kurz: Azure Active Directory integrace s Lifesize cloudu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Lifesize cloudu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 84af3a9b726d990e585e2b12b1c0a6f4609fcb7e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344414"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Kurz: Azure Active Directory integrace s Lifesize cloudu

V tomto kurzu zjistěte, jak integrovat Lifesize cloudu s Azure Active Directory (Azure AD).

Integrace Lifesize cloudu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do cloudu Lifesize
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Lifesize cloudu (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudem Lifesize, potřebujete následující položky:

- Předplatné služby Azure AD
- Cloudu Lifesize jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Lifesize cloudu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Přidání Lifesize cloudu z Galerie
Při konfiguraci integrace Lifesize cloudu do služby Azure AD, potřebujete přidat Lifesize cloudu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Lifesize cloudu z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Lifesize cloudu**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. Na panelu výsledků vyberte **Lifesize cloudu**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s cloudem Lifesize podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v cloudu Lifesize je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v cloudu Lifesize musí navázat.

V cloudu Lifesize přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Lifesize cloudu, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Lifesize Cloud](#creating-a-lifesize-cloud-test-user)**  – Pokud chcete mít protějšek Britta Simon Lifesize cloudu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Lifesize cloudu.

**Ke konfiguraci Azure AD jednotné přihlašování s Lifesize cloudu, proveďte následující kroky:**

1. Na portálu Azure na **Lifesize cloudu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. Na **Lifesize cloudové domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://login.lifesizecloud.com/ls/?acs`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://login.lifesizecloud.com/<companyname>`

     
4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**, proveďte následující krok:    
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    V **předávání stavu** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Upozorňujeme, že tyto nejsou skutečné hodnoty. budete muset tyto hodnoty aktualizovat skutečné přihlašovací adresa URL, stav předávání a identifikátor. Obraťte se na [tým podpory klient Cloud Lifesize](https://www.lifesize.com/support) získat přihlašovací adresa URL a identifikátor hodnoty a můžete získat hodnotu předávání stavu z konfigurace jednotného přihlašování, která je vysvětlená později v tomto kurzu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. Na **konfigurace cloudu Lifesize** klikněte na tlačítko **konfigurace cloudu Lifesize** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Chcete-li získat jednotné přihlašování, které jsou nakonfigurované pro vaši aplikaci, přihlášení do aplikace Lifesize cloudu s oprávněními správce.

8. V pravém horním rohu klikněte na název a potom klikněte na **rozšířená nastavení**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. V rozšířená nastavení nyní klikněte na **Konfigurace jednotného přihlašování k** odkaz. Otevře se stránka Konfigurace jednotného přihlašování pro vaše instance.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Teď můžete nakonfigurujte následující hodnoty v uživatelském rozhraní Konfigurace jednotného přihlašování.    
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. V **vystavitele zprostředkovatele Identity** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.

    b.  V **přihlašovací adresa URL** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    c. Otevření kódovaného certifikátu kódování base-64 v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509** textové pole.
  
    d. V mapování atributů SAML pro první název textového pole zadejte hodnotu jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. V mapování atributů SAML pro **příjmení** textového pole zadejte hodnotu jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. V mapování atributů SAML pro **e-mailu** textového pole zadejte hodnotu jako **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Ke kontrole konfigurace můžete kliknutím na **Test** tlačítko.
   
    >[!NOTE]
    >Pro úspěšné testování potřebujete k dokončení Průvodce konfigurací služby ve službě Azure AD a také poskytují přístup na uživatele nebo skupiny, které můžete provést test.

12. Povolit jednotné přihlašování kontrolou na **povolit jednotné přihlašování** tlačítko.

13. Nyní klikněte na **aktualizace** tlačítko tak, aby se ukládají všechna nastavení. Tím se vygeneruje RelayState hodnota. Kopírování RelayState hodnotu, která se generují do textového pole, vložte jej do **předávání stavu** textového pole pod **Lifesize cloudové domény a adresy URL** části. 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Vytvoření zkušebního uživatele Lifesize cloudu

V této části vytvoříte uživatele názvem Britta Simon v Lifesize cloudu. Lifesize cloudu podporovat zřizování automatické uživatelů. Po úspěšném ověření v Azure AD se automaticky zřídí uživatele v aplikaci. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup do cloudu Lifesize Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon Lifesize cloudu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Lifesize cloudu**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Lifesize cloudu na přístupovém panelu, měli byste obdržet přihlašovací stránku Lifesize cloudových aplikací.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

