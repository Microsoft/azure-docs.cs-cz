---
title: 'Kurz: Integrace Azure Active Directory s Citrix Netscaler | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Citrix Netscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: ca5fec00d264bb50c75338d36ec793946f495bff
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227294"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Kurz: Integrace Azure Active Directory s Citrix Netscaler

V tomto kurzu se dozvíte, jak integrace Citrix Netscaler s Azure Active Directory (Azure AD).
Integrace Citrix Netscaler s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Citrix Netscaler.
* Uživatelům se automaticky přihlášeni k Citrix Netscaler (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Citrix Netscaler, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Citrix Netscaler jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Citrix Netscaler podporuje **SP** jednotné přihlašování zahájené pomocí

* Citrix Netscaler podporuje **JIT** zřizování uživatelů

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Přidání Citrix Netscaler z Galerie

Konfigurace integrace Citrix Netscaler do služby Azure AD, budete muset přidat Citrix Netscaler z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Citrix Netscaler z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Citrix Netscaler**vyberte **Citrix Netscaler** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Citrix Netscaler v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Citrix Netscaler podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Citrix Netscaler.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Citrix Netscaler, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Citrix Netscaler Single Sign-On](#configure-citrix-netscaler-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Citrix Netscaler](#create-citrix-netscaler-test-user)**  – Pokud chcete mít protějšek Britta Simon Citrix Netscaler, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Citrix Netscaler, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Citrix Netscaler** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Citrix Netscaler domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<<Your FQDN>>`

    c. V **adresy URL odpovědi (adresa URL služby příjemce kontrolního výrazu)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Citrix Netscaler klienta](https://www.citrix.com/contact/technical-support.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

    > [!NOTE]
    > Pokud chcete začít pracovat jednotného přihlašování, tyto adresy URL musí být přístupný z veřejných webech. Je potřeba povolit bránu firewall nebo jiná nastavení zabezpečení na straně Netscaler enble Azure AD a odeslání tokenu nakonfigurovaná adresa URL služby ACS.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Citrix Netscaler** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-citrix-netscaler-single-sign-on"></a>Konfigurace Citrix Netscaler jednotného přihlašování

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi Citrix Netscaler jako správce.

2. Ujistěte se, že **verze firmwaru NetScaler = NS12.1: Sestavení 48.13.nc**.

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure01.png)

3. Na **virtuálního serveru VPN** stránce, proveďte následující kroky:

     ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure02.png)

    a. Nastavení brány **ICA pouze** jako **true**.
    
    b. Nastavte **povolit ověřování** jako **true**.
    
    c. **DTLS** je volitelný.
    
    d. Ujistěte se, že **protokolu SSLv3** jako **zakázané**.

4. Přizpůsobené **šifry SSL** se vytvoří skupina dosažení A + na https://www.ssllabs.com jak je znázorněno níže:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure03.png)

5. Na **konfigurovat Server ověřování SAML** stránce, proveďte následující kroky:

      ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure04.png)

    a. V **název** textového pole zadejte název vašeho serveru.

    b. V **adresy URL pro přesměrování** textového pole vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    c. V **adresu URL jednotného odhlašování** textového pole vložte hodnotu **odhlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    d. V **název certifikátu zprostředkovatele identity**, klikněte na tlačítko **"+"** přihlášení k přidání certifikátu, který jste si stáhli z portálu Azure portal. Po nahrání certifikátu prosím vyberte z rozevíracího seznamu.

    e. Podle více polí musí být nastavena na této stránce

      ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure24.png)

    f. Vyberte **požadovaná kontext ověřování** jako **přesné**.

    g. Vyberte **algoritmus podpisu** jako **RSA-SHA256**.

    h. Vyberte **algoritmem Digest metoda** jako **SHA256**.

    i. Zkontrolujte **vynutit uživatelské jméno**.

    j. Klikněte na tlačítko **OK**.

6. Ke konfiguraci **relace profilu**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure06.png)

    a. V **název** textového pole zadejte název profilu relace.

    b. Na **klientské prostředí** kartu, proveďte požadované změny, jak je znázorněno v následujícím snímku obrazovky.

    c. Pokračovat v provádění změn na **kartu Obecné** jak je znázorněno níže a klikněte na tlačítko **OK**

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure07.png)

    d. Na **publikované aplikace** kartu, proveďte požadované změny, jak je znázorněno v následujícím snímku obrazovky a klikněte na tlačítko **OK**.

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure08.png)

    e. Na **zabezpečení** kartu, proveďte požadované změny, jak je znázorněno v následujícím snímku obrazovky a klikněte na tlačítko **OK**.

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure09.png)

7. Vytvořit připojení ICA připojení na portu spolehlivost relace **. 2598** jak je znázorněno následující snímek obrazovky.

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure10.png)

8. Na **SAML** části, přidejte **servery** jak je znázorněno v následujícím snímku obrazovky.

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure11.png)

9. Na **SAML** části, přidejte **zásady** jak je znázorněno v následujícím snímku obrazovky.

     ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure12.png)

10. Na **globální nastavení** stránky, přejděte **pak přístup** části.

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure13.png)

11. Na **konfigurace** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure14.png)

    a. Vyberte **povolit domén**.

    b. V **název domény** textové pole, vyberte doménu.

    c. Klikněte na **OK**.

12. Ujistěte se, **z prodejních míst** nastavení **příjemce pro weby** jak je znázorněno v následujícím snímku obrazovky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure15.png)

13. Na **spravovat metody ověřování - Corp** rozbalovací, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure16.png)

    a. Vyberte **uživatelské jméno a heslo**.

    b. Vyberte **průchozí ze brána NetScaler**.

    c. Klikněte na **OK**.

14. Na **nakonfigurovat důvěryhodné domény** rozbalovací, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure17.png)

    a. Vyberte **důvěryhodných domén pouze**.

    b. Klikněte na **přidat** k přidání domény v **důvěryhodných domén** textového pole.

    c. Vyberte výchozí doménu z vaší **výchozí doménu** seznamu.

    d. Vyberte **zobrazit seznam domén na přihlašovací stránce**.

    e. Klikněte na **OK**.

15. Na **spravovat brány NetScaler** rozbalovací, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure18.png)

    a. Klikněte na **přidat** přidat NetScaler bran v **NetScaler brány** textového pole.

    b. Klikněte na **Zavřít**.

16. Na **z prodejních míst obecné nastavení** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure19.png)

    a. V **zobrazovaný název** textového pole zadejte název NetScaler brány.

    b. V **adresa URL brány NetScaler** textového pole zadejte adresu URL svého NetScaler brány.

    c. Vyberte **využití nebo roli** jako **ověřování a HDX směrování**.

    d. Klikněte na **OK**.

17. Na **z prodejních míst zabezpečení lístku orgán** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure20.png)

    a. Klikněte na **přidat** tlačítko pro přidání vašeho **zabezpečení lístku orgán adresy URL** do textového pole.

    b. Vyberte **povolit relaci spolehlivost**.

    c. Klikněte na **OK**.

18. Na **nastavení ověřování z prodejních míst** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure21.png)

    a. Vyberte vaše **verze**.

    b. Vyberte **typ přihlášení** jako **domény**.

    c. Zadejte vaše **adresu URL zpětného volání**.

    d. Klikněte na **OK**.

19. Na **z prodejních míst nasazení Citrix příjemce** kartu, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure22.png)

    a. Vyberte **možnost nasazení** jako **použití příjemce pro HTML5, pokud není k dispozici místní příjemce**.

    b. Klikněte na **OK**.

20. Na **spravovat signály** rozbalovací, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/citrix-netscaler-tutorial/configure23.png)

    a. Vyberte **interní signál** jako **použijte adresu URL služby**.

    b. Klikněte na tlačítko **přidat** přidat vaší adresy URL v **signální externí pakety** textového pole.

    c. Klikněte na **OK**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Citrix Netscaler.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Citrix Netscaler**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Citrix Netscaler**.

    ![Citrix Netscaler odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-citrix-netscaler-test-user"></a>Vytvořit testovacího uživatele Citrix Netscaler

V této části se vytvoří uživateli Britta Simon v Citrix Netscaler. Citrix Netscaler podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Citrix Netscaler, vytvoří se nový po ověření.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory Citrix Netscaler klienta](https://www.citrix.com/contact/technical-support.html).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Citrix Netscaler na přístupovém panelu, vám by měl být automaticky přihlášeni na Citrix Netscaler, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

