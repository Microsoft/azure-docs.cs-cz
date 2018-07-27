---
title: 'Kurz: Integrace Azure Active Directory s konektorem sítě Meta | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Meta sítě konektoru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.openlocfilehash: 8e27ba7d5b245d8857f0c07bfe2923afe9d7e3a0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265638"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Kurz: Integrace Azure Active Directory s konektorem Meta sítě

V tomto kurzu se dozvíte, jak integrovat Meta sítě konektor služby Azure Active Directory (Azure AD).

Integrace konektoru Meta sítí s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Meta sítě konektoru.
- Uživatele, aby automaticky získat přihlášení k Meta sítě konektoru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s konektorem Meta sítě, potřebujete následující položky:

- S předplatným služby Azure AD
- Konektor sítě Meta-jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání konektoru Meta sítě z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Přidání konektoru Meta sítě z Galerie
Ke konfiguraci integrace konektoru Meta sítě do služby Azure AD, budete muset přidat Meta sítě konektor z Galerie na váš seznam spravovaných aplikací SaaS.

**Meta sítě konektoru přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Meta sítě konektor**vyberte **Meta sítě konektor** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Meta sítě konektor v seznamu výsledků](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí konektoru Meta sítě podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatele v konektoru sítí Meta je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatel v konektoru sítí Meta je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s konektorem Meta sítě, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Meta sítě konektor](#create-a-meta-networks-connector-test-user)**  – Pokud chcete mít protějšek Britta Simon v Meta sítě konektor, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Meta sítě konektoru.

**Ke konfiguraci Azure AD jednotné přihlašování s konektorem Meta sítě, proveďte následující kroky:**

1. Na webu Azure Portal na **Meta sítě konektor** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

3. Na **Meta sítě konektor domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Meta sítě konektor domény a adresy URL jednotného přihlašování – informace](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Meta sítě konektor domény a adresy URL jednotného přihlašování – informace](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. V **stav přenosu** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat s identifikátorem skutečná adresa URL odpovědi, a přihlašovací adresa URL jsou vysvětleny dále v tomto kurzu.

5. Aplikace sítě konektor meta očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)

6. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu | Obor názvů|
    | ---------------| --------------- | -------- |
    | jméno | user.givenname | |
    | Příjmení | user.surname | |
    | EmailAddress| User.Mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | jméno | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | User.telephoneNumber | |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. V **Namespace** textového pole zadejte hodnotu oboru názvů, který je zobrazený pro tento řádek.

    e. Klikněte na tlačítko **Ok**

7. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)

8. Na **konfigurace konektoru sítě Meta** klikněte na tlačítko **nakonfigurovat konektor sítě Meta** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)

9. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/metanetworksconnector-tutorial/tutorial_general_400.png)

10. Otevření nové záložky v prohlížeči a přihlaste se k vašemu účtu správce konektoru Meta sítě.

    > [!NOTE]
    > Konektor sítí meta je zabezpečení systému. Proto před přístupem k jejich portálu musíte získat vaše veřejné IP adresy přidat na seznam povolených na své straně. Chcete-li získat veřejnou IP adresu, postupujte níže uvedeném odkazu [tady](https://whatismyipaddress.com/). Poslat vaši IP adresu, které [tým podpory Meta sítě konektor klienta](mailto:support@metanetworks.com) zobrazíte vašeho seznamu povolených IP adres.

11. Přejděte na **správce** a vyberte **nastavení**.

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure3.png)

12. Ujistěte se, že **protokol internetové přenosy** a **platnost VPN MFA** jsou nastaveny na vypnuto.

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure1.png)

13. Přejděte na **správce** a vyberte **SAML**.

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure4.png)

14. Následující postup proveďte **podrobnosti** stránky:

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure2.png)

    a. Kopírování **adresu URL jednotného přihlašování** hodnotu a vložte ho do **přihlašovací adresa URL** textového pole v **Meta sítě konektor domény a adresy URL** oddílu.

    b. Kopírování **příjemce URL** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **Meta sítě konektor domény a adresy URL** oddílu.

    c. Kopírování **identifikátor URI cílové skupiny (SP Entity ID)** hodnotu a vložte ho do **identifikátor (Entity ID)** textového pole v **Meta sítě konektor domény a adresy URL** oddílu.

    d. Povolit SAML

15. Na **Obecné** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/metanetworksconnector-tutorial/configure5.png)

    a. V **jednotné přihlašování – adresa URL zprostředkovatele Identity**, vložte **SAML jednotné přihlašování – adresa URL služby** hodnotu, která jste zkopírovali z portálu Azure portal.

    b. V **Vystavitel zprostředkovatele Identity**, vložte **SAML Entity ID** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. Otevřete stažený certifikát z webu Azure portal v programu Poznámkový blok, vložit do **certifikát X.509** textového pole.

    d. Povolit **Just-in-Time zřizování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/metanetworksconnector-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/metanetworksconnector-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/metanetworksconnector-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/metanetworksconnector-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-meta-networks-connector-test-user"></a>Vytvoření zkušebního uživatele Meta sítě konektoru

Cílem této části je vytvořte uživatele Britta Simon v Meta sítě konektoru. Meta sítě konektor podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Meta sítě konektor, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Meta sítě konektor klienta](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Meta sítě konektoru.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Meta sítě konektor, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Meta sítě konektor**.

    ![Odkaz Meta sítě konektor v seznamu aplikací](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Meta sítě konektor na přístupovém panelu, vám by měl získat automaticky přihlášení k Meta sítě konektoru aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

