---
title: 'Kurz: zřizování uživatelů pro Asana – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Asana.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 4abc117ae0e983cf684f0e70a363758f9be196aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359422"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Kurz: Konfigurace Asana pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v Asana a Azure Active Directory (Azure AD) k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až Asana.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenant Azure AD
* Tenant Asana s plánem [Enterprise](https://www.asana.com/pricing) nebo lepším povoleným
* Uživatelský účet v Asana s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní Asana API](https://asana.com/developers/api-reference/users), které je k dispozici pro Asana.

## <a name="assign-users-to-asana"></a>Přiřazení uživatelů k Asana

Azure AD používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé přiřazení k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, kteří uživatelé ve službě Azure AD potřebují přístup k vaší aplikaci Asana. Potom tyto uživatele můžete přiřadit do aplikace Asana podle pokynů uvedených tady:

[Přiřazení uživatele k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Důležité tipy pro přiřazení uživatelů k Asana

Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Asana k otestování konfigurace zřizování. Další uživatele je možné přiřadit později.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurace zřizování uživatelů na Asana

Tato část vás provede připojením služby Azure AD k Asana rozhraní API pro zřizování uživatelských účtů. Službu zřizování můžete také nakonfigurovat tak, aby vytvářela, aktualizovala a zakázala přiřazené uživatelské účty v Asana na základě přiřazení uživatelů v Azure AD.

> [!TIP]
> Pokud chcete povolit jednotné přihlašování založené na SAML pro Asana, postupujte podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na Asana ve službě Azure AD

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory**  >  **podnikové aplikace**  >  **všechny aplikace** .

1. Pokud jste už nakonfigurovali Asana pro jednotné přihlašování, vyhledejte vaši instanci Asana pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Asana** v galerii aplikací. Ve výsledcích hledání vyberte **Asana** a přidejte je do seznamu aplikací.

1. Vyberte instanci Asana a pak vyberte kartu **zřizování** .

1. Nastavte **režim zřizování** na **automaticky**.

    ![Zřizování Asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. V části **přihlašovací údaje správce** postupujte podle těchto pokynů a vygenerujte token a zadejte ho v  **tajném tokenu**:

    a. Přihlaste se k [Asana](https://app.asana.com) pomocí účtu správce.

    b. Vyberte profilovou fotografii z horního panelu a vyberte aktuální nastavení názvu organizace.

    c. Přejít na kartu **účty služby** .

    d. Vyberte **Přidat účet služby**.

    e. Podle potřeby aktualizujte **název** a **informace o** a fotografii profilu. Zkopírujte token v **tokenu** a vyberte ho v části **Uložit změny**.

1. V Azure Portal vyberte **Test připojení** , aby se služba Azure AD mohla připojit k vaší aplikaci Asana. Pokud se připojení nepovede, ujistěte se, že má váš účet Asana oprávnění správce, a zkuste krok **test Connection** znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, které chcete dostávat oznámení o chybách zřizování v  **oznamovacím e-mailu**. Zaškrtněte políčko pod položkou.

1. Vyberte **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Asana**.

1. V části **mapování atributů** zkontrolujte atributy uživatele, které se mají synchronizovat z Azure AD do Asana. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Asana pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny. Další informace najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů](../app-provisioning/customize-application-attributes.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Asana, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

1. Vyberte **Uložit**.

Nyní se spustí počáteční synchronizace pro všechny uživatele přiřazené k Asana v části **Uživatelé** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit. Protokoly auditu popisují všechny akce prováděné službou zřizování ve vaší aplikaci Asana.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [Sestava automatického zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](asana-tutorial.md)
