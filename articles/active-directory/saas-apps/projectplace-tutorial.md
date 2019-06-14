---
title: 'Kurz: Integrace Azure Active Directory s Projectplace | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093527"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Kurz: Projectplace integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Projectplace s Azure Active Directory (Azure AD). Když Projectplace můžete integrovat s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Projectplace.
* Aby uživatelé mohli být automaticky přihlášeni k Projectplace přes svoje účty Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.
* Uživatelům je možné zřídit Projectplace automaticky.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Projectplace jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Projectplace **SP a zprostředkovatele identity** iniciované jednotné přihlašování a podporuje **JIT** zřizování uživatelů.

## <a name="adding-projectplace-from-the-gallery"></a>Přidání Projectplace z Galerie

Konfigurace integrace Projectplace do služby Azure AD, budete muset přidat Projectplace v galerii na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Projectplace** do vyhledávacího pole.
1. Vyberte **Projectplace** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Projectplace pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Projectplace.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Projectplace, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Projectplace](#configure-projectplace)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovací uživatel Projectplace](#create-projectplace-test-user)**  mít protějšek B. Simon Projectplace, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Projectplace** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, aplikace je předem nakonfigurovaný a potřebné adresy URL se už předem vyplní s Azure . Uživatel musí uložte konfiguraci kliknutím **Uložit** tlačítko.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://service.projectplace.com`

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko kopírování **ikonu** zkopírovat **adresa Url federačních metadat aplikace** podle vašich požadavků a uložte ho do poznámkového bloku.

   ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

1. Na **nastavení Projectplace** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurace služby Projectplace

Ke konfiguraci jednotného přihlašování na **Projectplace** straně, je nutné odeslat zkopírovaný **adresa Url federačních metadat aplikace** z webu Azure portal [tým podpory Projectplace](https://success.planview.com/Projectplace/Support) . Tento tým zajistí, že je správně nastaveno připojení SAML SSO na obou stranách.

>[!NOTE]
>Konfigurace jednotného přihlašování musí provést [tým podpory Projectplace](https://success.planview.com/Projectplace/Support). Jakmile konfigurace je hotová, zobrazí se oznámení. 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B. Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure díky udělení přístupu k Projectplace.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Projectplace**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-projectplace-test-user"></a>Vytvořit testovací uživatel Projectplace

>[!NOTE]
>Pokud máte povolené v Projectplace zřizování, můžete tento krok přeskočit. Můžete klást [tým podpory Projectplace](https://success.planview.com/Projectplace/Support) umožňující zřizování, jakmile Hotovo uživatelé budou vytvořeny v Projectplace při prvním přihlášení.

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Projectplace, budete muset přidat k Projectplace. Musíte je přidat ručně.

**Chcete-li vytvořit uživatelský účet, proveďte tyto kroky:**

1. Přihlaste se k vaší **Projectplace** společnosti serveru jako správce.

2. Přejděte na **lidé**a pak vyberte **členy**:
   
    ![Přejděte na uživatelé a pak vyberte členy](./media/projectplace-tutorial/ic790228.png "osoby")

3. Vyberte **přidat člena**:
   
    ![Výběr možnosti Přidat člen](./media/projectplace-tutorial/ic790232.png "přidat členy")

4. V **přidat člen** části, proveďte následující kroky.
   
    ![Přidat člena oddíl](./media/projectplace-tutorial/ic790233.png "nové členy")
   
    1. V **nové členy** zadejte e-mailová adresa platná Azure AD účet, který chcete přidat.
   
    1. Vyberte **Poslat**.

   K držitel účtu Azure AD se odešle e-mail obsahující odkaz pro potvrzení účtu, pak se změní na aktivní.

>[!NOTE]
>Můžete použít také jakýkoliv jiný nástroj pro vytváření uživatelského účtu nebo rozhraní API poskytovaných Projectplace pro přidání uživatelských účtů služby Azure AD.


### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Projectplace na přístupovém panelu, vám by měl být automaticky přihlášeni k Projectplace, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)