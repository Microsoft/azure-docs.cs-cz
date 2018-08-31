---
title: 'Kurz: Integrace Azure Active Directory ArcGIS Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ea2b32b43fedacba7b8a60db29762c32fda65aa5
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306338"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Kurz: Integrace Azure Active Directory ArcGIS Enterprise

V tomto kurzu se dozvíte, jak integrovat ArcGIS organizace Azure Active Directory (Azure AD).

ArcGIS podnikové integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do firemní sítě ArcGIS.
- Uživatele, aby automaticky získat přihlášení k ArcGIS Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ArcGIS Enterprise, budete potřebovat následující položky:

- Předplatné Azure AD
- ArcGIS podnikové jednotné přihlašování povoleno předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání organizace ArcGIS z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Přidání organizace ArcGIS z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS organizace do služby Azure AD, budete muset přidat ArcGIS Enterprise na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání organizace ArcGIS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ArcGIS Enterprise**vyberte **ArcGIS Enterprise** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![ArcGIS Enterprise v seznamu výsledků](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s ArcGIS Enterprise podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ArcGIS Enterprise je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v podniku ArcGIS je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ArcGIS organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele organizace ArcGIS](#create-an-arcgis-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon ArcGIS Enterprise, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ArcGIS Enterprise.

**Ke konfiguraci Azure AD jednotné přihlašování s ArcGIS organizace, proveďte následující kroky:**

1. Na webu Azure Portal na **ArcGIS Enterprise** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. Na **ArcGIS podnikové domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![ArcGIS podnikové domény a adresy URL jednotného přihlašování – informace](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `<EXTERNAL_DNS_NAME>.portal`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![ArcGIS podnikové domény a adresy URL jednotného přihlašování – informace](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory ArcGIS Enterprise Client](mailto:support@esri.com) k získání těchto hodnot. Zobrazí se hodnota identifikátoru z **nastavit zprostředkovatele Identity** oddíl, což je vysvětleno dále v tomto kurzu.

5. Na **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku.

    ![Odkaz ke stažení certifikátu](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. V okně jiné webové prohlížeče Přihlaste se k webu ArcGIS Enterprise společnosti jako správce.

8. Vyberte **organizace > upravovat nastavení**.

    ![ArcGIS podniková konfigurace](./media/arcgisenterprise-tutorial/configure1.png)

9. Vyberte **zabezpečení** kartu.

    ![ArcGIS podniková konfigurace](./media/arcgisenterprise-tutorial/configure2.png)

10. Přejděte dolů k položce **podnikové přihlašování pomocí SAML** a vyberte **nastavit přihlášení ENTERPRISE**.

    ![ArcGIS podniková konfigurace](./media/arcgisenterprise-tutorial/configure3.png)

11. Na **nastavit zprostředkovatele Identity** části, proveďte následující kroky:

    ![ArcGIS podniková konfigurace](./media/arcgisenterprise-tutorial/configure4.png)

    a. Zadejte prosím název, například **Azure Active Directory Test** v **název** textového pole.

    b. V **URL** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. Klikněte na tlačítko **zobrazit pokročilé nastavení** a zkopírujte **Entity ID** hodnotu a vložte ho do **identifikátor** textového pole v **ArcGIS podnikové domény a adresy URL** části webu Azure Portal.
    
    ![ArcGIS podniková konfigurace](./media/arcgisenterprise-tutorial/configure5.png)

    d. Klikněte na tlačítko **aktualizace zprostředkovatele IDENTITY**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-an-arcgis-enterprise-test-user"></a>Vytvořit testovacího uživatele organizace ArcGIS

Cílem této části je vytvořte uživatele Britta Simon v ArcGIS Enterprise. ArcGIS Enterprise podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k podnikové ArcGIS, pokud ještě neexistuje.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory ArcGIS Enterprise](mailto:support@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do firemní sítě ArcGIS.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit ArcGIS organizace, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **ArcGIS Enterprise**.

    ![Odkaz ArcGIS Enterprise v seznamu aplikací](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ArcGIS Enterprise na přístupovém panelu, vám by měl získat automaticky přihlášení k ArcGIS podnikové aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png