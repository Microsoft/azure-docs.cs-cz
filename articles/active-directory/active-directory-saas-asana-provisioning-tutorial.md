---
title: 'Kurz: Konfigurace Asana pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Asana.
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
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: c7e33a46940c9bf0cda5e48c07d42db9f0c7cd96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Kurz: Konfigurace Asana pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v Asana a Azure Active Directory (Azure AD), a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do Asana.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klient služby Azure AD
*   Klient služby Asana s [Enterprise](https://www.asana.com/pricing) plánu nebo lépe povoleno 
*   Uživatelský účet v Asana s oprávněními správce 

> [!NOTE] 
> Azure AD zřizování integrace spoléhá na [Asana API](https://app.asana.com/api/1.0/scim/Users), která je k dispozici na Asana.

## <a name="assign-users-to-asana"></a>Přiřazení uživatelů k Asana

Azure AD používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu jsou synchronizovány pouze uživatelé, které jsou přiřazené k aplikaci ve službě Azure AD. 

Než začnete konfigurovat a povolit zřizování služby, musíte se rozhodnout, které uživatelé ve službě Azure AD potřebovat přístup k vaší aplikaci Asana. Pak můžete přiřadit těmto uživatelům aplikace Asana podle pokynů tady:

[Přiřazení uživatele k aplikaci enterprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Důležité tipy pro přiřazování uživatelů do Asana

Doporučujeme, abyste přiřadili jednoho uživatele Azure AD k Asana otestovat konfiguraci zřizování. Další uživatele lze přiřadit později.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurace pro Asana zřizování uživatelů 

Tato část vás provede připojení k Asana uživatelský účet zřizování rozhraní API služby Azure AD. Můžete také nakonfigurovat službu zřizování vytvářet, aktualizovat a zakázat přiřazené uživatelské účty v Asana na základě uživatele přiřazení ve službě Azure AD.

> [!TIP]
> Povolit na základě SAML jednotné přihlašování pro Asana, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurace automatického účet zřizování uživatelů k Asana ve službě Azure AD

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** části.

2. Pokud jste již nakonfigurovali Asana pro jednotné přihlašování, vyhledejte instanci Asana pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **Asana** v galerii aplikací. Vyberte **Asana** ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci Asana a pak vyberte **zřizování** kartě.

4. Nastavit **režimu zřizování** k **automatické**.

    ![Zřizování Asana](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5. V části **přihlašovací údaje správce** , postupujte podle těchto pokynů můžete vygenerovat token a zadejte ho v **tajný klíč tokenu**:

    a. Přihlaste se k [Asana](https://app.asana.com) pomocí účtu správce.

    b. Vyberte profil fotografie z horním panelu a vyberte aktuální nastavení název organizace.

    c. Přejděte na **účty služby** kartě.

    d. Vyberte **přidejte účet služby**.

    e. Aktualizace **název** a **o** a profilové fotky, podle potřeby. Zkopírujte token v **tokenu**a vyberte ho v **uložit změny**.

6. Na portálu Azure vyberte **Test připojení** zajistit, že Azure AD může připojit k aplikaci Asana. Pokud připojení nezdaří, zkontrolujte, zda má váš účet Asana oprávnění správce a zkuste **Test připojení** krok opakujte.

7. Zadejte e-mailovou adresu uživatele nebo skupinu, která chcete dostávat oznámení zřizování Chyba v **e-mailové oznámení**. Zaškrtněte políčko pod.

8. Vyberte **Uložit**. 

9. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Asana**.

10. V **mapování atributů** , projděte si atributy uživatele k Asana synchronizaci z Azure AD. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Asana pro operace aktualizace. Vyberte **Uložit** potvrzení změny. Další informace najdete v tématu [přizpůsobit mapování atributů uživatele zřídit](active-directory-saas-customizing-attribute-mappings.md).

11. Chcete-li povolit zřizování služby pro Asana, v Azure AD **nastavení** změňte **Stav zřizování** k **na**.

12. Vyberte **Uložit**. 

Teď spustí počáteční synchronizaci pro všechny uživatele přiřazené Asana v **uživatelé** části. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Použití **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity. Protokoly auditu popisují všechny akce prováděné ve vaší aplikaci Asana zřizování službou.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [sestav o zřizování účtu automatické uživatele](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Spravovat uživatele zřizování účtu pro podnikové aplikace](active-directory-enterprise-apps-manage-provisioning.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](active-directory-saas-asana-tutorial.md)
