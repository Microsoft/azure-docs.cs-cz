---
title: 'Kurz: Integrace služby Azure Active Directory s FreshDesk | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227618"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Integrace Azure Active Directory s FreshDesk

V tomto kurzu se dozvíte, jak integrovat FreshDesk s Azure Active Directory (Azure AD).
Integrace FreshDesk s Azure AD vám poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k FreshDesk.
* Můžete povolit, aby se uživatelé automaticky přihlašovali k FreshDesk (jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s FreshDesk, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením freshdesk

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* FreshDesk podporuje **sp** inicioval SSO

## <a name="adding-freshdesk-from-the-gallery"></a>Přidání FreshDesk z galerie

Chcete-li nakonfigurovat integraci FreshDesk do Azure AD, musíte přidat FreshDesk z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat FreshDesk z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **FreshDesk**, z panelu výsledků vyberte **FreshDesk** a klepnutím na tlačítko **Přidat** přidejte aplikaci.

     ![FreshDesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí FreshDesk na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v FreshDesk.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí FreshDesk, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace aplikace FreshDesk Single Sign-On](#configure-freshdesk-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu FreshDesk](#create-freshdesk-test-user)** – chcete-li mít protějšek Britta Simon v FreshDesk, který je propojen s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí freshdesk, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **FreshDesk** vyberte **Možnost Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![FreshDesk Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Přihlásit se na adresu** `https://<tenant-name>.freshdesk.com` URL zadejte adresu URL pomocí následujícího vzoru: nebo jakékoli jiné hodnoty, kterou freshdesk navrhl.

    b. Do textového pole **Identifikátor (ID entity)** zadejte `https://<tenant-name>.freshdesk.com` adresu URL pomocí následujícího vzoru: nebo jakékoli jiné hodnoty, kterou freshdesk navrhl.

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL a identifikátorem. Obraťte se na [tým podpory klienta FreshDesk,](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Aplikace FreshDesk očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje příklad pro toto. Výchozí hodnota **jedinečného identifikátoru uživatele** je **user.userprincipalname,** ale FreshDesk očekává, že bude mapována s e-mailovou adresou uživatele. Pro které můžete použít atribut **user.mail** ze seznamu nebo použít příslušnou hodnotu atributu na základě konfigurace vaší organizace. 

    ![image](common/edit-attribute.png)

6. V části **Deklarace identity uživatelů** v dialogovém okně **Atributy uživatele** upravte deklarace pomocí **ikony Upravit** nebo přidejte deklarace pomocí funkce Přidat **novou deklaraci** pro konfiguraci atributu tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:
    
    | Name (Název) | Atribut zdroje |
    | ---------------| --------------- |
    | Jedinečný identifikátor uživatele | user.mail |

    a. Kliknutím na **Přidat novou deklaraci** otevřete dialogové okno **Spravovat deklarace identity uživatelů.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **Název** zadejte název atributu zobrazený pro daný řádek.

    c. Ponechejte **obor názvů** prázdný.

    d. Vyberte zdroj jako **atribut**.

    e. Ze seznamu **atributů Zdroj** zadejte hodnotu atributu zobrazenou pro daný řádek.

    f. Klikněte na **Ok.**

    g. Klikněte na **Uložit**.

7. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Otevřete **příkazový řádek** a spusťte následující příkazy:

    a. Do `certutil.exe -dump FreshDesk.cer` příkazového řádku zadejte hodnotu.

    > [!NOTE]
    > Zde **FreshDesk.cer** je certifikát, který jste stáhli z portálu Azure.

    b. Zkopírujte hodnotu **Cert Hash(sha256)** a vložte ji do poznámkového bloku. 

9. V části **Nastavit FreshDesk** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-freshdesk-single-sign-on"></a>Konfigurace jednotného přihlášení freshdesk

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Freshdesk jako správce.

2. Vyberte **ikonu Nastavení** a v části **Zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshdesk-tutorial/IC776770.png "Jednotné přihlašování")
  
    a. V **nabídce Jednotné přihlášení (SSO)** vyberte **Možnost Zapnuto**.

    b. Vyberte **saml sso**.

    c. V textovém poli **adresy URL přihlášení saml** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    d. Do textového pole **Adresa URL odhlášení** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z portálu Azure.

    e. Do textového pole **Otisk prstu certifikátu zabezpečení** vložte hodnotu **Certifikát hash(sha256),** kterou jste získali dříve.
  
    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** zadejte **\@brittasimon vašecompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k FreshDesk.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **FreshDesk**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **FreshDesk**.

    ![Odkaz FreshDesk v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-freshdesk-test-user"></a>Vytvořit testovacího uživatele FreshDesk

Aby bylo možné uživatelům Azure AD přihlásit se do FreshDesk, musí být zřízena do FreshDesk.  
V případě FreshDesk zřizování je ruční úlohy.

**Chcete-li zřídit uživatelské účty, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Freshdesk.**

2. V horní nabídce klikněte na **Admin**.

    ![Správce](./media/freshdesk-tutorial/IC776772.png "Správce")

3. Na kartě **Obecné nastavení** klikněte na **příkaz Agenti**.
  
    ![Agenti](./media/freshdesk-tutorial/IC776773.png "Agenti")

4. Klepněte na tlačítko **Nový agent**.

    ![Nový agent](./media/freshdesk-tutorial/IC776774.png "Nový agent")

5. V dialogovém okně Informace o agentovi proveďte následující kroky:

    ![Informace o agentovi](./media/freshdesk-tutorial/IC776775.png "Informace o agentovi")

    a. Do textového pole **E-mail** zadejte e-mailovou adresu Azure AD účtu Azure AD, který chcete zřídit.

    b. Do textového pole **Celé jméno** zadejte název účtu Azure AD, který chcete zřídit.

    c. Do textového pole **Název** zadejte název účtu Azure AD, který chcete zřídit.

    d. Klikněte na **Uložit**.

    >[!NOTE]
    >Držitel účtu Azure AD obdrží e-mail, který obsahuje odkaz pro potvrzení účtu před aktivací.
    >
    >[!NOTE]
    >Můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Freshdesk nebo rozhraní API poskytované Freshdesk zřídit uživatelské účty Azure AD freshdesk.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici FreshDesk na přístupovém panelu, můžete by měl být automaticky přihlášeni k FreshDesk, u kterého nastavíte přistupující služby. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

