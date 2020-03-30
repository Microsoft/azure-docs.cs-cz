---
title: 'Kurz: Zřizování uživatelů pro Pingboard – Azure AD'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočatou uživatelské účty na Pingboard.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061271"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Kurz: Konfigurace pingboardu pro automatické zřizování uživatelů

Účelem tohoto kurzu je zobrazit kroky, které je třeba provést, abyste povolili automatické zřizování a zrušení zřizování uživatelských účtů z Azure Active Directory (Azure AD) do Pingboardu.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Klient Azure AD
* [Účet Pro](https://pingboard.com/pricing) klienta Pingboard
* Uživatelský účet v Pingboardu s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD závisí na [rozhraní Pingboard API](https://pingboard.docs.apiary.io/#), které je k dispozici pro váš účet.

## <a name="assign-users-to-pingboard"></a>Přiřazení uživatelů k příkazu Pingboard

Azure AD používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé přiřazení k aplikaci ve službě Azure AD. 

Než nakonfigurujete a povolíte zřizovací službu, musíte se rozhodnout, kteří uživatelé ve službě Azure AD potřebují přístup k vaší aplikaci Pingboard. Poté můžete přiřadit tyto uživatele k aplikaci Pingboard podle pokynů zde:

[Přiřazení uživatele k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Důležité tipy pro přiřazení uživatelů k pingboardu

Doporučujeme přiřadit jednoho uživatele Azure AD pingboard k testování konfigurace zřizování. Další uživatelé mohou být přiřazeny později.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurace zřizování uživatelů na Pingboard 

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelského účtu Pingboard. Službu zřizování můžete také nakonfigurovat tak, aby vytvářela, aktualizovala a zakazovala přiřazené uživatelské účty v pingboardu, které jsou založeny na přiřazeních uživatelů ve službě Azure AD.

> [!TIP]
> Chcete-li povolit jednotné přihlašování na saml pro Pingboard, postupujte podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na Pingboard ve službě Azure AD

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory** > **Enterprise Apps** > **Všechny aplikace.**

1. Pokud jste již nakonfigurovali Pingboard pro jednotné přihlašování, vyhledejte svou instanci Pingboard pomocí vyhledávacího pole. V opačném případě vgalerii aplikace vyberte **Přidat** a **vyhledejte pingboard.** Ve výsledcích hledání vyberte **Pingboard** a přidejte ho do seznamu aplikací.

1. Vyberte svou instanci Pingboard a pak vyberte kartu **Zřizování.**

1. Nastavte **režim zřizování** na **automatické**.

    ![Pingboard Zřizování](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. V části **Pověření správce** použijte následující kroky:

    a. V adrese URL `https://your_domain.pingboard.com/scim/v2` **klienta**zadejte a nahraďte "your_domain" skutečnou doménou.

    b. Přihlaste se k [Pingboardu](https://pingboard.com/) pomocí svého účtu správce.

    c. Vyberte **integrace doplňků** > **Azure** > **Active Directory**.

    d. Přejděte na kartu **Konfigurovat** a vyberte **Povolit zřizování uživatelů z Azure**.

    e. Zkopírujte token v **tokenu nosiče OAuth**a zadejte jej do **tajného tokenu**.

1. Na webu Azure Portal vyberte **Testovat připojení** a otestujte Azure AD, které se může připojit k vaší aplikaci Pingboard. Pokud se připojení nezdaří, otestujte, že váš účet Pingboard má oprávnění správce, a zkuste krok **Testovat připojení** znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která má dostávat oznámení o chybách zřizování v **e-mailu s oznámením**. Zaškrtněte políčko pod ním.

1. Vyberte **Uložit**.

1. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s pingboardem**.

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které mají být synchronizovány z Azure AD do Pingboard. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Pingboard pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.** Další informace naleznete v [tématu Přizpůsobení mapování atributů zřizování uživatelů](../app-provisioning/customize-application-attributes.md).

1. Chcete-li povolit službu zřizování Azure AD pro Pingboard, v části **Nastavení** **změňte stav zřizování** **na Zapnuto**.

1. Výběrem **možnosti Uložit** zahájíte počáteční synchronizaci uživatelů přiřazených k příkazu Pingboard.

Počáteční synchronizace trvá déle než následující synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** slouží ke sledování průběhu a sledování odkazů na protokoly aktivit zřizování. Protokoly popisují všechny akce provedené zřizovací službou v aplikaci Pingboard.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v [tématu Sestava automatického zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](pingboard-tutorial.md)
