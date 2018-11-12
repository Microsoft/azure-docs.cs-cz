---
title: 'Kurz: Integrace Azure Active Directory pomocí Freshdesku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshdesku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: jeedes
ms.openlocfilehash: b5968b83fc9beb481e2ad2c0cd44d2c284747fa1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010809"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Integrace Azure Active Directory pomocí Freshdesku

V tomto kurzu se dozvíte, jak integrovat Freshdesku s Azure Active Directory (Azure AD).

Freshdesku integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do Freshdesku.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Freshdesku (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Freshdesku, potřebujete následující položky:

- Předplatné Azure AD
- Freshdesku jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Freshdesku z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-freshdesk-from-the-gallery"></a>Přidání Freshdesku z Galerie

Pokud chcete nakonfigurovat integraci Freshdesku do služby Azure AD, budete muset přidat Freshdesku z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Freshdesku z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Freshdesku**vyberte **Freshdesku** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Freshdesku v seznamu výsledků](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Freshdesku podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve Freshdesku je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve Freshdesku musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Freshdesku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Freshdesku](#creating-a-freshdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon ve Freshdesku, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Freshdesku.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Freshdesku, proveďte následující kroky:**

1. Na webu Azure Portal na **Freshdesku** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Freshdesku domény a adresy URL jednotného přihlašování – informace](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.freshdesk.com` nebo jakoukoli jinou hodnotu navrhl Freshdesku.

    b. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.freshdesk.com` nebo jakoukoli jinou hodnotu navrhl Freshdesku.

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Freshdesku klienta](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) k získání těchto hodnot.

5. Freshdesku aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale **Freshdesku** očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace.

    ![image](./media/freshdesk-tutorial/i4-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele a deklarace identity** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:
    
    a. Klikněte na **ikonu pro úpravu** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/freshdesk-tutorial/i2-attribute.png)

    ![image](./media/freshdesk-tutorial/i3-attribute.png)

    b. Z **zdrojový atribut** seznamu vyberte **user.mail**.

    c. Klikněte na **Uložit**.

7. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

8. Otevřít **příkazového řádku** a spusťte následující příkazy:

    a. Zadejte `certutil.exe -dump FreshDesk.cer` hodnotu v příkazovém řádku.

    > [!NOTE]
    > Tady **FreshDesk.cer** je certifikát, který jste si stáhli z portálu Azure portal.

    b. Kopírovat **Cert Hash(sha256)** hodnotu a vložte ho do poznámkového bloku. 

9. Na **nastavení Freshdesku** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace Freshdesku](common/configuresection.png)

10. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Freshdesku.

11. Vyberte **ikona nastavení** a **zabezpečení** části, proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshdesk-tutorial/IC776770.png "jednotné přihlašování")
  
    a. Pro **jednotné přihlašování (SSO)** vyberte **na**.

    b. Vyberte **SAML SSO**.

    c. V **SAML přihlašovací adresa URL** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **otisku certifikátu zabezpečení** vložit do textového pole **Cert Hash(sha256)** hodnotu, která jste získali už dřív.
  
    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-freshdesk-test-user"></a>Vytvoření zkušebního uživatele Freshdesku

Chcete-li povolit přihlášení do Freshdesku uživatelů Azure AD, musí být poskytnuty do Freshdesku.  
V případě Freshdesku zřizování se ruční úlohy.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Freshdesku** tenanta.

2. V nabídce v horní části klikněte na tlačítko **správce**.

    ![Správce](./media/freshdesk-tutorial/IC776772.png "správce")

3. V **obecné nastavení** klikněte na tlačítko **agentů**.
  
    ![Agenti](./media/freshdesk-tutorial/IC776773.png "agentů")

4. Klikněte na tlačítko **nového agenta**.

    ![Nový Agent](./media/freshdesk-tutorial/IC776774.png "nového agenta")

5. V dialogovém okně informace o agentovi proveďte následující kroky:

    ![Informace o agentovi](./media/freshdesk-tutorial/IC776775.png "informace o agentovi")

    a. V **e-mailu** textové pole, typ služby Azure AD e-mailovou adresu účtu služby Azure AD, které chcete zřídit.

    b. V **jméno a příjmení** textového pole zadejte název účtu služby Azure AD, které chcete zřídit.

    c. V **název** textového pole zadejte název účtu služby Azure AD, které chcete zřídit.

    d. Klikněte na **Uložit**.

    >[!NOTE]
    >Držitel účtu Azure AD se zobrazí e-mailu, který obsahuje odkaz pro potvrzení účtu předtím, než se aktivuje.
    >
    >[!NOTE]
    >Můžete použít jakékoli jiné Freshdesku uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Freshdesku zřídit AAD uživatelských účtů do Freshdesku.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do Freshdesku.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Freshdesku**.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Freshdesku na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Freshdesku.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
