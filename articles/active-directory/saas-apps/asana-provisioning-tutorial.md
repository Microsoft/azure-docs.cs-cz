---
title: 'Kurz: Zřizování uživatelů pro Asana – Azure AD'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašňovala uživatelské účty do Asany.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058903"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Kurz: Konfigurace Asany pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v Asana a Azure Active Directory (Azure AD) automaticky zřídit a odzřízení uživatelských účtů z Azure AD do Asana.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Klient Azure AD
* Klient Asana s plánem [Enterprise](https://www.asana.com/pricing) nebo lépe povolený
* Uživatelský účet v Asaně s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD závisí na [rozhraní API Asana](https://asana.com/developers/api-reference/users), které je k dispozici Asana.

## <a name="assign-users-to-asana"></a>Přiřazení uživatelů k Asaně

Azure AD používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé přiřazení k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte zřizovací službu, musíte se rozhodnout, kteří uživatelé ve službě Azure AD potřebují přístup k vaší aplikaci Asana. Pak můžete přiřadit tyto uživatele k aplikaci Asana podle pokynů zde:

[Přiřazení uživatele k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Důležité tipy pro přiřazení uživatelů do Asany

Doporučujeme přiřadit jednoho uživatele Azure AD asana k testování konfigurace zřizování. Další uživatelé mohou být přiřazeny později.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurace zřizování uživatelů do Asany

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelského účtu Asana. Službu zřizování můžete také nakonfigurovat tak, aby vytvářela, aktualizovala a zakazovala přiřazené uživatelské účty v Asaně na základě přiřazení uživatelů ve službě Azure AD.

> [!TIP]
> Chcete-li povolit jednotné přihlašování saml pro Asana, postupujte podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů do Asany ve službě Azure AD

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory** > **Enterprise Apps** > **Všechny aplikace.**

1. Pokud jste již asanu nakonfigurovali pro jednotné přihlašování, vyhledejte svou instanci Asany pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Asanu** v galerii aplikací. Ve výsledcích hledání vyberte **Asana** a přidejte ji do seznamu aplikací.

1. Vyberte svou instanci Asany a pak vyberte kartu **Zřizování.**

1. Nastavte **režim zřizování** na **automatické**.

    ![Asana Zřizování](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. V části **Přihlašovací údaje správce vygenerujte** token a zadejte jej do **tajného tokenu**:

    a. Přihlaste se k [Asaně](https://app.asana.com) pomocí svého účtu správce.

    b. Vyberte profilovou fotku z horního panelu a vyberte aktuální nastavení názvu organizace.

    c. Přejděte na kartu **Účty služeb.**

    d. Vyberte **přidat účet služby**.

    e. Podle potřeby aktualizujte **název** a **informace** a profilovou fotku. Zkopírujte token v **tokenu**a vyberte ho v **možnosti Uložit změny**.

1. Na webu Azure Portal vyberte **Testovat připojení,** abyste zajistili, že se Azure AD může připojit k vaší aplikaci Asana. Pokud se připojení nezdaří, ujistěte se, že váš účet Asana má oprávnění správce a zkuste **krok Testovat připojení** znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která má dostávat oznámení o chybách zřizování v **e-mailu s oznámením**. Zaškrtněte políčko pod ním.

1. Vyberte **Uložit**.

1. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s Asanou**.

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které mají být synchronizovány z Azure AD do Asany. Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Asana pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.** Další informace naleznete v [tématu Customize user provision attribute mappings](../app-provisioning/customize-application-attributes.md).

1. Chcete-li povolit službu zřizování Azure AD pro Asana, v části **Nastavení** **změňte stav zřizování** **na Zapnuto**.

1. Vyberte **Uložit**.

Nyní se spustí počáteční synchronizace pro všechny uživatele přiřazené k Asana v části **Uživatelé.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** slouží ke sledování průběhu a sledování odkazů na protokoly aktivit zřizování. Protokoly auditu popisují všechny akce prováděné zřizovací službou v aplikaci Asana.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Sestava automatického zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](asana-tutorial.md)
