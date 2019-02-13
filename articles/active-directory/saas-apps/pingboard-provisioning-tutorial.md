---
title: 'Kurz: Konfigurace Pingboard pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Pingboard.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: e36d0985aee53f2a7f701f9bf7887ae26a426b68
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203739"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Kurz: Konfigurace Pingboard pro automatické zřizování uživatelů

Účelem tohoto kurzu je zobrazit kroky, které je potřeba k povolení automatického zřizování a zrušení zřizování uživatelských účtů ze služby Azure Active Directory (Azure AD) pro Pingboard.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Klient služby Azure AD
*   Pingboard tenant [účet Pro](https://pingboard.com/pricing) 
*   Uživatelský účet v Pingboard s oprávněními správce 

> [!NOTE] 
> Zřizování integrace Azure AD spoléhá na [Pingboard API](https://pingboard.docs.apiary.io/#), která je k dispozici ke svému účtu.

## <a name="assign-users-to-pingboard"></a>Přiřazení uživatelů k Pingboard

Azure AD používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé přiřazení k aplikaci v Azure AD. 

Než začnete konfigurovat a povolit službu zřizování, musíte se rozhodnout, které uživatelé ve službě Azure AD potřebují přístup k vaší aplikaci Pingboard. Pak můžete přiřadit tito uživatelé do vaší aplikace Pingboard podle zde uvedených pokynů:

[Přiřazení uživatele k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Důležité tipy pro přiřazování uživatelů k Pingboard

Doporučujeme vám, přiřadit jeden uživatele Azure AD k Pingboard k otestování konfigurace zřizování. Později můžete přiřadit dalším uživatelům.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurace zřizování uživatelů pro Pingboard 

Tato část vás provede připojení k rozhraní API zřizování Pingboard uživatelský účet služby Azure AD. Můžete také nakonfigurovat službu zřizování vytvářet, aktualizovat a vypnout přiřazené uživatelské účty v Pingboard, které jsou založeny na přiřazení uživatele ve službě Azure AD.

> [!TIP]
> Povolit založené na SAML jednotného přihlašování pro Pingboard, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů do Pingboard ve službě Azure AD

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory** > **podnikové aplikace** > **všechnyaplikace** oddílu.

1. Pokud jste už nakonfigurovali Pingboard pro jednotné přihlašování, vyhledejte pro vaši instanci Pingboard pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Pingboard** v galerii aplikací. Vyberte **Pingboard** ve výsledcích hledání a přidejte do seznamu aplikací.

1. Vyberte instanci Pingboard a pak vyberte **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**.

    ![Pingboard zřizování](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
1. V části **přihlašovacích údajů správce** oddílu, použijte následující postup:

    a. V **adresy URL Tenanta**, zadejte `https://your_domain.pingboard.com/scim/v2`a "doména" nahraďte doménou skutečný.

    b. Přihlaste se k [Pingboard](https://pingboard.com/) s použitím účtu správce.

    c. Vyberte **doplňky** > **integrace** > **Azure Active Directory**.

    d. Přejděte **konfigurovat** kartu a vyberte **povolit zřizování uživatelů z Azure**.

    e. Zkopírujte token v **tokenu nosiče OAuth**a zadejte ho v **tajný klíč tokenu**.

1. Na webu Azure Portal, vyberte **Test připojení** k otestování služby Azure AD můžete připojit k aplikaci Pingboard. Pokud se nepovede, testování, že váš účet Pingboard má oprávnění správce a zkuste **Test připojení** krok znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která chcete dostávat oznámení zřizování chyby v **e-mailové oznámení**. Zaškrtněte políčko pod.

1. Vyberte **Uložit**. 

1. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Pingboard**.

1. V **mapování atributů** , projděte si atributy uživatele ze služby Azure AD synchronizovány se službou Pingboard. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Pingboard pro operace update. Vyberte **Uložit** potvrďte všechny změny. Další informace najdete v tématu [zřizování mapování atributů uživatelů vlastní](../manage-apps/customize-application-attributes.md).

1. Povolit služba pro Pingboard, zřizování v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

1. Vyberte **Uložit** se spustit počáteční synchronizaci uživatelů přidružených k Pingboard.

Počáteční synchronizace trvá delší dobu než následující synchronizuje, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Použití **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokolů aktivit. Protokoly popisují všechny akce v aplikaci Pingboard zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [zprávu o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](pingboard-tutorial.md)
