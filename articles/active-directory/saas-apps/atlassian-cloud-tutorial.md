---
title: 'Kurz: Integrace Azure Active Directory s cloudem od společnosti Atlassian | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cloud od společnosti Atlassian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106591"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory Cloud od společnosti Atlassian

V tomto kurzu se dozvíte, jak integrovat Cloud od společnosti Atlassian s Azure Active Directory (Azure AD). Při integraci od společnosti Atlassian cloudu s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup pro Cloud od společnosti Atlassian.
* Aby uživatelé mohli být automaticky přihlášeni k Cloud od společnosti Atlassian pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Cloud od společnosti Atlassian jednotné přihlašování (SSO) povolené předplatné.
* Chcete-li povolit zabezpečení kontrolního výrazu SAML (Markup Language) jednotného přihlašování pro Cloud od společnosti Atlassian produkty, nastavení přístupu od společnosti Atlassian. Další informace o [přístupu od společnosti Atlassian]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Cloud od společnosti Atlassian **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Přidání Cloud od společnosti Atlassian z Galerie

Pokud chcete nakonfigurovat integraci od společnosti Atlassian Cloud do služby Azure AD, budete muset přidat Cloud od společnosti Atlassian z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Cloud od společnosti Atlassian** do vyhledávacího pole.
1. Vyberte **Cloud od společnosti Atlassian** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s cloudem od společnosti Atlassian pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v cloudu od společnosti Atlassian.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s cloudem od společnosti Atlassian, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace jednotného přihlašování Cloud od společnosti Atlassian](#configure-atlassian-cloud-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Cloud od společnosti Atlassian](#create-atlassian-cloud-test-user)**  – Pokud chcete mít protějšek B.Simon od společnosti Atlassian cloudu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Cloud od společnosti Atlassian** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, zadejte hodnoty pro následující pole:

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://auth.atlassian.com/saml/<unique ID>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klikněte na tlačítko **nastavit další adresy URL**.

    d. V **stav přenosu** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Zobrazí se tyto skutečné hodnoty z **konfigurace SAML služby Cloud od společnosti Atlassian** obrazovky, který je vysvětlen později v **konfigurace od společnosti Atlassian cloudu jednotného přihlašování** kurzu.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Přihlašovací adresa URL není skutečný. Vložte hodnotu z instance, kterou používáte k přihlášení na portál pro správu cloudu od společnosti Atlassian.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Od společnosti Atlassian cloudové aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace Cloud od společnosti Atlassian **nameidentifier** namapovat s **user.mail**, takže budete muset upravit mapování atributů po kliknutí na **upravit** ikonu a změňte mapování atributů.

    ![image](common/edit-attribute.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavit Cloud od společnosti Atlassian** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-atlassian-cloud-sso"></a>Konfigurace cloudu od společnosti Atlassian jednotného přihlašování

1. K automatizaci konfigurace v rámci Cloud od společnosti Atlassian, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **Cloud od společnosti Atlassian nastavení** vás nasměrujeme na to od společnosti Atlassian cloudové aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do Cloud od společnosti Atlassian. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 – 7.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit Cloud od společnosti Atlassian ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Cloud od společnosti Atlassian jako správce a proveďte následující kroky:

4. Budete muset ověřit doménu před přechodem ke konfiguraci jednotného přihlašování. Další informace najdete v tématu [ověření domény od společnosti Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentu.

5. V levém podokně vyberte **zabezpečení** > **SAML jednotného přihlašování**. Pokud jste tak již neučinili, přihlásit k odběru pro správce identit od společnosti Atlassian.

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. V **konfigurace přidat SAML** okno, postupujte takto:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. V **zprostředkovatele Identity Entity ID** pole, vložte **Azure AD identifikátor** , který jste zkopírovali z portálu Azure portal.

    b. V **zprostředkovatele Identity adresu URL jednotného přihlašování** pole, vložte **přihlašovací adresa URL** , který jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z portálu Azure portal v souboru .txt, zkopírujte hodnotu (bez *začít certifikát* a *End Certificate* řádků) a vložte jej do **veřejné X509 certifikát** pole.

    d. Klikněte na tlačítko **uložit konfiguraci**.

7. Aby bylo zajištěno, že jste nastavili správné adresy URL, aktualizujte nastavení Azure AD následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. V okně SAML Kopírovat **SP Identity ID** a potom na webu Azure portal, v části Cloud od společnosti Atlassian **základní konfiguraci SAML**, vložte ji **identifikátor** pole.

    b. V okně SAML Kopírovat **SP adresa URL služby příjemce kontrolního výrazu** a potom na webu Azure portal, v části Cloud od společnosti Atlassian **základní konfiguraci SAML**, vložte ji **adresy URL odpovědi**pole. Přihlašovací adresa URL je adresa URL tenanta Cloud od společnosti Atlassian.

    > [!NOTE]
    > Pokud jste stávající zákazník, po aktualizaci **SP Identity ID** a **SP adresa URL služby příjemce kontrolního výrazu** hodnoty na webu Azure Portal, vyberte **Ano, aktualizujte konfiguraci**. Pokud jste nového zákazníka, můžete tento krok přeskočit.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon používat jednotné přihlašování Azure díky udělení přístupu pro Cloud od společnosti Atlassian.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Cloud od společnosti Atlassian**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-atlassian-cloud-test-user"></a>Vytvořit testovacího uživatele Cloud od společnosti Atlassian

Pokud chcete povolit Azure AD uživatelům umožní přihlásit se na Cloud od společnosti Atlassian, zřízení uživatelských účtů v cloudu od společnosti Atlassian ručně následujícím způsobem:

1. V **správu** vyberte **uživatelé**.

    ![The Atlassian Cloud Users link](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. Chcete-li vytvořit uživatele v cloudu od společnosti Atlassian, vyberte **pozvání uživatele**.

    ![Vytvoření uživatele služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. V **e-mailová adresa** zadejte e-mailovou adresu uživatele a pak přiřadit přístup k aplikaci.

    ![Vytvoření uživatele služby Cloud od společnosti Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. Chcete-li odeslat e-mailová pozvánka pro uživatele, vyberte **pozvat uživatele**. Uživateli se odešle e-mailovou pozvánku a po přijetí e-mailové pozvánce, je uživatel aktivní v systému.

> [!NOTE]
> Můžete také hromadně – vytvořit uživatele tak, že vyberete **Hromadně vytvořit** tlačítko **uživatelé** části.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Cloud od společnosti Atlassian na přístupovém panelu, vám by měl být automaticky přihlášeni cloud od společnosti Atlassian, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)