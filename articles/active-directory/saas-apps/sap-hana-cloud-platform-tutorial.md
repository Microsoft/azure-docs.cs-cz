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
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964044"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Kurz: Azure Active Directory integrace s cloudovou platformou SAP

V tomto kurzu se dozvíte, jak integrovat cloudovou platformu SAP s Azure Active Directory (Azure AD). Když integruje cloudovou platformu SAP s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k platformě SAP Cloud Platform.
* Umožněte uživatelům, aby se automaticky přihlásili ke cloudové platformě SAP pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudovou platformou SAP potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Odběr povoleného jednotného přihlašování pro cloudovou platformu SAP

Po dokončení tohoto kurzu se uživatelé Azure AD, které jste přiřadili ke cloudové platformě SAP, budou moci přihlásit k aplikaci jediným přihlašovat pomocí [přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

>[!IMPORTANT]
>Abyste mohli testovat jednotné přihlašování, musíte nasadit vlastní aplikaci nebo se přihlásit k odběru aplikace na účtu cloudové platformy SAP. V tomto kurzu se v účtu nasadí aplikace.
> 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloudová platforma SAP podporuje jednotné přihlašování v systému **SP**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Přidání cloudové platformy SAP z Galerie

Pokud chcete nakonfigurovat integraci cloudové platformy SAP do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat cloudovou platformu SAP z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SAP Cloud Platform** .
1. Z panelu výsledků vyberte **cloudová platforma SAP** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro cloudovou platformu SAP

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s cloudovou platformou SAP pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudové platformě SAP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s cloudovou platformou SAP, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování pro cloudovou platformu SAP](#configure-sap-cloud-platform-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte testovacího uživatele pro cloudovou platformu SAP](#create-sap-cloud-platform-test-user)** , abyste měli protějšek Britta Simon v cloudové platformě SAP, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací pro **cloudovou platformu SAP** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

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

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k cloudové platformě SAP.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **cloudová platforma SAP**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-cloud-platform-sso"></a>Konfigurace jednotného přihlašování ke cloudové platformě SAP

1. V jiném okně webového prohlížeče se přihlaste do řídicího panelu pro cloudovou platformu SAP na webu `https://account.<landscape host>.ondemand.com/cockpit` (například: https://account.hanatrial.ondemand.com/cockpit) .

2. Klikněte na kartu **důvěryhodnost** .
   
    ![Důvěryhodnost](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Důvěryhodnost")

3. V části Správa důvěryhodnosti v části **místní poskytovatel služeb** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje oddíl "Správa důvěryhodnosti" se zvolenou kartou "místní poskytovatel služeb" a všemi zvýrazněnými textovými poli.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Správa důvěryhodnosti")
   
    a. Klikněte na **Upravit**.

    b. Jako **typ konfigurace** vyberte **vlastní**.

    c. Jako **název místního poskytovatele** ponechte výchozí hodnotu. Zkopírujte tuto hodnotu a vložte ji do pole **identifikátor** v konfiguraci Azure AD pro cloudovou platformu SAP.

    d. Pokud chcete vygenerovat **podpisový klíč** a dvojici klíčů **podpisového certifikátu** , klikněte na **vygenerovat pár klíčů**.

    e. Jako **rozšíření objektu zabezpečení** vyberte **zakázáno**.

    f. Jako **Vynutit ověřování** vyberte **zakázáno**.

    například Klikněte na **Uložit**.

4. Po uložení nastavení **místních zprostředkovatelů služeb** proveďte následující kroky, abyste získali adresu URL odpovědi:
   
    ![Získat metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Získat metadata")

    a. Stáhněte soubor metadat pro cloudovou platformu SAP kliknutím na **získat metadata**.

    b. Otevřete stažený soubor XML s metadaty pro cloudovou platformu SAP a vyhledejte značku **NS3: AssertionConsumerService** .
 
    c. Zkopírujte hodnotu atributu **Location** a pak ji vložte do pole **Adresa URL odpovědi** v konfiguraci Azure AD pro cloudovou platformu SAP.

5. Klikněte na kartu **důvěryhodný poskytovatel identity** a pak klikněte na **Přidat důvěryhodného zprostředkovatele identity**.
   
    ![Snímek obrazovky zobrazující stránku správy důvěryhodnosti se zvolenou kartou důvěryhodný zprostředkovatel identity](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Správa důvěryhodnosti")
   
    >[!NOTE]
    >Pokud chcete spravovat seznam důvěryhodných zprostředkovatelů identity, musíte zvolit typ vlastní konfigurace v části místní poskytovatel služeb. Pro výchozí typ konfigurace máte neupravitelný a implicitně důvěryhodný vztah ke službě SAP ID. V případě žádného není nastavení vztahu důvěryhodnosti k dispozici.
    > 
    > 

6. Klikněte na kartu **Obecné** a potom kliknutím na tlačítko **Procházet** nahrajte stažený soubor metadat.
    
    ![Správa důvěryhodnosti](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Správa důvěryhodnosti")
    
    >[!NOTE]
    >Po nahrání souboru metadat se automaticky načtou hodnoty **adresy URL jednotného přihlašování**, **adresy URL jednotného odhlášení** a **podpisového certifikátu** .
    > 
     
7. Klikněte na kartu **Atributy**.

8. Na kartě **atributy** proveďte následující krok:
    
    ![Atributy](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributy") 

    a. Klikněte na tlačítko **přidat Assertion-Based atribut** a přidejte následující atributy založené na kontrolním výrazu:
       
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

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení ke cloudové platformě SAP, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení ke cloudové platformě SAP přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici cloudové platformy SAP v okně moje aplikace byste se měli automaticky přihlášeni ke cloudové platformě SAP, pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete cloudovou platformu SAP, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).