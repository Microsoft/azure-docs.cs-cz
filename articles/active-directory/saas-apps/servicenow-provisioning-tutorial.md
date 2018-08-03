---
title: 'Kurz: Konfigurace ServiceNow pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD k ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8d05e314cb31aaba96e7db79e0e4dd287e6d2184
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426900"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Kurz: Konfigurace ServiceNow pro automatické zřizování uživatelů pomocí Azure Active Directory

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést ServiceNow a Azure AD a automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD k ServiceNow.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s použitím ServiceNow, potřebujete následující položky:

- S předplatným služby Azure AD
- Pro ServiceNow, instanci nebo tenanta ServiceNow, Calgary verze nebo novější
- Pro ServiceNow Express, instance ServiceNow Express, Helsinki verze nebo novější

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Přiřazování uživatelů k ServiceNow

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují, kteří potřebují přístup k vaší aplikaci ServiceNow. Po se rozhodli, můžete přiřadit tyto uživatele k aplikaci ServiceNow podle pokynů uvedených zde: [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Dále je doporučeno jednoho uživatele Azure AD je přiřazen k ServiceNow k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.
>*   Při přiřazení uživatele k ServiceNow, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede připojením služby Azure AD k uživatelskému účtu ServiceNow na rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v ServiceNow podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
>Můžete také povolena založené na SAML jednotného přihlašování pro ServiceNow, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurovat automatické účet zřizování uživatelů

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

1. Pokud jste už nakonfigurovali ServiceNow pro jednotné přihlašování, vyhledávání pro vaši instanci ServiceNow pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **ServiceNow** v galerii aplikací. Ve výsledcích hledání vyberte ServiceNow a přidat do seznamu aplikací.

1. Vyberte instanci ServiceNow a potom **zřizování** kartu.

1. Nastavte **zřizování** režimu **automatické**. 

    ![zřizování](./media/servicenow-provisioning-tutorial/provisioning.png)

1. V části přihlašovací údaje správce proveďte následující kroky:
   
    a. V **název Instance ServiceNow** textového pole zadejte název instance ServiceNow.

    b. V **uživatelské jméno správce ServiceNow** textového pole zadejte uživatelské jméno správce.

    c. V **heslo správce ServiceNow** textového pole hesla správce.

1. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci ServiceNow. Pokud se nepovede, zajistěte svému účtu ServiceNow má oprávnění správce týmu a **"Přihlašovací údaje správce"** krok znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko.

1. Klikněte na tlačítko **uložit.**

1. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelů k ServiceNow.**

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD k ServiceNow. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v ServiceNow pro operace update. Vyberte tlačítko Uložit potvrďte změny.

1. Chcete-li povolit služba pro ServiceNow zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

1. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k ServiceNow v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci ServiceNow zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](servicenow-tutorial.md)


