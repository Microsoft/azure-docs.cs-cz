---
title: 'Kurz: Azure Active Directory integrace s Qlik Sense Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Qlikem Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: b55e66eaf4bda06369711e389629b6a20765271d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522202"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Kurz: integrace Qlik Sense Enterprise s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Qlik Sense Enterprise s Azure Active Directory (Azure AD). Když integrujete Qlikal Enterprise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Qlik Sense Enterprise.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Qliku společnosti na základě svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Qlik je povolený podnikový odběr s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. 
* Qlik Sense Enterprise podporuje jednotné přihlašování iniciované v rámci **SP** .
* Qlik Sense Enterprise podporuje **zřizování za běhu**

* Jakmile nakonfigurujete Qlik Sense Enterprise, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Přidání Qlik Sense Enterprise z Galerie

Pokud chcete nakonfigurovat integraci Qlikho druhu Enterprise do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Qlik Sense Enterprise z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Qlik Sense Enterprise** .
1. Z panelu výsledků vyberte **Qlik Sense Enterprise** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Qlik Sense Enterprise pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Qlik smyslu Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Qlik Sense Enterprise, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte Qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    * **[Vytvořte Qlik (Enterprise Test User](#create-qlik-sense-enterprise-test-user)** ), abyste měli protějšek Britta Simon v Qliki Enterprise, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce věnované integraci **podnikových aplikací Qlik** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí jednoho z následujících vzorů:

    ```http
    https://<Fully Qualified Domain Name>.qlikpoc.com
    https://<Fully Qualified Domain Name>.qliksense.com
    ```

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí Sign-On skutečné adresy URL, identifikátoru a adresy URL odpovědi, které jsou vysvětleny dále v tomto kurzu, nebo se obraťte na [tým podpory Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) pro získání těchto hodnot. Výchozí port pro adresy URL je 443, ale můžete ho přizpůsobit podle potřeb vaší organizace.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte z daných možností **XML federačních metadat** podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Qliki Enterprise.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Qlik Sense Enterprise**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-qlik-sense-enterprise-sso"></a>Konfigurace Qlikho podnikového jednotného přihlašování

1. Připravte soubor XML s federačními metadaty, abyste ho mohli nahrát na Qlik Server pro rozpoznávání.

    > [!NOTE]
    > Než nahrajete metadata IdP na server pro rozpoznávání Qlik, je potřeba upravit soubor a odebrat informace, aby se zajistila správná operace mezi Azure AD a serverem pro Qlik rozpoznávání.

    ![Snímek obrazovky ukazuje okno Visual Studio Code se souborem federačních metadat X M L.][qs24]

    a. Otevřete FederationMetaData.xml soubor, který jste stáhli z Azure Portal v textovém editoru.

    b. Vyhledejte hodnotu **RoleDescriptor**.  Existují čtyři položky (dvě dvojice levé a pravé značky elementu).

    c. Odstraňte ze souboru značky RoleDescriptor a všechny informace.

    d. Uložte soubor a nechte ho v jeho blízkosti pro pozdější použití v tomto dokumentu.

2. Přejděte do konzoly pro správu Qlik Qlik (QMC) jako uživatel, který může vytvářet konfigurace virtuálních proxy serverů.

3. V QMC klikněte na položku nabídky **virtuální proxy servery** .

    ![Snímek obrazovky se zobrazí virtuální proxy servery vybrané z konfigurace systému.][qs6]

4. V dolní části obrazovky klikněte na tlačítko **vytvořit nové** .

    ![Snímek obrazovky se zobrazí jako možnost vytvořit novou.][qs7]

5. Zobrazí se obrazovka pro úpravu virtuálního proxy serveru.  Na pravé straně obrazovky je nabídka, která usnadňuje zobrazení možností konfigurace.

    ![Snímek obrazovky zobrazuje identifikaci vybranou z vlastností.][qs9]

6. Když je zaškrtnutá možnost nabídky identifikace, zadejte identifikační informace pro konfiguraci virtuálního proxy serveru Azure.

    ![Snímek obrazovky ukazuje oddíl upravit identifikaci virtuálního proxy serveru, kde můžete zadat hodnoty, které jsou popsány.][qs8]  

    a. Pole **Popis** je popisný název pro konfiguraci virtuálního proxy serveru.  Zadejte hodnotu pro popis.

    b. V poli **předpona** se určuje koncový bod virtuálního proxy serveru pro připojení k Qlik smyslu pomocí jednotného přihlašování služby Azure AD.  Zadejte jedinečný název předpony pro tento virtuální proxy server.

    c. **Časový limit nečinnosti relace (minuty)** je časový limit pro připojení prostřednictvím tohoto virtuálního proxy serveru.

    d. **Název hlavičky souboru cookie relace** je název souboru cookie, který ukládá identifikátor relace pro relaci Qlik Sense, kterou uživatel obdrží po úspěšném ověření.  Tento název musí být jedinečný.

7. Klikněte na možnost nabídky ověřování, aby se zobrazila.  Zobrazí se obrazovka ověřování.

    ![Snímek obrazovky ukazuje oddíl upravit ověřování virtuálního proxy serveru, kde můžete zadat hodnoty, které jsou popsány.][qs10]

    a. Rozevírací seznam **režim anonymního přístupu** určuje, jestli můžou anonymní uživatelé přistupovat k Qlik smyslu prostřednictvím virtuálního proxy serveru.  Výchozí možnost není anonymní uživatel.

    b. Rozevírací seznam **metoda ověřování** Určuje schéma ověřování, které bude virtuální proxy server používat.  V rozevíracím seznamu vyberte SAML.  V důsledku toho se zobrazí další možnosti.

    c. Do **pole identifikátor URI hostitele SAML**zadejte název hostitele, který pomocí tohoto virtuálního proxy serveru SAML přistupují k Qlik smyslu.  Název hostitele je identifikátor URI serveru Qlik Sense.

    d. Do pole **ID entity SAML**zadejte stejnou hodnotu, jakou jste zadali v poli identifikátor URI hostitele SAML.

    e. **Metadata IDP SAML** je soubor upravený dříve v části **Upravit metadata federace z Azure AD Configuration** .  **Před nahráním metadat IDP je potřeba upravit soubor,** aby se odebraly informace, aby se zajistila správná operace mezi Azure AD a serverem Qlik Sense.  **Pokud je soubor ještě upravován, přečtěte si prosím pokyny výše.**  Pokud je soubor upravený, klikněte na tlačítko Procházet a vyberte upravený soubor metadat, který chcete odeslat do konfigurace virtuálního proxy serveru.

    f. Zadejte název atributu nebo odkaz na schéma pro atribut SAML představující **ID uživatele** Azure AD, který je odesílán na Qlik Server pro rozpoznávání.  Referenční informace schématu jsou k dispozici na stránce Azure App screens po konfiguraci.  Chcete-li použít atribut Name, zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    například Zadejte hodnotu pro **adresář uživatele** , který se připojí uživatelům při ověřování k serveru Qlik Sense prostřednictvím služby Azure AD.  Hodnoty pevně zakódované musí být obklopeny **hranatými závorkami []**.  Pokud chcete použít atribut odeslaný v kontrolním výrazu SAML služby Azure AD, zadejte název atributu do tohoto textového pole **bez** hranatých závorek.

    h. **Podpisový algoritmus SAML** nastaví poskytovatele služeb (v tomto případě Qlik Server pro rozpoznávání certifikátů) pro konfiguraci virtuálního proxy serveru.  Pokud server Qlik Sense používá důvěryhodný certifikát vygenerovaný pomocí rozšířených zprostředkovatelů šifrování RSA a AES společnosti Microsoft, změňte podpisový algoritmus SAML na **SHA-256**.

    i. Oddíl mapování atributů SAML umožňuje, aby se pro použití v pravidlech zabezpečení odesílaly další atributy, jako jsou skupiny, které se mají odeslat Qlik smyslu.

8. Klikněte na možnost nabídky **VYrovnávání zatížení** , aby se zobrazila.  Zobrazí se obrazovka vyrovnávání zatížení.

    ![Snímek obrazovky ukazuje obrazovku pro úpravu virtuálního proxy serveru pro vyrovnávání zatížení, kde můžete vybrat přidat nový uzel serveru.][qs11]

9. Klikněte na tlačítko **Přidat nový uzel serveru** , vyberte uzel modulu nebo uzly Qlik smysl budou odesílat relace pro účely vyrovnávání zatížení a klikněte na tlačítko **Přidat** .

    ![Snímek obrazovky ukazuje tlačítko Přidat uzly serveru pro vyrovnávání zatížení v dialogovém okně, kde můžete přidat servery.][qs12]

10. Klikněte na možnost Upřesnit nabídku a nastavte ji jako viditelnou. Zobrazí se obrazovka Upřesnit.

    ![Snímek obrazovky s pokročilou obrazovkou pro úpravu virtuálního proxy serveru.][qs13]

    Seznam povolených hostitelů identifikuje názvy hostitelů, které jsou přijaty při připojování k serveru Qlik Sense.  **Zadejte název hostitele, který budou uživatelé zadávat při připojování k serveru Qlik Sense.** Název hostitele má stejnou hodnotu jako identifikátor URI hostitele SAML bez https://.

11. Klikněte na tlačítko **použít** .

    ![Snímek obrazovky se zobrazí na tlačítku použít.][qs14]

12. Kliknutím na OK potvrďte, že se restartuje zpráva s upozorněním, že jsou stavy proxy spojené s virtuálním proxy serverem.

    ![Snímek obrazovky s potvrzením změn použít změny virtuálního proxy serveru.][qs15]

13. Na pravé straně obrazovky se zobrazí nabídka přidružené položky.  Klikněte na možnost nabídky **proxy** .

    ![Snímek obrazovky zobrazuje proxy servery vybrané z přidružených položek.][qs16]

14. Zobrazí se obrazovka proxy serveru.  Kliknutím na tlačítko **odkaz** v dolní části propojíte proxy server s virtuálním proxy serverem.

    ![Snímek obrazovky se zobrazí v tlačítku odkaz.][qs17]

15. Vyberte uzel proxy, který bude podporovat toto připojení k virtuálnímu proxy serveru, a klikněte na tlačítko **odkaz** .  Po propojování se proxy server zobrazí v části přidružené proxy servery.

    ![Snímek obrazovky ukazuje vybrat služby proxy serveru.][qs18]
  
    ![Snímek obrazovky zobrazuje přidružené proxy servery v dialogovém okně přidružené položky virtuálního proxy serveru.][qs19]

16. Po asi pěti až deseti sekundách se zobrazí zpráva QMC aktualizace.  Klikněte na tlačítko **aktualizovat qmc** .

    ![Snímek obrazovky se zobrazí zpráva, že vaše relace skončila.][qs20]

17. Po aktualizaci QMC klikněte na položku nabídky **virtuální proxy servery** . Nová položka virtuálního proxy serveru SAML je uvedena v tabulce na obrazovce.  Jediným kliknutím na položku virtuálního proxy serveru.

    ![Snímek obrazovky zobrazuje virtuální proxy servery s jednou položkou.][qs51]

18. V dolní části obrazovky se aktivuje tlačítko Stáhnout metadata SP.  Kliknutím na tlačítko **Stáhnout metadata SP** uložte metadata do souboru.

    ![Snímek obrazovky se zobrazí na tlačítku pro stažení metadat S P.][qs52]

19. Otevřete soubor metadat SP.  Sledujte položku **entityID** a položku **AssertionConsumerService** .  Tyto hodnoty jsou ekvivalentní **identifikátoru**, **přihlašovací adrese URL** a **adrese URL odpovědi** v konfiguraci aplikace Azure AD. Tyto hodnoty vložte v části **Qlik Sense Enterprise doména a adresy URL** v konfiguraci aplikace Azure AD, pokud se neshodují, a pak je nahraďte v průvodci konfigurací aplikace Azure AD.

    ![Snímek obrazovky se zobrazí v editoru prostého textu s EntityDescriptorem, který se zavolal entityID a AssertionConsumerService.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Vytvořit testovacího uživatele Qlik Sense Enterprise

Qlik Sense Enterprise podporuje **zřizování za běhu**, uživatelé se automaticky přidají do úložiště uživatelů Qlik rozpoznávání firmy, protože používají funkci jednotného přihlašování. Kromě toho můžou klienti používat QMC a vytvořit UDC (User Directory Connector) pro předvyplnění uživatelů v Qlik Sense Enterprise od jejich zvoleného protokolu LDAP, jako je Active Directory a další.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Qlik Sense Enterprise, měli byste být automaticky přihlášeni ke Qlik, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png