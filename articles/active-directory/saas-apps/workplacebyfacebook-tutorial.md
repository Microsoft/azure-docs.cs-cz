---
title: 'Kurz: Azure Active Directory integrace s síti na pracovišti ve službě Facebook. | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a síti na pracovišti ve službě Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 687320af3130a8fd3dbd6e680216c5f6f7f1c73f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901609"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Kurz: Azure Active Directory integrace s síti na pracovišti ve službě Facebook.

V tomto kurzu zjistěte, jak k síti na pracovišti ve službě Facebook integraci s Azure Active Directory (Azure AD).

Integrace síti na pracovišti ve službě Facebook s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti ve službě Facebook.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k síti na pracovišti ve službě Facebook (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se na pracovišti ve službě Facebook, potřebujete následující položky:

- Předplatné služby Azure AD
- Firemní síti pomocí sítě Facebook jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook má dva produkty, síti na pracovišti Standard (zdarma) a síti na pracovišti Premium (placené). Všechny klienta síti na pracovišti Premium můžete nakonfigurovat žádné dopady náklady nebo licence potřebné integrace SCIM a jednotné přihlašování. Jednotné přihlašování a SCIM nejsou k dispozici v síti na pracovišti standardní instancí.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání síti na pracovišti ve službě Facebook z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Přidání síti na pracovišti ve službě Facebook z Galerie
Při konfiguraci integrace síti na pracovišti ve službě Facebook do služby Azure AD potřebujete přidat síti na pracovišti ve službě Facebook z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat síti na pracovišti ve službě Facebook z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **síti na pracovišti ve službě Facebook**.

    ![Vytváření testovacího uživatele Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. Na panelu výsledků vyberte **síti na pracovišti ve službě Facebook**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování se na pracovišti ve službě Facebook podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v síti na pracovišti ve službě Facebook je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživateli v síti na pracovišti ve službě Facebook.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v síti na pracovišti ve službě Facebook.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování se na pracovišti ve službě Facebook, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Konfiguraci frekvence opětovné ověření](#configuring-reauthentication-frequency)**  – Pokud chcete nakonfigurovat síti na pracovišti, aby se zobrazila výzva pro kontrolu SAML.
3. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytváření pracovišti uživatelem testovací Facebook](#creating-a-workplace-by-facebook-test-user)**  – Pokud chcete mít protějšek Britta Simon v síti na pracovišti podle Facebook, propojené služby Azure AD reprezentace daného uživatele.
5. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
6. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování na vašem pracovišti aplikace Facebook.

**Ke konfiguraci Azure AD jednotné přihlašování se na pracovišti ve službě Facebook, proveďte následující kroky:**

1. Na portálu Azure na **síti na pracovišti ve službě Facebook** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Na **síti na pracovišti Facebook domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<instancename>.facebook.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Naleznete na stránce ověřování na řídicím panelu firemní síti na pracovišti pro správné hodnoty pro vaše pracoviště komunity. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

6. Na **síti na pracovišti v konfiguraci sítě Facebook** klikněte na tlačítko **pracoviště nakonfigurovat ve službě Facebook** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/workplacebyfacebook-tutorial/config.png) 

7. V okně prohlížeče jiný web a přihlášení k pracovní ploše lokalitou Facebook společnosti jako správce.
  
   > [!NOTE] 
   > Jako součást procesu ověřování SAML může síti na pracovišti využívat řetězce dotazu až 2,5 kilobajtů velikost, aby bylo možné předat parametry do služby Azure AD.

8. V **řídicí panel společnosti**, přejděte na **ověřování** kartě.

9. V části **ověřování SAML**, vyberte **pouze jednotné přihlašování** z rozevíracího seznamu.

10. Vstupní hodnoty zkopírovaných z **síti na pracovišti v konfiguraci sítě Facebook** části portálu Azure do odpovídajícího pole:

    *   V **SAML URL** textovému poli, vložte hodnotu **jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.
    *   V **URL vystavitele SAML textbox**, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.
    *   V **přesměrování odhlašovací SAML** (volitelné), vložte hodnotu **Sign-Out URL**, který jste zkopírovali z portálu Azure.
    *   Otevřete váš **certifikátu s kódováním base-64** v poznámkovém bloku stáhli z portálu Azure, kopírovat obsah ho do schránky a vložte jej do **certifikátu SAML** textové pole.

11. Budete muset zadat adresu URL cílové skupiny, adresa URL příjemce, a adresa URL služby ACS (služba Assertion příjemce) uvedené v části **konfigurace SAML** části.

12. Přejděte do dolní části a klikněte na **Test jednotného přihlašování k** tlačítko. Zobrazí se tato výsledky v automaticky otevíraném okně, zobrazí se přihlašovací stránku služby Azure AD. Zadejte přihlašovací údaje v jako normální k ověření. 

    **Řešení potíží:** zkontrolujte e-mailová adresa se vrací zpět z Azure AD je stejný jako pracovní účet, který jste se přihlásili.

13. Po úspěšném dokončení testu, přejděte do dolní části stránky a klikněte na **Uložit** tlačítko.

14. Všichni uživatelé používali síti na pracovišti nyní zobrazí se přihlašovací stránku služby Azure AD pro ověřování.

15. **SAML odhlášení přesměrovat (volitelné)** - 

    Můžete volitelně lze konfigurovat adresu Url odhlašovací SAML, který můžete použít tak, aby odkazoval na Azure AD odhlašovací stránce. Pokud je toto nastavení povolené a nakonfigurované, se uživatel přesměruje už k síti na pracovišti odhlašovací stránce. Místo toho bude uživatel přesměrován na adresu url, která byla přidána do nastavení přesměrování odhlašovací SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfiguraci frekvence opětovné ověření

Můžete nakonfigurovat síti na pracovišti vyžadovat kontrolu SAML každý den, tři dny týdne dvou týdnů, měsíců nebo vůbec.

> [!NOTE] 
>Minimální hodnota pro kontrolu SAML na mobilní aplikace nastavena na jeden týden.

Můžete taky přinutit SAML obnovit pro všechny uživatele pomocí tlačítka: vyžadovat ověřování SAML pro všechny uživatele nyní.


### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>Vytváření pracovišti Facebook testovací uživatel

V této části uživatele volat Britta Simon vytvoří v síti na pracovišti Facebook. Síti na pracovišti ve službě Facebook podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná akce pro vás v této části. Pokud uživatel neexistuje v síti na pracovišti ve službě Facebook, nový vytvoří se při pokusu o přístup k síti na pracovišti Facebook.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [síti na pracovišti tým podpory klienta sítě Facebook](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k síti na pracovišti ve službě Facebook.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit k firemní síti pomocí sítě Facebook, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **síti na pracovišti ve službě Facebook**.

    ![Konfigurovat jednotné přihlašování](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Pokud chcete testovat vaše nastavení jednotného přihlašování, otevřete Panel přístupu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfiguraci zřizování uživatelů](workplacebyfacebook-provisioning-tutorial.md)



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
