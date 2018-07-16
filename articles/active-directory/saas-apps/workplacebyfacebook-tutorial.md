---
title: 'Kurz: Integrace Azure Active Directory s síti na pracovišti ve službě Facebook | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a síti na pracovišti ve službě Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: fe876db13cc5fb97f827a80a328003a7d2585adc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041977"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Kurz: Integrace Azure Active Directory s síti na pracovišti ve službě Facebook.

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) síti na pracovišti ve službě Facebook.

Integrace síti na pracovišti ve službě Facebook s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti ve službě Facebook.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k síti na pracovišti ve službě Facebook (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s síti na pracovišti ve službě Facebook, potřebujete následující položky:

- S předplatným služby Azure AD
- Firemní síti pomocí sítě Facebook jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook má dva produkty, síti na pracovišti standardní (zdarma) a pracoviště Premium (placené). Žádného tenanta pracoviště Premium můžete konfigurovat integraci SCIM a jednotné přihlašování s ostatní důsledky, a náklady na licence vyžaduje. Jednotné přihlašování a SCIM nejsou k dispozici v síti na pracovišti standardní instance.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání síti na pracovišti ve službě Facebook z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Přidání síti na pracovišti ve službě Facebook z Galerie
Pokud chcete nakonfigurovat integrace pracoviště pomocí Facebooku do služby Azure AD, potřebujete přidat síti na pracovišti ve službě Facebook z Galerie na váš seznam spravovaných aplikací SaaS.

**K síti na pracovišti ve službě Facebook přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **síti na pracovišti ve službě Facebook**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. Na panelu výsledků vyberte **síti na pracovišti ve službě Facebook**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s síti na pracovišti ve službě Facebook podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v síti na pracovišti ve službě Facebook je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v síti na pracovišti ve službě Facebook.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v síti na pracovišti ve službě Facebook.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s síti na pracovišti ve službě Facebook, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfiguraci frekvence opětovné ověření](#configuring-reauthentication-frequency)**  – Pokud chcete nakonfigurovat síti na pracovišti na výzvu k zadání kontrolu SAML.
3. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytváření firemní síti pomocí testovacího uživatele Facebooku](#creating-a-workplace-by-facebook-test-user)**  – Pokud chcete mít protějšek Britta Simon v síti na pracovišti ve službě Facebook, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování na pracovišti aplikace Facebook.

**Ke konfiguraci Azure AD jednotné přihlašování s síti na pracovišti ve službě Facebook, proveďte následující kroky:**

1. Na webu Azure Portal na **síti na pracovišti ve službě Facebook** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Na **síti na pracovišti ve službě Facebook domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<instancename>.facebook.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Najdete na stránce ověřování řídicího panelu firemní síti na pracovišti pro správné hodnoty pro vaše pracoviště komunity. 

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

6. Na **pracoviště podle konfigurace sítě Facebook** klikněte na tlačítko **pracoviště nakonfigurovat ve službě Facebook** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/workplacebyfacebook-tutorial/config.png) 

7. V jiné okno webového prohlížeče, přihlaste se k pracovišti lokalitou Facebooku společnosti jako správce.
  
   > [!NOTE] 
   > Jako součást procesu ověřování SAML můžou využívat pracoviště řetězce dotazu ve velikosti až 2.5 kB k předání parametrů do služby Azure AD.

8. V **řídicí panel společnosti**, přejděte **ověřování** kartu.

9. V části **ověřování SAML**vyberte **jednotného přihlašování pouze** z rozevíracího seznamu.

10. Vstupní hodnoty zkopírovány z **pracoviště podle konfigurace sítě Facebook** tématu na webu Azure portal do odpovídajícího pole:

    *   V **adresu URL SAML** textového pole vložte hodnotu **jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
    *   V **textového pole URL vystavitele SAML**, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.
    *   V **SAML odhlášení přesměrovat** (volitelné), vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.
    *   Otevřete váš **certifikáty s kódováním base-64** v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **certifikát SAML** textového pole.

11. Budete muset zadat adresu URL cílové skupiny, adresa URL příjemce, a podle adresy URL služby ACS (Assertion Consumer Service) **konfigurace SAML** oddílu.

12. Přejděte do dolní části a klikněte na tlačítko **Test jednotného přihlašování** tlačítko. Zobrazí se tato výsledky v automaticky otevíraném okně zobrazovaných s přihlašovací stránku Azure AD. Zadejte svoje přihlašovací údaje v jako za normálních okolností k ověření. 

    **Řešení potíží:** zkontrolujte e-mailovou adresu, se vrací zpět z Azure AD je stejný jako pracovní účet, jste přihlášeni.

13. Jakmile testu bylo úspěšně dokončeno, přejděte do dolní části stránky a klikněte na tlačítko **Uložit** tlačítko.

14. Všichni uživatelé používající pracovní plocha nyní se zobrazí přihlašovací stránku Azure AD pro ověřování.

15. **Odhlášení přesměrovat SAML (volitelné)** - 

    Můžete volitelně nakonfigurovat SAML odhlašovací adresa Url, který můžete použít tak, aby odkazoval na stránku služby Azure AD odhlášení. Když toto nastavení povolené a nakonfigurované, uživatel již přesměruje k odhlašovací stránce síti na pracovišti. Místo toho uživatel bude přesměrován na adresu url, která byla přidána do nastavení přesměrování odhlašování SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfiguraci frekvence opětovné ověření

Můžete nakonfigurovat síti na pracovišti na výzvu k zadání kontrolu SAML každý den, tří dnů, týdnů, dva týdny, měsíc nebo nikdy.

> [!NOTE] 
>Minimální hodnota pro kontrolu SAML na mobilní aplikace nastavená na týden.

Můžete také vynutit obnovení pro všechny uživatele pomocí tlačítka SAML: vyžadovat ověřování SAML pro všechny uživatele nyní.


### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Vytváření firemní síti pomocí testovacího uživatele služby Facebook

V této části volat Britta Simon vytváření uživatele v síti na pracovišti ve službě Facebook. Síti na pracovišti ve službě Facebook podporuje zřizování just-in-time je ve výchozím nastavení povolená.

Neexistuje žádná akce vám v této části. Pokud uživatel neexistuje v síti na pracovišti ve službě Facebook, je vytvořen nový při pokusu o přístup k síti na pracovišti ve službě Facebook.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [pracoviště týmem podpory klienta sítě Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k síti na pracovišti ve službě Facebook.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k síti na pracovišti ve službě Facebook, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **síti na pracovišti ve službě Facebook**.

    ![Konfigurace jednotného přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Pokud chcete otestovat jednotné přihlašování – nastavení, otevřete na přístupovém panelu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png
