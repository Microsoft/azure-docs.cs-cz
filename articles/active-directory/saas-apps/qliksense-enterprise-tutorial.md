---
title: 'Kurz: Azure Active Directory integrace s Qlik Sense Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Qlikem Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9265a5951ceb7b0cb757e392c2e26aa19bfefd06
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678497"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Kurz: Integrace Qlik Sense Enterprise s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Qlik Sense Enterprise s Azure Active Directory (Azure AD). Když integrujete Qlikal Enterprise s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Qlik Sense Enterprise.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Qliku společnosti na základě svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Qlik je povolený podnikový odběr s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Qlik Sense Enterprise podporuje jednotné přihlašování iniciované v rámci **SP** .

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Přidání Qlik Sense Enterprise z Galerie

Pokud chcete nakonfigurovat integraci Qlikho druhu Enterprise do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Qlik Sense Enterprise z galerie.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Qlik Sense Enterprise** .
1. Z panelu výsledků vyberte **Qlik Sense Enterprise** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Qlik Sense Enterprise pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Qlik smyslu Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Qlik Sense Enterprise, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte Qlik Sense Enterprise SSO](#configure-qlik-sense-enterprise-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte Qlik (Enterprise Test User](#create-qlik-sense-enterprise-test-user)** ), abyste měli protějšek Britta Simon v Qliki Enterprise, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce věnované integraci **podnikových aplikací Qlik** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi, které jsou vysvětleny dále v tomto kurzu, nebo se obraťte na [tým podpory Qlik Sense Enterprise Client](https://www.qlik.com/us/services/support) pro získání těchto hodnot. Výchozí port pro adresy URL je 443, ale můžete ho přizpůsobit podle potřeb vaší organizace.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte z daných možností **XML federačních metadat** podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-sso"></a>Konfigurace Qlikho podnikového jednotného přihlašování

1. Připravte soubor XML s federačními metadaty, abyste ho mohli nahrát na Qlik Server pro rozpoznávání.

    > [!NOTE]
    > Než nahrajete metadata IdP na server pro rozpoznávání Qlik, je potřeba upravit soubor a odebrat informace, aby se zajistila správná operace mezi Azure AD a serverem pro Qlik rozpoznávání.

    ![QlikSense][qs24]

    a. Otevřete soubor FederationMetaData. XML, který jste stáhli z Azure Portal v textovém editoru.

    b. Vyhledejte hodnotu **RoleDescriptor**.  Existují čtyři položky (dvě dvojice levé a pravé značky elementu).

    c. Odstraňte ze souboru značky RoleDescriptor a všechny informace.

    d. Uložte soubor a nechte ho v jeho blízkosti pro pozdější použití v tomto dokumentu.

2. Přejděte do konzoly pro správu Qlik Qlik (QMC) jako uživatel, který může vytvářet konfigurace virtuálních proxy serverů.

3. V QMC klikněte na položku nabídky **virtuální proxy servery** .

    ![QlikSense][qs6]

4. V dolní části obrazovky klikněte na tlačítko **vytvořit nové** .

    ![QlikSense][qs7]

5. Zobrazí se obrazovka pro úpravu virtuálního proxy serveru.  Na pravé straně obrazovky je nabídka, která usnadňuje zobrazení možností konfigurace.

    ![QlikSense][qs9]

6. Když je zaškrtnutá možnost nabídky identifikace, zadejte identifikační informace pro konfiguraci virtuálního proxy serveru Azure.

    ![QlikSense][qs8]  

    a. Pole **Popis** je popisný název pro konfiguraci virtuálního proxy serveru.  Zadejte hodnotu pro popis.

    b. V poli Předpona se určuje koncový bod virtuálního proxy serveru pro připojení k Qlik smyslu pomocí jednotného přihlašování služby Azure AD.  Zadejte jedinečný název předpony pro tento virtuální proxy server.

    c. **Časový limit nečinnosti relace (minuty)** je časový limit pro připojení prostřednictvím tohoto virtuálního proxy serveru.

    d. **Název hlavičky souboru cookie relace** je název souboru cookie, který ukládá identifikátor relace pro relaci Qlik Sense, kterou uživatel obdrží po úspěšném ověření.  Tento název musí být jedinečný.

7. Klikněte na možnost nabídky ověřování, aby se zobrazila.  Zobrazí se obrazovka ověřování.

    ![QlikSense][qs10]

    a. Rozevírací seznam **režim anonymního přístupu** určuje, jestli můžou anonymní uživatelé přistupovat k Qlik smyslu prostřednictvím virtuálního proxy serveru.  Výchozí možnost není anonymní uživatel.

    b. Rozevírací seznam **metoda ověřování** Určuje schéma ověřování, které bude virtuální proxy server používat.  V rozevíracím seznamu vyberte SAML.  V důsledku toho se zobrazí další možnosti.

    c. Do **pole identifikátor URI hostitele SAML**zadejte název hostitele, který pomocí tohoto virtuálního proxy serveru SAML přistupují k Qlik smyslu.  Název hostitele je identifikátor URI serveru Qlik Sense.

    d. Do pole **ID entity SAML**zadejte stejnou hodnotu, jakou jste zadali v poli identifikátor URI hostitele SAML.

    e. **Metadata IDP SAML** je soubor upravený dříve v části **Upravit metadata federace z Azure AD Configuration** .  **Před nahráním metadat IDP je potřeba upravit soubor,** aby se odebraly informace, aby se zajistila správná operace mezi Azure AD a serverem Qlik Sense.  **Pokud je soubor ještě upravován, přečtěte si prosím pokyny výše.**  Pokud je soubor upravený, klikněte na tlačítko Procházet a vyberte upravený soubor metadat, který chcete odeslat do konfigurace virtuálního proxy serveru.

    f. Zadejte název atributu nebo odkaz na schéma pro atribut SAML představující **ID uživatele** Azure AD, který je odesílán na Qlik Server pro rozpoznávání.  Referenční informace schématu jsou k dispozici na stránce Azure App screens po konfiguraci.  Chcete-li použít atribut Name, `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`zadejte.

    g. Zadejte hodnotu pro **adresář uživatele** , který se připojí uživatelům při ověřování k serveru Qlik Sense prostřednictvím služby Azure AD.  Hodnoty pevně zakódované musí být obklopeny **hranatými závorkami []** .  Pokud chcete použít atribut odeslaný v kontrolním výrazu SAML služby Azure AD, zadejte název atributu do tohoto textového pole **bez** hranatých závorek.

    h. **Podpisový algoritmus SAML** nastaví poskytovatele služeb (v tomto případě Qlik Server pro rozpoznávání certifikátů) pro konfiguraci virtuálního proxy serveru.  Pokud server Qlik Sense používá důvěryhodný certifikát vygenerovaný pomocí rozšířených zprostředkovatelů šifrování RSA a AES společnosti Microsoft, změňte podpisový algoritmus SAML na **SHA-256**.

    i. Oddíl mapování atributů SAML umožňuje, aby se pro použití v pravidlech zabezpečení odesílaly další atributy, jako jsou skupiny, které se mají odeslat Qlik smyslu.

8. Klikněte na možnost nabídky vyrovnávání **zatížení** , aby se zobrazila.  Zobrazí se obrazovka vyrovnávání zatížení.

    ![QlikSense][qs11]

9. Klikněte na tlačítko **Přidat nový uzel serveru** , vyberte uzel modulu nebo uzly Qlik smysl budou odesílat relace pro účely vyrovnávání zatížení a klikněte na tlačítko **Přidat** .

    ![QlikSense][qs12]

10. Klikněte na možnost Upřesnit nabídku a nastavte ji jako viditelnou. Zobrazí se obrazovka Upřesnit.

    ![QlikSense][qs13]

    Seznam povolených hostitelů identifikuje názvy hostitelů, které jsou přijaty při připojování k serveru Qlik Sense.  **Zadejte název hostitele, který budou uživatelé zadávat při připojování k serveru Qlik Sense.** Název hostitele má stejnou hodnotu jako identifikátor URI hostitele SAML bez https://.

11. Klikněte na tlačítko **použít** .

    ![QlikSense][qs14]

12. Kliknutím na OK potvrďte, že se restartuje zpráva s upozorněním, že jsou stavy proxy spojené s virtuálním proxy serverem.

    ![QlikSense][qs15]

13. Na pravé straně obrazovky se zobrazí nabídka přidružené položky.  Klikněte na možnost nabídky **proxy** .

    ![QlikSense][qs16]

14. Zobrazí se obrazovka proxy serveru.  Kliknutím na tlačítko **odkaz** v dolní části propojíte proxy server s virtuálním proxy serverem.

    ![QlikSense][qs17]

15. Vyberte uzel proxy, který bude podporovat toto připojení k virtuálnímu proxy serveru, a klikněte na tlačítko **odkaz** .  Po propojování se proxy server zobrazí v části přidružené proxy servery.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Po asi pěti až deseti sekundách se zobrazí zpráva QMC aktualizace.  Klikněte na tlačítko **aktualizovat qmc** .

    ![QlikSense][qs20]

17. Po aktualizaci QMC klikněte na položku nabídky **virtuální proxy servery** . Nová položka virtuálního proxy serveru SAML je uvedena v tabulce na obrazovce.  Jediným kliknutím na položku virtuálního proxy serveru.

    ![QlikSense][qs51]

18. V dolní části obrazovky se aktivuje tlačítko Stáhnout metadata SP.  Kliknutím na tlačítko **Stáhnout metadata SP** uložte metadata do souboru.

    ![QlikSense][qs52]

19. Otevřete soubor metadat SP.  Sledujte položku **entityID** a položku **AssertionConsumerService** .  Tyto hodnoty jsou ekvivalentní identifikátoru, **přihlašovací adrese URL** a **adrese URL odpovědi** v konfiguraci aplikace Azure AD. Tyto hodnoty vložte v části **Qlik Sense Enterprise doména a adresy URL** v konfiguraci aplikace Azure AD, pokud se neshodují, a pak je nahraďte v průvodci konfigurací aplikace Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Qliki Enterprise.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Qlik Sense Enterprise**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-qlik-sense-enterprise-test-user"></a>Vytvořit testovacího uživatele Qlik Sense Enterprise

V této části vytvoříte uživatele s názvem Britta Simon v Qliku Enterprise. Pokud chcete přidat uživatele na platformě Qlik Sense Enterprise, pracujte s [týmem Qlik Sense Enterprise support](https://www.qlik.com/us/services/support) . Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Qlik Sense Enterprise, měli byste být automaticky přihlášeni ke Qlik, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
