---
title: 'Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85783339c7d1348f598f924f14d9b40cd0c8cd22
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967171"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Kurz: Konfigurace ServiceNow pro Automatické zřizování uživatelů pomocí Azure Active Directory

Cílem tohoto kurzu je Ukázat kroky, které musíte v ServiceNow a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až ServiceNow.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s ServiceNow potřebujete následující položky:

- Předplatné Azure AD
- Pro ServiceNow, instanci nebo tenant ServiceNow, verze Calgary nebo vyšší
- Pro ServiceNow Express je to instance ServiceNow Express, Helsinky verze nebo vyšší.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte zkušební prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="assigning-users-to-servicenow"></a>Přiřazování uživatelů k ServiceNow

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci ServiceNow. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace ServiceNow podle pokynů uvedených tady: [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Doporučuje se, abyste k testování konfigurace zřizování přiřadili jednoho uživatele Azure AD ServiceNow. Další uživatele a skupiny můžete přiřadit později.
>*   Při přiřazování uživatele k ServiceNow je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.
>*   Další informace o tom, jak vytvořit a nakonfigurovat role v Azure AD, najdete na tomto [odkazu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) .

## <a name="enable-automated-user-provisioning"></a>Povolit automatizované zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v ServiceNow na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
>Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro ServiceNow, a to podle pokynů uvedených v tématu [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

1. Pokud jste už nakonfigurovali ServiceNow pro jednotné přihlašování, vyhledejte vaši instanci ServiceNow pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **ServiceNow** v galerii aplikací. Ve výsledcích hledání vyberte ServiceNow a přidejte je do seznamu aplikací.

1. Vyberte svou instanci ServiceNow a pak vyberte kartu **zřizování** .

1. Nastavte režim **zřizování** na **automaticky**. 

    ![zřizování](./media/servicenow-provisioning-tutorial/provisioning.png)

1. V části pověření správce proveďte následující kroky:
   
    a. Do textového pole **název instance ServiceNow** zadejte název instance ServiceNow.

    b. Do textového pole **uživatelské jméno správce ServiceNow** zadejte uživatelské jméno správce.

    c. V textovém poli **heslo správce ServiceNow** je heslo správce.

1. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci ServiceNow. Pokud se připojení nepovede, ujistěte se, že má váš účet ServiceNow oprávnění správce týmu, a zkuste znovu spustit krok **"přihlašovací údaje správce"** .

1. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail** s oznámením, a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé ServiceNow.**

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do ServiceNow. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v ServiceNow pro operace aktualizace. Vyberte tlačítko Uložit potvrďte změny.

1. Pokud chcete povolit službu Azure AD Provisioning pro ServiceNow, změňte **stav zřizování** na **zapnuto** v části nastavení.

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených ServiceNow v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci ServiceNow.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](servicenow-tutorial.md)


