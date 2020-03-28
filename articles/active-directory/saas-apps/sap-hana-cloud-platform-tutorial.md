---
title: 'Kurz: Integrace Služby Azure Active Directory s cloudovou platformou SAP | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudovou platformou SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160083"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Kurz: Integrace Azure Active Directory s cloudovou platformou SAP

V tomto kurzu se dozvíte, jak integrovat cloudovou platformu SAP s Azure Active Directory (Azure AD).
Integrace cloudové platformy SAP s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k cloudové platformě SAP.
* Uživatelům můžete povolit automatické přihlášení k cloudové platformě SAP (jednotné přihlašování) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s cloudovou platformou SAP, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením sap cloudové platformy

Po dokončení tohoto kurzu budou uživatelé Azure AD, které jste přiřadili k cloudové platformě SAP, moci se k aplikaci přihlásit pomocí [úvodu k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Chcete-li otestovat jednotné přihlašování, musíte nasadit vlastní aplikaci nebo se přihlásit k odběru aplikace na účtu SAP Cloud Platform. V tomto kurzu je aplikace nasazena v účtu.
> 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloudová platforma SAP podporuje sonda iniciovaná **sp**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Přidání cloudové platformy SAP z galerie

Chcete-li nakonfigurovat integraci cloudové platformy SAP do Azure AD, musíte přidat cloudovou platformu SAP z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat cloudovou platformu SAP z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Cloud Platform**, z panelu výsledků vyberte SAP Cloud **Platform** a pak klikněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Cloudová platforma SAP v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí cloudové platformy SAP na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v cloudové platformě SAP.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí cloudové platformy SAP, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování sap cloudové platformy](#configure-sap-cloud-platform-single-sign-on)** – pro konfiguraci nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele SAP Cloud Platform](#create-sap-cloud-platform-test-user)** – chcete-li mít protějšek Britta Simon v cloudové platformě SAP, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí cloudové platformy SAP, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **SAP Cloud Platform** vyberte Jednotné **přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Domény cloudové platformy SAP a adresy URL jednotné přihlašovací informace](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlásit se na adresu URL** zadejte adresu URL, kterou uživatelé používají k přihlášení do aplikace SAP Cloud **Platform.** Toto je adresa URL chráněného prostředku v aplikaci SAP Cloud Platform pro konkrétní účet. Adresa URL je založena na následujícím vzoru:`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Toto je adresa URL v aplikaci SAP Cloud Platform, která vyžaduje ověření uživatele.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. V textovém poli **Identifikátor** zadáte adresu URL své cloudové platformy SAP pomocí jednoho z následujících vzorů: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Do textového pole **Adresa URL pro odpověď** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Obraťte se na [tým podpory klienta cloudové platformy SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) a získejte přihlašovací adresu URL a identifikátor. Url odpovědi můžete získat z části správy důvěryhodnosti, která je vysvětlena dále v tutoriálu.
    > 
4. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si xml **metadat federace** z daných možností podle vašeho požadavku a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Konfigurace jednotného přihlášení na sap cloudové platformě

1. V jiném okně webového prohlížeče se přihlaste `https://account.<landscape host>.ondemand.com/cockpit`do kokpitu cloudové platformy SAP na adrese (například: https://account.hanatrial.ondemand.com/cockpit).

2. Klikněte na kartu **Vztah důvěryhodnosti.**
   
    ![Důvěryhodnost](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Důvěryhodnost")

3. V části Správa důvěryhodnosti proveďte v části **Local Service Provider**následující kroky:

    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Správa důvěryhodnosti")
   
    a. Klikněte na **Upravit**.

    b. Jako **typ konfigurace**vyberte **vlastní**.

    c. Jako **název místního zprostředkovatele**ponechte výchozí hodnotu. Zkopírujte tuto hodnotu a vložte ji do pole **Identifikátor** v konfiguraci Azure AD pro cloudovou platformu SAP.

    d. Chcete-li vygenerovat **podpisový klíč** a dvojici klíčů **podpisového certifikátu,** klepněte na **tlačítko Generovat dvojici klíčů**.

    e. Jako **hlavní šíření**vyberte **Zakázáno**.

    f. Jako **vynucené ověřování**vyberte **Možnost Zakázáno**.

    g. Klikněte na **Uložit**.

4. Po uložení nastavení **místního poskytovatele služeb** vyjděte na adresu URL odpovědi následujícím postupem:
   
    ![Získat metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Získat metadata")

    a. Stáhněte si soubor metadat sap cloudové platformy kliknutím na **získat metadata**.

    b. Otevřete stažený soubor XML metadat sap cloudové platformy a vyhledejte značku **ns3:AssertionConsumerService.**
 
    c. Zkopírujte hodnotu atributu **Umístění** a vložte ji do pole **Adresa URL odpovědi** v konfiguraci Azure AD pro cloudovou platformu SAP.

5. Klikněte na kartu **Zprostředkovatel důvěryhodné identity** a potom klikněte na **Přidat důvěryhodného zprostředkovatele identity**.
   
    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Správa důvěryhodnosti")
   
    >[!NOTE]
    >Chcete-li spravovat seznam důvěryhodných poskytovatelů identity, musíte v části Local Service Provider zvolit typ vlastní konfigurace. Pro výchozí typ konfigurace máte neupravitelný a implicitní vztah důvěryhodnosti ke službě SAP ID. Pro žádné, nemáte žádné nastavení důvěryhodnosti.
    > 
    > 

6. Klikněte na kartu **Obecné** a potom kliknutím na **Procházet** nahrajte stažený soubor metadat.
    
    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Správa důvěryhodnosti")
    
    >[!NOTE]
    >Po nahrání souboru metadat se automaticky vyplní hodnoty **adresy URL jednotného přihlášení**, adresy URL **jednotného odhlášení**a **podpisového certifikátu.**
    > 
     
7. Klikněte na kartu **Atributy**.

8. Na kartě **Atributy** proveďte následující krok:
    
    ![Atributy](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributy") 

    a. Klikněte na **Přidat atribut založený na kontrolním výrazu**a přidejte následující atributy založené na kontrolním výrazu:
       
    | Atribut kontrolního výrazu | Hlavní atribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Jméno |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Konfigurace Atributy závisí na tom, jak jsou vyvinuty aplikace na SCP, to znamená, které atributy očekávají v odpovědi SAML a pod kterým názvem (Principal Attribute) přistupují k tomuto atributu v kódu.
     > 
    
    b. **Výchozí atribut** na snímku obrazovky je pouze pro ilustrační účely. Není nutné, aby scénář fungoval.  
 
    c. Názvy a hodnoty pro **hlavní atribut** zobrazený na snímku obrazovky závisí na vývoji aplikace. Je možné, že vaše aplikace vyžaduje různá mapování.

### <a name="assertion-based-groups"></a>Skupiny založené na kontrolním výrazu

Jako volitelný krok můžete nakonfigurovat skupiny založené na kontrolních výrazech pro poskytovatele identity služby Azure Active Directory.

Použití skupin na cloudové platformě SAP umožňuje dynamicky přiřadit jednoho nebo více uživatelů k jedné nebo více rolím v aplikacích cloudové platformy SAP, které jsou určeny hodnotami atributů v kontrolním výrazu SAML 2.0. 

Pokud například kontrolní výraz obsahuje atribut "*contract=temporary*", můžete chtít, aby všichni ovlivnění uživatelé byli přidáni do skupiny*DOČASNÉ*. Skupina "*TEMPORARY*" může obsahovat jednu nebo více rolí z jedné nebo více aplikací nasazených ve vašem účtu cloudové platformy SAP.
 
Skupiny založené na kontrolních výrazech použijte, pokud chcete současně přiřadit mnoho uživatelů k jedné nebo více rolím aplikací v účtu cloudové platformy SAP. Pokud chcete přiřadit ke konkrétním rolím pouze jeden nebo malý počet uživatelů, doporučujeme je přiřadit přímo na kartě Oprávnění v řídicím panelu cloudové**platformy**SAP.

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

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k cloudové platformě SAP.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte SAP **Cloud Platform**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací zadejte a vyberte **SAP Cloud Platform**.

    ![Odkaz SAP cloudová platforma v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-sap-cloud-platform-test-user"></a>Vytvoření testovacího uživatele cloudové platformy SAP

Aby se uživatelům Azure AD povolili přihlašovat k cloudové platformě SAP, musíte jim přiřadit role v cloudové platformě SAP.

**Chcete-li přiřadit roli uživateli, proveďte následující kroky:**

1. Přihlaste se do kokpitu **cloudové platformy SAP.**

2. Proveďte následující kroky:
   
    ![Autorizace](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizace")
   
    a. Klepněte na **položku Autorizace**.

    b. Klikněte na kartu **Uživatelé.**

    c. Do textového pole **Uživatel** zadejte e-mailovou adresu uživatele.

    d. Chcete-li přiřadit uživatele k roli, klepněte na **tlačítko Přiřadit.**

    e. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici SAP cloudové platformy na přístupovém panelu, můžete by měl být automaticky přihlášeni k cloudové platformě SAP, pro které nastavíte přispojené k onomu. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

