---
title: 'Kurz: Konfigurace Pingboard pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Pingboard.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4d483f045a8e778953ddb578dd7bb81fdb05fdf2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292404"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Kurz: Konfigurace Pingboard pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést, chcete-li povolit automatické zřizování a jeho rušení uživatelských účtů ze služby Azure Active Directory (Azure AD) k Pingboard.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klient služby Azure AD
*   Klient Pingboard [Pro účet](https://pingboard.com/pricing) 
*   Uživatelský účet v Pingboard s oprávněními správce 

> [!NOTE] 
> Azure AD zřizování integrace spoléhá na [Pingboard API](`https://your_domain.pingboard.com/scim/v2`), která je k dispozici ke svému účtu.

## <a name="assign-users-to-pingboard"></a>Přiřazení uživatelů k Pingboard

Azure AD používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu jsou synchronizovány pouze uživatelé, které jsou přiřazené k aplikaci ve službě Azure AD. 

Než začnete konfigurovat a povolit zřizování služby, musíte se rozhodnout, které uživatelé ve službě Azure AD potřebovat přístup k vaší aplikaci Pingboard. Pak můžete přiřadit těmto uživatelům aplikace Pingboard podle pokynů tady:

[Přiřazení uživatele k aplikaci enterprise](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Důležité tipy pro přiřazování uživatelů do Pingboard

Doporučujeme, abyste přiřadili jednoho uživatele Azure AD k Pingboard otestovat konfiguraci zřizování. Další uživatele lze přiřadit později.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurace pro Pingboard zřizování uživatelů 

Tato část vás provede připojení k uživatelskému účtu Pingboard zřizování rozhraní API služby Azure AD. Můžete také nakonfigurovat službu zřizování vytvářet, aktualizovat a zakázat přiřazené uživatelské účty v Pingboard na základě uživatele přiřazení ve službě Azure AD.

> [!TIP]
> Povolit na základě SAML jednotné přihlašování pro Pingboard, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Konfigurace automatického účet zřizování uživatelů k Pingboard ve službě Azure AD

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** části.

2. Pokud jste již nakonfigurovali Pingboard pro jednotné přihlašování, vyhledejte instanci Pingboard pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **Pingboard** v galerii aplikací. Vyberte **Pingboard** ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci Pingboard a pak vyberte **zřizování** kartě.

4. Nastavit **režimu zřizování** k **automatické**.

    ![Zřizování Pingboard](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5. V části **přihlašovací údaje správce** část, proveďte následující kroky:

    a. V **URL klienta**, zadejte `https://your_domain.pingboard.com/scim/v2`a nahraďte "Vaše_Doména" skutečné domény.

    b. Přihlaste se k [Pingboard](https://pingboard.com/) pomocí účtu správce.

    c. Vyberte **doplňky** > **integrace** > **Azure Active Directory**.

    d. Přejděte na **konfigurace** a vyberte **povolit zřizování uživatelů z Azure**.

    e. Zkopírujte token v **tokenu nosiče OAuth**a zadejte ho v **tajný klíč tokenu**.

6. Na portálu Azure vyberte **Test připojení** zajistit Azure AD může připojit k aplikaci Pingboard. Pokud připojení nezdaří, zkontrolujte, zda má váš účet Pingboard oprávnění správce a zkuste **Test připojení** krok opakujte.

7. Zadejte e-mailovou adresu uživatele nebo skupinu, která chcete dostávat oznámení zřizování Chyba v **e-mailové oznámení**. Zaškrtněte políčko pod.

8. Vyberte **Uložit**. 

9. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Pingboard**.

10. V **mapování atributů** , projděte si atributy uživatele k Pingboard synchronizaci z Azure AD. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Pingboard pro operace aktualizace. Vyberte **Uložit** potvrzení změny. Další informace najdete v tématu [zřizování mapování atributů uživatelů přizpůsobit](active-directory-saas-customizing-attribute-mappings.md).

11. Chcete-li povolit zřizování služby pro Pingboard, v Azure AD **nastavení** změňte **Stav zřizování** k **na**.

12. Vyberte **Uložit** se spustit počáteční synchronizaci uživatelů se přiřadila Pingboard.

Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Použití **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity. Protokoly popisují všechny akce prováděné ve vaší aplikaci Pingboard zřizování službou.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [sestav o zřizování účtu automatické uživatele](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Spravovat uživatele zřizování účtu pro podnikové aplikace](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](active-directory-saas-pingboard-tutorial.md)
