---
title: 'Kurz: Azure Active Directory integrace s vyrovná se se zatížením pro správu vzdělávacího procesu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a vyrovná se se zatížením pro správu vzdělávacího procesu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 4cf4e0ffc339bb90f013315a53025c83fcaad21f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217567"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Kurz: Azure Active Directory integrace s vyrovná se se zatížením pro správu vzdělávacího procesu

V tomto kurzu zjistěte, jak integrovat vyrovná se se zatížením pro správu vzdělávacího procesu s Azure Active Directory (Azure AD).

Integrace vyrovná se se zatížením pro správu vzdělávacího procesu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k vyrovná se se zatížením pro správu vzdělávacího procesu.
- Můžete povolit uživatelům automaticky se přihlaste k vyrovná se se zatížením vzdělávacího procesu (přes jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě, portálu Azure.

Pokud chcete získat další informace o softwaru jako integraci aplikace služby (SaaS) s Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vyrovná se se zatížením pro správu vzdělávacího procesu, potřebujete následující položky:

- Předplatné služby Azure AD
- Vyrovná se se zatížením pro správu vzdělávacího procesu jednotné přihlašování povolené předplatné

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro účely tohoto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

* Přidání vyrovná se se zatížením pro správu vzdělávacího procesu z Galerie
* Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-absorb-lms-from-the-gallery"></a>Přidat vyrovná se se zatížením pro správu vzdělávacího procesu z Galerie
Při konfiguraci integrace vyrovná se se zatížením pro správu vzdělávacího procesu do služby Azure AD přidáte do seznamu spravovaných aplikací SaaS vyrovná se se zatížením pro správu vzdělávacího procesu z galerie.

Pokud chcete přidat vyrovná se se zatížením pro správu vzdělávacího procesu z galerie, postupujte takto:

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![V podokně podnikových aplikací][2]
    
3. Chcete-li přidat aplikaci, vyberte **novou aplikaci** tlačítko.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **vyrovná se se zatížením pro správu vzdělávacího procesu**, vyberte **vyrovná se se zatížením pro správu vzdělávacího procesu** v vést panelu a pak vyberte **přidat** tlačítko.

    ![Vyrovná se se zatížením pro správu vzdělávacího procesu v seznamu výsledků](./media/absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s vyrovná se se zatížením vzdělávacího procesu na základě testovací uživatele, nazývá Britta Simon.

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku vyrovná se se zatížením pro správu vzdělávacího procesu je ve službě Azure AD. Jinými slovy je potřeba vytvořit vztah propojení mezi uživatele ve službě Azure AD a odpovídající uživatele v vyrovná se se zatížením pro správu vzdělávacího procesu.

Vytvořit tento odkaz vztah přiřazením *uživatelské jméno* hodnotu ve službě Azure AD, jako *uživatelské jméno* hodnota v vyrovná se se zatížením pro správu vzdělávacího procesu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vyrovná se se zatížením pro správu vzdělávacího procesu, dokončete stavebních bloků v následujících pět částech.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci vyrovná se se zatížením pro správu vzdělávacího procesu.

Ke konfiguraci Azure AD jednotné přihlašování s vyrovná se se zatížením pro správu vzdělávacího procesu, postupujte takto:

1. Na portálu Azure na **vyrovná se se zatížením pro správu vzdělávacího procesu** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. V **jednotného přihlašování** dialogovém **režimu** vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. V **vyrovná se se zatížením pro správu vzdělávacího procesu domény a adresy URL** část, postupujte takto:

    ![Pro správu vzdělávacího procesu adresy URL jeden přihlašování informace o doméně a vyrovná se se zatížením](./media/absorblms-tutorial/tutorial_absorblms_url.png)

    a. V **identifikátor** zadejte adresu URL, která se používá následující syntaxe: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. V **adresa URL odpovědi** zadejte adresu URL, která se používá následující syntaxe: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Tyto adresy URL nejsou skutečné hodnoty. Aktualizaci se skutečným identifikátorem a adresy URL odpovědí. Pokud chcete získat tyto hodnoty, obraťte se [tým podpory klienta vyrovná se se zatížením pro správu vzdělávacího procesu](https://www.absorblms.com/support). 

4. V **SAML podpisový certifikát** v části **Stáhnout** sloupce, vyberte **soubor XML s metadaty**a potom uložte soubor metadat pro váš počítač.

    ![Podpisový certifikát odkaz ke stažení](./media/absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Vyberte **Uložit**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/absorblms-tutorial/tutorial_general_400.png)
    
6. V **vyrovná se se zatížením konfigurace pro správu vzdělávacího procesu** vyberte **konfigurace vyrovná se se zatížením vzdělávacího procesu** otevřete **konfigurovat přihlášení** okno a zkopírujte **Sign-Out URL** v **Stručná referenční příručka části.**

    ![V podokně pro správu vzdělávacího procesu konfigurace vyrovná se se zatížením](./media/absorblms-tutorial/tutorial_absorblms_configure.png) 

7. V novém okně webového prohlížeče Přihlaste se k serveru vaší společnosti vyrovná se se zatížením pro správu vzdělávacího procesu jako správce.

8. Vyberte **účet** tlačítko vpravo nahoře. 

    ![Tlačítko účtu](./media/absorblms-tutorial/1.png)

9. V podokně účet vyberte **nastavení portálu**.

    ![Odkaz Nastavení portálu](./media/absorblms-tutorial/2.png)
    
10. Vyberte kartu **Uživatelé**.

    ![Na kartě Uživatelé](./media/absorblms-tutorial/3.png)

11. Na stránce Konfigurace přihlášení postupujte takto:

    ![Stránka Konfigurace přihlášení](./media/absorblms-tutorial/4.png)

    a. V **režimu** vyberte **iniciované zprostředkovatele Identity**.

    b. V poznámkovém bloku otevřete certifikát, který jste si stáhli z portálu Azure. Odeberte **---BEGIN CERTIFICATE---** a **---END CERTIFICATE---** značky. Pak na **klíč** pole, vložte zbývající obsah.
    
    c. V **Vlastnost Id** vyberte atribut, který jste nakonfigurovali jako identifikátor uživatele ve službě Azure AD. Například pokud *userPrincipalName* je vybrána ve službě Azure AD, vyberte **uživatelské jméno**.

    d. V **přihlašovací adresa URL** pole, vložte **adresu URL pro přístup uživatelů** z aplikace **vlastnosti** stránce portálu Azure.

    e. V **adresy URL odhlašovací**, vložte **Sign-Out URL** hodnotu, kterou jste zkopírovali z **konfigurovat přihlášení** okno portálu Azure.

12. Přepnutí **pouze povolit přihlášení SSO** k **na**.

    ![Přepínač pouze povolit přihlášení SSO](./media/absorblms-tutorial/5.png)

13. Vyberte **uložit.**

> [!TIP]
> Můžete si přečíst stručným verzi tyto pokyny v [portál Azure](https://portal.azure.com) při k nastavení aplikace. Po přidání aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a přístup k vložený dokumentace prostřednictvím **konfigurace** v dolní části. Další informace najdete v tématu [Azure AD vložených dokumentaci]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

V této části vytvoříte testovacího uživatele Britta Simon na portálu Azure.

![Vytvořit testovací uživatele Azure AD][100]

Vytvoření zkušebního uživatele ve službě Azure AD, postupujte takto:

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](./media/absorblms-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, vyberte **uživatelů a skupin** > **všichni uživatelé**.
    
    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/absorblms-tutorial/create_aaduser_02.png) 

3. V horní části dialogových oken, vyberte **přidat**.
 
    ![Tlačítko Přidat](./media/absorblms-tutorial/create_aaduser_03.png) 

4. V **uživatele** dialogové okno pole, postupujte takto:
 
    ![Dialogové okno uživatele](./media/absorblms-tutorial/create_aaduser_04.png) 

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, zadejte e-mailovou adresu Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a poznamenejte si hodnotu v **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="create-an-absorb-lms-test-user"></a>Vytvořit testovací uživatele s vyrovná se se zatížením pro správu vzdělávacího procesu

Pro uživatele Azure AD pro přihlášení k vyrovná se se zatížením pro správu vzdělávacího procesu se musí být nastavena v vyrovná se se zatížením pro správu vzdělávacího procesu.  

Instalační program pro vyrovná se se zatížením vzdělávacího procesu je ruční úlohy.

Nastavit účet uživatele, postupujte takto:

1. Přihlaste se k serveru vaší společnosti vyrovná se se zatížením pro správu vzdělávacího procesu jako správce.

2. V levém podokně vyberte **uživatelé**.

    ![Odkaz vyrovná se se zatížením uživatelé vzdělávacího procesu](./media/absorblms-tutorial/absorblms_users.png)

3. V **uživatelé** podokně, vyberte **uživatelé**.

    ![Odkaz uživatelé](./media/absorblms-tutorial/absorblms_userssub.png)

4. V **přidat nové** rozevíracího seznamu vyberte **uživatele**.

    ![Přidat nový rozevíracího seznamu](./media/absorblms-tutorial/absorblms_createuser.png)

5. Na **přidat uživatele** proveďte následující:

    ![Stránka Přidat uživatel](./media/absorblms-tutorial/user.png)

    a. V **křestní jméno** pole, zadejte jméno, například **Britta**.

    b. V **příjmení** zadejte příjmení, jako například **Simon**.
    
    c. V **uživatelské jméno** zadejte úplný název, například **Britta Simon**.

    d. V **heslo** zadejte Britta Simon heslo.

    e. V **Potvrdit heslo** pole, zadejte heslo znovu.
    
    f. Nastavte **je aktivní** přepnutím **Active**.  

6. Vyberte **uložit.**
 
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolit uživatele Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu vyrovná se se zatížením pro správu vzdělávacího procesu.

![Přiřadit role uživatele][200]

Pokud chcete přiřadit uživatele Britta Simon vyrovná se se zatížením pro správu vzdělávacího procesu, postupujte takto:

1. Na portálu Azure otevřete zobrazení aplikace, přejděte do zobrazení adresáře a pak vyberte **podnikové aplikace, které** > **všechny aplikace**.

    ![Na odkaz "Všechny aplikace"][201] 

2. V **aplikace** seznamu, vyberte **vyrovná se se zatížením pro správu vzdělávacího procesu**.

    ![Odkaz vyrovná se se zatížením pro správu vzdělávacího procesu v seznamu aplikací](./media/absorblms-tutorial/tutorial_absorblms_app.png) 

3. V levém podokně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202] 

4. Vyberte **přidat** a pak na **přidat přiřazení** podokně, vyberte **uživatelů a skupin**.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** v dialogovém **uživatelé** seznamu, vyberte **Britta Simon**.

6. V **uživatelů a skupin** dialogové okno, vyberte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Na přístupovém panelu Výběr **vyrovná se se zatížením pro správu vzdělávacího procesu** dlaždice automaticky přihlásí vám do vaší aplikace vyrovná se se zatížením pro správu vzdělávacího procesu. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/absorblms-tutorial/tutorial_general_01.png
[2]: ./media/absorblms-tutorial/tutorial_general_02.png
[3]: ./media/absorblms-tutorial/tutorial_general_03.png
[4]: ./media/absorblms-tutorial/tutorial_general_04.png

[100]: ./media/absorblms-tutorial/tutorial_general_100.png

[200]: ./media/absorblms-tutorial/tutorial_general_200.png
[201]: ./media/absorblms-tutorial/tutorial_general_201.png
[202]: ./media/absorblms-tutorial/tutorial_general_202.png
[203]: ./media/absorblms-tutorial/tutorial_general_203.png

