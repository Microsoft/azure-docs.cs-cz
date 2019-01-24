---
title: 'Kurz: Integrace Azure Active Directory s síti na pracovišti ve službě Facebook | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a síti na pracovišti ve službě Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: a7e9276de2144318c2f8a510a7dc4211791d2ed6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814031"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Kurz: Integrace Azure Active Directory s síti na pracovišti ve službě Facebook.

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) síti na pracovišti ve službě Facebook.
Integrace síti na pracovišti ve službě Facebook s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k síti na pracovišti ve službě Facebook.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k síti na pracovišti ve službě Facebook (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s síti na pracovišti ve službě Facebook, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Pracovní plocha ve službě Facebook jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook má dva produkty, síti na pracovišti standardní (zdarma) a pracoviště Premium (placené). Žádného tenanta pracoviště Premium můžete konfigurovat integraci SCIM a jednotné přihlašování s ostatní důsledky, a náklady na licence vyžaduje. Jednotné přihlašování a SCIM nejsou k dispozici v síti na pracovišti standardní instance.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje síti na pracovišti ve službě Facebook **SP** jednotné přihlašování zahájené pomocí
* Podporuje síti na pracovišti ve službě Facebook **just-in-time zřizování**
* Podporuje síti na pracovišti ve službě Facebook  **[automatické zřizování uživatelů](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Přidání síti na pracovišti ve službě Facebook z Galerie

Pokud chcete nakonfigurovat integrace pracoviště pomocí Facebooku do služby Azure AD, potřebujete přidat síti na pracovišti ve službě Facebook z Galerie na váš seznam spravovaných aplikací SaaS.

**K síti na pracovišti ve službě Facebook přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **síti na pracovišti ve službě Facebook**vyberte **síti na pracovišti ve službě Facebook** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Pracovní plocha ve službě Facebook v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s pracoviště pomocí Facebooku podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelů v síti na pracovišti ve službě Facebook.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s síti na pracovišti ve službě Facebook, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace pracoviště pomocí Facebooku Single Sign-On](#configure-workplace-by-facebook-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření pracoviště Facebooku testovací uživatel](#create-workplace-by-facebook-test-user)**  – Pokud chcete mít protějšek Britta Simon v síti na pracovišti ve službě Facebook, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s síti na pracovišti ve službě Facebook, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **síti na pracovišti ve službě Facebook** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Pracovní plocha Facebooku domény a adresy URL jednotné přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instancename>.facebook.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Najdete na stránce ověřování řídicího panelu firemní síti na pracovišti pro správné hodnoty pro vaše pracoviště komunity.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení pracoviště ve službě Facebook** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Konfigurovat pracovní plocha ve službě Facebook jednotné přihlašování

1. V jiné okno webového prohlížeče, přihlaste se k pracovišti lokalitou Facebooku společnosti jako správce.
  
   > [!NOTE]
   > Jako součást procesu ověřování SAML můžou využívat pracoviště řetězce dotazu ve velikosti až 2.5 kB k předání parametrů do služby Azure AD.

2. V **panelem správce pro**, přejděte **zabezpečení** kartě pak **ověřování**.

3. V části **ověřování SAML**vyberte **jednotného přihlašování pouze** z rozevíracího seznamu.

4. Vstupní hodnoty zkopírovány z **pracoviště podle konfigurace sítě Facebook** tématu na webu Azure portal do odpovídajícího pole:

    * V **adresu URL SAML** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.
    * V **textového pole URL vystavitele SAML**, vložte hodnotu **Azure Ad identifikátor**, který jste zkopírovali z portálu Azure portal.
    * V **SAML odhlášení přesměrovat** (volitelné), vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.
    * Otevřete váš **certifikáty s kódováním base-64** v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **certifikát SAML** textového pole.

5. Budete muset zadat adresu URL cílové skupiny, adresa URL příjemce, a podle adresy URL služby ACS (Assertion Consumer Service) **základní konfiguraci SAML** oddílu.

6. Přejděte do dolní části a klikněte na tlačítko **Test jednotného přihlašování** tlačítko. Zobrazí se tato výsledky v automaticky otevíraném okně zobrazovaných s přihlašovací stránku Azure AD. Zadejte svoje přihlašovací údaje v jako za normálních okolností k ověření.

    **Řešení potíží:** Zajistěte, aby e-mailovou adresu, se vrací zpět z Azure AD je stejný jako pracovní účet, ke kterému jste přihlášeni s.

7. Jakmile testu bylo úspěšně dokončeno, přejděte do dolní části stránky a klikněte na tlačítko **Uložit** tlačítko.

8. Všichni uživatelé používající pracovní plocha nyní se zobrazí přihlašovací stránku Azure AD pro ověřování.

9. **Odhlášení přesměrovat SAML (volitelné)** -

    Můžete volitelně nakonfigurovat SAML odhlašovací adresa Url, který můžete použít tak, aby odkazoval na stránku služby Azure AD odhlášení. Když toto nastavení povolené a nakonfigurované, uživatel již přesměruje k odhlašovací stránce síti na pracovišti. Místo toho uživatel bude přesměrován na adresu url, která byla přidána do nastavení přesměrování odhlašování SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfiguraci frekvence opětovné ověření

Můžete nakonfigurovat síti na pracovišti na výzvu k zadání kontrolu SAML každý den, tří dnů, týdnů, dva týdny, měsíc nebo nikdy.

> [!NOTE]
> Minimální hodnota pro kontrolu SAML na mobilní aplikace nastavená na týden.

Můžete také vynutit SAML obnovit pro všechny uživatele pomocí tlačítka: Vyžadovat ověřování SAML pro všechny uživatele.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k síti na pracovišti ve službě Facebook.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **síti na pracovišti ve službě Facebook**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **síti na pracovišti ve službě Facebook**.

    ![Propojením sítě Facebook v seznamu aplikací na pracovišti](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-workplace-by-facebook-test-user"></a>Vytvoření pracoviště pomocí Facebooku testovacího uživatele

V této části volat Britta Simon vytváření uživatele v síti na pracovišti ve službě Facebook. Síti na pracovišti ve službě Facebook podporuje zřizování just-in-time je ve výchozím nastavení povolená.

Neexistuje žádná akce vám v této části. Pokud uživatel neexistuje v síti na pracovišti ve službě Facebook, je vytvořen nový při pokusu o přístup k síti na pracovišti ve službě Facebook.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [pracoviště týmem podpory klienta sítě Facebook](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na pracovišti ve službě Facebook dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni k pracovní ploše pomocí Facebooku, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](workplacebyfacebook-provisioning-tutorial.md)