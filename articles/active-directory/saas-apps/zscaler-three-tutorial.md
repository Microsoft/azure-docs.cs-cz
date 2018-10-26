---
title: 'Kurz: Integrace Azure Active Directory se Zscalerem tři | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem tři.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: b148967af0882993d8ab113bdf0fd3ad3835296f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092606"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Kurz: Integrace Azure Active Directory s tři Zscaler

V tomto kurzu se dozvíte, jak integrovat tři Zscalerem se službou Azure Active Directory (Azure AD).

Integrace s Azure AD Zscalerem tři poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k tři Zscaler
- Uživatele, aby automaticky získat přihlášení k Zscalerem tři (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem tři, potřebujete následující položky:

- Předplatné Azure AD
- Tři Zscalerem jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební tady: [nabídka zkušební verze](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tří Zscalerem z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-three-from-the-gallery"></a>Přidání tří Zscalerem z Galerie

Konfigurovat integraci Zscalerem tři do služby Azure AD, budete muset přidat Zscalerem tři z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Zscalerem tři z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![Aplikace][2]

3. Klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Na panelu výsledků vyberte **Zscalerem tři**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Zscalerem tři podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zscalerem tři je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Zscalerem tři potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem tři, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace nastavení proxy serveru](#configuring-proxy-settings)**  – konfigurace nastavení proxy serveru v aplikaci Internet Explorer
3. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Vytváří se Zscalerem tři testovací uživatel](#creating-a-zscaler-three-test-user)**  – Pokud chcete mít protějšek Britta Simon Zscalerem tří, který je propojený s Azure AD reprezentace uživatele.
5. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
6. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zscalerem tři.

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem tři, proveďte následující kroky:**

1. Na webu Azure Portal na **Zscalerem tři** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. Pokud potřebujete změnit na **SAML** klikněte na libovolný jiný režim, režim **změnit jednotné přihlašování režim** na obrazovce.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    V přihlašování textového pole URL zadejte adresu URL: `https://login.zscalerthree.net/sfc_sso`

6. Na **podpisový certifikát SAML** části, klikněte na **Stáhnout** Stáhnout **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. Na **nastavení Zscalerem tři** tématu, zkopírujte **přihlašovací adresa URL**.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. V okně jiné webové prohlížeče Přihlaste se k webu Zscalerem tři společnosti jako správce.

10. V nabídce v horní části klikněte na tlačítko **správu**.

    ![Správa](./media/zscaler-three-tutorial/ic800206.png "správy")

9. V části **Správa správců & role**, klikněte na tlačítko **spravovat uživatele a ověření**.

    ![Správa uživatelů a ověřování](./media/zscaler-three-tutorial/ic800207.png "správu uživatelů a ověřování")

10. V **zvolte možnosti ověřování pro vaši organizaci** části, proveďte následující kroky:

    ![Ověřování](./media/zscaler-three-tutorial/ic800208.png "ověřování")

    a. Vyberte **ověření pomocí SAML jednotného přihlašování**.

    b. Klikněte na tlačítko **konfigurace SAML jednotné přihlašování – parametry**.

11. Na **konfigurace SAML jednotné přihlašování parametrů** dialogového okna stránky, proveďte následující kroky a pak klikněte na **Hotovo**

    ![Jednotné přihlašování](./media/zscaler-three-tutorial/ic800209.png "jednotného přihlašování")

    a. Vložit **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal do **URL portálu SAML, ke kterému jsou uživatelé nasměrovaní ověřování** textového pole.

    b. V **atribut, který obsahuje přihlašovací jméno** textové pole, typ **NameID**.

    c. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **Zscalerem pem**.

    d. Vyberte **povolení automatického zřizování SAML**.

12. Na **konfiguraci ověření uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Správa](./media/zscaler-three-tutorial/ic800210.png "správy")

    a. Klikněte na **Uložit**.

    b. Klikněte na tlačítko **aktivovat**.

## <a name="configuring-proxy-settings"></a>Konfigurace nastavení proxy serveru

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurace nastavení proxy serveru v aplikaci Internet Explorer

1. Spustit **aplikace Internet Explorer**.

2. Vyberte **Možnosti Internetu** z **nástroje** nabídku otevřít **Možnosti Internetu** dialogového okna.

     ![Možnosti Internetu](./media/zscaler-three-tutorial/ic769492.png "Možnosti Internetu")

3. Klikněte na tlačítko **připojení** kartu.
  
     ![Připojení](./media/zscaler-three-tutorial/ic769493.png "připojení")

4. Klikněte na tlačítko **nastavení místní sítě** otevřít **nastavení místní sítě** dialogového okna.

5. V části Proxy server proveďte následující kroky:

    ![Proxy server](./media/zscaler-three-tutorial/ic769494.png "Proxy serveru")

    a. Vyberte **používat proxy server pro síť LAN**.

    b. Do textového pole Adresa zadejte **gateway.zscalerthree.net**.

    c. V textovém poli portu zadejte **80**.

    d. Vyberte **obejít proxy server pro místní adresy**.

    e. Klikněte na tlačítko **OK** zavřete **nastavení místní sítě (LAN)** dialogového okna.

6. Klikněte na tlačítko **OK** zavřete **Možnosti Internetu** dialogového okna.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-zscaler-three-test-user"></a>Vytváří se Zscalerem tři testovacího uživatele

Umožňuje uživatelům Azure AD se přihlaste k Zscalerem tři musí být poskytnuty k Zscalerem tři. V případě tří Zscalerem zřizování je ruční úloha.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:

1. Přihlaste se k vaší **Zscalerem tři** tenanta.

2. Klikněte na tlačítko **správu**.

    ![Správa](./media/zscaler-three-tutorial/ic781035.png "správy")

3. Klikněte na tlačítko **Správa uživatelů**.

     ![Přidat](./media/zscaler-three-tutorial/ic781036.png "přidat")

4. V **uživatelé** klikněte na tlačítko **přidat**.

    ![Přidat](./media/zscaler-three-tutorial/ic781037.png "přidat")

5. V části přidat uživatele proveďte následující kroky:

    ![Přidání uživatele](./media/zscaler-three-tutorial/ic781038.png "přidat uživatele")

    a. Typ **UserID**, **zobrazované jméno uživatele**, **heslo**, **potvrzení hesla**a pak vyberte **skupiny**a **oddělení** platný Azure AD účtu, které chcete zřídit.

    b. Klikněte na **Uložit**.

> [!NOTE]
> Všechny tři Zscalerem nástrojů pro vytváření účtu uživatele nebo rozhraní API poskytovaných Zscalerem tři můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem tři.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Zscalerem tři**.

    ![Konfigurace jednotného přihlašování](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na tři Zscalerem dlaždici na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Zscalerem tři.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png