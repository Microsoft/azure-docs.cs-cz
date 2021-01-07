---
title: 'Kurz: Azure Active Directory integrace s portálem Palo Alto Networks na více sítích | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Palo Alto Networks na portálu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 0e16cb3d24f9dd33b156bca72ce1c99e7aae957a
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963600"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Kurz: Integrace Azure Active Directory s Palo Alto Networks na portálu

V tomto kurzu se dozvíte, jak integrovat portál Palo Alto Networks na portálu s Azure Active Directory (Azure AD).
Integrace Palo Alto Networks na portálu s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k portálu Palo Alto Networks.
* Uživatelům můžete povolit, aby se automaticky přihlásili k portálu Palo Alto Networks (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud chcete integrovat Azure AD s portálem Palo Alto Networks, budete potřebovat následující položky:

* Azure Active Directory předplatné. Pokud nemáte Azure AD, můžete získat měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné s podporou jednotného přihlašování (SSO) Palo Alto Networks na portálu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Palo Alto Networks na portále podporuje **IDP** iniciované jednotné přihlašování.
* Palo Alto Networks na portálu **pro poskytování časových intervalů podporuje právě** zřizování uživatelů

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Přidávání Palo Alto Networks na portál z Galerie

Pokud chcete nakonfigurovat integraci Palo Alto Networks na portálu do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Palo Alto Networks bez portálu z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Palo Alto Networks na portál** .
1. Z panelu výsledků vyberte **Palo Alto Networks** nezávisle na portálu a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí portálu Palo Alto Networks pro samoobslužné zpracování založeného na testovacím uživateli, který se nazývá **B. Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Palo Alto Networks na portálu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí portálu Palo Alto Networks, proveďte následující kroky:

1. **[Nakonfigurovat jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – povolí uživateli používat tuto funkci.
    * **[Vytvoření testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – testování jednotného přihlašování Azure AD s uživatelem B. Simon
    * **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – nastavte B. Simon pro použití jednotného přihlašování Azure AD.
2. **[Konfigurace jednotného přihlašování k Palo Alto Networks na portálu](#configure-palo-alto-networks-captive-portal-sso)** – nakonfigurujte nastavení jednotného přihlašování v aplikaci.
    * **[Vytvořte testovacího uživatele na portálu Palo Alto Networks](#create-a-palo-alto-networks-captive-portal-test-user)** , který bude mít protějšek B. Simon v Palo Alto sítě Samoobslužný portál, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **portálu Palo Alto Networks na portálu** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V podokně **základní konfigurace SAML** proveďte následující kroky:

   1. V poli **identifikátor** zadejte adresu URL, která má vzor `https://<customer_firewall_host_name>/SAML20/SP` .

   2. V poli **Adresa URL odpovědi** zadejte adresu URL, která má vzor `https://<customer_firewall_host_name>/SAML20/SP/ACS` .

      > [!NOTE]
      > Aktualizujte zástupné hodnoty v tomto kroku skutečným identifikátorem a adresou URL pro odpovědi. Chcete-li získat skutečné hodnoty, obraťte se na [tým podpory klienta podpory Palo Alto Networks na portálu](https://support.paloaltonetworks.com/support).

5. V části **podpisový certifikát SAML** vedle **XML federačních metadat** vyberte **Stáhnout**. Uložte stažený soubor do počítače.

    ![Odkaz na stažení XML federačních metadat](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k portálu Palo Alto Networks.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Palo Alto Networks na portálu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Konfigurace jednotného přihlašování k Palo Alto Networks na portálu

V dalším kroku nastavte jednotné přihlašování v Palo Alto Networks na samoobslužném portálu:

1. V jiném okně prohlížeče se přihlaste k webu Palo Alto Networks jako správce.

2. Vyberte kartu **zařízení** .

    ![Karta zařízení webu Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. V nabídce vyberte **zprostředkovatel identity SAML** a pak vyberte **importovat**.

    ![Tlačítko Import](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. V dialogovém okně **Importovat profil serveru zprostředkovatele identity SAML** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování sítí Palo Alto](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Jako **název profilu** zadejte název, třeba **AzureAD-CaptivePortal**.
    
    2. Vedle pole **metadata zprostředkovatele identity** vyberte **Procházet**. Vyberte soubor metadata.xml, který jste stáhli v Azure Portal.
    
    3. Vyberte **OK**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Vytvoření testovacího uživatele na portálu Palo Alto Networks pro nedobrovolný test

V dalším kroku vytvořte uživatele s názvem *Britta Simon* v Palo Alto Networks na portálu. Palo Alto Networks na samoobslužném portálu podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není nutné provádět žádné úkoly. Pokud uživatel ještě v Palo Alto Networks na portálu neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud chcete uživatele vytvořit ručně, obraťte se na [tým podpory klienta podpory Palo Alto Networks na portálu](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste být automaticky přihlášeni k portálu Palo Alto Networks, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Palo Alto Networks na portálu v okně moje aplikace byste měli být automaticky přihlášeni k portálu Palo Alto Networks, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování portálu Palo Alto Networks můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).