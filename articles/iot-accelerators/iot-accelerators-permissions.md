---
title: Akcelerátorů řešení Azure IoT a Azure Active Directory | Microsoft Docs
description: Popisuje, jak akcelerátorů řešení Azure IoT používá ke správě oprávnění nástroje Azure Active Directory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa4e1d1f78549a8d1955def7a1c57e61d405e347
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297134"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Oprávnění na webu azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Co se stane, když se přihlásíte

Přihlaste se na prvním [azureiotsuite.com][lnk-azureiotsolutions], lokality Určuje úrovně oprávnění, můžete mít na základě aktuálně vybraného klienta Azure Active Directory (AAD) a předplatné Azure.

1. Nejprve k naplnění seznamu klientů vidět vedle vaše uživatelské jméno, lokality zjistí z Azure které AAD klienty patří do. V současné době lokality může obsahovat pouze tokeny uživatele pro jednoho klienta v čase. Proto že při přepnutí klientů pomocí rozevíracího seznamu v pravém horním rohu webu v protokoly na tohoto tenanta získat tokeny pro tohoto klienta.

2. V dalším kroku webu zjistí z Azure, které odběry byla přidružena vybraného klienta. Když vytvoříte nový akcelerátor řešení zobrazí dostupných předplatných.

3. Nakonec webu načte všechny prostředky v skupiny prostředků a předplatná označené jako řešení akcelerátorů a naplní dlaždice na domovské stránce.

Následující části popisují role, která řídí přístup k akcelerátorů řešení.

## <a name="aad-roles"></a>Role AAD

Role AAD řídit možnost akcelerátorů zřízení řešení, pro správu uživatelů a některé služby Azure v akcelerátor řešení.

Další informace o rolích správce můžete najít v AAD v [přiřazení rolí správce ve službě Azure AD][lnk-aad-admin]. Aktuální článek se týká **globálního správce** a **uživatele** role directory používaného akcelerátorů řešení.

### <a name="global-administrator"></a>Globální správce

Může být mnoho globální správci za klienta AAD:

* Při vytváření klienta služby AAD se ve výchozím nastavení globální správce tohoto klienta.
* Globální správce můžete zřídit akcelerátorů řešení úrovně basic a standard (základní nasazení používá jeden virtuální počítač Azure).

### <a name="domain-user"></a>Uživatel domény

Může být velký počet uživatelů domény na klienta AAD:

* Uživatel domény můžete zřídit akcelerátor základní řešení prostřednictvím [azureiotsolutions.com] [ lnk-azureiotsolutions] lokality.
* Uživatel domény můžete vytvořit akcelerátor základní řešení pomocí rozhraní příkazového řádku.

### <a name="guest-user"></a>Uživatele Guest

Může být velký počet uživatelů typu Host za klienta AAD. Uživatele typu Host mají omezenou sadu oprávnění v tenantovi AAD. V důsledku toho uživatele typu Host nejde zřídit akcelerátor řešení v tenantovi AAD.

Další informace týkající se uživatelů a rolí v AAD najdete v následujících zdrojích informací:

* [Vytvořte uživatele ve službě Azure AD][lnk-create-edit-users]
* [Přiřazení uživatelů k aplikacím][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role správce předplatného Azure

Role Azure správce řízení možnost mapovat předplatné Azure pro klienta služby AAD.

Další informace o Azure správce rolí v článku [postup přidání nebo změna Azure Spolusprávcem, Správce služeb a správce účtu][lnk-admin-roles].

## <a name="faq"></a>Nejčastější dotazy

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Správce služby a v chcete změnit adresář mapování mezi Moje předplatné a konkrétní klienta AAD. Jak tento úkol provést?

V tématu [přidat odběr do adresáře Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Změna služby správce nebo Spolusprávce při přihlášení k účtu organizace

Najdete v článku na podporu [změna správce služeb a Spolusprávce při přihlášení k účtu organizace][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Proč se zobrazuje tato chyba? "Váš účet nemá dostatečná oprávnění k vytvoření řešení. "Svého účtu správce nebo zkuste to znovu s jiným účtem."

Podívejte se na následující diagram pokyny:

![][img-flowchart]

> [!NOTE]
> Pokud se bude opakovat chybu po ověření, je globálním správcem klienta AAD spolusprávcem předplatného a musí správce účtu odebrat uživatele a přiřadit potřebná oprávnění v tomto pořadí. Nejdřív přidejte uživatele jako globální správce a poté přidejte uživatele jako spolusprávce předplatného Azure. Pokud problémy potrvají, obraťte se na [centru pro nápovědu a podporu][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Proč se zobrazuje tato chyba, pokud předplatné Azure? "K vytvoření předem nakonfigurovaná řešení je potřeba předplatné Azure. Bezplatný zkušební účet můžete vytvořit během několika minut."

Pokud jste si jisti, že máte předplatné Azure, ověření klienta mapování pro vaše předplatné a ujistěte se, že je vybrána správná klienta v rozevírací nabídce. Pokud jste ověřit správnost požadované klienta, postupujte podle na předchozím obrázku a ověřit mapování vaše předplatné a tohoto klienta AAD.

## <a name="next-steps"></a>Další postup
Pokračujte ve čtení o akcelerátorů řešení IoT, najdete v tématu jak můžete [přizpůsobit akcelerátor řešení][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles-azure-portal.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]: ../active-directory/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
