---
title: 'Kurz: Azure Active Directory integrace s Mozy Enterprise | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: 37b0cdce9b77b304a24ff3419f618f193fb2ad52
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Kurz: Azure Active Directory integrace s Mozy Enterprise

V tomto kurzu zjistěte, jak integrovat Mozy Enterprise s Azure Active Directory (Azure AD).

Integrace Mozy Enterprise s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do firemní sítě Mozy
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Mozy Enterprise (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Mozy Enterprise, potřebujete následující položky:

- Předplatné služby Azure AD
- Mozy Enterprise jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Mozy Enterprise z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Přidání Mozy Enterprise z Galerie
Při konfiguraci integrace Mozy Enterprise do služby Azure AD potřebujete přidat Mozy Enterprise z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Mozy Enterprise z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Mozy Enterprise**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. Na panelu výsledků vyberte **Mozy Enterprise**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat, testovací Azure AD jednotného přihlašování k Mozy organizace podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Mozy Enterprise je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v podniku Mozy musí navázat.

V Mozy Enterprise, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování k Mozy organizace, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon v Mozy organizace, která je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Mozy Enterprise.

**Ke konfiguraci Azure AD jednotného přihlašování k Mozy organizace, proveďte následující kroky:**

1. Na portálu Azure na **Mozy Enterprise** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. Na **Mozy podnikové domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory klient systému Enterprise Mozy](http://support.mozy.com/) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. Na **Mozy podniková konfigurace** klikněte na tlačítko **konfigurace Mozy Enterprise** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Mozy Enterprise.

8. V **konfigurace** klikněte na tlačítko **zásady ověřování**.
   
   ![Zásady ověřování](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "zásady ověřování")

9. Na **zásady ověřování** část, proveďte následující kroky:
   
   ![Zásady ověřování](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "zásady ověřování")
   
   a. Vyberte **adresářová služba** jako **zprostředkovatele**.
   
   b. Vyberte **použít nabízenou LDAP**.
   
   c. Klikněte **ověřování SAML** kartě.
   
   d. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **adresy URL ověřování** textové pole.
   
   e. Vložení **SAML Entity ID**, který jste zkopírovali z portálu Azure do **koncový bod SAML** textové pole.
   
   f. V poznámkovém bloku otevřete stažené kódovaného certifikátu kódování base-64, zkopírujte obsah ho do schránky a vložte celý certifikát do **certifikátu SAML** textové pole.
   
   g. Vyberte **povolit jednotné přihlašování pro správce k přihlášení pomocí přihlašovacích údajů sítě**.
   
   h. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Vytvoření zkušebního uživatele Mozy Enterprise

Pokud chcete povolit uživatelům Azure AD přihlášení do Mozy Enterprise, musí být zřízená do Mozy Enterprise. V případě Mozy Enterprise zřizování je ruční úloha.

>[!NOTE]
>Můžete použít všechny ostatní Mozy Enterprise uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Mozy Enterprise zřídit AAD uživatelské účty.

**Ke zřízení uživatelských účtů, proveďte následující kroky:**

1. Přihlaste se k vaší **Mozy Enterprise** klienta.

2. Klikněte na tlačítko **uživatelé**a potom klikněte na **přidat nové uživatele**.
   
   ![Uživatelé](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "uživatelů")
   
   >[!NOTE]
   >**Přidat nové uživatele** možnost se zobrazí pouze pouze v případě **Mozy** je vybrán jako zprostředkovatel pod **zásady ověřování**. Pokud je nakonfigurované ověřování SAML, pak uživatelé jsou automaticky přidá na jejich první přihlášení pomocí jednotného přihlašování na.
    
3. V dialogovém okně Nový uživatel proveďte následující kroky:
   
   ![Přidání uživatelů](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "přidat uživatele")
   
   a. Z **zvolte skupinu** seznamu, vyberte skupinu.
   
   b. Z **jaký typ uživatele** seznamu, vyberte typ.
   
   c. V **uživatelské jméno** textovému poli, zadejte jméno uživatele Azure AD.
   
   d. V **e-mailu** textovému poli, zadejte e-mailovou adresu uživatele Azure AD.
   
   e. Vyberte **odeslat e-mail uživatele instrukce**.
   
   f. Klikněte na tlačítko **přidat jiní uživatelé**.

     >[!NOTE]
     > Po vytvoření uživatele, odešle e-mailu pro uživatele Azure AD, která obsahuje odkaz pro potvrzení účtu před stane aktivní.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat tak, že udělíte přístup do firemní sítě Mozy Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Mozy Enterprise, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Mozy Enterprise**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Mozy Enterprise na přístupovém panelu, měli byste obdržet přihlašovací stránku Mozy podnikové aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png

