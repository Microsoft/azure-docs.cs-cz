---
title: 'Kurz: Konfigurace Asana pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Asana.
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
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: f35de1865f2e02331b8455bbdce92e8fe2f9a1b6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210471"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Kurz: Konfigurace Asana pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v Asaně a Azure Active Directory (Azure AD) automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do Asana.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Klient služby Azure AD
*   K tenantovi Asana s [Enterprise](https://www.asana.com/pricing) plán nebo lépe povoleno 
*   Uživatelský účet v Asaně s oprávněními správce 

> [!NOTE] 
> Zřizování integrace Azure AD spoléhá na [Asana API](https://asana.com/developers/api-reference/users), která je k dispozici v Asaně.

## <a name="assign-users-to-asana"></a>Přiřazení uživatelů k Asaně

Azure AD používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé přiřazení k aplikaci v Azure AD. 

Než začnete konfigurovat a povolit službu zřizování, musíte se rozhodnout, které uživatelé ve službě Azure AD potřebují přístup k vaší aplikaci Asana. Pak můžete přiřadit tito uživatelé do vaší aplikace Asana podle zde uvedených pokynů:

[Přiřazení uživatele k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Důležité tipy pro přiřazování uživatelů k Asaně

Doporučujeme vám, přiřadit jeden Asana pro testování konfigurace zřizování uživatele Azure AD. Později můžete přiřadit dalším uživatelům.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurace zřizování uživatelů pro Asaně 

Tato část vás provede připojením služby Azure AD na uživatelský účet v Asaně zřizování rozhraní API. Můžete také nakonfigurovat službu zřizování vytvářet, aktualizovat a vypnout přiřazené uživatelské účty v Asaně podle přiřazení uživatelů ve službě Azure AD.

> [!TIP]
> Povolit založené na SAML jednotného přihlašování pro Asana, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů do Asana ve službě Azure AD

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory** > **podnikové aplikace** > **všechnyaplikace** oddílu.

1. Pokud jste již nakonfigurovali Asana pro jednotné přihlašování, vyhledejte pomocí vyhledávacího pole vaší instance Asana. V opačném případě vyberte **přidat** a vyhledejte **Asana** v galerii aplikací. Vyberte **Asana** ve výsledcích hledání a přidejte do seznamu aplikací.

1. Vyberte instanci Asana a pak vyberte **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování v Asaně](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. V části **přihlašovacích údajů správce** , postupujte podle těchto pokynů k vygenerování tokenu a zadejte ho v **tajný klíč tokenu**:

    a. Přihlaste se k [Asana](https://app.asana.com) s použitím účtu správce.

    b. Vyberte profilové fotky na horním panelu a vyberte vaše aktuální nastavení název organizace.

    c. Přejděte **účty služeb** kartu.

    d. Vyberte **přidejte účet služby**.

    e. Aktualizace **název** a **o** a profilové fotky, podle potřeby. Zkopírujte token v **Token**a vyberte ho v **uložit změny**.

1. Na webu Azure Portal, vyberte **Test připojení** zajistit, že Azure AD může připojit k aplikaci Asana. Pokud se nepovede, ujistěte se, že má váš účet Asana oprávnění správce a zkuste **Test připojení** krok znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která chcete dostávat oznámení zřizování chyby v **e-mailové oznámení**. Zaškrtněte políčko pod.

1. Vyberte **Uložit**. 

1. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Asana**.

1. V **mapování atributů** , projděte si atributy uživatele ze služby Azure AD synchronizovány se službou Asana. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Asaně pro operace update. Vyberte **Uložit** potvrďte všechny změny. Další informace najdete v tématu [přizpůsobení mapování atributů zřízení uživatele](../manage-apps/customize-application-attributes.md).

1. Povolit služba pro Asana, zřizování v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

1. Vyberte **Uložit**. 

Nyní spustí počáteční synchronizaci pro všechny uživatele s přiřazenými v Asaně **uživatelé** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Použití **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokolů aktivit. Protokoly auditu popisují všechny akce provedené v Asaně aplikaci zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [zprávu o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](asana-tutorial.md)
