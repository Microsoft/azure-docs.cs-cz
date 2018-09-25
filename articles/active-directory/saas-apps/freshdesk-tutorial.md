---
title: 'Kurz: Integrace Azure Active Directory pomocí Freshdesku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshdesku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ce302db74f831e67b576e4c0001f21473fd7f2e0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037520"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Integrace Azure Active Directory pomocí Freshdesku

V tomto kurzu se dozvíte, jak integrovat Freshdesku s Azure Active Directory (Azure AD).

Freshdesku integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do Freshdesku
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Freshdesku (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Freshdesku, potřebujete následující položky:

- Předplatné Azure AD
- Freshdesku jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Freshdesku z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-freshdesk-from-the-gallery"></a>Přidání Freshdesku z Galerie

Pokud chcete nakonfigurovat integraci Freshdesku do služby Azure AD, budete muset přidat Freshdesku z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Freshdesku z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Freshdesku**. Vyberte **Freshdesku** z panel výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Freshdesku podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek ve Freshdesku je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské ve Freshdesku musí být vytvořeno.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **e-mailová adresa** ve Freshdesku.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Freshdesku, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Freshdesku](#creating-a-freshdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon ve Freshdesku, který je propojený s Azure AD reprezentace jí.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Freshdesku.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Freshdesku, proveďte následující kroky:**

1. Na webu Azure Portal na **Freshdesku** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Na **Freshdesku domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<tenant-name>.freshdesk.com` nebo jakoukoli jinou hodnotu navrhl Freshdesku.

    > [!NOTE]
    > Všimněte si, že to není skutečné hodnoty. Budete muset zaktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Freshdesku klienta](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) tuto výhodu získáte.

4. Vaše aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale **Freshdesku** očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_attribute.png)

5. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Pokud máte nějaké problémy, najdete na tomto [odkaz](https://support.freshdesk.com/support/discussions/topics/317543).

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Nainstalujte **OpenSSL** ve vašem systému, pokud jste nenainstalovali ve vašem systému.

8. Otevřít **příkazového řádku** a spusťte následující příkazy:

    a. Zadejte `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` hodnotu v příkazovém řádku.

    > [!NOTE]
    > Tady **FreshDesk.cer** je certifikát, který jste si stáhli z portálu Azure portal.

    b. Zadejte `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` hodnotu v příkazovém řádku. 
    
    > [!NOTE]
    > Tady **certificate.crt** je výstupní certifikát, který je generován v předchozím kroku.

    c. Kopírovat **kryptografický otisk** hodnotu a vložte ho do poznámkového bloku. Použití dvojteček odebrání kryptografický otisk a získejte poslední hodnotu kryptografického otisku.

9. Na **Freshdesku konfigurace** klikněte na tlačítko **nakonfigurovat Freshdesku** konfigurovat přihlašování – okno. Zkopírujte SAML jednotné přihlašování – adresa URL služby a adresu URL odhlašování z **Stručná referenční příručka** oddílu.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Freshdesku.

11. Vyberte **ikona nastavení** a **zabezpečení** části, proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshdesk-tutorial/IC776770.png "jednotné přihlašování")
  
    a. Pro **jednotné přihlašování (SSO)** vyberte **na**.

    b. Vyberte **SAML SSO**.

    c. V **SAML přihlašovací adresa URL** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL** vložit do textového pole **odhlašování URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **otisku certifikátu zabezpečení** vložit do textového pole **kryptografický otisk** hodnotu, která jste získali dříve po odebrání dvojtečky.
  
    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k poli.

![Přiřadit uživatele][200]

**Přiřadit Freshdesku Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Freshdesku**.

    ![Konfigurace jednotného přihlašování](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Freshdesku na přístupovém panelu, měli byste obdržet přihlašovací stránku pro získání přihlášení k aplikaci Freshdesku.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png