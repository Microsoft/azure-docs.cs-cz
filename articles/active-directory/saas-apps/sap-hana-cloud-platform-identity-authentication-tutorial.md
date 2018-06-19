---
title: 'Kurz: Azure Active Directory integrace s SAP cloudové platformy identitu ověřování | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP cloudové platformy identitu ověřování.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 388107e262f4780c6df307de51475b6bca3f5c2f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899000"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Kurz: Azure Active Directory integrace s SAP cloudové platformy identitu ověřování

V tomto kurzu zjistěte, jak integrovat SAP cloudové platformy identitu ověřování s Azure Active Directory (Azure AD). SAP cloudové platformy identitu ověřování se používá jako proxy server poskytovatelů identity pro přístup k SAP aplikace, které používají Azure AD jako hlavní rozšíření IdP.

Pokud integrujete SAP cloudové platformy identitu ověřování s Azure AD, získáte následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k aplikacím SAP.
- Můžete povolit uživatelům automaticky přihlásit k SAP aplikace s účty služby Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP cloudové platformy identitu ověřování, potřebujete následující položky:

- Předplatné služby Azure AD.
- V rámci jednoho přihlášení na povoleno předplatného pro SAP cloudové platformy identitu ověřování.

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí, zkušební verze Azure AD, [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář, který je popsané v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP cloudové platformy identitu ověřování z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

Před podrobně technické informace, je důležité pochopit, koncepty, které se chystáte podívejte se na. SAP cloudové platformy identitu ověřování a Active Directory Federation Services umožňují implementaci jednotného přihlašování napříč aplikace nebo služby, které jsou chráněné službou Azure AD (jako IdP) s aplikacemi SAP a služby, které jsou chráněny SAP cloudu Ověření Identity platformy.

V současné době SAP cloudové platformy identitu ověřování funguje jako zprostředkovatel Identity Proxy aplikací SAP. Azure Active Directory pak funguje jako počáteční zprostředkovatele Identity v rámci této instalace. 

Následující obrázek znázorňuje tuto relaci:

![Vytváření testovacího uživatele Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

S tímto nastavením vašeho klienta SAP cloudové platformy identitu ověřování je nakonfigurován jako důvěryhodné aplikace v Azure Active Directory. 

Všechny aplikace SAP a služby, které chcete chránit tímto způsobem jsou následně nakonfigurovat v konzole pro správu SAP cloudové platformy identitu ověřování.

Autorizace pro udělení přístupu k SAP aplikacím a službám proto musí proběhnout v SAP cloudové platformy identitu ověřování (na rozdíl od Azure Active Directory).

Nakonfigurováním SAP cloudové platformy identitu ověřování jako aplikaci prostřednictvím Azure Active Directory Marketplace, nemusíte konfigurovat jednotlivé deklaracích a tvrzením SAML.

>[!NOTE] 
>Aktuálně jedinou jednotného přihlašování k webu byl testován obě strany. Toky, které jsou potřebné pro aplikaci API nebo rozhraní API API komunikace by měla fungovat, ale ještě nebyly testovány. Bude být testována při následné aktivity.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidat SAP cloudové platformy identitu ověřování z Galerie
Konfigurace integrace SAP cloudové platformy identitu ověřování ve službě Azure AD, potřebujete přidat SAP cloudové platformy identitu ověřování z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat SAP cloudové platformy identitu ověřování z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), na levém navigačním panelu, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat nové novou aplikaci, vyberte **novou aplikaci** tlačítka v horní části dialogových oken.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP cloudové platformy identitu ověřování**. 

5. Vyberte **SAP cloudové platformy identitu ověřování** z panelu výsledky a potom vyberte **přidat** tlačítko.

    ![SAP cloudové platformy identitu ověřování v seznamu výsledků](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s SAP cloudové platformy identitu ověřování. Můžete nakonfigurovat a otestovat s názvem "Britta Simon." zkušebního uživatele

Pro jednotné přihlašování pro práci je potřeba vědět, kdo je uživatel protějškem v SAP cloudové platformy identitu ověřování Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v SAP cloudové platformy identitu ověřování.

V SAP cloudové platformy identitu ověřování, zadejte hodnotu **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dvěma uživateli.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SAP cloudové platformy identitu ověřování, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvořit testovací uživatele s SAP cloudové platformy identitu ověřování](#create-an-sap-cloud-platform-identity-authentication-test-user) tak, aby měl protějšek Britta Simon v SAP cloudové platformy identitu ověřování propojeném s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SAP cloudové platformy identitu ověřování.

**Ke konfiguraci Azure AD jednotné přihlašování s SAP cloudové platformy identitu ověřování, proveďte následující kroky:**

1. Na portálu Azure na **SAP cloudové platformy identitu ověřování** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. V **jednotného přihlašování** dialogovém **na základě SAML přihlašování**, vyberte **režimu** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

3. Pokud chcete nakonfigurovat aplikace **IDP** spustil v režimu **SAP cloudové platformy identitu ověřování domény a adresy URL** část, proveďte následující kroky:  

    ![SAP cloudové platformy identitu ověřování domény a adresy URL jednotné přihlašování informace](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. V **identifikátor** pole, zadejte adresu URL pomocí vzoru následující: `<IAS-tenant-id>.accounts.ondemand.com`

    b. V **adresa URL odpovědi** pole, zadejte adresu URL pomocí vzoru následující: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se [tým podpory SAP cloudové platformy identitu ověřování klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) k získání těchto hodnot. Pokud nerozumíte hodnota identifikátoru, přečtěte si dokumentaci SAP cloudové platformy identitu ověřování o [konfigurace klienta SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Pokud chcete nakonfigurovat aplikace **SP** initiated režimu, vyberte **zobrazit upřesňující nastavení adresy URL**.

    ![SAP cloudové platformy identitu ověřování domény a adresy URL jednotné přihlašování informace](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí následujícího vzorce: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Použijte prosím vaše konkrétní obchodní aplikace přihlašovací adresa URL. Obraťte se [tým podpory SAP cloudové platformy identitu ověřování klienta](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) Pokud máte jakékoli nejistých.

5. V **SAML podpisový certifikát** vyberte **soubor XML s metadaty**. Uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

6. Ověření Identity platformy cloudové aplikace SAP očekává SAML kontrolní výrazy ve specifickém formátu. Správa hodnoty z těchto atributů **uživatelské atributy** části na stránce integrace aplikace. Následující snímek obrazovky ukazuje příklad formátu. 

    ![Konfigurovat jednotné přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

7. Pokud vaše aplikace SAP očekává atribut, jako **firstName**, přidejte **firstName** atribut **uživatelské atributy** části. Tato možnost je dostupná v **jednotného přihlašování** dialogové okno s **atributy tokenu SAML** dialogové okno...

    a. Chcete-li otevřít **přidat atribut** dialogové okno, vyberte **přidat atribut**. 
    
    ![Konfigurovat jednotné přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. V **název** zadejte název atributu **firstName**.
    
    c. Z **hodnotu** vyberte hodnotu atributu **user.givenname**.
    
    d. Vyberte **Ok**.

8. Vyberte tlačítko **Uložit**.

    ![Konfigurovat jednotné přihlašování tlačítko Uložit](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

9. V **SAP cloudové platformy identitu ověřování konfigurace** vyberte **konfigurace SAP cloudové platformy identitu ověřování** otevřete **konfigurovat přihlašování**okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace ověřování SAP cloudové platformy Identity](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

10. Jednotné přihlašování, které jsou nakonfigurované pro vaši aplikaci, přejděte do konzoly pro správu SAP cloudové platformy identitu ověřování. Adresa URL má následující vzor: `https://<tenant-id>.accounts.ondemand.com/admin`. Přečtěte si dokumentaci o SAP cloudové platformy identitu ověřování v [integrace se službou Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. Na portálu Azure vyberte **Uložit** tlačítko.

12. V následující pokračujte, pouze pokud chcete přidat a povolení jednotného přihlašování pro jinou aplikaci SAP. Opakujte kroky v části **přidání SAP cloudové platformy ověření Identity z Galerie**.

13. Na portálu Azure na **SAP cloudové platformy identitu ověřování** stránky integrace aplikací, vyberte **propojené přihlášení**.

    ![Konfigurovat propojené přihlášení](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

14. Uložte konfiguraci.

>[!NOTE] 
>Nová aplikace využívá konfiguraci jednoho přihlášení předchozí aplikaci SAP. Ujistěte se, že používáte stejné podnikové poskytovatelů identit v konzole pro správu SAP cloudové platformy identitu ověřování.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com) při nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a přístup k vložený dokumentace prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Vytvořit testovací uživatele s SAP cloudové platformy identitu ověřování

Nemusíte vytvořte uživatele v SAP cloudové platformy identitu ověřování. Uživatelé, kteří jsou v úložišti uživatele Azure AD můžete použít funkci jednotného přihlašování.

SAP cloudové platformy identitu ověřování podporuje možnost federaci identit. Tato možnost umožňuje aplikace a zjistit, jestli uživatelé, kteří se ověřují zprostředkovatelem podnikové identitě existují v úložišti z SAP cloudové platformy Identity ověření uživatele. 

Ve výchozím nastavení vypnutá možnost federaci identit. Pokud je povoleno federaci identit, pouze uživatelé, kteří jsou importovány v SAP cloudové platformy identitu ověřování přístup k aplikaci. 

Další informace o tom, jak povolit nebo zakázat federaci identit s SAP cloudové platformy identitu ověřování najdete v části "Povolení federace s SAP cloudové platformy Identity ověření Identity" v [konfigurace federaci identit pomocí Ověření Identity uživatele úložiště SAP cloudové platformy](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k SAP cloudové platformy identitu ověřování.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SAP cloudové platformy identitu ověřování, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace a pak přejděte do zobrazení adresáře. Potom přejděte na **podnikové aplikace, které**a potom vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SAP cloudové platformy identitu ověřování**.

    ![Odkaz SAP cloudové platformy identitu ověřování v seznamu aplikací](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** v tlačítko **uživatelů a skupin** dialogové okno.

7. Vyberte **přiřadit** v tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždici SAP cloudové platformy Identity ověřování na přístupovém panelu, můžete získat automaticky přihlášeni k aplikaci SAP cloudové platformy identitu ověřování.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
