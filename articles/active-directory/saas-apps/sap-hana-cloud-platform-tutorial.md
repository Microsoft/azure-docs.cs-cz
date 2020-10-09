---
title: 'Kurz: Azure Active Directory integrace s cloudovou platformou SAP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudovou platformou SAP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: c2738e1a6168440adee79ebaa599a313600153a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546759"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Kurz: Azure Active Directory integrace s cloudovou platformou SAP

V tomto kurzu se dozvíte, jak integrovat cloudovou platformu SAP s Azure Active Directory (Azure AD).
Integrace cloudové platformy SAP s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k platformě SAP Cloud Platform.
* Uživatelům můžete povolit, aby se automaticky přihlásili k platformě SAP Cloud Platform (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudovou platformou SAP potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování pro cloudovou platformu SAP

Po dokončení tohoto kurzu se uživatelé Azure AD, které jste přiřadili ke cloudové platformě SAP, budou moci přihlásit k aplikaci jediným přihlašovat pomocí [přístupového panelu](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Abyste mohli testovat jednotné přihlašování, musíte nasadit vlastní aplikaci nebo se přihlásit k odběru aplikace na účtu cloudové platformy SAP. V tomto kurzu se v účtu nasadí aplikace.
> 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloudová platforma SAP podporuje jednotné přihlašování v systému **SP**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Přidání cloudové platformy SAP z Galerie

Pokud chcete nakonfigurovat integraci cloudové platformy SAP do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat cloudovou platformu SAP z galerie.

**Pokud chcete přidat cloudovou platformu SAP z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Cloud Platform**, vyberte možnost **SAP Cloud Platform** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Cloudová platforma SAP v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s cloudovou platformou SAP na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudové platformě SAP.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí cloudové platformy SAP, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování pro cloudovou platformu SAP](#configure-sap-cloud-platform-single-sign-on)** a nakonfigurujte nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele pro cloudovou platformu SAP](#create-sap-cloud-platform-test-user)** , abyste měli protějšek Britta Simon v cloudové platformě SAP, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí cloudové platformy SAP, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací pro **cloudovou platformu SAP** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL cloudové platformy SAP](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL, kterou vaši uživatelé používají k přihlášení do aplikace pro **cloudovou platformu SAP** . Jedná se o konkrétní adresu URL chráněného prostředku v aplikaci pro cloudovou platformu SAP. Adresa URL je založena na následujícím vzoru: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Toto je adresa URL aplikace pro cloudovou platformu SAP, která vyžaduje ověření uživatele.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Do textového pole **identifikátoru** zadejte adresu URL typu Cloud Platform SAP a použijte jeden z následujících vzorů: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátorem a adresou URL odpovědi. Pokud chcete získat Sign-On adresu URL a identifikátor, obraťte se na [tým podpory klientů pro cloudovou platformu SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) . Adresa URL odpovědi, kterou můžete získat z oddílu správy důvěryhodnosti, který je vysvětlen později v tomto kurzu.
    > 
4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Konfigurace jediné Sign-On pro cloudovou platformu SAP

1. V jiném okně webového prohlížeče se přihlaste do řídicího panelu pro cloudovou platformu SAP na webu `https://account.<landscape host>.ondemand.com/cockpit` (například: https://account.hanatrial.ondemand.com/cockpit) .

2. Klikněte na kartu **důvěryhodnost** .
   
    ![Důvěryhodnost](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Důvěryhodnost")

3. V části Správa důvěryhodnosti v části **místní poskytovatel služeb**proveďte následující kroky:

    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Správa důvěryhodnosti")
   
    a. Klikněte na **Upravit**.

    b. Jako **typ konfigurace**vyberte **vlastní**.

    c. Jako **název místního poskytovatele**ponechte výchozí hodnotu. Zkopírujte tuto hodnotu a vložte ji do pole **identifikátor** v konfiguraci Azure AD pro cloudovou platformu SAP.

    d. Pokud chcete vygenerovat **podpisový klíč** a dvojici klíčů **podpisového certifikátu** , klikněte na **vygenerovat pár klíčů**.

    e. Jako **rozšíření objektu zabezpečení**vyberte **zakázáno**.

    f. Jako **Vynutit ověřování**vyberte **zakázáno**.

    například Klikněte na **Uložit**.

4. Po uložení nastavení **místních zprostředkovatelů služeb** proveďte následující kroky, abyste získali adresu URL odpovědi:
   
    ![Získat metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Získat metadata")

    a. Stáhněte soubor metadat pro cloudovou platformu SAP kliknutím na **získat metadata**.

    b. Otevřete stažený soubor XML s metadaty pro cloudovou platformu SAP a vyhledejte značku **NS3: AssertionConsumerService** .
 
    c. Zkopírujte hodnotu atributu **Location** a pak ji vložte do pole **Adresa URL odpovědi** v konfiguraci Azure AD pro cloudovou platformu SAP.

5. Klikněte na kartu **důvěryhodný poskytovatel identity** a pak klikněte na **Přidat důvěryhodného zprostředkovatele identity**.
   
    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Správa důvěryhodnosti")
   
    >[!NOTE]
    >Pokud chcete spravovat seznam důvěryhodných zprostředkovatelů identity, musíte zvolit typ vlastní konfigurace v části místní poskytovatel služeb. Pro výchozí typ konfigurace máte neupravitelný a implicitně důvěryhodný vztah ke službě SAP ID. V případě žádného není nastavení vztahu důvěryhodnosti k dispozici.
    > 
    > 

6. Klikněte na kartu **Obecné** a potom kliknutím na tlačítko **Procházet** nahrajte stažený soubor metadat.
    
    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Správa důvěryhodnosti")
    
    >[!NOTE]
    >Po nahrání souboru metadat se automaticky načtou hodnoty **adresy URL jednotného přihlašování**, **adresy URL jednotného odhlášení**a **podpisového certifikátu** .
    > 
     
7. Klikněte na kartu **Atributy**.

8. Na kartě **atributy** proveďte následující krok:
    
    ![Atributy](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributy") 

    a. Klikněte na tlačítko **přidat Assertion-Based atribut**a přidejte následující atributy založené na kontrolním výrazu:
       
    | Atribut kontrolního výrazu | Atribut objektu zabezpečení |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |polím |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >Konfigurace atributů závisí na tom, jak jsou vyvíjeny aplikace v bodu připojení služby (SCP). to znamená, které atributy očekávají v odpovědi SAML a pod kterým názvem (atribut Principal) přistupují k tomuto atributu v kódu.
     > 
    
    b. **Výchozí atribut** na snímku obrazovky je pouze pro ilustraci. Není nutné, aby scénář fungoval.  
 
    c. Názvy a hodnoty pro **atribut zabezpečení** zobrazené na snímku obrazovky závisí na vývoji aplikace. Je možné, že vaše aplikace vyžaduje jiné mapování.

### <a name="assertion-based-groups"></a>Skupiny založené na kontrolních výrazech

Jako volitelný krok můžete nakonfigurovat skupiny založené na kontrolních výrazech pro poskytovatele identity Azure Active Directory.

Použití skupin na cloudové platformě SAP umožňuje dynamicky přiřadit jednoho nebo více uživatelů k jedné nebo více rolím v aplikacích pro cloudovou platformu SAP, které určí hodnoty atributů v kontrolním výrazu SAML 2,0. 

Pokud například kontrolní výraz obsahuje atribut "*kontrakt = dočasná*", můžete chtít, aby všechny ovlivněné uživatele byly přidány do skupiny "*dočasné*". Skupina "*dočasné*" může obsahovat jednu nebo více rolí z jedné nebo více aplikací nasazených ve vašem účtu cloudové platformy SAP.
 
Použijte skupiny založené na kontrolních výrazech, pokud chcete současně přiřadit mnoho uživatelů k jedné nebo více rolím aplikací v účtu cloudové platformy SAP. Pokud chcete určitým rolím přiřadit jenom jeden nebo malý počet uživatelů, doporučujeme je přiřadit přímo na kartě "**autorizace**" v řídicím panelu pro cloudovou platformu SAP.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k cloudové platformě SAP.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **SAP Cloud Platform**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte možnost **SAP Cloud Platform**.

    ![Odkaz na cloudovou platformu SAP v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sap-cloud-platform-test-user"></a>Vytvořit testovacího uživatele pro cloudovou platformu SAP

Aby se uživatelé Azure AD mohli přihlásit k platformě SAP Cloud Platform, musíte jim přiřadit role v cloudové platformě SAP.

**Chcete-li uživateli přiřadit roli, proveďte následující kroky:**

1. Přihlaste se ke svému řídicímu panelu pro **cloudovou platformu SAP** .

2. Proveďte následující:
   
    ![Autorizace](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizace")
   
    a. Klikněte na **autorizace**.

    b. Klikněte na kartu **Uživatelé** .

    c. Do textového pole **uživatel** zadejte e-mailovou adresu uživatele.

    d. Kliknutím na **přiřadit** přiřaďte uživatele k roli.

    e. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici cloudová platforma SAP na přístupovém panelu, měli byste se automaticky přihlásit ke cloudové platformě SAP, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

