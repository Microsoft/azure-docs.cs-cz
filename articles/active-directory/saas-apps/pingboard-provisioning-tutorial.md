---
title: 'Kurz: zřizování uživatelů pro Pingboard – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357891"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Kurz: Konfigurace Pingboard pro Automatické zřizování uživatelů

Účelem tohoto kurzu je Ukázat kroky, které musíte provést, abyste mohli zapnout automatické zřizování a zrušení zřizování uživatelských účtů od Azure Active Directory (Azure AD) až po Pingboard.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenant Azure AD
* Účet tenanta Pingboard [pro](https://pingboard.com/pricing)
* Uživatelský účet v Pingboard s oprávněními správce

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní Pingboard API](https://pingboard.docs.apiary.io/#), které je k dispozici pro váš účet.

## <a name="assign-users-to-pingboard"></a>Přiřazení uživatelů k Pingboard

Azure AD používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé přiřazení k aplikaci v Azure AD. 

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, kteří uživatelé ve službě Azure AD potřebují přístup k vaší aplikaci Pingboard. Potom tyto uživatele můžete přiřadit do aplikace Pingboard podle pokynů uvedených tady:

[Přiřazení uživatele k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Důležité tipy pro přiřazení uživatelů k Pingboard

Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Pingboard k otestování konfigurace zřizování. Další uživatele je možné přiřadit později.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurace zřizování uživatelů na Pingboard 

Tato část vás provede připojením služby Azure AD k rozhraní API pro zřizování uživatelských účtů Pingboard. Službu zřizování můžete také nakonfigurovat tak, aby vytvořila, aktualizovala a zakázala přiřazené uživatelské účty v Pingboard, které jsou založené na přiřazeních uživatelů ve službě Azure AD.

> [!TIP]
> Pokud chcete povolit jednotné přihlašování založené na SAML pro Pingboard, postupujte podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na Pingboard ve službě Azure AD

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory**  >  **podnikové aplikace**  >  **všechny aplikace** .

1. Pokud jste už nakonfigurovali Pingboard pro jednotné přihlašování, vyhledejte vaši instanci Pingboard pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Pingboard** v galerii aplikací. Ve výsledcích hledání vyberte **Pingboard** a přidejte je do seznamu aplikací.

1. Vyberte instanci Pingboard a pak vyberte kartu **zřizování** .

1. Nastavte **režim zřizování** na **automaticky**.

    ![Zřizování Pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. V části **přihlašovací údaje správce** použijte následující postup:

    a. Do **adresy URL tenanta** zadejte `https://your_domain.pingboard.com/scim/v2` a nahraďte "your_domain" skutečnou doménou.

    b. Přihlaste se k [Pingboard](https://pingboard.com/) pomocí účtu správce.

    c. Vyberte možnost **Doplňky pro**  >  **integraci**  >  **Azure Active Directory**.

    d. Otevřete kartu **Konfigurace** a vyberte **Povolit zřizování uživatelů z Azure**.

    e. Zkopírujte token v **nosných tokenech OAuth** a zadejte ho do **tajného tokenu**.

1. V Azure Portal vyberte **Test připojení** pro otestování služby Azure AD se může připojit k vaší aplikaci Pingboard. Pokud se připojení nepovede, otestujte, jestli má váš účet Pingboard oprávnění správce, a zkuste krok **test Connection** znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, které chcete dostávat oznámení o chybách zřizování v **oznamovacím e-mailu**. Zaškrtněte políčko pod položkou.

1. Vyberte **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Pingboard**.

1. V části **mapování atributů** zkontrolujte atributy uživatele, které se mají synchronizovat z Azure AD do Pingboard. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Pingboard pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny. Další informace najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů](../app-provisioning/customize-application-attributes.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Pingboard, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

1. Výběrem **Uložit** spusťte počáteční synchronizaci uživatelů přiřazených k Pingboard.

Počáteční synchronizace trvá déle než následující synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit. Protokoly popisují všechny akce prováděné službou zřizování ve vaší aplikaci Pingboard.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [Sestava automatického zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](pingboard-tutorial.md)
